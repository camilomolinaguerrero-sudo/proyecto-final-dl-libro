# 5. Resultados confirmatorios en 2025

*Generado por `src/capitulo_resultados.py` desde `resultados/`; no se edita a mano.*

## Corrida definitiva

Se entrenaron 32 modelos: 4 familias (Lineal, CNN, MLP, LSTM)
con las semillas 1 a 8, cada una con la configuración que eligió la búsqueda. Cada corrida
siguió las dos etapas preregistradas: primero fijó la mejor época E con parada temprana, y después
reentrenó desde cero con todo el entrenamiento (hasta 2024-10-21) durante E épocas. Ese segundo modelo
evaluó 2025 una sola vez.

Las 32 evaluaciones quedaron registradas en `resultados/evaluaciones_prueba.csv` antes de ocurrir.
Todas citan el mismo conjunto de prueba (hash `988ab7ac22cb…`) y el mismo commit, `e56750d`, el del
cierre del preregistro. Ninguna corrida falló y ninguna se repitió. Las referencias ingenuas se
calcularon sobre las mismas filas de 2025 con `src/referencias.py --prueba`.

## Tabla de resultados en 2025

Media y desviación estándar entre semillas; el intervalo es t al 95 % entre las 8 semillas. Las
referencias ingenuas son deterministas y tienen una sola corrida. El tiempo de entrenamiento es el de la
segunda etapa en una Tesla T4. Fuente: `resultados/comparacion_definitiva/tabla_comparativa.csv`.

| Modelo | Corridas | MAE (media ± DE) | IC 95 % del MAE | RMSE | MASE | AUC de interrupción | Parámetros | Entrenamiento (s) | Inferencia (s) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CNN | 8 | 0,2739 ± 0,0004 | [0,2736; 0,2742] | 0,4022 | 0,7910 | 0,7623 | 137.534 | 548 | 0,59 |
| MLP | 8 | 0,2741 ± 0,0004 | [0,2738; 0,2745] | 0,4026 | 0,7915 | 0,7579 | 82.622 | 71 | 0,02 |
| LSTM | 8 | 0,2743 ± 0,0006 | [0,2738; 0,2748] | 0,4007 | 0,7920 | 0,7566 | 229.694 | 1.754 | 1,98 |
| Lineal | 8 | 0,2829 ± 0,0009 | [0,2821; 0,2836] | 0,4027 | 0,8167 | 0,7637 | 15.920 | 27 | 0,01 |
| Mediana del entrenamiento | 1 | 0,3060 | — | 0,4087 | 0,8834 | 0,5000 | — | — | — |
| Persistencia | 1 | 0,3450 | — | 0,5078 | 0,9962 | 0,5838 | — | — | — |
| Ingenuo estacional | 1 | 0,3764 | — | 0,5429 | 1,0868 | 0,5850 | — | — | — |

## Pregunta 1: ¿difieren las tres redes entre sí?

**No.** Ninguna diferencia es significativa con la prueba principal, y las tres son equivalentes dentro
del margen preregistrado δ = 0,0032.

| Par (a − b) | Diferencia de MAE | IC 95 % (t) | Wilcoxon p | Wilcoxon p Holm | t p Holm | DM-HLN p Holm | TOST p | P(dentro de ±δ) | δ de Cliff |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CNN − LSTM | −0,0003 | [−0,0010; 0,0003] | 0,250 | 0,750 | 0,699 | 0,108 | < 0,0001 | 1,000 | −0,312 |
| CNN − MLP | −0,0002 | [−0,0007; 0,0003] | 0,742 | 1,000 | 0,828 | 0,429 | < 0,0001 | 1,000 | −0,219 |
| LSTM − MLP | 0,0002 | [−0,0004; 0,0007] | 0,742 | 1,000 | 0,828 | 0,429 | < 0,0001 | 1,000 | 0,219 |

