# Modelos seleccionados

Acordado por Camilo y Lina el 2026-11-11. Parte del borrador de Camilo (versión 1 del 2026-11-08 y
versión 2 en el commit 621dec5), con los ajustes de Lina del 10 de noviembre y sus dos precisiones
del 11: los tamaños de los *embeddings* son fijos en todos los pliegues y la CNN usa relleno causal.
Es el archivo que lee `src/`. Las cifras salen de estos archivos:

- `conteo_modelos_conjunto.json` (rama `lina/pipeline`, commit 80a3914): conteos de la sección 1.
- `configuraciones_texto_completo.json` y `extraccion_texto_completo.csv`: lo que hacen los
  estudios (sección 3).
- `parametros_modelos.json`, que escribe `scripts_camilo/parametros_modelos.py`: cardinalidades,
  tamaños de los *embeddings* y número de parámetros (secciones 1, 4 y 5).

## 1. Selección

Las tres familias de redes más mencionadas en el conjunto acordado de 491 estudios son **LSTM, MLP y
CNN**. Encabezan los tres cortes que fijamos antes de leer los textos completos, y también el conteo
sin el filtro de años:

| Corte | LSTM | MLP/ANN | CNN | 4.º |
| --- | --- | --- | --- | --- |
| Todos los estudios (491) | 272 | 142 | 74 | Transformer (62) |
| Nivel A (172) | 131 | 59 | 66 | GRU (55) |
| Solo texto completo (96) | 65 | 40 | 33 | Transformer (27) |
| Sin filtro de años (543) | 274 | 182 | 76 | Transformer y GRU (62) |

En el nivel A la CNN supera al MLP, pero el grupo de tres no cambia.

**Qué se cuenta.** El enunciado pide contar los modelos que los estudios usan como referencia. Nuestro
conteo es de menciones: una familia cuenta si el estudio la nombra en el título, el resumen o las
palabras clave, o en el texto completo cuando lo leímos. Así, un estudio que propone una LSTM y la
compara con un MLP suma a las dos familias. No separamos el modelo propuesto de los de comparación
porque los resúmenes casi nunca permiten hacerlo. Lo declaramos como una desviación del criterio del
enunciado.

### Orden por complejidad

Las tres seleccionadas leen la ventana de 52 semanas de tres formas distintas. De menor a mayor
complejidad de cálculo:

1. **MLP:** sin estructura temporal. Aplana la ventana en un vector de 156 valores y aplica productos
   matriciales; cada semana tiene su propio peso.
2. **CNN 1D:** filtros locales de 3 semanas que comparten pesos a lo largo de la ventana. Captan
   patrones cortos con pocos parámetros, y todas las posiciones se calculan en paralelo.
3. **LSTM:** recurrencia de 52 pasos con cuatro bloques de compuerta y una celda de memoria. Es la
   única de las tres que debe recorrer la ventana en orden, y por eso es la más lenta por época.

En número de parámetros, el MLP y la CNN quedan parecidos y la LSTM se separa. Con 64 unidades y 2
capas, el cuerpo de cada red tiene 14.208 parámetros (MLP), 12.992 (CNN) y 50.944 (LSTM). La sección
5 da los rangos completos.

**Por qué no entran las tres siguientes.** La regla fijada de antemano era quedarnos con las tres
familias más mencionadas. La complejidad muestra, además, que las siguientes no añaden una forma
nueva de leer la ventana:

- **GRU y RNN simple** usan la misma recurrencia que la LSTM, con tres bloques de compuerta y con uno.
  Agregar cualquiera de las dos pondría una segunda red recurrente junto a la LSTM, y la comparación
  tendría dos variantes de la misma idea.
- **Transformer** es la más compleja de todas. Su atención compara cada par de semanas (52 × 52), cada
  capa suma las matrices de atención y una red densa interna, y necesita codificación posicional. En
  nuestro conteo queda cuarta en tres de los cuatro cortes (en uno, empatada con la GRU), con 62
  menciones frente a 74 de la CNN en todos los estudios. Incluirla habría exigido sacar una de las
  tres primeras.

## 2. Estudios que los respaldan

Diez estudios con texto completo, sobre series agroalimentarias, nombran las tres arquitecturas en
sus resultados. Lina comprobó que los diez siguen incluidos en el conjunto acordado. La lectura de
cada uno para la tabla de síntesis (`sintesis_y_equivalencia_camilo.md`, 2026-11-11) mostró que
solo cinco entrenan las tres por separado: S1-0718, S1-1151, S1-1181, A1-01 y A1-14. Los otros cinco
entrenan dos y nombran la tercera, o la usan dentro de un híbrido. La selección no depende de estos
diez: sale del conteo de la sección 1.

