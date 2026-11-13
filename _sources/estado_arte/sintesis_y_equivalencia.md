# Tabla de síntesis y equivalencia con los modelos clásicos

Borrador de Camilo, 2026-11-11, para revisar con Lina. Cubre dos productos del enunciado:

- la tabla de síntesis, con referencia, problema, *dataset*, modelos de Deep Learning usados como
  referencia, métricas y valores obtenidos;
- la justificación de la equivalencia entre los tres modelos base y la SVR lineal (y el modelo
  logístico).

Los datos de la tabla están en `tabla_sintesis_camilo.csv`, transcritos del texto completo de cada
estudio. La columna `fuente_valores` dice de qué tabla o sección sale cada cifra. Los números de
parámetros salen de `parametros_modelos.json`.

**Uso de inteligencia artificial.** La lectura de los diez artículos, la transcripción de las cifras
y el primer borrador de este documento los hizo Claude (Anthropic), en Claude Code, el 2026-11-11.
Camilo no cotejó todavía las cifras contra los artículos, uno por uno; la columna `fuente_valores`
permite hacerlo. Lina aceptó la corrección de la sección 1.

## 1. Tabla de síntesis

Cubre los diez estudios con texto completo que la sección 2 de `modelos_seleccionados.md` citaba
como respaldo. Para cada uno se leyó qué redes entrena de verdad como referencia, no solo cuáles
nombra.

| Referencia | Problema | Datos | Redes de referencia | Métricas | Valores obtenidos |
| --- | --- | --- | --- | --- | --- |
| S1-0101 (doi:10.1038/s41598-026-34993-w) | Inflación de alimentos en Bangladés | Mensual, 2010-2025, BBS, clima y energía | ANN (cuatro arquitecturas), LSTM | RMSE, MAE, R² | Mejor ANN: RMSE 4,17, MAE 3,4. LSTM: 4,23 y 3,47 |
| S1-0610 (doi:10.1145/3729706.3729746) | Precio del aguacate Hass | Diario, 2015-2023 | LSTM, CNN | MSE, RMSE, MAE, R² | LSTM: RMSE 0,1272, MAE 0,0971. CNN: 0,0810 y 0,0644 |
| S1-0718 (doi:10.1016/j.asoc.2025.114049) | Futuros de soya y maíz | Diario, tres series | BPNN, LSTM, CNN, GRU, RNN, Transformer e híbridos | MAPE, MAE, RMSE | Soya: BPNN 4,84 %, LSTM 4,79 %, CNN 4,88 % (MAPE) |
| S1-0863 (doi:10.1590/0103-8478cr20240444) | Hortalizas en Turquía | Diario, 2018-2022 | CNN, LSTM | MAE, RMSE | A 10 días: CNN MAE 0,107; LSTM 10,109 |
| S1-0885 (doi:10.1371/journal.pone.0337103) | Futuros de maíz y trigo | Diario, 1970-2021 | BPNN, LSTM, BiLSTM | RMSE, MAPE, MAE, Dstat | Maíz, con SVMD: BPNN 1,9051 %, LSTM 1,1969 % (MAPE) |
| S1-1151 (doi:10.1016/j.compag.2024.108933) | Producción de leche en tres países | Mensual, Eurostat y clima | FCNN, LSTM, CNN1D y otras seis | R², RMSE, MAPE, MAE | Italia: FCNN MAE 32,064; LSTM 33,206; CNN1D 25,179 |
| S1-1162 (doi:10.1371/journal.pone.0304881) | Hortalizas en Beijing | Diario, 2009-2023 | LSTM, CNN | R², MAE | LSTM: R² 0,958, MAE 0,143 |
| S1-1181 (doi:10.1007/s44230-023-00046-y) | Producción de alimentos en Irán | Anual, FAOSTAT, 1961-2017 | MLP, AE, DBN, CNN, RNN, LSTM | Exactitud, F1 | MLP 80,54; CNN 97,29; LSTM 92,69 (exactitud) |
| A1-01 (arXiv:2501.16697) | Futuros de maní | Diario, desde 2021 | MLP, CNN, LSTM | MSE, MAE por paso | Paso 1: MLP MAE 270,25; CNN 235,77; LSTM 132,76 |
| A1-14 (arXiv:2606.08314) | Demanda de café | 1.062 registros, 70/15/15 | ANN (MLP), LSTM, CNN | MAE, RMSE, MAPE, sMAPE, MASE, R² | MASE: MLP 0,67; LSTM 0,62; CNN 0,61; ingenuo 1,00 |

### Lo que muestra la tabla

