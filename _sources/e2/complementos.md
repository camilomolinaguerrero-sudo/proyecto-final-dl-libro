# Complementos: sobreajuste, efectos mixtos y espacio de arquitecturas

Generados por `estadistica/complementos_e2.py` (salidas en `estadistica/resultados_e2_complementos/`) después del análisis confirmatorio. No cambian ninguna decisión preregistrada.

## Brecha entre entrenamiento y validación (tabla B, condición ii)

g = MAE de validación − MAE de entrenamiento en la mejor época de la etapa 1, por semilla. H0: g ≥ δg = 0,032; Wilcoxon unilateral sobre g − δg, como fija el preregistro. Con 8 semillas el p exacto mínimo es 1/256 = 0,0039, que no alcanza el primer umbral de Holm con 15 modelos; el p de Holm se informa como dato. La pérdida de entrenamiento es la media de la época con abandono activo, como la registra el entrenamiento, por eso algunas brechas son negativas.

| Modelo | Brecha mediana | Brecha máxima | p Wilcoxon | p Holm | Cumple |
| --- | --- | --- | --- | --- | --- |
| Referencia lineal | -0,0045 | -0,0040 | 0,0039 | 0,059 | sí |
| LSTM (E1) | -0,0020 | +0,0005 | 0,0039 | 0,059 | sí |
| CNN (E1) | -0,0017 | -0,0007 | 0,0039 | 0,059 | sí |
| MLP (E1) | -0,0011 | +0,0001 | 0,0039 | 0,059 | sí |
| Transformer | -0,0009 | +0,0004 | 0,0039 | 0,059 | sí |
| N-HiTS | -0,0005 | +0,0024 | 0,0039 | 0,059 | sí |
| TCN | -0,0001 | +0,0011 | 0,0039 | 0,059 | sí |
| TFT | -0,0001 | +0,0033 | 0,0039 | 0,059 | sí |
| MLP (E2) | +0,0001 | +0,0014 | 0,0039 | 0,059 | sí |
| DLinear | +0,0003 | +0,0023 | 0,0039 | 0,059 | sí |
| GRU | +0,0004 | +0,0010 | 0,0039 | 0,059 | sí |
| PatchTST | +0,0007 | +0,0019 | 0,0039 | 0,059 | sí |
| N-BEATS | +0,0008 | +0,0017 | 0,0039 | 0,059 | sí |
| RNN simple | +0,0009 | +0,0020 | 0,0039 | 0,059 | sí |
| iTransformer | +0,0010 | +0,0032 | 0,0039 | 0,059 | sí |

## Validación frente a prueba (tabla B, condición iv)

Diferencia por semilla entre el MAE de validación (bloque de parada, 2023-2024) y el de 2025. Positiva: la prueba tiene menos error que la validación.

| Modelo | MAE validación | MAE prueba | Diferencia (IC 95 %) | p t | p Wilcoxon |
| --- | --- | --- | --- | --- | --- |
| Referencia lineal | 0,2881 | 0,2829 | +0,0053 (+0,0046 a +0,0060) | 3,6e-7 | 0,0078 |
| N-HiTS | 0,2824 | 0,2757 | +0,0067 (+0,0053 a +0,0081) | 1,1e-5 | 0,0078 |
| iTransformer | 0,2826 | 0,2758 | +0,0068 (+0,0063 a +0,0074) | 1,2e-8 | 0,0078 |
| TFT | 0,2800 | 0,2729 | +0,0071 (+0,0062 a +0,0079) | 2,1e-7 | 0,0078 |
| Transformer | 0,2808 | 0,2737 | +0,0072 (+0,0064 a +0,0079) | 5,7e-8 | 0,0078 |
| MLP (E1) | 0,2814 | 0,2741 | +0,0072 (+0,0068 a +0,0077) | 3,0e-9 | 0,0078 |
| N-BEATS | 0,2826 | 0,2753 | +0,0073 (+0,0070 a +0,0076) | 2,0e-10 | 0,0078 |
| TCN | 0,2829 | 0,2756 | +0,0073 (+0,0068 a +0,0078) | 5,6e-9 | 0,0078 |
| GRU | 0,2820 | 0,2747 | +0,0074 (+0,0065 a +0,0082) | 1,9e-7 | 0,0078 |
| CNN (E1) | 0,2814 | 0,2739 | +0,0075 (+0,0071 a +0,0079) | 7,4e-10 | 0,0078 |
| MLP (E2) | 0,2823 | 0,2748 | +0,0076 (+0,0071 a +0,0080) | 1,7e-9 | 0,0078 |
| PatchTST | 0,2819 | 0,2741 | +0,0079 (+0,0069 a +0,0089) | 2,9e-7 | 0,0078 |
| LSTM (E1) | 0,2822 | 0,2743 | +0,0079 (+0,0070 a +0,0088) | 1,3e-7 | 0,0078 |
| DLinear | 0,2830 | 0,2751 | +0,0080 (+0,0076 a +0,0083) | 2,1e-10 | 0,0078 |
| RNN simple | 0,2829 | 0,2743 | +0,0085 (+0,0081 a +0,0090) | 1,3e-9 | 0,0078 |

