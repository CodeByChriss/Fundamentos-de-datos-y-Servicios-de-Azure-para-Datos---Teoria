# Clase 07 - Fundamentos de datos y servicios de Azure para Datos

**Autor:** Christian Salguero Varas <br>
**Fecha:** 23/09/2026

---

## Ejercicio 1 — Clasifica las fuentes

Clasifica cada fuente como estructurada, semiestructurada o no estructurada y justifica tu respuesta:

**1. Exportación diaria del ERP de compras en CSV.** <br>
Estructurada.

**2. Lecturas de temperatura de las cámaras frigoríficas enviadas cada 30 segundos en JSON.**<br>
Semiestructurada.

**3. Grabaciones MP3 del servicio de atención al cliente.**<br>
No estructurada.

**4. Tabla `Empleados` de la base de datos de RR. HH.**<br>
Estructurada.

**5. Logs de acceso de la tienda online.**<br>
Semiestructurada.

**6. Fotografías de los lineales tomadas por los reponedores.**<br>
No estructurada.

## Ejercicio 2 - Elige el formato

Para cada escenario de Grupo Alimenta, elige el formato más adecuado y justifica la decisión:

**1. Un proveedor pequeño nos envía cada semana su tarifa de precios y solo sabe trabajar con Excel.**<br>
CSV, desde Excel puedes exportar directamente a CSV y si no son una cantidad masiva de datos se puede trabajar con ellos y convertirlos a otro formato mejor diseñado para el objetivo del proyecto.

**2. Almacenar 5 años de líneas de ticket para que los analistas estudien la estacionalidad de ventas por familia de producto.**<br>
El mejor formato es Parquet. Parquet ofrece almacenamiento columnar, lo que nos permite leer directamente las columnas relevantes sin escanear todo el dataset. Además, incluye comprensión y metadatos.

**3. Tabla de socios de fidelización que se actualiza a diario y sobre la que el delegado de protección de datos exige poder ver cómo estaba hace un mes.**<br>
El formato recomendado es Delta Lake ya que aporta transacciones ACID (para poder realizar actualizaciones y borrados diarios de forma segura) y, control de versiones a través de su transaction log. Esto permite consultar el estado exacto de la tabla en cualquier punto pasado.

**4. Flujo continuo de lecturas de temperatura de las cámaras frigoríficas.**<br>
El formato recomendado es Avro para la transmisión e ingesta en streaming y Delta/Parquet para el almacenamiento histórico. Avro es el estándar debido a su baja latencia y su compactación. Una vez ingeridoo sería mejor hacer uso de Delta Lake / Parquet.

**5. Recepción de pedidos de compra con un gran proveedor que usa EDI.**<br>
El formato recomendado es EDIFACT o X12 que es el estándar internacional para el intercambio de documentos comerciales en EDI.

## Ejercicio 3 - OLTP u OLAP

Indica si cada necesidad corresponde a un sistema transaccional o analítico:

**1. Aplicar un cupón de descuento en la caja.**<br>
OLTP. Requiere baja latencia, procesamiento en tiempo real y una operación rápida.

**2. Calcular la evolución de las ventas de productos ecológicos en los últimos 3 años.**<br>
OLAP. Requiere de agregaciones complejas sobre un gran volumen de datos históricos.

**3. Consultar si queda aceite de oliva en la tienda de Valencia ahora mismo.**<br>
OLTP. Una consulta puntual de inventario sobre el estado actual en tiempo real.

**4. Detectar qué tiendas tienen más roturas de stock los lunes.** <br>
OLAP. Requiere analizar el comportamiento histórico de múltiples tiendas agrupando la información por día de la semana para identificar patrones.

**5. Actualizar la dirección de entrega de un pedido online en curso.**<br>
OLTP. Se trata de una operación de modificación (UPDATE) puntual en la base de datos, donde importa la consistencia de la transacción (propiedades ACID).

## Ejercicio 4 — ¿Qué rol es responsable?

**1. El informe de ventas lleva dos días sin actualizarse porque ha fallado la carga nocturna.**<br>
Ingeniero de Datos (Data Engineer).

**2. Hay que restaurar la base de datos de la tienda online tras un borrado accidental.**<br>
Administrador de Bases de Datos (DBA) / Ingeniero de Datos. Depende de quién tenga los permisos para resturar las copias de seguridad.