Diferencia `a − b` del MAE medio; negativa si `a` tiene menor error. La prueba principal es el Wilcoxon de
rangos con signo sobre las 8 semillas, con Holm sobre los 3 pares; la t pareada es la sensibilidad y el
Diebold-Mariano con la corrección HLN compara la pérdida de las 52 semanas. Fuente:
`resultados/comparacion_definitiva/comparaciones_pareadas.csv`.

- **Diferencias mínimas.** La mayor diferencia media entre dos redes es 0,0003, el
  11 % de δ. Ningún p ajustado baja de 0,05 con ninguna de las tres pruebas. El menor p sin
  ajustar es el del Diebold-Mariano CNN − LSTM (0,036), que con Holm
  sube a 0,108.
- **Prueba global.** El Friedman con las semillas como bloques da χ² = 0,25 y p = 0,882
  (W de Kendall = 0,016; rangos medios: CNN 1,875, MLP 2,000, LSTM 2,125). Con las semanas como bloques da
  p = 0,098, y ningún par del Nemenyi es significativo. El ANOVA de medidas repetidas da
  F = 1,03 y p = 0,384.
- **Equivalencia.** El TOST rechaza en los tres pares que la diferencia salga de ±δ, y la probabilidad
  posterior de quedar dentro de ±δ es prácticamente 1 (la menor es 0,99999). No es solo falta de
  evidencia: el diseño detecta diferencias desde 0,0009 de MAE, el 27 % de δ, con una
  potencia para δ de 1,00 en los tres pares.
- **Semillas frente a modelos.** La varianza entre semillas es 7,8 veces la
  varianza entre modelos; elegir la red explica el 11 % de la variación del MAE
  (`varianza.json`).

## Pregunta 2: ¿superan las redes a las referencias?

**Sí, a las cuatro.** Las doce comparaciones de las tres redes contra las cuatro referencias son
significativas con Holm, y todas las mejoras superan δ.

| Red | Referencia | Diferencia de MAE | IC 95 % (bootstrap por bloques) | Reducción del MAE | p Holm |
| --- | --- | --- | --- | --- | --- |
| CNN | Lineal | −0,0089 | [−0,0097; −0,0081] | 3,1 % | < 0,0001 |
| MLP | Lineal | −0,0087 | [−0,0096; −0,0078] | 3,1 % | < 0,0001 |
| LSTM | Lineal | −0,0086 | [−0,0093; −0,0078] | 3,0 % | < 0,0001 |
| CNN | Mediana del entrenamiento | −0,0320 | [−0,0338; −0,0300] | 10,5 % | < 0,0001 |
| MLP | Mediana del entrenamiento | −0,0318 | [−0,0337; −0,0298] | 10,4 % | < 0,0001 |
| LSTM | Mediana del entrenamiento | −0,0316 | [−0,0334; −0,0298] | 10,3 % | < 0,0001 |
| CNN | Persistencia | −0,0711 | [−0,0737; −0,0685] | 20,6 % | < 0,0001 |
| MLP | Persistencia | −0,0709 | [−0,0737; −0,0682] | 20,5 % | < 0,0001 |
| LSTM | Persistencia | −0,0707 | [−0,0734; −0,0682] | 20,5 % | < 0,0001 |
| CNN | Ingenuo estacional | −0,1024 | [−0,1057; −0,0985] | 27,2 % | < 0,0001 |
| MLP | Ingenuo estacional | −0,1022 | [−0,1055; −0,0983] | 27,2 % | < 0,0001 |
| LSTM | Ingenuo estacional | −0,1020 | [−0,1053; −0,0982] | 27,1 % | < 0,0001 |

Diebold-Mariano con la corrección HLN sobre la pérdida semanal de 2025 (52 semanas), promediando las 8
semillas de cada red; Holm sobre las doce comparaciones. Fuente:
`resultados/comparacion_definitiva/frente_a_referencias.csv`.