| Id | Año | Tema | Identificador |
| --- | --- | --- | --- |
| S1-0101 | 2026 | Inflación de alimentos | doi:10.1038/s41598-026-34993-w |
| S1-0610 | 2025 | Ventas | doi:10.1145/3729706.3729746 |
| S1-0718 | 2025 | Futuros agrícolas | doi:10.1016/j.asoc.2025.114049 |
| S1-0863 | 2025 | Precios de hortalizas para ensalada | doi:10.1590/0103-8478cr20240444 |
| S1-0885 | 2025 | Precios de productos agrícolas | doi:10.1371/journal.pone.0337103 |
| S1-1151 | 2024 | Producción de leche nacional | doi:10.1016/j.compag.2024.108933 |
| S1-1162 | 2024 | Precios de hortalizas en Beijing | doi:10.1371/journal.pone.0304881 |
| S1-1181 | 2023 | Producción de alimentos | doi:10.1007/s44230-023-00046-y |
| A1-01 | 2025 | Futuros de maní | arXiv:2501.16697 |
| A1-14 | 2026 | Demanda en la cadena del café | arXiv:2606.08314 |

El detector las encontró nombradas en la sección de resultados, pero nombrar no es entrenar: en
S1-0101, por ejemplo, la CNN solo aparece al citar otro estudio. Los demás estudios que usan cada familia están en `extraccion_texto_completo.csv`
(columna `familias`).

## 3. Lo que hacen los estudios

Extraído por `scripts_camilo/configuraciones.py` de los 90 textos completos de mi extracción. Los 6
textos que añadí el 9 de noviembre solo se clasificaron por familia y no entran en esta tabla. Los
conteos son estudios que mencionan cada opción. Los valores numéricos son menciones dentro de esos
estudios, así que un mismo estudio puede aportar varias.

| | LSTM (63 estudios) | MLP (38) | CNN (31) |
| --- | --- | --- | --- |
| Optimizador | Adam 47, SGD 7 | Adam 19, SGD 6 | Adam 25 |
| Pérdida declarada | MSE 17, MAE 5, Huber 5 | MSE 8, MAE 3 | MSE 11, MAE 4, Huber 4 |
| Tasa de aprendizaje (mediana) | 0,001 | 0,001 | 0,001 |
| Épocas (mediana; moda) | 100; 50 | 50; 50 | 50; 50 |
| Tamaño de lote (moda) | 32 | 32 | 32 |
| Unidades o filtros (moda; máximo) | 64; 512 | 10; 128 | 64; 512 |
| Capas (moda; menciones) | 2; 8 | 3; 11 | 2; 3 |
| Dropout / parada temprana | 25 / 27 | 14 / 10 | 11 / 12 |
| Búsqueda de hiperparámetros | metaheurística 24, grid 16, bayesiana 16 | metaheurística 18, grid 9 | metaheurística 9, grid 7 |

Límites de esta extracción:

- **Los valores se asignan por estudio, no por modelo.** Si un estudio usa una LSTM y un MLP y declara
  Adam, el Adam cuenta para las dos familias, aunque solo lo haya usado en una.
- **«Capas» sale de muy pocas menciones**: 8 en la LSTM, 11 en el MLP y 3 en la CNN. La moda orienta,
  pero no es una estimación.
- Los conteos de `tanh` y `sigmoid` reflejan en gran parte la descripción de las compuertas de la
  LSTM, no la activación elegida.
- La ventana de entrada solo se detectó en 25, 13 y 13 menciones, con medianas de 7, 5 y 4 pasos.
  Casi todos esos estudios usan series diarias o mensuales, así que el dato no se traslada a nuestro
  caso. Nuestra ventana de 52 semanas viene del EDA.

## 4. Configuración para nuestro pipeline

### Entrada

Las tres redes reciben el mismo `Lote` de `src/entrenamiento.py`:

- `x`, con forma (lote, 52, 3): los canales `nivel`, `observado` e `iniciado`;
- 33 covariables: 29 escaladas y 4 indicadores de ausencia;
- 4 categóricas: `mercado`, `depto_origen`, `cod_mpio` y `grupo_dom`.

**Regla de los *embeddings*.** La dimensión de cada categórica es la mitad de su cardinalidad,
redondeada hacia arriba, con un tope de 16. La cardinalidad incluye el 0 que `src/` reserva para las
categorías nuevas. Con las 622.244 filas de entrenamiento (semana objetivo hasta el 21 de octubre de
2024), queda así:

| Categórica | Cardinalidad | Dimensión | Parámetros |
| --- | --- | --- | --- |
| `mercado` | 33 | 16 | 528 |
| `depto_origen` | 31 | 16 | 496 |
| `cod_mpio` | 913 | 16 | 14.608 |
| `grupo_dom` | 9 | 5 | 45 |

