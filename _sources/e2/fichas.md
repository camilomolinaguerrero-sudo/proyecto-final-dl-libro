# Fichas de modelo (sección 11.3)

Campos comunes a todos: sin preentrenamiento; optimizador Adam, lote 512, hasta 100 épocas con paciencia 10 sobre el bloque de parada y reentrenamiento con todo el entrenamiento hasta la mejor época; pérdida MAE; precisión float32; una Tesla T4 en Colab. En el Top 10, la tasa se eligió entre {1e-4, 3e-4, 1e-3} (3 intentos) en k4; los modelos del E1 y el MLP del E2 usan la tasa de su propia búsqueda. No se midieron los FLOPs ni la memoria pico; se declara como faltante. Las referencias se verificaron en Crossref o arXiv (`literatura/referencias_modelos_e2.csv`).

## TFT

| Campo | Valor |
| --- | --- |
| Identificación | Lim, Arık, Loeff y Pfister (2021). Temporal Fusion Transformers for interpretable multi-horizon time series forecasting. International Journal of Forecasting, 37(4), 1748-1764. https://doi.org/10.1016/j.ijforecast.2021.03.012. Código en `src/`, commit 1f8466e |
| Arquitectura | del artículo; 6.741.870 parámetros |
| Hiperparámetros | tasa 3e-4 |
| Entrenamiento | épocas de la etapa 1: 15 a 28; mejor época: 5 a 18 |
| Cómputo | entrenamiento medio 32,3 min por corrida; inferencia en 2025 12,89 s |
| MAE 2025 por semilla | 0,2736, 0,2727, 0,2716, 0,2751, 0,2731, 0,2725, 0,2729, 0,2719 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## Transformer

| Campo | Valor |
| --- | --- |
| Identificación | Vaswani et al. (2017). Attention is all you need. arXiv:1706.03762. https://arxiv.org/abs/1706.03762. Código en `src/`, commit 1f8466e |
| Arquitectura | referencia de la sección 14.3: (64, 64, 64), abandono (0, 1, 0); 178.942 parámetros |
| Hiperparámetros | tasa 1e-3 |
| Entrenamiento | épocas de la etapa 1: 16 a 21; mejor época: 6 a 11 |
| Cómputo | entrenamiento medio 6,5 min por corrida; inferencia en 2025 2,25 s |
| MAE 2025 por semilla | 0,2743, 0,2738, 0,2724, 0,2741, 0,2731, 0,2749, 0,2738, 0,2732 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## PatchTST

| Campo | Valor |
| --- | --- |
| Identificación | Nie, Nguyen, Sinthong y Kalagnanam (2023). A time series is worth 64 words: Long-term forecasting with Transformers. arXiv:2211.14730. https://arxiv.org/abs/2211.14730. Código en `src/`, commit 1f8466e |
| Arquitectura | del artículo; 422.655 parámetros |
| Hiperparámetros | tasa 3e-4 |
| Entrenamiento | épocas de la etapa 1: 21 a 39; mejor época: 11 a 29 |
| Cómputo | entrenamiento medio 14,8 min por corrida; inferencia en 2025 2,05 s |
| MAE 2025 por semilla | 0,2729, 0,2732, 0,2763, 0,2736, 0,2729, 0,2742, 0,2746, 0,2747 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## iTransformer

| Campo | Valor |
| --- | --- |
| Identificación | Liu et al. (2024). iTransformer: Inverted Transformers are effective for time series forecasting. arXiv:2310.06625. https://arxiv.org/abs/2310.06625. Código en `src/`, commit 1f8466e |
| Arquitectura | del artículo, con D = 256, L = 2 y 8 cabezas; 826.879 parámetros |
| Hiperparámetros | tasa 1e-4 |
| Entrenamiento | épocas de la etapa 1: 19 a 28; mejor época: 9 a 18 |
| Cómputo | entrenamiento medio 2,9 min por corrida; inferencia en 2025 0,26 s |
| MAE 2025 por semilla | 0,2751, 0,2766, 0,2758, 0,2753, 0,2759, 0,2755, 0,2752, 0,2767 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## DLinear