**3. Dirección quiere un nuevo gráfico de margen por familia de producto en el cuadro de mando.**<br>
Analista de Datos (Data Analyst).

**4. Atención al cliente quiere un asistente que responda "¿cuándo llega mi pedido?".**<br>
Ingeniero de IA.

**5. Hay que enmascarar los teléfonos de los socios antes de que lleguen a la capa analítica.**<br>
Ingeniero de Datos (Data Engineer).

**6. Marketing quiere saber qué socios tienen mayor probabilidad de dejar de comprar en los próximos 3 meses.**<br>
Científico de Datos (Data Scientist).

**7. Se debe decidir si la plataforma se construye en Fabric, en Databricks o combinando ambos.**<br>
Arquitecto de Datos (Data Architect).

**8. Dos departamentos presentan en el comité cifras distintas de "venta neta" para el mismo mes.**<br>
Analytics Engineer.

## Ejercicio 5 : Investiga y arma un diagrama de arquitectura de capas (como el de la sección 8) con cada una de estas tecnologías:

- Databricks (no Azure Databricks)
- Microsoft Fabric. Es decir todo enteramente dentro de Fabric
- AWS
- GCP
- Herramientas Open Source ( el mas importante de todos )
- ¿Y que pasa con Snowflake , dbt y DuckDB?  ¿En que casos se utilizan? ¿En que capas se pueden incluir o con cuales otras tecnologías se puede combinar? Crear al menos 3 diagramas para este caso

## Sección 10: Caso práctico — Plataforma de datos de Grupo Alimenta

<aside>

### Contexto

Grupo Alimenta quiere tres cosas:

1. Un **cuadro de mando de ventas, margen y roturas de stock** por tienda y familia de producto.
2. Un **modelo de previsión de demanda** de productos frescos para reducir la merma.
3. Un **asistente para atención al cliente** que responda sobre el estado de los pedidos online.
</aside>

**Fuentes disponibles:**

| Fuente | Tecnología | Tipo de dato | Frecuencia |
| --- | --- | --- | --- |
| TPV de tiendas y ERP de compras | Azure SQL Database | Estructurado | Cambios continuos |
| App de fidelización | Azure Cosmos DB (JSON) | Semiestructurado | Eventos continuos |
| Sensores de cámaras frigoríficas | Dispositivos IoT | Semiestructurado (streaming) | Cada 30 segundos |
| Tarifas de proveedores | CSV por SFTP | Estructurado | Semanal |
| Facturas de proveedores | PDF escaneados | No estructurado | Por factura |
| Meteorología y festivos | API pública | Semiestructurado | Diario |

### Tareas

**1. **Clasificación.** Clasifica cada fuente (estructurada, semiestructurada, no estructurada) e indica si es OLTP, fichero o stream.**<br>
| Fuente | Tipo de dato | OLTP, fichero o stream |
| :--- | :--- | :--- |
| **TPV / ERP (Azure SQL)** | Estructurado | OLTP |
| **App Fidelización (Cosmos DB)** | Semiestructurado | OLTP |
| **Sensores Frigoríficos (IoT)** | Semiestructurado | Stream |
| **Tarifas Proveedores (SFTP)** | Estructurado | Fichero |
| **Facturas (PDF)** | No estructurado | Fichero |
| **Meteorología y Festivos (API)** | Semiestructurado | Stream / Batch (API) |

<br>

**2. **Arquitectura.** Dibuja  una arquitectura con capas Bronze, Silver y Gold. Indica qué servicio de Azure usarías en cada paso y justifica si optas por Fabric, Databricks u otro stack**<br>


**3. **Formatos.** Indica el formato de almacenamiento de cada capa y por qué.**<br>
* **Bronze:**
  Delta Lake (para fuentes de datos estructuradas/semiestructuradas) y BLOB/Archivos originales (PDF en su formato nativo). Porque mantienen los fichero como en el origen, admitiendo la ingesta sin modificar tipos de datos ni estructuras.
* **Silver:**
  Delta Lake (Parquet + Transaction Log).
  Porque ofrece una compresión eficiente, capacidades de actualización/borrado (`UPDATE`/`DELETE`), evolución de esquemas y transacciones ACID para garantizar datos limpios, sin duplicar y unificados.
* **Gold:**
  Delta Lake / Parquet. Permite la conexión mediante **Direct Lake** desde Power BI, proporcionando el rendimiento de la memoria RAM (In-Memory) directamente sobre el Data Lake sin necesidad de duplicar ni importar los datos.

