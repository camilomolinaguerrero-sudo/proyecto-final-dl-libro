# Análisis confirmatorio del Entregable 2

Generado por `estadistica/ejecutar_e2.py`. δ = 0.0032. Pérdida: MAE semanal (52 semanas de 2025),
promediada sobre las semillas. Diferencia = modelo − referencia (negativa: el modelo es mejor).

## Métricas por modelo

| Modelo | Semillas | MAE (media ± DE) | RMSE | MASE | AUC interrupción |
| --- | --- | --- | --- | --- | --- |
| tft | 10 | 0.2730 ± 0.0012 | 0.4130 | 0.7883 | 0.7522 |
| transformer | 10 | 0.2737 ± 0.0007 | 0.4083 | 0.7902 | 0.7564 |
| cnn | 10 | 0.2740 ± 0.0004 | 0.4024 | 0.7911 | 0.7630 |
| mlp | 10 | 0.2741 ± 0.0004 | 0.4027 | 0.7914 | 0.7573 |
| patchtst | 10 | 0.2742 ± 0.0011 | 0.4022 | 0.7917 | 0.7540 |
| rnn | 10 | 0.2745 ± 0.0007 | 0.4030 | 0.7926 | 0.7588 |
| lstm | 10 | 0.2745 ± 0.0007 | 0.4009 | 0.7926 | 0.7570 |
| gru | 10 | 0.2747 ± 0.0007 | 0.4061 | 0.7931 | 0.7505 |
| mlp_e2 | 10 | 0.2748 ± 0.0005 | 0.4032 | 0.7935 | 0.7588 |
| dlinear | 10 | 0.2751 ± 0.0003 | 0.4025 | 0.7943 | 0.7617 |
| nbeats | 10 | 0.2753 ± 0.0003 | 0.4054 | 0.7949 | 0.7520 |
| tcn | 10 | 0.2755 ± 0.0006 | 0.4051 | 0.7956 | 0.7563 |
| nhits | 10 | 0.2756 ± 0.0013 | 0.4062 | 0.7957 | 0.7516 |
| itransformer | 10 | 0.2757 ± 0.0006 | 0.4061 | 0.7962 | 0.7498 |
| lineal | 10 | 0.2830 ± 0.0009 | 0.4030 | 0.8171 | 0.7631 |
| mediana_entrenamiento | 1 | 0.3060 | 0.4087 | 0.8834 | 0.5000 |
| persistencia | 1 | 0.3450 | 0.5078 | 0.9962 | 0.5838 |
| ingenuo_estacional | 1 | 0.3764 | 0.5429 | 1.0868 | 0.5850 |

## F1: frente a la CNN del Entregable 1

| Modelo | Diferencia | IC 95 % (bloques) | p DM | p Holm | p Wilcoxon semanas | dz | Decisión (preregistro) | Lectura A.3 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| patchtst | +0.00022 | -0.00006 a +0.00052 | 0.16 | 0.16 | 0.36 | 0.20 | equivalente | equivalente |
| tft | -0.00094 | -0.00144 a -0.00048 | 0.00014 | 0.0007 | 0.00054 | -0.57 | no concluyente | significativa sin relevancia (mejor) |
| itransformer | +0.00175 | +0.00128 a +0.00224 | 3.6e-08 | 2.1e-07 | 6e-07 | 0.90 | no concluyente | significativa sin relevancia (peor) |
| dlinear | +0.00112 | +0.00078 a +0.00144 | 8.9e-09 | 6.2e-08 | 2.8e-07 | 0.95 | no concluyente | significativa sin relevancia (peor) |
| nbeats | +0.00133 | +0.00091 a +0.00175 | 3.6e-10 | 2.9e-09 | 6.3e-08 | 1.07 | no concluyente | significativa sin relevancia (peor) |
| nhits | +0.00161 | +0.00121 a +0.00201 | 1.4e-11 | 1.3e-10 | 5e-09 | 1.20 | no concluyente | significativa sin relevancia (peor) |
| transformer | -0.00032 | -0.00063 a -0.00001 | 0.074 | 0.15 | 0.073 | -0.25 | equivalente | equivalente |
| tcn | +0.00157 | +0.00118 a +0.00195 | 1.1e-11 | 1.1e-10 | 4.5e-09 | 1.21 | no concluyente | significativa sin relevancia (peor) |
| gru | +0.00069 | +0.00040 a +0.00099 | 0.00015 | 0.0007 | 0.00028 | 0.57 | no concluyente | significativa sin relevancia (peor) |
| rnn | +0.00051 | +0.00022 a +0.00079 | 0.00052 | 0.0016 | 0.00086 | 0.51 | no concluyente | significativa sin relevancia (peor) |

