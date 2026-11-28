# Evaluación ampliada

Capítulo 8 del cuadro 7. Los conjuntos se eligieron antes de correr cualquier modelo en ellos; su fuente, licencia, versión, hash, selección y partición están en `preregistro/conjuntos_adicionales.md`.

| Conjunto | Corridas | Semillas | Mejor modelo (MAE medio) | MAE del original |
| --- | --- | --- | --- | --- |
| M4 semanal | *pendiente* | | | |
| Dominick | *pendiente* | | | |
| ILINet por estado | 150 | 10 | TFT (0,0727) | *pendiente* |

Fuente: `resultados/conjuntos_adicionales/<conjunto>/metricas_por_corrida.csv`.

La condición 4 de R3 se decide dentro de cada conjunto, con contrastes por semillas y Holm (`consistencia_por_conjunto`). La prueba entre conjuntos (Friedman con Iman–Davenport y la prueba bayesiana de rangos con signo) se informa, pero con tres o cuatro conjuntos tiene potencia muy limitada.