**4. **Modelo Gold.** Diseña el esquema en estrella de ventas: tabla de hechos, granularidad, medidas y al menos cuatro dimensiones.**<br>


**5. **Roles.** Asigna cada paso de la arquitectura a uno de los siete roles vistos, incluyendo arquitecto de datos, analytics engineer y científico de datos.**<br>
1. Definición de Arquitectura y Topología: Arquitecto de Datos (Data Architect).
2. Configuración de BBDD Operativas y Backups: Administrador de Bases de Datos (DBA).
3. Desarrollo de Pipelines Ingesta (Bronze) y Limpieza (Silver): Ingeniero de Datos (Data Engineer).
4. Modelado Dimensional (Gold), Definición Semántica de Métricas y dbt: Analytics Engineer.
5. Desarrollo del Modelo Predictivo de Previsión de Demanda: Científico de Datos (Data Scientist).
6. Desarrollo del Asistente RAG / LLM en Azure AI Foundry: Ingeniero de IA (AI Engineer).
7. Construcción del Cuadro de Mando en Power BI: Analista de Datos (Data Analyst).

**6. **Métricas.** Como analytics engineer, redacta la definición oficial de "rotura de stock" y dos pruebas de calidad que aplicarías a la tabla Gold.**<br>

Rotura de Stock: Situación operativa en la que el stock físico en tienda es igual a 0 unidades durante el horario de apertura al público, o cuando la demanda no atendida por la falta de stock genera una cancelación de pedidos en las ventas online.

1. Prueba de Integridad Referencial (Unicidad y No Nulos):
   * `ASSERT_NOT_NULL`: Ningún campo clave en `Fact_Ventas` (`ID_Fecha`, `ID_Tienda`, `ID_Producto`) puede contener valores nulos.
2. Prueba de Consistencia de Negocio (Reconciliation Test):
   * `ASSERT`: `Importe_Venta_Neto >= 0` AND `Margen_Bruto <= Importe_Venta_Neto`. Falla la prueba si existen ventas netas negativas no justificadas por devoluciones registradas explícitamente.

**7. **Ciencia de datos.** Indica qué tablas y variables necesitaría el científico de datos para el modelo de previsión de demanda y cómo escribirías sus predicciones de vuelta en la plataforma.**<br>

* Tabla Histórico de Ventas (`Fact_Ventas`): Unidades vendidas por SKU, tienda y día.
* Tabla de Inventario e Incidencias: Registros diarios de merma y roturas de stock.
* Tabla de Variables Externas (`Dim_Meteorologia` / `Dim_Festivos`):
  * `Temperatura_Media`, `Precipitacion_mm`.
  * `Es_Festivo`, `Víspera_De_Festivo`.
  * `Precio_Promocional` (Descuentos vigentes).

Reescritura de predicciones en la plataforma:
1. El modelo entrenado (ej. XGBoost / LightGBM en PySpark) ejecuta la inferencia por lotes diariamente.
2. Las predicciones se guardan en la capa Gold en una tabla Delta llamada `Gold_Prevision_Demanda` (`ID_Producto`, `ID_Tienda`, `Fecha_Futura`, `Unidades_Estimadas`).
3. El ERP de Compras lee directamente esa tabla Delta mediante un conector o vista SQL para sugerir automáticamente los pedidos de reposición a los proveedores.<br><br>

**8. **Gobierno.** Los datos de socios incluyen nombre, teléfono y correo. Explica qué harías en cada capa y qué papel juega Purview.**<br>
* Bronze: Almacena los datos de socios tal como llegan. Restricción estricta de acceso (solo rol de ingesta).
* Silver: Se aplican técnicas de enmascaramiento y hasheado (SHA-256) sobre `Telefono` y `Correo`.
* Gold: Se eliminan completamente las columnas `Nombre`, `Telefono` y `Correo`. Solo se mantienen atributos agregados/demográficos (`Segmento`, `Provincia`).

Papel de Microsoft Purview:
* Descubrimiento y Clasificación: Escanea OneLake automáticamente e identifica los campos que contienen PII (ej. detección de patrones de teléfono y email).
* Linaje de Datos (Data Lineage): Registra el recorrido completo del dato desde Cosmos DB hasta el informe final de Power BI.
* Control de Acceso (Access Policies): Define políticas de masking unificadas a nivel de catálogo para evitar que usuarios no autorizados consulten información confidencial.