En total son 53 dimensiones y 15.677 parámetros, iguales para las tres redes. La regla es nuestra; el
tope evita que `cod_mpio` domine la entrada de la cabeza.

**Los tamaños son fijos en todos los pliegues.** Se calculan una vez con todo el entrenamiento y no
con el vocabulario de cada pliegue. Si se recalcularan, la red cambiaría de un pliegue a otro: en k1 a
k3, `mercado` tiene 30 categorías (contando el 0) y la regla le daría 15 dimensiones en vez de 16. Lo
que sí cambia por pliegue es el número de filas de cada tabla, que sigue el vocabulario del pliegue;
las categorías que el pliegue no vio en entrenamiento van al 0. Por eso los 15.677 parámetros de los
*embeddings* son los del entrenamiento completo, y en k1 a k4 son algo menos.

### Cuerpo de cada red

- **MLP:** aplana la ventana (52 × 3 = 156 valores) y aplica 2 o 3 capas densas con ReLU y *dropout*.
  Su representación es la salida de la última capa.
- **LSTM:** 1 o 2 capas sobre la secuencia, con *dropout* entre capas cuando hay dos. Su
  representación es el último estado oculto de la última capa.
- **CNN 1D:** 2 o 3 bloques de convolución con *kernel* 3, **relleno causal** (2 ceros solo hacia el
  pasado), ReLU y *dropout*. Su representación concatena dos vectores: el promedio global sobre las
  52 semanas, que resume el año, y la salida en la última semana, que con 2 o 3 bloques ve las últimas
  5 o 7 semanas. El promedio global por sí solo perdería la posición, y en un pronóstico a una semana
  la más reciente es la que más pesa. Con relleno simétrico, la última semana vería solo 3 o 4
  semanas, porque la mitad de cada filtro caería sobre ceros del futuro; ambos lo comprobamos en
  PyTorch.

### Cabeza común

Las tres terminan en la misma cabeza. Concatenan su representación con las 33 covariables y los 53
valores de los *embeddings*, y aplican una capa oculta de 64 unidades con ReLU y *dropout*, seguida
de una capa densa a una salida. El *dropout* de la cabeza es el mismo que el del cuerpo en cada
configuración. Con una sola capa densa, las covariables y los *embeddings* entrarían
solo de forma lineal, y parte de la diferencia entre modelos vendría de cuánto compensa cada cuerpo
esa limitación y no de cómo lee la ventana. La capa oculta es fija y no entra en la búsqueda.

### Entrenamiento, común a los tres

- Adam, con tasa inicial en {1e-4, 3e-4, 1e-3}: la moda de la literatura y sus vecinos.
- **Pérdida MAE**, porque es la métrica primaria de DECISIONES.md. La literatura usa más la MSE; lo
  declaramos como decisión propia.
- **Sin planificador de la tasa, sin decaimiento de pesos y sin recorte de gradiente.** Son los
  valores por defecto de `ConfigEntrenamiento`, y así cualquier diferencia entre modelos viene de la
  arquitectura. La regularización queda a cargo del *dropout* y la parada temprana.
- Parada temprana con un máximo de 100 épocas y paciencia 10.
- **Tamaño de lote 512**, fijo para los tres. La literatura usa 32 con series de cientos de puntos;
  con 622.244 filas de entrenamiento, una época con lote 32 sería impráctica. Los datos caben
  completos en la GPU, así que la razón es el tiempo por época y no la memoria.
- Inicialización por defecto de PyTorch y una semilla fija.

### Búsqueda de hiperparámetros

Búsqueda aleatoria con el mismo presupuesto para las tres: 20 configuraciones por modelo y una
semilla. Cada configuración se elige con el bloque interno de parada temprana del pliegue k4. Ese
pliegue ya se usó para elegir la ventana, así que el bloque externo queda limpio para el piloto.

**Por qué 20.** El número quedó confirmado el 2026-11-11 con el tiempo por época medido en una GPU
Tesla T4, con datos sintéticos del tamaño de k4 (`resultados/tiempos_por_epoca.csv`, de
`src/medir_tiempo.py`). Una época tarda de 2,3 a 2,8 s en el MLP, de 3,1 a 9,1 s en la CNN y de 2,8
a 22,6 s en la LSTM, según la configuración. En el peor caso, las 20 configuraciones con el tamaño
de la más grande y las 100 épocas completas, la búsqueda de la LSTM tomaría 12,6 h, repartibles en
varias sesiones gracias a los checkpoints. Ninguna red se entrenó con datos reales para fijar este
número.

### Implementación

