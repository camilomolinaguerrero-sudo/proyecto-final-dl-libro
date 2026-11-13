# Análisis por modelo base

Sección 3.3 del Entregable 1, a cargo de Lina. Describe los tres modelos base (MLP, CNN 1D y LSTM) tal
como se implementaron en `src/modelos.py`, con lo que la literatura reporta de cada uno. La selección y
la configuración están en `modelos_seleccionados.md`; la tabla de síntesis y la equivalencia con la SVR
lineal, en `sintesis_y_equivalencia_camilo.md`.

Fuentes de las cifras:

- Estudios, datos, métricas y valores: `tabla_sintesis_camilo.csv`, que Camilo extrajo del texto completo
  de los diez estudios de respaldo.
- Pérdida, optimizador y demás ajustes que usa la literatura: `configuraciones_texto_completo.json`, sobre
  los 90 textos completos de la extracción de Camilo.
- Número de parámetros: sección 5 de `modelos_seleccionados.md`, comprobada en `src/pruebas/test_modelos.py`.
- Tiempo por época: `resultados/tiempos_por_epoca.csv`, medido en una Tesla T4 con datos sintéticos del
  tamaño del pliegue k4 (364.214 ventanas de entrenamiento, lote de 512).

## Estudios que los respaldan

Diez estudios con texto completo nombran las tres redes en sus resultados. Al leerlos, no todos entrenan
cada red por separado: el MLP se entrena solo en 7, la LSTM en los 10 y la CNN en 8. Solo cinco entrenan
las tres (S1-0718, S1-1151, S1-1181, A1-01 y A1-14).

| Id | Referencia | MLP | LSTM | CNN |
| --- | --- | --- | --- | --- |
| S1-0101 | Javed, A. (2026). Benchmarking econometric, decomposable additive, and neural network methods for food inflation prediction featuring policy insights. *Scientific Reports*, 16(1), 5460. https://doi.org/10.1038/s41598-026-34993-w | sí | sí | no |
| S1-0610 | Chen, X., Horata, P. y Sunat, K. (2025). A Hybrid TCN-LSTM-Attention Model for Accurate Sales Forecasting. En *Proceedings of the 2025 4th International Conference on Cyber Security, Artificial Intelligence and the Digital Economy (CSAIDE 2025)* (pp. 254-258). https://doi.org/10.1145/3729706.3729746 | no | sí | sí |
| S1-0718 | Wu, B., Gao, R., Wang, X. y Zeng, H. (2025). Agricultural futures prices combination forecasting with whole-process interpretable method. *Applied Soft Computing*, 185, 114049. https://doi.org/10.1016/j.asoc.2025.114049 | sí | sí | sí |
| S1-0863 | Bulut, M. (2025). Forecasting prices of salad vegetables using statistical and artificial intelligence techniques. *Ciência Rural*, 55(11), e20240444. https://doi.org/10.1590/0103-8478cr20240444 | no | sí | sí |
| S1-0885 | Sun, K., Yao, Q. y Li, Y. (2025). A novel agricultural commodity price prediction model integrating deep learning and enhanced swarm intelligence algorithm. *PLOS One*, 20(12), e0337103. https://doi.org/10.1371/journal.pone.0337103 | sí | sí | no |
| S1-1151 | Cesarini, L., Gonçalves, R., Martina, M., Romão, X., Monteleone, B., Lobo Pereira, F. y Figueiredo, R. (2024). Comparison of deep learning models for milk production forecasting at national scale. *Computers and Electronics in Agriculture*, 221, 108933. https://doi.org/10.1016/j.compag.2024.108933 | sí | sí | sí |
| S1-1162 | Zhang, Q., Yang, W., Zhao, A., Wang, X., Wang, Z. y Zhang, L. (2024). Short-term forecasting of vegetable prices based on LSTM model—Evidence from Beijing's vegetable data. *PLOS One*, 19(7), e0304881. https://doi.org/10.1371/journal.pone.0304881 | no | sí | sí |
| S1-1181 | Baswaraju, S., Maheswari, V. U., Chennam, K. K., Thirumalraj, A., Kantipudi, M. V. V. P. y Aluvalu, R. (2023). Future Food Production Prediction Using AROA Based Hybrid Deep Learning Model in Agri-Sector. *Human-Centric Intelligent Systems*, 3(4), 521-536. https://doi.org/10.1007/s44230-023-00046-y | sí | sí | sí |
| A1-01 | Li, S. (2025). An Analysis of the Interdependence Between Peanut and Other Agricultural Commodities in China's Futures Market. arXiv:2501.16697. https://doi.org/10.48550/arXiv.2501.16697 | sí | sí | sí |
| A1-14 | Budak, G., Gholamzadeh Gharehgheshlaghi, F., Barjesteh Vaezi, M. y Gholizadeh Lonbar, A. (2026). Integrating Deep Learning Demand Forecasting with Multi-Objective Optimization for Circular Coffee Supply Chains: A Data-Driven Framework for Cost, Emissions, and Freshness Management. arXiv:2606.08314. https://doi.org/10.48550/arXiv.2606.08314 | sí | sí | sí |