**Solo cinco de los diez estudios entrenan las tres redes por separado**: S1-0718, S1-1151, S1-1181,
A1-01 y A1-14. La LSTM aparece sola en los diez, la CNN en ocho y el MLP en siete. Los otros cinco
nombran la tercera red, pero no la entrenan como referencia:

- S1-0101 solo cita una CNN en la discusión.
- S1-0610 usa el MLP como componente del modelo propuesto.
- S1-0863 y S1-1162 no entrenan un MLP.
- S1-0885 usa la CNN solo dentro de un híbrido.

Esto corrige la sección 2 de `modelos_seleccionados.md`, que decía que los diez «mencionan las tres
en sus resultados». La selección no cambia, porque viene del conteo del conjunto de 491 estudios.
Lo que cambia es el respaldo directo: son cinco estudios con las tres redes, y diez con al menos
dos.

**No hay un ganador estable.** La red con menor error cambia entre estudios, entre conjuntos de
datos del mismo estudio y hasta entre métricas:

- La CNN gana en S1-0610, S1-0863 y A1-14.
- La LSTM gana en S1-1162 y en la mayoría de los cortes de A1-01, pero en la Tabla 4 de A1-01 gana
  el MLP.
- En S1-1151 gana la CNN1D en Italia y Alemania, y la FCNN en Francia.
- En la soya de S1-0718, la LSTM tiene el menor MAPE y la BPNN el menor MAE.

Esa variación respalda comparar las tres con el mismo protocolo, en vez de suponer que una domina.

**Las cifras no se comparan entre estudios.** Cada estudio usa otra escala (precios, litros,
índices), otra frecuencia y otra partición. Solo A1-14 informa el MASE frente a un pronóstico
ingenuo, que es la medida que nosotros usamos. La mayoría trabaja con series diarias; ninguno es
semanal.

**Calidad.** S1-1181 informa métricas de clasificación (exactitud, F1) para un problema de
pronóstico, y su texto conserva frases de otro trabajo. Lo mantenemos en la tabla porque está
incluido en el conjunto, pero no lo usamos para justificar ninguna decisión.

**Valores que faltan.** S1-0885 y S1-1162 publican sus tablas comparativas como imagen. Para ellos
la tabla solo trae las cifras que aparecen en el texto.

## 2. Equivalencia con la SVR lineal y el modelo logístico

El enunciado pide que los tres modelos base cumplan el papel que tienen la SVR lineal en regresión y
el modelo logístico en clasificación. Ese papel es fijar un nivel mínimo de desempeño. Nuestra tarea
principal es una regresión: el objetivo `y_reg` es continuo. La referencia clásica que corresponde
es entonces la SVR lineal. La sección 2.5 trata el modelo logístico, que corresponde a la métrica
secundaria de interrupciones.

### 2.1 El punto de partida: la SVR lineal

Con las mismas entradas que nuestras redes, la SVR lineal sería *f*(**x**) = **w**ᵀ**x** + *b*:

- **x** junta la ventana aplanada (156 valores), las 33 covariables y las cuatro categóricas en
  codificación *one-hot*;
- *f* se ajusta minimizando ½‖**w**‖² + *C* Σ max(0, |*y* − *f*(**x**)| − ε).

Tiene 156 + 33 + 986 + 1 = 1.176 parámetros. La parte *one-hot* suma las cardinalidades de las
cuatro categóricas: 33, 31, 913 y 9.

| Aspecto | SVR lineal |
| --- | --- |
| Capacidad de representación | Una función lineal de las entradas |
| Supuestos | Cada semana de la ventana y cada covariable aportan con un peso fijo, sin interacciones |
| Pérdida | Insensible a ε: no penaliza errores menores que ε y crece de forma lineal después |
| Regularización | Penalización ‖**w**‖² |

### 2.2 MLP: la SVR lineal más capas ocultas

**Capacidad de representación.** Si al MLP se le quitan las capas ocultas, queda la SVR lineal.
Sobre la misma entrada aplanada, una sola capa densa a una salida calcula exactamente **w**ᵀ**x** +
*b*. Un *embedding* seguido de esa capa da un peso por categoría, igual que la codificación *one-hot*.
Con una sola salida no se pierde nada: cualquier vector de pesos *one-hot* se puede escribir así. Las capas ocultas con ReLU convierten el modelo en una función lineal a
trozos de las mismas entradas. Esa función puede aproximar interacciones entre semanas y
covariables.

**Supuestos.** El MLP comparte el supuesto central de la SVR lineal: la ventana es un vector de 156
variables sin orden. Si se permutan las semanas y se permutan igual los pesos, el modelo es el
mismo. No supone nada sobre el tiempo.

