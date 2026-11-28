# Incertidumbre, calibración y costo

Los intervalos conformales en línea y la calibración salen de `estadistica/incertidumbre_e3.py`, con las predicciones de 2025 ya registradas (semillas 1 a 8). El costo de inferencia sale de `src/costo_artefactos.py`, con los artefactos ONNX medidos en la misma CPU. Nada de esto reentrena ni elige modelos.

## Intervalos conformales adaptativos al 90 %

| Modelo | Cobertura | En interrupción | En el resto | Ancho medio |
| --- | --- | --- | --- | --- |
| Referencia lineal | 0,902 | 0,924 | 0,895 | 1,273 |
| DLinear | 0,902 | 0,914 | 0,898 | 1,284 |
| CNN (E1) | 0,902 | 0,914 | 0,898 | 1,284 |
| LSTM (E1) | 0,902 | 0,903 | 0,901 | 1,286 |
| MLP (E2) | 0,902 | 0,910 | 0,898 | 1,287 |
| TCN | 0,902 | 0,913 | 0,898 | 1,288 |
| MLP (E1) | 0,902 | 0,907 | 0,900 | 1,290 |
| RNN simple | 0,902 | 0,911 | 0,899 | 1,290 |
| PatchTST | 0,902 | 0,907 | 0,900 | 1,293 |
| N-BEATS | 0,902 | 0,908 | 0,900 | 1,302 |
| GRU | 0,902 | 0,905 | 0,900 | 1,303 |
| Transformer | 0,902 | 0,913 | 0,898 | 1,304 |
| N-HiTS | 0,902 | 0,904 | 0,901 | 1,307 |
| iTransformer | 0,902 | 0,893 | 0,905 | 1,314 |
| TFT | 0,902 | 0,907 | 0,900 | 1,325 |

Fuente: `estadistica/resultados_e3_incertidumbre/conformal_resumen.csv`.

## Costo de inferencia

| Modelo | MAE 2025 (semilla 1) | Parámetros | MFLOPs por observación | ms por muestra (p50 / p95) | Memoria pico (MB) |
| --- | --- | --- | --- | --- | --- |
| PatchTST | 0,2729 | 422.655 | 7,17 | 0,400 / 0,405 | 170 |
| TFT | 0,2736 | 6.741.870 | 100,17 | 1,893 / 2,326 | 342 |
| LSTM (E1) | 0,2737 | 229.694 | 20,63 | 0,369 / 0,379 | 109 |
| CNN (E1) | 0,2741 | 137.534 | 10,39 | 0,103 / 0,106 | 39 |
| MLP (E1) | 0,2742 | 82.622 | 0,13 | 0,020 / 0,021 | 12 |
| GRU | 0,2742 | 88.574 | 6,47 | 0,343 / 0,351 | 54 |
| RNN simple | 0,2742 | 46.462 | 2,17 | 0,620 / 0,629 | 56 |
| Transformer | 0,2743 | 178.942 | 10,26 | 0,429 / 0,437 | 268 |
| MLP (E2) | 0,2746 | 32.574 | 0,03 | 0,023 / 0,024 | 11 |
| DLinear | 0,2750 | 21.608 | 0,01 | 0,031 / 0,032 | 12 |
| iTransformer | 0,2751 | 826.879 | 1,67 | 0,189 / 0,197 | 50 |
| TCN | 0,2751 | 88.446 | 6,49 | 0,096 / 0,116 | 33 |
| N-BEATS | 0,2754 | 28.488.164 | 56,81 | 2,829 / 3,686 | 195 |
| N-HiTS | 0,2756 | 1.163.093 | 2,29 | 0,078 / 0,121 | 24 |
| Referencia lineal | 0,2824 | 15.920 | 0,00 | 0,017 / 0,018 | 12 |

Fuente: `resultados/costo_inferencia.csv`. N-BEATS se sirve en INT8: su TOST semanal frente a δ da equivalencia.
