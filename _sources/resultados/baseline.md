# 5.2-5.3 Ranking, baseline y margen de mejora

*Generado por `estadistica/seccion_baseline.py` desde `resultados/comparacion_definitiva/`; no se edita a
mano. Cubre las secciones 5.2 y 5.3 del enunciado; las pruebas están en el capítulo anterior.*

## Ranking de los tres modelos base

Orden por la métrica primaria, el MAE medio de 2025 entre las 8 semillas. Las reducciones relativas son
(ref − modelo)/ref × 100, frente al mejor y al peor de los tres; un valor negativo indica más error que la
referencia. El tiempo es el de entrenar la segunda etapa en una Tesla T4.

| Puesto | Modelo | MAE (media ± DE) | IC 95 % | ΔMAE vs. mejor | ΔMAE vs. peor | ΔRMSE vs. mejor | ΔRMSE vs. peor | Parámetros | Entrenamiento (s) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | CNN | 0,2739 ± 0,0004 | [0,2736; 0,2742] | 0,00 % | 0,13 % | 0,00 % | -0,39 % | 137.534 | 548 |
| 2 | MLP | 0,2741 ± 0,0004 | [0,2738; 0,2745] | -0,07 % | 0,06 % | -0,10 % | -0,48 % | 82.622 | 71 |
| 3 | LSTM | 0,2743 ± 0,0006 | [0,2738; 0,2748] | -0,13 % | 0,00 % | 0,38 % | 0,00 % | 229.694 | 1.754 |

El orden no tiene respaldo estadístico. Entre la primera y la última red hay 0,13 % de
MAE, y ninguna comparación es significativa (capítulo anterior). Los tamaños de efecto frente al mejor
modelo son pequeños y van en sentidos que el Wilcoxon no distingue de cero:

| Par | Diferencia de MAE | d de Cohen pareada | r rango-biserial | δ de Cliff | Wilcoxon p (Holm) |
| --- | --- | --- | --- | --- | --- |
| CNN − MLP | -0,0002 | -0,31 | -0,17 | -0,22 | 1,00 |
| CNN − LSTM | -0,0003 | -0,46 | -0,50 | -0,31 | 0,75 |

**Precisión frente a costo y complejidad.** El aumento de complejidad no compra precisión:

- La MLP queda a 0,07 % del MAE de la CNN, con 60 % de sus
  parámetros y 13 % de su tiempo de entrenamiento.
- La LSTM tiene 2,8 veces los parámetros de la MLP y entrena
  24,9 veces más lento, sin mejorar el MAE.
- En RMSE el orden se invierte: la LSTM tiene el menor RMSE. La diferencia también es menor que 1 %.

## Baseline

**El baseline es la CNN: MAE de 0,2739 ± 0,0004 en 2025, con IC 95 % de
0,2736 a 0,2742** (t entre 8 semillas). Es el mejor modelo base por la
métrica primaria, como pide la sección 5.3 del enunciado.

La MLP y la LSTM son equivalentes a la CNN dentro de δ (TOST), así que el nivel de error del baseline no
depende de la arquitectura: cualquiera de las tres lo reproduce. Para un modelo de referencia barato en
el Entregable 2, la MLP alcanza el mismo nivel con una fracción del costo. El umbral de abajo se mide
siempre contra el MAE de la CNN.

## Umbral de mejora para modelos avanzados

Un modelo avanzado, evaluado con el mismo protocolo (8 semillas, misma partición y la misma prueba de
2025), debe cumplir dos umbrales frente al baseline:

| Criterio | Diferencia de MAE | MAE que debe alcanzar | Reducción relativa |
| --- | --- | --- | --- |
| Estadísticamente distinguible | 0,0009 | ≤ 0,2731 | 0,32 % |
| Prácticamente relevante (δ) | 0,0032 | ≤ 0,2707 | 1,17 % |

- **Distinguible.** 0,0009 es la diferencia mínima detectable con 8 semillas, con α = 0,05 y potencia
  0,80, a partir de la mayor desviación de las diferencias pareadas entre los modelos base
  (`baseline.json`). Por debajo, una mejora no se distinguiría del ruido entre semillas.
- **Relevante.** δ = 0,0032 es el margen preregistrado: el 1 % del MAE de la mejor referencia ingenua en
  validación. El criterio que manda es este, porque es 3,7 veces mayor que el detectable.
  Un modelo avanzado es una contribución si su MAE en 2025 queda en 0,2707 o menos y la
  diferencia con la CNN es significativa con la misma inferencia del capítulo 4.

## Contraste con una cota de referencia

Para este panel no hay una estimación de ruido irreducible ni un pronóstico operativo con el que
comparar. El contraste disponible son las referencias:

- Sin usar ninguna variable, la mediana del entrenamiento logra un MAE de 0,3060. El baseline baja
  0,0320 desde ahí.
- La referencia lineal, con las mismas entradas y sin capas ocultas, logra 0,2829. Ya recorre el
  72 % del camino entre la mediana y el baseline.

Lo que queda por ganar con más capacidad sobre las mismas entradas parece pequeño frente a lo que ya
ganó la no linealidad. Un modelo avanzado probablemente necesite información nueva (otras variables, otra
escala espacial) más que una arquitectura más grande. Esto es una interpretación, no una prueba.

## Conclusión

- **Significativo frente a relevante.** Frente a las cuatro referencias, las tres redes mejoran de forma
  significativa y relevante (más que δ). Entre ellas no hay una diferencia significativa ni relevante, y el
  TOST las da por equivalentes.
- **Complejidad.** Pasar de 82.622 a 229.694 parámetros y de 71 s a
  1.754 s de entrenamiento no reduce el error.
- **Baseline y margen.** El baseline es la CNN, con un MAE de 0,2739 en 2025. Un modelo avanzado debe
  llegar a un MAE de 0,2707 o menos (1,17 % menos error) para representar una
  mejora relevante.