Los autores, títulos, revistas y DOI salen del registro de Scopus (`registros/scopus_2026-11-07_S1.csv`) y
del de arXiv (`registros/arxiv_2026-11-07_A1.xml`). S1-1181 tiene calidad dudosa: reporta métricas de
clasificación para un problema de pronóstico y conserva frases de otro trabajo.

## Entrada y cabeza comunes

Las tres redes reciben la misma entrada y terminan en la misma cabeza, así que solo cambia la forma de leer
la ventana.

- **Ventana** $X \in \mathbb{R}^{52 \times 3}$: 52 semanas, la última es $t$, con los canales `nivel`,
  `observado` e `iniciado`.
- **Covariables** $c \in \mathbb{R}^{33}$: 29 escaladas y 4 indicadores de ausencia.
- **Embeddings** $e \in \mathbb{R}^{53}$: `mercado`, `depto_origen` y `cod_mpio` con 16 dimensiones cada uno
  y `grupo_dom` con 5, de tamaño fijo en todos los pliegues.
- **Cabeza:** con la representación $r$ que entrega cada cuerpo,

$$
z = [\,r;\ c;\ e\,], \qquad \hat{y} = w_2^\top\,\mathrm{Dropout}\big(\mathrm{ReLU}(W_1 z + b_1)\big) + b_2,
$$

  con $W_1 \in \mathbb{R}^{64 \times \dim z}$. El *dropout* de la cabeza es el mismo del cuerpo.

**Objetivo y pérdida.** Las tres pronostican $y = \log\!\big(1 + \text{tasa}_{t+1}/\text{base8}_t\big)$, una semana
adelante, y minimizan el error absoluto medio:

$$
\mathcal{L} = \frac{1}{n}\sum_{i=1}^{n} \lvert y_i - \hat{y}_i \rvert .
$$

Se entrenan con Adam, lote de 512, hasta 100 épocas con paciencia 10, sin planificador, sin decaimiento
de pesos y sin recorte de gradiente.

## MLP

**Tipo de arquitectura.** Perceptrón multicapa con 2 o 3 capas densas de $u \in \{32, 64, 128\}$ unidades.

**Descripción técnica.** Aplana la ventana en un vector de 156 valores y aplica capas densas:

$$
h^{(1)} = \mathrm{Dropout}\big(\mathrm{ReLU}(W^{(1)}\,\mathrm{vec}(X) + b^{(1)})\big), \qquad
h^{(\ell)} = \mathrm{Dropout}\big(\mathrm{ReLU}(W^{(\ell)} h^{(\ell-1)} + b^{(\ell)})\big),
$$

con $W^{(1)} \in \mathbb{R}^{u \times 156}$. La representación es $r = h^{(L)}$, de dimensión $u$.

```text
ventana 52×3 ─ aplanar ─▶ 156 ─▶ [densa u, ReLU, dropout] × L ─▶ r (u) ─┐
covariables (33) ───────────────────────────────────────────────────────┼─▶ concatenar ─▶ densa 64, ReLU, dropout ─▶ densa 1 ─▶ ŷ
embeddings (53) ────────────────────────────────────────────────────────┘
```

**Supuestos y sesgo inductivo.** Ninguno temporal: cada semana de la ventana tiene su propio peso y el
modelo no sabe que la semana 51 está junto a la 52. Supone que el pronóstico es una función no lineal
suave del vector completo, sin estructura de orden. Sin capas ocultas sería la referencia lineal de
contexto (`lineal`), la versión de red de la SVR lineal.

**Pérdida y optimización en la literatura.** De los 38 textos completos con MLP, 19 usan Adam y 6 SGD. Los
que declaran la pérdida usan sobre todo MSE (8) y menos MAE (3). La moda es de 3 capas y 10 unidades, con
tasa de 0,001 y lote de 32. Para elegir la configuración, 18 usan metaheurísticas y 9 búsqueda en rejilla.

