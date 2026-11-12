# 5. Resultados

## Estado

Los tres modelos base todavía no se entrenan. Este capítulo se completará con el piloto y la
comparación del capítulo 4: tabla comparativa con media ± desviación estándar e intervalos de
confianza, tiempos y número de parámetros, definición del *baseline* e inferencia estadística.

## Referencias ingenuas en validación

Son el piso que cualquier red debe superar. Se calculan sobre los cuatro bloques de validación de
origen móvil (k1 a k4); ninguna lee la prueba de 2025.

- **persistencia:** la tasa de la semana siguiente repite la de la semana actual.
- **ingenuo_estacional:** repite el objetivo de hace 52 semanas; si no existe, usa la persistencia.
- **mediana_entrenamiento:** un pronóstico constante, la mediana del entrenamiento de cada bloque.
  Sirve de contexto para el margen δ.

| Referencia | Pliegue | Filas | MAE | RMSE | MASE | AUC de interrupción |
| --- | --- | --- | --- | --- | --- | --- |
| persistencia | k1 | 90.003 | 0,3496 | 0,5134 | 1,0201 | 0,6180 |
| persistencia | k2 | 92.840 | 0,3496 | 0,5122 | 1,0149 | 0,6051 |
| persistencia | k3 | 93.023 | 0,3502 | 0,5119 | 1,0126 | 0,5960 |
| persistencia | k4 | 98.874 | 0,3457 | 0,5064 | 0,9973 | 0,5915 |
| persistencia | todos | 374.740 | 0,3487 | 0,5109 | 1,0109 | 0,6028 |
| ingenuo_estacional | k1 | 90.003 | 0,3971 | 0,5612 | 1,1586 | 0,5796 |
| ingenuo_estacional | k2 | 92.840 | 0,3977 | 0,5614 | 1,1548 | 0,5809 |
| ingenuo_estacional | k3 | 93.023 | 0,3863 | 0,5505 | 1,1169 | 0,5801 |
| ingenuo_estacional | k4 | 98.874 | 0,3854 | 0,5488 | 1,1118 | 0,5772 |
| ingenuo_estacional | todos | 374.740 | 0,3915 | 0,5553 | 1,1349 | 0,5795 |
| mediana_entrenamiento | k1 | 90.003 | 0,3280 | 0,4294 | 0,9570 | 0,5000 |
| mediana_entrenamiento | k2 | 92.840 | 0,3211 | 0,4216 | 0,9322 | 0,5000 |
| mediana_entrenamiento | k3 | 93.023 | 0,3168 | 0,4180 | 0,9161 | 0,5000 |
| mediana_entrenamiento | k4 | 98.874 | 0,3103 | 0,4118 | 0,8952 | 0,5000 |
| mediana_entrenamiento | todos | 374.740 | 0,3188 | 0,4200 | 0,9243 | 0,4889 |

Fuente: `resultados/referencias_validacion.csv`, que escribe `src/referencias.py`. El MASE usa la
escala de cada pliegue, y «todos» junta los cuatro bloques.

**Lo que muestra la tabla.** Con todos los bloques, la persistencia tiene un MAE de 0,3487
y el ingenuo estacional de 0,3915: repetir la semana actual pronostica mejor que
repetir la del año anterior. Pero el menor MAE no es de ninguna de las dos. Es de la mediana del
entrenamiento, con 0,3188, un pronóstico constante que no usa ninguna
variable. Como el MAE premia acercarse a la mediana, una red que no supere ese valor no aporta
información útil sobre el nivel, aunque supere a la persistencia.

La mediana no sirve para detectar interrupciones. Da el mismo pronóstico a todas las filas de un
bloque, así que su AUC es 0,5 por construcción. Al juntar los bloques baja a 0,4889, porque
cada bloque tiene su propia mediana. En AUC, la referencia es la persistencia, con
0,6028.