**9. **IA.** Explica qué datos necesitaría el ingeniero de IA para construir el asistente en Foundry y qué requisitos de calidad le exigirías como ingeniero de datos.**<br>
1. Estructurados / Transaccionales: Estado del pedido en tiempo real (Base de datos relacional / API de e-commerce: `ID_Pedido`, `Estado`, `Fecha_Estimada_Entrega`, `Direccion`).
2. No Estructurados: Preguntas frecuentes (FAQ), Políticas de devolución, Tiempos de envío por zona geográfica en formato PDF/Markdown.

Requisitos de Calidad Exigidos al Ingeniero de IA:
* Latencia MÁXIMA: La respuesta del bot a la API de estado del pedido no debe superar los X segundos.
* Latencia de Actualización de Datos: Acceso a los datos de tracking con una latencia de sincronización mínima desde el sistema origen.
* Prevención de Alucinaciones: La IA debe limitarse a responder utilizando únicamente el contexto de la orden y la política definida; si el pedido no existe o el dato no está disponible, debe derivar a un agente humano en lugar de estimar o inventar la respuesta.

## Sección 12: Repaso de conceptos

**1. Un fichero donde cada evento puede tener campos distintos y anidados es un ejemplo de dato:**<br>
a) Estructurado **b) Semiestructurado** c) No estructurado d) Binario

**2. ¿Qué formato almacena juntos los valores de cada columna y es el estándar de facto de los lakehouses?**<br>
a) Avro b) CSV **c) Parquet** d) XML

**3. ¿Qué aporta Delta Lake sobre Parquet?**<br>
a) Legibilidad humana **b) Un registro de transacciones con ACID y versionado** c) Almacenamiento orientado a filas d) Soporte exclusivo para imágenes

**4. Si una transacción crea un pedido pero falla la reserva de stock y se deshace todo, se está garantizando la:**<br>
a) Durabilidad **b) Atomicidad** c) Consistencia d) Disponibilidad

**5. En el patrón ELT, las transformaciones se realizan:**<br>
a) Antes de extraer b) En un servidor intermedio **c) En el sistema de destino** d) En la aplicación de origen

**6. ¿Qué capa de la arquitectura medallón contiene datos limpios, deduplicados y con tipos estandarizados?**<br>
a) Bronze **b) Silver** c) Gold d) Platinum

**7. ¿Qué rol es el responsable de monitorizar que los pipelines de carga se ejecutan correctamente?**<br>
a) DBA b) Analista de datos **c) Ingeniero de datos** d) Ingeniero de IA

**8. ¿Qué característica de Azure Storage permite usarlo como data lake?**<br>
a) File shares b) Tables **c) Espacio de nombres jerárquico sobre Blob** d) Colas

**9. ¿Cuál es la opción recomendada de orquestación cuando todo el trabajo de datos se realiza dentro de Microsoft Fabric?**<br>
a) Azure Data Factory **b) Fabric Data Factory** c) Azure Stream Analytics d) Azure Data Explorer

**10. ¿Qué servicio usarías para trazar el linaje de un dato desde el TPV hasta un informe de Power BI?**<br>
a) Microsoft Foundry b) Azure Cosmos DB **c) Microsoft Purview** d) Azure Databricks

**11. Microsoft Fabric se ofrece como:**<br>
a) IaaS b) PaaS **c) SaaS** d) On-premises

**12. Una empresa necesita detectar en tiempo real cámaras frigoríficas que superan un umbral de temperatura. ¿Qué servicio encaja mejor?**<br>
a) Power BI **b) Azure Stream Analytics** c) Azure SQL Managed Instance d) Microsoft Purview

**13. ¿Qué rol es responsable de que la métrica "venta neta" tenga una única definición oficial, probada y documentada?**<br>
a) DBA **b) Analytics engineer** c) Ingeniero de IA d) Científico de datos

**14. ¿Qué rol construiría un modelo para predecir qué clientes dejarán de comprar en los próximos meses?**<br>
a) Analista de datos b) Arquitecto de datos **c) Científico de datos** d) DBA

**15. ¿Qué rol decide si la plataforma de la empresa se organiza por dominios y qué servicios se usan en cada capa?**<br>
**a) Arquitecto de datos** b) Analytics engineer c) Analista de datos d) Ingeniero de IA