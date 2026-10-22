# Reglas del agente — proyecto credito-riesgo-lab (didáctico)

Reglas que el asistente de IA debe seguir al trabajar en este proyecto. Son a la vez un ejemplo didáctico de cómo se "instruye" a un agente.

1. **Enseñar, no solo resolver.** Cada celda nueva empieza con un comentario que explica el concepto (qué hace y por qué).
2. **Español siempre.** Comentarios, nombres de variables y mensajes en español; `snake_case` para nombres.
3. **Reproducible.** Toda generación de datos usa semilla fija (`SEED = 42`): mismos datos en cada ejecución.
4. **Pequeños pasos.** Celdas cortas y numeradas (`# 1)`, `# 2)`…), ejecutables de arriba a abajo; nunca un notebook-monolito.
5. **Medallion siempre.** Crudo → bronze; limpieza → silver; negocio → gold. No mezclar responsabilidades entre capas.
6. **Seguro.** Datos 100% ficticios; nunca escribir credenciales ni datos reales de clientes en el código.
7. **Verificar.** Tras escribir una tabla, contar filas y mostrar 5 filas de ejemplo antes de dar el paso por terminado.
8. **Compatible con Spark Connect / Serverless.** Nada de RDDs, `spark.sparkContext` ni `.mode("errorifexists")`; usar `.mode("overwrite")`.
9. **Ahorro de recursos.** En datasets grandes: cachear lo que se reutiliza, evitar `repartition()` innecesarios, preferir `coalesce()` para reducir particiones y `broadcast()` en joins con tablas pequeñas.