| Campo | Valor |
| --- | --- |
| Identificación | Zeng, Chen, Zhang y Xu (2023). Are Transformers effective for time series forecasting? Proceedings of the AAAI Conference on Artificial Intelligence, 37(9), 11121-11128. https://doi.org/10.1609/aaai.v37i9.26317. Código en `src/`, commit 1f8466e |
| Arquitectura | del artículo; 21.608 parámetros |
| Hiperparámetros | tasa 1e-3 |
| Entrenamiento | épocas de la etapa 1: 16 a 25; mejor época: 6 a 15 |
| Cómputo | entrenamiento medio 0,7 min por corrida; inferencia en 2025 0,02 s |
| MAE 2025 por semilla | 0,2750, 0,2754, 0,2752, 0,2752, 0,2754, 0,2747, 0,2749, 0,2746 |
| Incidencias | 1 corrida retomada desde el punto de control; ninguna divergió |

## N-BEATS

| Campo | Valor |
| --- | --- |
| Identificación | Oreshkin, Carpov, Chapados y Bengio (2020). N-BEATS: Neural basis expansion analysis for interpretable time series forecasting. arXiv:1905.10437. https://arxiv.org/abs/1905.10437. Código en `src/`, commit 1f8466e |
| Arquitectura | del artículo; 28.488.164 parámetros |
| Hiperparámetros | tasa 1e-3 |
| Entrenamiento | épocas de la etapa 1: 17 a 21; mejor época: 7 a 11 |
| Cómputo | entrenamiento medio 10,7 min por corrida; inferencia en 2025 1,98 s |
| MAE 2025 por semilla | 0,2754, 0,2751, 0,2752, 0,2752, 0,2754, 0,2756, 0,2746, 0,2757 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## N-HiTS

| Campo | Valor |
| --- | --- |
| Identificación | Challu, Olivares, Oreshkin, Garza Ramirez, Mergenthaler Canseco y Dubrawski (2023). NHITS: Neural hierarchical interpolation for time series forecasting. Proceedings of the AAAI Conference on Artificial Intelligence, 37(6), 6989-6997. https://doi.org/10.1609/aaai.v37i6.25854. Código en `src/`, commit 1f8466e |
| Arquitectura | del artículo, con pooling [8, 4, 1] y 2 capas de 512; 1.163.093 parámetros |
| Hiperparámetros | tasa 3e-4 |
| Entrenamiento | épocas de la etapa 1: 18 a 26; mejor época: 8 a 16 |
| Cómputo | entrenamiento medio 1,3 min por corrida; inferencia en 2025 0,09 s |
| MAE 2025 por semilla | 0,2756, 0,2747, 0,2743, 0,2746, 0,2747, 0,2764, 0,2786, 0,2768 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## TCN

| Campo | Valor |
| --- | --- |
| Identificación | Bai, Kolter y Koltun (2018). An empirical evaluation of generic convolutional and recurrent networks for sequence modeling. arXiv:1803.01271. https://arxiv.org/abs/1803.01271. Código en `src/`, commit 1f8466e |
| Arquitectura | referencia de la sección 14.3: (64, 64, 64), abandono (0, 1, 0); 88.446 parámetros |
| Hiperparámetros | tasa 1e-4 |
| Entrenamiento | épocas de la etapa 1: 26 a 55; mejor época: 16 a 45 |
| Cómputo | entrenamiento medio 7,9 min por corrida; inferencia en 2025 0,56 s |
| MAE 2025 por semilla | 0,2751, 0,2755, 0,2746, 0,2759, 0,2757, 0,2750, 0,2765, 0,2764 |
| Incidencias | 1 corrida retomada desde el punto de control; ninguna divergió |

## GRU

| Campo | Valor |
| --- | --- |
| Identificación | Cho et al. (2014). Learning phrase representations using RNN encoder-decoder for statistical machine translation. EMNLP 2014, 1724-1734. https://doi.org/10.3115/v1/D14-1179. Código en `src/`, commit 1f8466e |
| Arquitectura | referencia de la sección 14.3: (64, 64, 64), abandono (0, 1, 0); 88.574 parámetros |
| Hiperparámetros | tasa 1e-3 |
| Entrenamiento | épocas de la etapa 1: 17 a 22; mejor época: 7 a 12 |
| Cómputo | entrenamiento medio 2,4 min por corrida; inferencia en 2025 0,69 s |
| MAE 2025 por semilla | 0,2742, 0,2743, 0,2753, 0,2749, 0,2755, 0,2733, 0,2744, 0,2755 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## RNN simple