**Problema que aborda.** Regresión de series agroalimentarias a partir de rezagos y variables externas. En
los siete estudios donde se entrena solo, pronostica inflación de alimentos (S1-0101), futuros de soya,
maíz, trigo o maní (S1-0718, S1-0885, A1-01), producción de leche (S1-1151) o de alimentos (S1-1181) y
demanda de café (A1-14).

**Datos de esos estudios.** Series mensuales del índice de precios de Bangladés (S1-0101) y de Eurostat
(S1-1151), diarias de futuros (S1-0718, S1-0885, A1-01), anuales de FAOSTAT de 1961 a 2017 (S1-1181) y
1.062 registros de ventas de café (A1-14).

**Métricas y valores reportados.** Ninguno de los valores de la tabla de síntesis trae desviación estándar
ni intervalo: son corridas únicas.

| Estudio | Valores del MLP |
| --- | --- |
| S1-0101 | Mejor ANN: RMSE 4,17, MAE 3,4 |
| S1-0718 | Soya de China: MAPE 4,84 %, MAE 0,0186, RMSE 0,0252 |
| S1-0885 | Maíz, con descomposición SVMD: MAPE 1,9051 % |
| S1-1151 | Italia (FCNN): RMSE 39,045, MAE 32,064, R² 0,772 |
| S1-1181 | Exactitud 80,54, F1 0,817 (calidad dudosa) |
| A1-01 | Paso 1: MSE 111938,37, MAE 270,25 |
| A1-14 | MAE 27,85, RMSE 39,73, MASE 0,67, R² 0,82 |

**Fortalezas.** Es la arquitectura más simple de reproducir. En nuestra medición, una época tarda de 2,3 a
2,8 s casi sin depender del tamaño. Usa toda la ventana a la vez, así que puede combinar semanas lejanas
sin recorrerlas en orden.

**Limitaciones.** Al no compartir pesos en el tiempo, necesita más datos para aprender un mismo patrón en
distintas posiciones de la ventana, y un corrimiento de una semana cambia por completo su entrada. Es
sensible a la escala de las entradas. En S1-1181 y A1-01 queda por detrás de la CNN y la LSTM.

**Complejidad computacional.** Con $u$ unidades y $L$ capas, el cuerpo tiene
$157u + (L-1)(u+1)u$ parámetros. El costo por observación es $O(156u + (L-1)u^2)$ en el paso hacia adelante
y del mismo orden en el de entrenamiento; todas las operaciones son productos de matrices paralelos.

| Configuración | Parámetros sin embeddings | Segundos por época |
| --- | --- | --- |
| 32 unidades, 2 capas | 13.761 | 2,66 |
| 64 unidades, 2 capas | 23.937 | 2,29 |
| 128 unidades, 3 capas | 66.945 | 2,85 |

## CNN 1D

**Tipo de arquitectura.** Red convolucional en una dimensión, con 2 o 3 bloques de $u \in \{32, 64, 128\}$
filtros de *kernel* 3.

**Descripción técnica.** Cada bloque aplica una convolución causal: rellena con dos ceros solo hacia el
pasado, así que la salida en la semana $t$ depende de $t$, $t-1$ y $t-2$:

$$
a^{(\ell)}_{t,j} = \mathrm{Dropout}\Big(\mathrm{ReLU}\Big(b^{(\ell)}_j + \sum_{i} \sum_{s=0}^{2} K^{(\ell)}_{j,i,s}\; a^{(\ell-1)}_{t-2+s,\,i}\Big)\Big),
$$

con $a^{(0)} = X$ y ceros antes de la primera semana. La representación concatena el promedio global y la
salida en la última semana:

$$
r = \Big[\ \tfrac{1}{52}\textstyle\sum_{t=1}^{52} a^{(L)}_{t}\ ;\ a^{(L)}_{52}\ \Big] \in \mathbb{R}^{2u}.
$$

Con 2 bloques, $a^{(L)}_{52}$ ve las últimas 5 semanas; con 3, las últimas 7.

```text
ventana 52×3 ─▶ [relleno causal, conv k=3 u filtros, ReLU, dropout] × L ─▶ mapa 52×u ─┬─ promedio ─┐
                                                                                       └─ semana 52 ─┴─▶ r (2u) ─┐
covariables (33) ────────────────────────────────────────────────────────────────────────────────────────────────┼─▶ cabeza ─▶ ŷ
embeddings (53) ─────────────────────────────────────────────────────────────────────────────────────────────────┘
```

