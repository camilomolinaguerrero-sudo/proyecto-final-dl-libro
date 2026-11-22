# Datos, EDA y auditoría de fuga

La Data Card, el EDA general y el de series son los del Entregable 1 (partes 1-2 del libro);
los datos, el objetivo y la partición no cambian. Prueba: 2025, hash `988ab7ac…2c78753`. Lo nuevo del Entregable 2 es la predictibilidad, la auditoría de fuga
ejecutable y la tabla hallazgo-decisión (página siguiente).

## Predictibilidad

Entropía de permutación normalizada (orden 4) de `y_reg`: mediana 0,978 en 300 corredores elegibles (de 1.984) y 0,986 en la serie nacional, frente a 0,989 del ruido blanco. Las series están muy cerca del ruido, así que hay poco margen para que un modelo más complejo mejore el pronóstico.

## Auditoría de fuga (sección 10.1)

Generada por `auditoria/auditar_fuga.py` (salida completa en `auditoria/resultados/auditoria_fuga.md`).

| Verificación | Resultado |
| --- | --- |
| fuga_temporal | aprueba |
| interseccion_corredores | aprueba |
| duplicados_exactos | aprueba |
| casi_duplicados | aprueba |
| fuga_por_transformaciones | aprueba |
| covariables_permitidas | aprueba |
| fuga_por_atributos | aprueba |
| validacion_adversarial | NO aprueba |
| etiquetas_permutadas | aprueba |

La validación adversarial no aprueba: un clasificador separa entrenamiento de prueba con AUC 0,99. La covariable que más separa es `frac_caida_nacional_t` (AUC univariado 0,75); sin ella el AUC baja a 0,71. Es un cambio de distribución entre años, no una copia de la etiqueta: las demás verificaciones aprueban, y la prueba con etiquetas permutadas da el MAE de la mediana. Por eso se preregistró una corrida de sensibilidad sin esa variable (resultados, sección de sensibilidad).
