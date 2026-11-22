# Resultados

Todo sale de `estadistica/resultados_e2/` (análisis confirmatorio, `estadistica/ejecutar_e2.py`), de `resultados/exploratorios_e2/` y `resultados/robustez_e2/` (exploratorios, `src/exploratorios_e2.py` y `src/robustez_interpretabilidad.py`) y de `resultados/piloto_limitacion_e2.json`.

## Tabla principal del benchmark (A.2)

MAE de 2025, 8 semillas por modelo. MZ b es la pendiente de Mincer–Zarnowitz sobre la serie semanal (1 = calibrado). La robustez es el aumento medio del MAE con la severidad más alta de ruido, en el bloque de parada (solo los tres modelos analizados). El rango medio es el de Friedman entre las redes.

| Modelo | MAE (media ± DE) | IC 95 % (semillas) | MZ b | Robustez (ΔMAE alta) | Parámetros | Inferencia (s) | Rango medio |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Mediana del entrenamiento | 0,3060 | — | — | — | — | — | — |
| Persistencia | 0,3450 | — | — | — | — | — | — |
| Ingenuo estacional | 0,3764 | — | — | — | — | — | — |
| Referencia lineal | 0,2829 ± 0,0009 | 0,2821 a 0,2836 | — | — | 15.920 | 0,01 | — |
| MLP (E1) | 0,2741 ± 0,0004 | 0,2738 a 0,2745 | 0,65 | — | 82.622 | 0,02 | 5,25 |
| CNN (E1) | 0,2739 ± 0,0004 | 0,2736 a 0,2742 | 0,67 | +0,0077 | 137.534 | 0,59 | 4,25 |
| LSTM (E1) | 0,2743 ± 0,0006 | 0,2738 a 0,2748 | 0,65 | — | 229.694 | 1,98 | 5,75 |
| MLP (E2) | 0,2748 ± 0,0005 | 0,2744 a 0,2752 | 0,66 | — | 32.574 | 0,01 | 8,38 |
| TFT | 0,2729 ± 0,0011 | 0,2720 a 0,2739 | 0,64 | +0,0067 | 6.741.870 | 12,89 | 2,50 |
| Transformer | 0,2737 ± 0,0008 | 0,2730 a 0,2744 | 0,71 | +0,0069 | 178.942 | 2,25 | 4,12 |
| PatchTST | 0,2741 ± 0,0011 | 0,2731 a 0,2750 | 0,59 | — | 422.655 | 2,05 | 5,00 |
| RNN simple | 0,2743 ± 0,0006 | 0,2739 a 0,2748 | 0,61 | — | 46.462 | 0,23 | 6,25 |
| GRU | 0,2747 ± 0,0008 | 0,2740 a 0,2753 | 0,63 | — | 88.574 | 0,69 | 7,88 |
| DLinear | 0,2751 ± 0,0003 | 0,2748 a 0,2753 | 0,62 | — | 21.608 | 0,02 | 9,75 |
| N-BEATS | 0,2753 ± 0,0003 | 0,2750 a 0,2756 | 0,66 | — | 28.488.164 | 1,98 | 11,00 |
| TCN | 0,2756 ± 0,0007 | 0,2750 a 0,2762 | 0,52 | — | 88.446 | 0,56 | 11,75 |
| N-HiTS | 0,2757 ± 0,0015 | 0,2745 a 0,2770 | 0,60 | — | 1.163.093 | 0,09 | 10,50 |
| iTransformer | 0,2758 ± 0,0006 | 0,2753 a 0,2763 | 0,58 | — | 826.879 | 0,26 | 12,62 |

## Contrastes confirmatorios (A.3)

### F1: frente a la CNN del Entregable 1