**Supuestos y sesgo inductivo.** Localidad y equivariancia a traslaciones: un patrón de pocas semanas
seguidas significa lo mismo en cualquier parte de la ventana, y los filtros lo detectan con los mismos
pesos. El relleno causal impide que una semana use información posterior dentro de la ventana. El
promedio resume el año y la salida final conserva la posición de lo más reciente.

**Pérdida y optimización en la literatura.** De los 31 textos completos con CNN, 25 usan Adam. Los que
declaran la pérdida usan MSE (11), MAE (4) o Huber (4). La moda es de 2 capas y 64 filtros, con tasa de
0,001 y lote de 32. Para elegir la configuración, 9 usan metaheurísticas y 7 búsqueda en rejilla.

**Problema que aborda.** Regresión de precios, producción y demanda. En los ocho estudios donde se entrena
sola, pronostica el precio del aguacate (S1-0610), futuros de soya o maní (S1-0718, A1-01), precios de
hortalizas (S1-0863, S1-1162), producción de leche (S1-1151) o de alimentos (S1-1181) y demanda de café
(A1-14).

**Datos de esos estudios.** Series diarias de precios (S1-0610, S1-0718, S1-0863, S1-1162, A1-01), mensuales
de Eurostat (S1-1151), anuales de FAOSTAT (S1-1181) y registros de ventas de café (A1-14).

**Métricas y valores reportados.** Ninguno trae medida de dispersión.

| Estudio | Valores de la CNN |
| --- | --- |
| S1-0610 | RMSE 0,0810, MAE 0,0644, R² 0,6645 |
| S1-0718 | Soya de China: MAPE 4,88 %, MAE 0,0279, RMSE 0,0367 |
| S1-0863 | Horizonte de 10 días: MAE 0,107, RMSE 0,162 |
| S1-1151 | Italia (CNN1D): RMSE 32,445, MAE 25,179, R² 0,843 |
| S1-1162 | La tabla comparativa está en imagen; sin valores en el texto |
| S1-1181 | Exactitud 97,29, F1 0,972 (calidad dudosa) |
| A1-01 | Paso 1: MSE 90663,54, MAE 235,77 |
| A1-14 | MAE 25,31, RMSE 36,12, MASE 0,61, R² 0,86 |

En S1-0863 la CNN y la LSTM tienen errores de escalas muy distintas (MAE 0,107 frente a 10,109), y la tabla
del artículo no dice a qué producto corresponden, así que esa comparación no es confiable.

**Fortalezas.** Comparte pesos en el tiempo: su primera capa tiene $10u$ parámetros, frente a $157u$ en la
del MLP. Calcula todas las posiciones en paralelo. En S1-1151 y A1-14 tiene el menor error de las
tres redes entrenadas por separado.

**Limitaciones.** Su campo de visión es corto: la salida final ve 5 o 7 semanas, y el resto de la ventana
solo entra por el promedio, que pierde el orden. Un patrón de más de 7 semanas, como la estacionalidad
anual, no lo capta un solo filtro.

**Complejidad computacional.** Con $u$ filtros y $L$ bloques, el cuerpo tiene $10u + (L-1)(3u^2 + u)$
parámetros, y la cabeza recibe $2u$ valores del cuerpo. El costo por observación es $O(52 \cdot 3\,(3u + (L-1)u^2))$,
paralelo en las 52 posiciones.

| Configuración | Parámetros sin embeddings | Segundos por época |
| --- | --- | --- |
| 32 filtros, 2 bloques | 13.153 | 3,11 |
| 64 filtros, 2 bloques | 26.817 | 3,50 |
| 128 filtros, 3 bloques | 121.857 | 9,06 |

## LSTM

**Tipo de arquitectura.** Red recurrente de memoria de corto y largo plazo, con 1 o 2 capas de
$u \in \{32, 64, 128\}$ unidades y *dropout* entre capas cuando hay dos.

**Descripción técnica.** Recorre la ventana semana a semana. En cada paso, con la entrada $x_t$ y el estado
anterior $(h_{t-1}, c_{t-1})$:

$$
\begin{aligned}
i_t &= \sigma(W_i x_t + U_i h_{t-1} + b_i), & f_t &= \sigma(W_f x_t + U_f h_{t-1} + b_f),\\
o_t &= \sigma(W_o x_t + U_o h_{t-1} + b_o), & g_t &= \tanh(W_g x_t + U_g h_{t-1} + b_g),\\
c_t &= f_t \odot c_{t-1} + i_t \odot g_t, & h_t &= o_t \odot \tanh(c_t).
\end{aligned}
$$