Las tres redes están en `src/modelos.py` (rama `lina/pipeline`, commit 7324194). Su cuerpo y su
cabeza tienen exactamente los parámetros de la sección 5. El total de `tiempos_por_epoca.csv` es
14.861 parámetros más el de la sección 5 en cada configuración medida: es el tamaño de los
*embeddings* con los vocabularios de los datos sintéticos. Una prueba de `src/pruebas/test_modelos.py`
comprueba que la CNN con relleno causal ve 5 o 7 semanas.

### Referencia lineal de contexto

Además de las tres redes, se entrena un modelo lineal con el mismo pipeline: una sola capa densa
sobre la ventana aplanada, las 33 covariables y los *embeddings*, sin capas ocultas ni en el cuerpo
ni en la cabeza y sin *dropout*. Con una sola salida, los *embeddings* seguidos de la capa densa
equivalen a un peso por categoría, como en la codificación *one-hot*. Es la versión lineal de los
tres modelos base, con la pérdida de la SVR lineal con ε = 0 y sin el término ‖**w**‖²
(`sintesis_y_equivalencia_camilo.md`, sección 3). Solo se busca la tasa de aprendizaje, en el mismo
conjunto y con el mismo bloque de k4. Es una referencia de contexto, como las ingenuas, no un cuarto
modelo base.

| Hiperparámetro | MLP | LSTM | CNN |
| --- | --- | --- | --- |
| Unidades o filtros | {32, 64, 128} | {32, 64, 128} | {32, 64, 128} |
| Capas o bloques | {2, 3} | {1, 2} | {2, 3} |
| *Kernel* | — | — | 3 (fijo) |
| *Dropout* | {0, 0,1, 0,3} | {0, 0,1, 0,3} | {0, 0,1, 0,3} |
| Tasa de aprendizaje | {1e-4, 3e-4, 1e-3} | {1e-4, 3e-4, 1e-3} | {1e-4, 3e-4, 1e-3} |
| Combinaciones | 54 | 54 | 54 |

Las tres tienen el mismo número de combinaciones. Por eso el *kernel* de la CNN queda fijo en 3: con
{3, 5} habría 108, y las 20 configuraciones cubrirían la mitad del espacio que cubren en las otras.

Los rangos se centran en la moda de la literatura (64 unidades, 2 capas, tasa 0,001) y se amplían un
paso hacia cada lado. En el MLP, la literatura tiene moda de 10 unidades y máximo de 128. Usamos el
mismo espacio de los otros dos porque nuestra entrada tiene 156 valores, mientras que esos estudios
usan ventanas de unos 5 pasos. La literatura recurre con frecuencia a metaheurísticas (PSO,
algoritmos genéticos). Preferimos la búsqueda aleatoria porque es reproducible, se fija con la
semilla y no añade otra fuente de variación entre modelos.

## 5. Número de parámetros

Fórmulas de PyTorch, con *u* unidades o filtros, *d* entradas por paso y *k* = 3:

- Capa densa: (entradas + 1) · salidas.
- Capa LSTM: 4*u*(*d* + *u*) + 8*u*. Son cuatro bloques de compuerta, y PyTorch usa dos sesgos por
  bloque.
- Capa convolucional: *d* · *u* · *k* + *u*.
- Cabeza: (*r* + 33 + 53 + 1) · 64 + 65, donde *r* es la representación del cuerpo: *u* en el MLP y
  la LSTM, y 2*u* en la CNN.

`parametros_modelos.py` comprueba estas fórmulas contra los módulos de PyTorch en las 18
combinaciones de unidades y capas. Parámetros sin contar los 15.677 de los *embeddings*:

| Modelo | Mínimo | 64 unidades, 2 capas | Máximo |
| --- | --- | --- | --- |
| MLP | 13.761 (32, 2) | 23.937 | 66.945 (128, 3) |
| CNN | 13.153 (32, 2) | 26.817 | 121.857 (128, 3) |
| LSTM | 12.417 (32, 1) | 60.673 | 214.017 (128, 2) |

Con las configuraciones pequeñas, las tres redes tienen un tamaño parecido. Con las grandes, la LSTM
pasa de tres veces los parámetros del MLP. Por eso conviene reportar el número de parámetros de la
configuración ganadora de cada modelo junto a su error: si la LSTM gana con 200.000 parámetros, parte
de la ventaja puede venir del tamaño.

## 6. Pendiente

1. Lina: la búsqueda de hiperparámetros, el piloto y el análisis por modelo. La sección 5 ya da las
   fórmulas de los parámetros.
2. Camilo: la comparación estadística sobre las predicciones del piloto (`estadistica/`).

Cerrado el 2026-11-12: las 20 configuraciones, la implementación de las tres redes, la tabla de
síntesis con la equivalencia (`sintesis_y_equivalencia_camilo.md`) y la referencia lineal de contexto.