Todos los modelos, incluida la referencia lineal, tienen menos error en 2025 que en validación, y por cantidades parecidas. Un sobreajuste a la validación daría la diferencia opuesta; esto es un cambio de dificultad entre años. El p mínimo del Wilcoxon bilateral con 8 semillas es 0,0078.

## Sensibilidad a la semilla (tabla B)

Coeficiente de variación del MAE entre las 8 semillas y Friedman de las semillas con las 52 semanas como bloques (las semillas comparten semanas, así que la prueba es pareada).

| Modelo | CV del MAE | DE del MAE | χ² Friedman | p Holm |
| --- | --- | --- | --- | --- |
| DLinear | 0,10 % | 0,00028 | 25,3 | 0,0013 |
| N-BEATS | 0,12 % | 0,00033 | 18,0 | 0,012 |
| CNN (E1) | 0,13 % | 0,00036 | 45,2 | 7,6e-7 |
| MLP (E1) | 0,14 % | 0,00040 | 39,0 | 7,9e-6 |
| MLP (E2) | 0,19 % | 0,00052 | 43,4 | 1,4e-6 |
| RNN simple | 0,20 % | 0,00056 | 51,9 | 4,3e-8 |
| iTransformer | 0,22 % | 0,00060 | 37,8 | 9,9e-6 |
| LSTM (E1) | 0,23 % | 0,00064 | 101,5 | 6,8e-18 |
| TCN | 0,25 % | 0,00069 | 86,0 | 8,3e-15 |
| GRU | 0,29 % | 0,00079 | 93,1 | 3,4e-16 |
| Transformer | 0,29 % | 0,00079 | 77,1 | 4,9e-13 |
| Referencia lineal | 0,31 % | 0,00089 | 90,7 | 9,8e-16 |
| TFT | 0,40 % | 0,00109 | 72,3 | 4,0e-12 |
| PatchTST | 0,42 % | 0,00115 | 132,6 | 2,6e-24 |
| N-HiTS | 0,53 % | 0,00147 | 136,8 | 3,6e-25 |

Las semillas difieren de forma significativa en 15 de 15 modelos, pero el CV no pasa de 0,53 %: la DE entre semillas es menor que δ = 0,0032 en todos. Las 8 semillas preregistradas alcanzan.

## Efectos mixtos y descomposición de la varianza (sección 13.2)

Modelo lineal de efectos mixtos sobre el MAE semanal de 2025 (5.824 observaciones: 14 redes × 8 semillas × 52 semanas), con efecto fijo del modelo y efectos aleatorios cruzados de la semana (la unidad) y de la semilla. REML con statsmodels.

| Componente | Varianza | Proporción |
| --- | --- | --- |
| modelo | 6.41e-07 | 0,42 % |
| semana | 1.49e-04 | 97,38 % |
| semilla | 2.34e-08 | 0,02 % |
| residuo | 3.35e-06 | 2,19 % |

| Modelo − CNN | Diferencia (IC 95 %) | Efecto estandarizado | p Holm |
| --- | --- | --- | --- |
| TFT | -0,00099 (-0,00124 a -0,00074) | -0,54 | 4,9e-14 |
| Transformer | -0,00025 (-0,00049 a +0,00000) | -0,13 | 0,157 |
| PatchTST | +0,00011 (-0,00014 a +0,00036) | 0,06 | 0,381 |
| MLP (E1) | +0,00018 (-0,00007 a +0,00043) | 0,10 | 0,306 |
| LSTM (E1) | +0,00035 (+0,00010 a +0,00059) | 0,19 | 0,026 |
| RNN simple | +0,00038 (+0,00013 a +0,00063) | 0,21 | 0,015 |
| GRU | +0,00074 (+0,00049 a +0,00099) | 0,41 | 3,1e-8 |
| MLP (E2) | +0,00085 (+0,00060 a +0,00109) | 0,46 | 1,9e-10 |
| DLinear | +0,00113 (+0,00088 a +0,00138) | 0,62 | 5,2e-18 |
| N-BEATS | +0,00134 (+0,00109 a +0,00159) | 0,73 | 3,8e-25 |
| TCN | +0,00167 (+0,00142 a +0,00192) | 0,91 | 1,3e-38 |
| N-HiTS | +0,00177 (+0,00152 a +0,00202) | 0,97 | 2,9e-43 |
| iTransformer | +0,00180 (+0,00155 a +0,00205) | 0,98 | 1,4e-44 |

