# credito-riesgo-lab

Proyecto didáctico de **PySpark en Databricks** (nivel estudiante) con arquitectura **medallion** estilo industria: pipeline de riesgo de crédito con clientes sintéticos.

## Arquitectura

```
MANUAL (una vez):  00_bootstrap_datos_sinteticos → tabla landing (simula el sistema origen)
                                                     ↓
JOB (recurrente):  01_bronze_ingesta → 02_silver_limpieza → 03_gold_riesgo
                   copia cruda +      limpieza +           score de riesgo +
                   _fecha_ingesta     coalesce(4)          broadcast join + resúmenes
OPTIMIZACIÓN:      integrada en cada capa, no como etapa aparte
                   · silver (02): coalesce(4) al escribir, con celda antes → después
                   · gold (03): broadcast() en el join, con celda antes → después
```

## Estructura

- `notebooks/00_bootstrap_datos_sinteticos` — genera 10.000 clientes ficticios con "suciedad" → `landing_clientes`. **Ejecutar a mano, una sola vez** (simula el sistema origen; en un banco real lo sustituye la ingesta con Lakeflow Connect)
- `notebooks/01_bronze_ingesta` — copia fiel del origen + `_fecha_ingesta` → `clientes_bronze`
- `notebooks/02_silver_limpieza` — deduplicación, nulos, rangos válidos + `coalesce(4)` → `clientes_silver`
- `notebooks/03_gold_riesgo` — score y nivel de riesgo + `broadcast` join con dimensión + resúmenes → `clientes_gold`, `resumen_riesgo_sector`
- Optimizaciones **integradas en las capas** (como en producción): `coalesce(4)` al escribir silver (02) y `broadcast()` en el join de gold (03), cada una con su celda "antes → después" para ver la mejora
- `rules/reglas_agente.md` — reglas didácticas que sigue el asistente de IA en este proyecto
- Job orquestador: `credito-riesgo-lab-orquestador` (tareas bronze → silver → gold, fuente en Git)

## Cómo ejecutar

1. **Una vez**: ejecuta `00_bootstrap_datos_sinteticos` a mano
2. **Cada vez**: ejecuta el job `credito-riesgo-lab-orquestador` (o añádele un trigger diario)
3. Las técnicas de optimización están DENTRO de 02 y 03 (celdas "antes → después")

## Tablas generadas (`workspace.credito_riesgo_lab`)

| Tabla | Capa | Contenido |
| --- | --- | --- |
| `landing_clientes` | Landing | Simulación del sistema origen (con duplicados y nulos a propósito) |
| `clientes_bronze` | Bronze | Copia cruda + `_fecha_ingesta` |
| `clientes_silver` | Silver | Limpia: sin duplicados, sin nulos, rangos válidos |
| `clientes_gold` | Gold | Score, nivel de riesgo y enriquecimiento por sector |
| `resumen_riesgo_sector` | Gold | Agregados por sector (listo para dashboards) |

## Variables del cliente

`ingresos`, `endeudamiento` (DTI), `ltv`, `antiguedad_meses`, `historial_pagos` (puntual / atrasos_ocasionales / moroso), `sector` (retail, industria, servicios, agro, construccion, tecnologia), `edad`, `num_prestamos`.