| Contraste | Diferencia e IC 95 % | dz | δ de Cliff | p DM-HLN | p Holm (DM) | p Wilcoxon semanas (Holm) | Decisión (preregistro) | Lectura A.3 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| TFT | -0,00099 (-0,00151 a -0,00050) | -0,58 | -0,07 | 0,00012 | 0,00055 | 0,0014 | no concluyente | significativa sin relevancia (mejor) |
| Transformer | -0,00025 (-0,00056 a +0,00007) | -0,20 | -0,03 | 0,161 | 0,322 | 0,350 | equivalente | equivalente |
| PatchTST | +0,00011 (-0,00014 a +0,00037) | 0,11 | 0,00 | 0,449 | 0,449 | 0,736 | equivalente | equivalente |
| RNN simple | +0,00038 (+0,00011 a +0,00063) | 0,40 | 0,04 | 0,0061 | 0,018 | 0,023 | no concluyente | significativa sin relevancia (peor) |
| GRU | +0,00074 (+0,00045 a +0,00102) | 0,58 | 0,05 | 0,00011 | 0,00055 | 0,00052 | no concluyente | significativa sin relevancia (peor) |
| DLinear | +0,00113 (+0,00081 a +0,00143) | 1,05 | 0,07 | 7,4e-10 | 5,2e-9 | 4,4e-7 | no concluyente | significativa sin relevancia (peor) |
| N-BEATS | +0,00134 (+0,00093 a +0,00176) | 1,16 | 0,11 | 3,7e-11 | 2,9e-10 | 1,4e-7 | no concluyente | significativa sin relevancia (peor) |
| TCN | +0,00167 (+0,00129 a +0,00205) | 1,24 | 0,11 | 5,6e-12 | 5,0e-11 | 2,7e-8 | no concluyente | significativa sin relevancia (peor) |
| N-HiTS | +0,00177 (+0,00138 a +0,00217) | 1,38 | 0,11 | 1,5e-13 | 1,5e-12 | 1,7e-8 | no concluyente | significativa sin relevancia (peor) |
| iTransformer | +0,00180 (+0,00131 a +0,00230) | 0,92 | 0,12 | 2,2e-8 | 1,3e-7 | 4,0e-6 | no concluyente | significativa sin relevancia (peor) |

### F2: frente a la mediana del entrenamiento

| Contraste | Diferencia e IC 95 % | dz | δ de Cliff | p DM-HLN | p Holm (DM) | p Wilcoxon semanas (Holm) | Decisión (preregistro) | Lectura A.3 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| TFT | -0,03298 (-0,03511 a -0,03072) | -4,54 | -0,94 | 6,9e-36 | 8,6e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| Transformer | -0,03224 (-0,03421 a -0,03008) | -4,78 | -0,93 | 5,5e-37 | 3,3e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| PatchTST | -0,03188 (-0,03367 a -0,02995) | -5,10 | -0,92 | 2,3e-38 | 2,1e-37 | 3,5e-9 | supera | significativa y relevante (mejor) |
| RNN simple | -0,03161 (-0,03356 a -0,02953) | -4,83 | -0,92 | 3,2e-37 | 2,3e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| GRU | -0,03125 (-0,03315 a -0,02924) | -4,62 | -0,93 | 2,9e-36 | 8,6e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| DLinear | -0,03086 (-0,03282 a -0,02881) | -4,77 | -0,92 | 5,8e-37 | 3,3e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| N-BEATS | -0,03065 (-0,03265 a -0,02850) | -4,74 | -0,92 | 8,4e-37 | 3,4e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| TCN | -0,03032 (-0,03221 a -0,02830) | -4,60 | -0,91 | 3,4e-36 | 8,6e-36 | 3,5e-9 | supera | significativa y relevante (mejor) |
| N-HiTS | -0,03022 (-0,03213 a -0,02816) | -4,96 | -0,92 | 8,6e-38 | 6,8e-37 | 3,5e-9 | supera | significativa y relevante (mejor) |
| iTransformer | -0,03019 (-0,03193 a -0,02840) | -5,38 | -0,92 | 1,6e-39 | 1,6e-38 | 3,5e-9 | supera | significativa y relevante (mejor) |

### Procedimiento global

Friedman con las semillas como bloques (14 modelos, 8 semillas): χ² = 61,46, p = 2,9e-8; W de Kendall = 0,59. Diferencia crítica de Nemenyi: 7,01 rangos. Model Confidence Set (α = 0,10): TFT.

## Adecuación y calibración

