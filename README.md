# credito-riesgo-lab

Proyecto didáctico de **PySpark en Databricks** (nivel estudiante): datos **sintéticos** de clientes con variables de riesgo de crédito y un **pipeline medallion** (bronze → silver → gold).

## Estructura

- `notebooks/00_datos_sinteticos` — genera 10.000 clientes ficticios → `clientes_bronze`
- `notebooks/01_medallion_pipeline` — bronze → silver → gold (`score_riesgo` + resumen por sector)
- `notebooks/02_exploracion_pyspark` — exploración básica: `select`, `filter`, `groupBy`, SQL
- `notebooks/03_optimizacion_pyspark` — optimización de recursos: `cache`, `repartition/coalesce`, `broadcast`, `partitionBy`
- `rules/reglas_agente.md` — reglas didácticas que sigue el asistente de IA en este proyecto

## Cómo ejecutar (en orden)

1. `00_datos_sinteticos` — crea el esquema `workspace.credito_riesgo_lab` y la tabla bronze
2. `01_medallion_pipeline` — crea silver y gold
3. `02_exploracion_pyspark` — consulta las tablas
4. `03_optimizacion_pyspark` — técnicas de optimización sobre los datos

## Tablas generadas (`workspace.credito_riesgo_lab`)

| Tabla | Capa | Contenido |
| --- | --- | --- |
| `clientes_bronze` | Bronze | Datos crudos + `_fecha_ingesta` (con duplicados y nulos a propósito) |
| `clientes_silver` | Silver | Limpia: sin duplicados, sin nulos, rangos válidos |
| `clientes_gold` | Gold | Score y nivel de riesgo por cliente |
| `resumen_riesgo_sector` | Gold | Agregados por sector |

## Variables del cliente

`ingresos`, `endeudamiento` (DTI), `ltv`, `antiguedad_meses`, `historial_pagos` (puntual / atrasos_ocasionales / moroso), `sector` (retail, industria, servicios, agro, construccion, tecnologia), `edad`, `num_prestamos`.