**Pérdida y regularización.** Nuestra pérdida MAE es la pérdida insensible a ε con ε = 0. La
diferencia está en la regularización. La SVR usa ‖**w**‖²; nosotros no usamos decaimiento de pesos
y regularizamos con *dropout* y parada temprana. La declaramos como diferencia.

**Propósito comparativo.** El MLP es la referencia más cercana a la clásica. Lo que el MLP gane
sobre una SVR lineal mide cuánto aporta la no linealidad, sin ninguna estructura temporal.

### 2.3 CNN 1D: un modelo lineal con pesos compartidos en el tiempo

**Capacidad de representación.** Una capa convolucional sin activación es un filtro lineal de 3
semanas que se aplica igual en toda la ventana. Leer la última semana de ese filtro equivale a una
autorregresión lineal de orden 3 sobre los tres canales. El promedio global de la salida es otra
combinación lineal de la ventana. Por tanto, sin activaciones, la CNN es un caso restringido de la
SVR lineal: los pesos de semanas distintas no son libres, porque salen del mismo filtro. Las
activaciones y los bloques apilados le permiten detectar patrones locales no lineales. Con relleno
causal, la última semana ve 5 o 7 semanas.

**Supuestos.** Los patrones relevantes son locales, de pocas semanas, y significan lo mismo en
cualquier parte del año. Es el supuesto de equivariancia a traslaciones.

**Propósito comparativo.** Mide si una estructura local y con pocos parámetros basta, o si hace falta
la memoria larga de la LSTM.

### 2.4 LSTM: una recurrencia con memoria controlada por compuertas

**Capacidad de representación.** Sin compuertas ni activaciones, una red recurrente es un modelo
lineal de espacio de estados: **h**ₜ = *A***h**ₜ₋₁ + *B***x**ₜ, con la salida tomada del último
estado. La predicción sigue siendo una combinación lineal de la ventana, y el peso de la semana
*t* − *k* depende de *A*ᵏ. Las compuertas de la LSTM hacen que el olvido y la escritura en la celda dependan de
los datos. Así puede retener una semana lejana, como la del año anterior, cuando importa, y
olvidarla cuando no.

**Supuestos.** El pasado relevante se puede resumir en un estado de tamaño fijo que se actualiza en
orden. Es la única de las tres que procesa la ventana como una secuencia.

**Propósito comparativo.** Mide si la memoria de largo plazo aporta sobre las otras dos.

### 2.5 El modelo logístico y la métrica de interrupciones

DECISIONES.md también evalúa la detección de interrupciones: hay interrupción cuando
`y_reg` < ln(1,5), y la métrica secundaria es el AUC de interrupción. Para esa tarea, el modelo
logístico es la referencia clásica: σ(**w**ᵀ**x** + *b*) sobre las mismas entradas. Nuestras redes no
se entrenan para clasificar. Su pronóstico ŷ se usa como puntaje: cuanto menor es ŷ, más probable es la interrupción. Para el
AUC solo importa el orden de ese puntaje. En el caso lineal, ordenar por **w**ᵀ**x** + *b* da el
mismo AUC que el modelo logístico con los mismos pesos, porque σ es creciente. La equivalencia, por tanto, se mantiene en la
parte lineal. Lo que cambia es la pérdida con que se ajustan los pesos: MAE sobre `y_reg` en lugar de
entropía cruzada sobre la etiqueta.

### 2.6 Resumen

| Modelo | Sin no linealidades equivale a | Qué añade | Sesgo inductivo |
| --- | --- | --- | --- |
| MLP | SVR lineal sobre la ventana aplanada | Interacciones no lineales | Ninguno temporal: la ventana es un vector |
| CNN 1D | Autorregresión lineal de orden 3 con pesos compartidos | Patrones locales no lineales | Localidad y equivariancia a traslaciones |
| LSTM | Modelo lineal de espacio de estados | Memoria controlada por los datos | Secuencia resumida en un estado |

Las tres parten del modelo lineal y le añaden una forma distinta de leer la ventana. Por eso
cumplen el requisito de no ser redundantes.

## 3. Propuesta para Lina

Una referencia lineal de contexto saldría casi gratis con el pipeline actual: un MLP con cero capas
ocultas, entrenado con la misma pérdida MAE, las mismas entradas y la misma parada temprana. Sería
la versión lineal de los tres modelos base, con pérdida insensible a ε = 0 y sin el término ‖**w**‖².
El enunciado acepta los modelos clásicos solo como contexto, y esta referencia permitiría decir
cuánto aporta cada red sobre el modelo lineal. Hoy `src/referencias.py` tiene la persistencia, el
ingenuo estacional y la mediana, pero ningún modelo lineal.

Pendiente: ampliar la tabla de síntesis a los 96 estudios con texto completo, si el informe lo
necesita.