| Modelo | MZ a | MZ b | p MZ | p Ljung–Box (4) | p ARCH-LM |
| --- | --- | --- | --- | --- | --- |
| MLP (E1) | 0,249 | 0,653 | 1,1e-25 | 0,86 | 0,98 |
| CNN (E1) | 0,241 | 0,671 | 8,2e-29 | 0,83 | 0,98 |
| LSTM (E1) | 0,247 | 0,652 | 1,3e-24 | 0,86 | 0,99 |
| MLP (E2) | 0,245 | 0,663 | 2,6e-28 | 0,83 | 0,96 |
| PatchTST | 0,285 | 0,591 | 8,0e-27 | 0,90 | 1,00 |
| TFT | 0,259 | 0,641 | 8,3e-27 | 0,90 | 0,98 |
| iTransformer | 0,282 | 0,578 | 5,1e-17 | 0,81 | 0,99 |
| DLinear | 0,269 | 0,620 | 6,1e-26 | 0,79 | 0,97 |
| N-BEATS | 0,246 | 0,661 | 4,5e-28 | 0,84 | 0,96 |
| N-HiTS | 0,277 | 0,601 | 8,3e-25 | 0,85 | 0,97 |
| Transformer | 0,222 | 0,710 | 2,4e-29 | 0,83 | 0,98 |
| TCN | 0,328 | 0,521 | 4,2e-25 | 0,75 | 0,97 |
| GRU | 0,266 | 0,625 | 1,9e-27 | 0,88 | 0,99 |
| RNN simple | 0,275 | 0,609 | 8,0e-26 | 0,83 | 0,98 |

Mincer–Zarnowitz rechaza en todos: las pendientes van de 0,52 a 0,71, así que los pronósticos semanales están comprimidos hacia la media. Los residuos no muestran autocorrelación ni efectos ARCH.

## Paper guía: limitación en semanas de interrupción (piloto)

Criterio preregistrado: IC 95 % (bootstrap por corredor, B = 2000) de la razón por encima de 1 en los tres modelos. Validación de k4.

| Modelo | MAE interrupción | MAE resto | Razón | IC 95 % |
| --- | --- | --- | --- | --- |
| TFT | 0,3294 | 0,2616 | 1,259 | 1,216 a 1,305 |
| PatchTST | 0,3317 | 0,2635 | 1,259 | 1,218 a 1,298 |
| N-BEATS | 0,3220 | 0,2688 | 1,198 | 1,158 a 1,238 |

Los tres intervalos quedan por encima de 1: la limitación es compartida por la familia y no propia del TFT.

## Paper guía: réplica parcial del TFT en Electricity

Script `src/replica_electricity.py` (370 clientes, 53.650 ventanas de prueba, 1 semilla, 15 épocas, unas 2,8 h en una Tesla T4, `pytorch-forecasting` 1.4.0). Métrica: q-Risk normalizado del artículo. El tiempo es estimado: el entrenamiento se hizo en 4 sesiones retomadas desde el último punto de control, y solo la última se midió con el script.

| Cuantil | Obtenido | Publicado | Diferencia relativa |
| --- | --- | --- | --- |
| P50 | 0,0640 | 0,055 | +16 % |
| P90 | 0,0382 | 0,027 | +41 % |

La réplica no cumple el criterio preregistrado (diferencia relativa menor del 10 %); con una sola semilla no se puede aplicar el del intervalo de confianza. Es una réplica reducida, con estos desvíos frente al artículo: otra implementación (`pytorch-forecasting` en lugar del código original), una semilla con tope de 3 horas de entrenamiento frente a unas 6 del artículo, una validación de 50.000 ventanas con parada temprana y agregación horaria por media.

## Sensibilidad sin `frac_caida_nacional_t` (exploratoria)

Mismas configuraciones y tasas de la corrida definitiva, semillas 1 a 3; la referencia son esas mismas semillas en la definitiva.

| Modelo | MAE con la variable | MAE sin la variable | Diferencia |
| --- | --- | --- | --- |
| PatchTST | 0,2742 | 0,2748 | +0,0006 |
| TFT | 0,2727 | 0,2725 | -0,0002 |
| Transformer | 0,2735 | 0,2744 | +0,0009 |

La mayor diferencia en valor absoluto es 0,0009, menor que δ = 0,0032: quitar la variable que señaló la auditoría no cambia la lectura del análisis confirmatorio.

## Error por segmento (exploratorio)