| Campo | Valor |
| --- | --- |
| Identificación | Elman (1990). Finding structure in time. Cognitive Science, 14(2), 179-211. https://doi.org/10.1207/s15516709cog1402_1. Código en `src/`, commit 1f8466e |
| Arquitectura | referencia de la sección 14.3: (64, 64, 64), abandono (0, 1, 0); 46.462 parámetros |
| Hiperparámetros | tasa 1e-4 |
| Entrenamiento | épocas de la etapa 1: 44 a 65; mejor época: 34 a 55 |
| Cómputo | entrenamiento medio 5,2 min por corrida; inferencia en 2025 0,23 s |
| MAE 2025 por semilla | 0,2742, 0,2752, 0,2746, 0,2737, 0,2742, 0,2750, 0,2737, 0,2740 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## MLP (E2)

| Campo | Valor |
| --- | --- |
| Identificación | Rumelhart, Hinton y Williams (1986). Learning representations by back-propagating errors. Nature, 323(6088), 533-536. https://doi.org/10.1038/323533a0. Código en `src/`, commit 1f8466e |
| Arquitectura | ganadora de la búsqueda de la sección 14; 32.574 parámetros |
| Hiperparámetros | tasa 1e-3 |
| Entrenamiento | épocas de la etapa 1: 15 a 22; mejor época: 5 a 12 |
| Cómputo | entrenamiento medio 0,6 min por corrida; inferencia en 2025 0,01 s |
| MAE 2025 por semilla | 0,2746, 0,2751, 0,2739, 0,2747, 0,2750, 0,2755, 0,2751, 0,2744 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## CNN (E1)

| Campo | Valor |
| --- | --- |
| Identificación | LeCun, Bottou, Bengio y Haffner (1998). Gradient-based learning applied to document recognition. Proceedings of the IEEE, 86(11), 2278-2324. https://doi.org/10.1109/5.726791. Código en `src/`, commit e56750d |
| Arquitectura | configuración del Entregable 1; 137.534 parámetros |
| Hiperparámetros | tasa 1e-4 |
| Entrenamiento | épocas de la etapa 1: 31 a 57; mejor época: 21 a 47 |
| Cómputo | entrenamiento medio 9,1 min por corrida; inferencia en 2025 0,59 s |
| MAE 2025 por semilla | 0,2741, 0,2738, 0,2738, 0,2734, 0,2742, 0,2737, 0,2740, 0,2746 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## MLP (E1)

| Campo | Valor |
| --- | --- |
| Identificación | Rumelhart, Hinton y Williams (1986). Learning representations by back-propagating errors. Nature, 323(6088), 533-536. https://doi.org/10.1038/323533a0. Código en `src/`, commit e56750d |
| Arquitectura | configuración del Entregable 1; 82.622 parámetros |
| Hiperparámetros | tasa 3e-4 |
| Entrenamiento | épocas de la etapa 1: 21 a 32; mejor época: 11 a 22 |
| Cómputo | entrenamiento medio 1,2 min por corrida; inferencia en 2025 0,02 s |
| MAE 2025 por semilla | 0,2742, 0,2740, 0,2736, 0,2749, 0,2737, 0,2742, 0,2740, 0,2744 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

## LSTM (E1)

| Campo | Valor |
| --- | --- |
| Identificación | Hochreiter y Schmidhuber (1997). Long short-term memory. Neural Computation, 9(8), 1735-1780. https://doi.org/10.1162/neco.1997.9.8.1735. Código en `src/`, commit e56750d |
| Arquitectura | configuración del Entregable 1; 229.694 parámetros |
| Hiperparámetros | tasa 3e-4 |
| Entrenamiento | épocas de la etapa 1: 54 a 70; mejor época: 44 a 60 |
| Cómputo | entrenamiento medio 29,2 min por corrida; inferencia en 2025 1,98 s |
| MAE 2025 por semilla | 0,2737, 0,2733, 0,2751, 0,2746, 0,2737, 0,2745, 0,2746, 0,2749 |
| Incidencias | 0 corridas retomadas desde el punto de control; ninguna divergió |