- **Frente a la mediana del entrenamiento**, el piso de MAE preregistrado, el error baja entre
  0,0316 y 0,0320, unas
  10 veces δ (10,3 % a
  10,5 %).
- **Frente a la persistencia y el ingenuo estacional** las reducciones son de 20,5 % a
  20,6 % y de 27,1 % a
  27,2 %.
- **Frente a la referencia lineal**, que usa las mismas entradas sin capas ocultas, la mejora es menor
  pero clara: 0,0086 a 0,0089 de MAE
  (3,0 % a 3,1 %), entre 2,7 y
  2,8 veces δ. Es lo que aporta la no linealidad con las mismas variables.

## Métricas secundarias y diagnóstico de residuos

Las métricas secundarias no sustentan conclusiones confirmatorias; se reportan para describir el
resultado.

- **La ventaja de las redes está en el MAE, no en el RMSE ni en el AUC.** En RMSE las cuatro quedan entre
  0,4007 y 0,4027, y la lineal (0,4027) a la par de las redes. En AUC de
  interrupción la lineal tiene la media más alta (0,7637) y las redes van de
  0,7566 a 0,7623. El piloto ya mostraba ese patrón.
- **Sesgo.** El error medio `y − ŷ` de las redes es positivo, de 0,047 a 0,055:
  en promedio subestiman la tasa. No es un error de nivel: el MAE lleva el pronóstico hacia la mediana
  condicional, así que el residuo típico queda cerca de cero, y la media sube por la cola derecha de los
  residuos: corredores y semanas con tasas mucho más altas de lo pronosticado.
- **Residuos.** Sobre el residuo medio de cada semana de 2025, el Ljung-Box no rechaza independencia en ningún rezago
  (menor p entre 1, 4, 8 y 13 semanas: CNN 0,429, MLP 0,367, LSTM 0,391, Lineal 0,324), y el BDS
  tampoco encuentra dependencia no lineal (menor p: CNN 0,562, MLP 0,592, LSTM 0,430, Lineal 0,480).
  No queda estructura temporal evidente en el residuo semanal (`residuos.json`).

## Lectura del resultado

1. **Las redes sirven, pero ninguna arquitectura gana.** Las tres reducen el error frente a todas las
   referencias por bastante más que δ, y entre ellas son equivalentes. Para este problema importa usar
   una red no lineal con la historia de 52 semanas, no qué tipo de red.
2. **Baseline y costo.** El baseline es el mejor modelo según la métrica primaria: la CNN, con
   un MAE de 0,2739 (IC 95 % [0,2736; 0,2742]).
   Como las tres redes son equivalentes dentro de δ, el costo sirve para elegir una referencia práctica: la
   MLP entrena en 71 s con 82.622 parámetros,
   frente a 1.754 s y 229.694 de la LSTM.
   Es la opción barata, no el baseline.
3. **El margen que queda parece pequeño.** De la reducción del MAE que logra la mejor red frente a la
   mediana del entrenamiento, la lineal ya consigue el 72 %. Que tres arquitecturas
   tan distintas lleguen al mismo error sugiere que el límite está en la información de las entradas, no
   en la capacidad del modelo; es una interpretación, no una prueba.

## Amenazas a la validez

- **Un solo año de prueba.** 2025 es un único período de 52 semanas; el resultado vale para ese año.
- **2025 no era una prueba ciega.** Ya se había usado en el curso de Machine Learning y el EDA comparó sus
  distribuciones (preregistro, sección 9). Ninguna decisión de modelado usó esas cifras.
- **El espacio de búsqueda quedó corto por arriba.** La búsqueda eligió la configuración más grande en las
  tres redes, así que pueden existir configuraciones mejores; está declarado como limitación.
- **Alcance de la inferencia.** Las pruebas por semanas trabajan con la pérdida semanal, que ya agrega la
  correlación entre corredores dentro de cada semana. Pero la inferencia vale para los corredores de este
  panel; no dice cómo le iría a un corredor nuevo.