| Modelo | Semanas de interrupción (IC 95 %) | Semanas normales (IC 95 %) |
| --- | --- | --- |
| TFT | 0,3145 (0,2964 a 0,3326) | 0,2583 (0,2524 a 0,2642) |
| Transformer | 0,3170 (0,2972 a 0,3368) | 0,2584 (0,2508 a 0,2661) |
| CNN (E1) | 0,3249 (0,3139 a 0,3360) | 0,2560 (0,2519 a 0,2601) |
| Mediana del entrenamiento | 0,5392 | 0,2238 |

El TFT gana en las semanas de interrupción y pierde un poco en las normales; los intervalos entre semillas se solapan, así que es una descripción. La mediana trivial es la mejor en las semanas normales, lo que concuerda con los pronósticos comprimidos hacia la media. El detalle por historia, mercado y semestre está en `segmentos.csv`.

## Incertidumbre conforme por bloques semanales (exploratoria)

El cuantil de cada semana se calibra con los residuos de las semanas anteriores de la misma corrida.

| Modelo | Nominal | Marginal | Tercil bajo de ŷ | Interrupción | Ancho medio |
| --- | --- | --- | --- | --- | --- |
| CNN (E1) | 0,80 | 0,802 | 0,689 | 0,722 | 0,894 |
| CNN (E1) | 0,90 | 0,903 | 0,833 | 0,915 | 1,287 |
| TFT | 0,80 | 0,803 | 0,691 | 0,702 | 0,920 |
| TFT | 0,90 | 0,902 | 0,834 | 0,908 | 1,328 |
| Transformer | 0,80 | 0,803 | 0,690 | 0,716 | 0,909 |
| Transformer | 0,90 | 0,903 | 0,831 | 0,914 | 1,307 |

La cobertura marginal es la nominal, pero la condicional falla en el tercil bajo de la predicción y, al 80 %, en las semanas de interrupción.

## Robustez, interpretabilidad y ablación (exploratorias)

Modelos de la etapa 1 (semillas 1 a 3) evaluados en el bloque de parada 2023-2024; no se consulta 2025.

| Modelo | Perturbación | Severidad | ΔMAE medio |
| --- | --- | --- | --- |
| CNN (E1) | faltantes | 0,10 | +0,0015 |
| CNN (E1) | faltantes | 0,25 | +0,0043 |
| CNN (E1) | faltantes | 0,50 | +0,0096 |
| CNN (E1) | ruido | 0,10 | +0,0003 |
| CNN (E1) | ruido | 0,25 | +0,0018 |
| CNN (E1) | ruido | 0,50 | +0,0077 |
| TFT | faltantes | 0,10 | +0,0013 |
| TFT | faltantes | 0,25 | +0,0032 |
| TFT | faltantes | 0,50 | +0,0087 |
| TFT | ruido | 0,10 | +0,0005 |
| TFT | ruido | 0,25 | +0,0017 |
| TFT | ruido | 0,50 | +0,0067 |
| Transformer | faltantes | 0,10 | +0,0010 |
| Transformer | faltantes | 0,25 | +0,0030 |
| Transformer | faltantes | 0,50 | +0,0067 |
| Transformer | ruido | 0,10 | +0,0003 |
| Transformer | ruido | 0,25 | +0,0016 |
| Transformer | ruido | 0,50 | +0,0069 |

**Importancia por permutación** (las tres primeras, promedio de semillas):

- CNN (E1): `ventana_nivel`, `ratio_t`, `pend8_rel`; `frac_caida_nacional_t` en el puesto 30 de 40.
- TFT: `ventana_nivel`, `pend8_rel`, `ratio_t`; `frac_caida_nacional_t` en el puesto 21 de 40.
- Transformer: `ventana_nivel`, `ratio_t`, `pend8_rel`; `frac_caida_nacional_t` en el puesto 33 de 40.

**Fidelidad:** permutar las 5 entradas más importantes sube el MAE en 0,046 (CNN (E1)), 0,046 (TFT), 0,057 (Transformer); permutar 5 al azar, en 0,006, 0,006, 0,008.

**Estabilidad entre semillas** (Spearman de las importancias): CNN (E1) 0,91 a 0,94; TFT 0,75 a 0,81; Transformer 0,89 a 0,91.

**Ablación del TFT en inferencia** (sin reentrenar, permutando cada grupo): covariables +0,030, ventana +0,021, categóricas +0,004.