## F2: frente a la mediana del entrenamiento

| Modelo | Diferencia | IC 95 % (bloques) | p DM | p Holm | p Wilcoxon semanas | dz | Decisión (preregistro) | Lectura A.3 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| patchtst | -0.03173 | -0.03351 a -0.02982 | 1.6e-38 | 1.4e-37 | 3.5e-10 | -5.13 | supera | significativa y relevante (mejor) |
| tft | -0.03290 | -0.03500 a -0.03065 | 5.6e-36 | 5.6e-36 | 3.5e-10 | -4.56 | supera | significativa y relevante (mejor) |
| itransformer | -0.03020 | -0.03194 a -0.02840 | 1.5e-39 | 1.5e-38 | 3.5e-10 | -5.38 | supera | significativa y relevante (mejor) |
| dlinear | -0.03084 | -0.03280 a -0.02877 | 7.9e-37 | 4e-36 | 3.5e-10 | -4.74 | supera | significativa y relevante (mejor) |
| nbeats | -0.03062 | -0.03261 a -0.02850 | 4.5e-37 | 3.2e-36 | 3.5e-10 | -4.80 | supera | significativa y relevante (mejor) |
| nhits | -0.03034 | -0.03223 a -0.02831 | 8.2e-38 | 6.5e-37 | 3.5e-10 | -4.97 | supera | significativa y relevante (mejor) |
| transformer | -0.03227 | -0.03426 a -0.03009 | 8e-37 | 4e-36 | 3.5e-10 | -4.74 | supera | significativa y relevante (mejor) |
| tcn | -0.03039 | -0.03229 a -0.02836 | 2.6e-36 | 5.3e-36 | 3.5e-10 | -4.63 | supera | significativa y relevante (mejor) |
| gru | -0.03126 | -0.03314 a -0.02927 | 1.8e-36 | 5.3e-36 | 3.5e-10 | -4.66 | supera | significativa y relevante (mejor) |
| rnn | -0.03144 | -0.03338 a -0.02935 | 5.9e-37 | 3.5e-36 | 3.5e-10 | -4.77 | supera | significativa y relevante (mejor) |

## Global

- Friedman: χ² = 70.80, p = 5.7e-10; Iman–Davenport F = 10.76, p = 9.7e-15; W de Kendall = 0.54; diferencia crítica de Nemenyi = 6.27 rangos.
- Rangos medios: tft 2.70, transformer 3.60, cnn 4.20, mlp 4.80, patchtst 5.70, lstm 6.80, rnn 6.80, gru 7.60, mlp_e2 8.20, dlinear 9.80, nhits 10.10, nbeats 11.00, tcn 11.30, itransformer 12.40.
- Model Confidence Set (α = 0,10): tft.

## Adecuación (residuos semanales)

| Modelo | MZ a | MZ b | p MZ | p Ljung–Box (4) | p ARCH-LM | p BDS (descriptivo) |
| --- | --- | --- | --- | --- | --- | --- |
| mlp | 0.248 | 0.654 | 1.2e-25 | 0.85 | 0.98 | 0.60 |
| cnn | 0.242 | 0.672 | 4.2e-29 | 0.83 | 0.99 | 0.63 |
| lstm | 0.260 | 0.628 | 5.4e-24 | 0.87 | 0.99 | 0.86 |
| mlp_e2 | 0.268 | 0.622 | 1.3e-27 | 0.85 | 0.97 | 0.44 |
| patchtst | 0.292 | 0.577 | 1.1e-25 | 0.89 | 1.00 | 0.47 |
| tft | 0.253 | 0.650 | 2e-26 | 0.91 | 0.99 | 0.18 |
| itransformer | 0.280 | 0.581 | 1.2e-17 | 0.80 | 0.98 | 0.36 |
| dlinear | 0.285 | 0.592 | 1.9e-25 | 0.82 | 0.98 | 0.50 |
| nbeats | 0.239 | 0.670 | 1.2e-26 | 0.84 | 0.97 | 0.70 |
| nhits | 0.291 | 0.575 | 1.9e-24 | 0.86 | 0.97 | 0.41 |
| transformer | 0.233 | 0.689 | 1e-28 | 0.84 | 0.98 | 0.59 |
| tcn | 0.323 | 0.531 | 6.6e-26 | 0.77 | 0.98 | 0.61 |
| gru | 0.258 | 0.638 | 6.6e-27 | 0.89 | 0.99 | 0.41 |
| rnn | 0.288 | 0.589 | 3.5e-26 | 0.83 | 0.99 | 0.65 |