La semana explica el 97,4 % de la varianza; el modelo, el 0,42 %, y la semilla, el 0,015 %. La varianza por semilla es mucho menor que la diferencia entre modelos, así que las conclusiones son estables. Los contrastes coinciden en signo y tamaño con los de la familia F1.

## Espacio de arquitecturas del MLP (sección 14.6)

Modelo de efectos principales e interacciones sobre las 1.000 arquitecturas de la fase 1 (R² = 0,37). ANOVA de tipo II con Holm sobre los factores.

| Factor | F | η² | p Holm |
| --- | --- | --- | --- |
| ancho capa 1 | 16,7 | 0,043 | 1,5e-12 |
| ancho capa 2 | 5,9 | 0,015 | 0,00044 |
| ancho capa 3 | 40,1 | 0,103 | 3,4e-30 |
| b1 | 124,1 | 0,080 | 2,5e-26 |
| b2 | 86,0 | 0,055 | 6,6e-19 |
| b3 | 94,2 | 0,061 | 1,8e-20 |
| b1:b2 | 7,1 | 0,005 | 0,024 |
| b1:b3 | 3,9 | 0,003 | 0,048 |
| b2:b3 | 5,2 | 0,003 | 0,045 |

**Tendencia sobre log2 del ancho** (cambio del MAE de parada al duplicar el ancho de la capa):

- Capa 1: -0,00005 (IC -0,00007 a -0,00004).
- Capa 2: -0,00003 (IC -0,00004 a -0,00001).
- Capa 3: -0,00008 (IC -0,00010 a -0,00007).

**Abandono por posición** (efecto en MAE con IC):

- b1: +0,00031 (+0,00024 a +0,00038).
- b2: +0,00028 (+0,00021 a +0,00035).
- b3: +0,00027 (+0,00021 a +0,00034).
- b1:b2: -0,00010 (-0,00018 a -0,00003).
- b1:b3: -0,00008 (-0,00015 a -0,00000).
- b2:b3: -0,00009 (-0,00017 a -0,00001).

**Sin abandono frente a con abandono**, pareado por las 125 combinaciones de anchos: el abandono sube el MAE en +0,00043 (mediana); Wilcoxon p = 4,4e-22, correlación rango-biserial 1,00. Solo en el 1,6 % de los anchos ayuda.

**Ancho × abandono:** la interacción es -0,00010 por cada duplicación del ancho total (IC -0,00017 a -0,00002): el costo del abandono baja un poco en las redes anchas, sin llegar a compensarse.

**Sensibilidad:** la DE entre arquitecturas en la fase 1 es 0,00039 y la DE entre semillas de una misma arquitectura en la fase 2 es 0,00031 (razón 1,24). El rango completo del espacio es 0,0022, menor que δ. Como pide la sección 14.6, se concluye que la arquitectura del MLP es casi irrelevante para este problema.

**Frontera de Pareto:** 8 arquitecturas no dominadas en MAE frente a parámetros y 4 frente a segundos de entrenamiento (listas en `busqueda_resumen.json`).

**Brecha entrenamiento-validación por patrón de abandono y tercil de ancho total** (fase 1):

| Patrón | Ancho bajo | Medio | Alto |
| --- | --- | --- | --- |
| b000 | 0,0046 | 0,0042 | 0,0045 |
| b001 | 0,0053 | 0,0047 | 0,0047 |
| b010 | 0,0054 | 0,0049 | 0,0046 |
| b011 | 0,0049 | 0,0053 | 0,0052 |
| b100 | 0,0051 | 0,0048 | 0,0049 |
| b101 | 0,0051 | 0,0054 | 0,0049 |
| b110 | 0,0052 | 0,0049 | 0,0042 |
| b111 | 0,0053 | 0,0052 | 0,0046 |

Todas las brechas quedan lejos de δg. En la ganadora (`w032-064-032_b000`), la prueba de la tabla B con sus 5 semillas de la fase 2 da brecha mediana 0,0039 y p = 0,031 (el mínimo posible con 5 semillas): cumple.