La segunda capa recibe la secuencia $h_t$ de la primera. La representación es el último estado oculto de
la última capa, $r = h^{(L)}_{52}$, de dimensión $u$.

```text
ventana 52×3 ─▶ semana 1 ─▶ semana 2 ─▶ … ─▶ semana 52 ─▶ r = h₅₂ (u) ─┐
               (estado h, c que pasa de una semana a la siguiente)      │
covariables (33) ───────────────────────────────────────────────────────┼─▶ cabeza ─▶ ŷ
embeddings (53) ────────────────────────────────────────────────────────┘
```

**Supuestos y sesgo inductivo.** La historia se puede resumir en un estado que se actualiza en orden. Las
compuertas deciden cuánto olvidar y cuánto incorporar en cada semana, así que la memoria depende de los
datos y puede conservar información de muchas semanas atrás. Supone que el orden importa y que las mismas
reglas de actualización valen en toda la ventana.

**Pérdida y optimización en la literatura.** Es la familia con más textos completos (63). De ellos, 47 usan
Adam y 7 SGD. Los que declaran la pérdida usan MSE (17), MAE (5) o Huber (5). La moda es de 2 capas y 64
unidades, con tasa de 0,001 y lote de 32; la mediana de épocas es 100. Para elegir la configuración, 24
usan metaheurísticas, 16 búsqueda en rejilla y 16 búsqueda bayesiana.

**Problema que aborda.** Es la referencia más común: aparece entrenada sola en los diez estudios, sobre
precios, producción y demanda, con series diarias, mensuales o anuales.

**Datos de esos estudios.** Los de las dos secciones anteriores, más el índice de precios de alimentos de
Bangladés (S1-0101) y los precios de futuros de maíz y trigo de 1970 a 2021 (S1-0885).

**Métricas y valores reportados.** Ninguno trae medida de dispersión.

| Estudio | Valores de la LSTM |
| --- | --- |
| S1-0101 | RMSE 4,23, MAE 3,47 |
| S1-0610 | RMSE 0,1272, MAE 0,0971, R² 0,1725 |
| S1-0718 | Soya de China: MAPE 4,79 %, MAE 0,0267, RMSE 0,0334 |
| S1-0863 | Horizonte de 10 días: MAE 10,109, RMSE 15,698 |
| S1-0885 | Maíz, con descomposición SVMD: MAPE 1,1969 % |
| S1-1151 | Italia: RMSE 41,274, MAE 33,206, R² 0,741 |
| S1-1162 | R² 0,958, MAE 0,143 |
| S1-1181 | Exactitud 92,69, F1 0,963 (calidad dudosa) |
| A1-01 | Paso 1: MSE 26772,50, MAE 132,76 |
| A1-14 | MAE 25,94, RMSE 36,88, MASE 0,62, R² 0,85 |

**Fortalezas.** Puede usar toda la ventana conservando el orden y aprender cuánto pesa cada parte de la
historia. Es la red con más respaldo en la literatura revisada, y en A1-01 tiene el menor error de las
tres.

**Limitaciones.** Es la más costosa: el estado de cada semana depende del anterior, así que las 52 semanas
se procesan en secuencia y no en paralelo. Cada capa tiene cuatro bloques de compuerta, cuatro veces los
parámetros de una red recurrente simple del mismo ancho, lo que la hace más propensa al sobreajuste. En S1-0610 y S1-1151 queda por detrás de la CNN.

**Complejidad computacional.** Con $u$ unidades, la primera capa tiene $4u(3+u) + 8u$ parámetros y la
segunda $4u(2u) + 8u$; PyTorch usa dos sesgos por compuerta. El costo por observación es
$O(52\,L\,u^2)$, con 52 pasos secuenciales.

| Configuración | Parámetros sin embeddings | Segundos por época |
| --- | --- | --- |
| 32 unidades, 1 capa | 12.417 | 2,80 |
| 64 unidades, 2 capas | 60.673 | 5,98 |
| 128 unidades, 2 capas | 214.017 | 22,61 |

## Parámetros de la cabeza y de los embeddings

Los parámetros de las tablas incluyen la cabeza, que tiene $(r + 33 + 53 + 1) \cdot 64 + 65$, con $r = u$
en el MLP y la LSTM y $r = 2u$ en la CNN. Los *embeddings* suman 15.677 parámetros más, iguales para las
tres. Los segundos por época son la mediana sin la primera época y no incluyen el guardado del checkpoint.
La extracción de los textos completos no registró tiempos de entrenamiento, así que no hay con qué
compararlos.
