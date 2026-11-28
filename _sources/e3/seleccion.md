# Bloque B: selección de las mejores configuraciones

Cada configuración se eligió en validación, nunca en 2025. La tabla da el motivo de cada selección y la métrica de validación como media ± DE con su IC 95 % cuando hubo varias semillas. Los modelos publicados se compararon en tres tasas con una sola ejecución cada una, así que no tienen DE. Parámetros, FLOPs y latencia salen de `resultados/costo_inferencia.csv`, medidos en las mismas condiciones para todos.

| Modelo | Configuración | Motivo de la selección | MAE en validación (k4) | Parámetros | MFLOPs | ms por muestra (p50 / p95) |
| --- | --- | --- | --- | --- | --- | --- |
| CNN (E1) | 128 unidades, 3 capas, dropout 0,1, tasa 0,0001 | menor MAE en el bloque de parada de k4 entre 20 configuraciones (`busqueda_resultados.csv`) | 0,2818 ± 0,0002 [0,2816, 0,2820] (5 semillas) | 137.534 | 10,39 | 0,103 / 0,106 |
| Referencia lineal | tasa 0,0003 | menor MAE en el bloque de parada de k4 entre 3 configuraciones (`busqueda_resultados.csv`) | 0,2879 ± 0,0002 [0,2877, 0,2881] (5 semillas) | 15.920 | 0,00 | 0,017 / 0,018 |
| LSTM (E1) | 128 unidades, 2 capas, dropout 0,3, tasa 0,0003 | menor MAE en el bloque de parada de k4 entre 20 configuraciones (`busqueda_resultados.csv`) | 0,2835 ± 0,0011 [0,2821, 0,2848] (5 semillas) | 229.694 | 20,63 | 0,369 / 0,379 |
| MLP (E1) | 128 unidades, 3 capas, dropout 0,1, tasa 0,0003 | menor MAE en el bloque de parada de k4 entre 20 configuraciones (`busqueda_resultados.csv`) | 0,2819 ± 0,0004 [0,2815, 0,2824] (5 semillas) | 82.622 | 0,13 | 0,020 / 0,021 |
| DLinear | configuración publicada, tasa 0,001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2874 (1 ejecución) | 21.608 | 0,01 | 0,031 / 0,032 |
| GRU | configuración publicada, tasa 0,001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2869 (1 ejecución) | 88.574 | 6,47 | 0,343 / 0,351 |
| iTransformer | configuración publicada, tasa 0,0001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2873 (1 ejecución) | 826.879 | 1,67 | 0,189 / 0,197 |
| N-BEATS | configuración publicada, tasa 0,001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2873 (1 ejecución) | 28.488.164 | 56,81 | 2,829 / 3,686 |
| N-HiTS | configuración publicada, tasa 0,0003 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2872 (1 ejecución) | 1.163.093 | 2,29 | 0,078 / 0,121 |
| PatchTST | configuración publicada, tasa 0,0003 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2865 (1 ejecución) | 422.655 | 7,17 | 0,400 / 0,405 |
| RNN simple | configuración publicada, tasa 0,0001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2879 (1 ejecución) | 46.462 | 2,17 | 0,620 / 0,629 |
| TCN | configuración publicada, tasa 0,0001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2873 (1 ejecución) | 88.446 | 6,49 | 0,096 / 0,116 |
| TFT | configuración publicada, tasa 0,0003 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2837 (1 ejecución) | 6.741.870 | 100,17 | 1,893 / 2,326 |
| Transformer | configuración publicada, tasa 0,001 | menor MAE de parada en k4 entre 3 tasas (`publicados_tasa.csv`) | 0,2848 (1 ejecución) | 178.942 | 10,26 | 0,429 / 0,437 |
| MLP (E2) | arquitectura `w032-064-032_b000` (31.758 parámetros) | la de menos parámetros entre las 21 indistinguibles de la mejor (`arquitectura_mlp_elegida.csv`); sesgo del ganador: Spearman fase 1–fase 2 0,19 | 0,2869 ± 0,0004 [0,2864, 0,2874] (5 semillas) | 32.574 | 0,03 | 0,023 / 0,024 |
| Modelo original | CNN con mezcla por régimen (128 unidades, 3 bloques, dropout 0,1) | tasa con menor MAE de parada en k4 con la semilla 0 (`original_tasa.csv`) | 0,2852 (1 ejecución) | — | — | — |

## Mejor modelo global

Con `resultados/semillas_e3/comparacion_10`, el de menor MAE en 2025 es TFT (0,2730). El Model Confidence Set (α = 0,10) queda en {TFT}, así que el mejor modelo pertenece al conjunto de finalistas.
