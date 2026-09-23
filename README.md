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