# Protocolo de tamizaje (búsquedas S1 y A1)

Fijado el 2026-11-07, antes de leer los títulos. Aplica a `registros/scopus_2026-11-07_S1.csv` y
`registros/arxiv_2026-11-07_A1.xml`. Las decisiones quedan en `tamizaje_camilo.csv`.

## Uso de inteligencia artificial

Todo lo que describe este protocolo lo hizo una herramienta de IA a pedido de Camilo:
Claude (Anthropic), usado en la aplicación Claude Code entre el 2026-11-07 y el 2026-11-09. La
sesión del 2026-11-09 reporta el modelo Claude Opus 5.5; Camilo debe confirmar ese dato antes del
informe. La herramienta hizo lo siguiente:

- redactó los criterios I1 a I4 y E1 a E6, las reglas de fallo y este texto;
- hizo las búsquedas en Scopus y arXiv desde el navegador de Camilo;
- leyó los títulos y los resúmenes y decidió cada registro;
- hizo las dos auditorías por muestra y falló los 79 dudosos;
- abrió los textos completos con la sesión institucional de Camilo en Uninorte, después de que él
  iniciara sesión y diera los permisos del navegador;
- escribió y corrió los scripts de extracción.

Camilo pidió cada paso y recibió los resultados, pero no leyó los registros uno por uno ni revisó
cada decisión. Donde el texto dice «se leyeron», «se excluyó» o «revisar uno por uno», quien lo hizo
fue la herramienta. Los criterios se fijaron antes de leer los registros, y cada decisión quedó con
su criterio y su nota en `tamizaje_camilo.csv`, de modo que cualquiera la puede auditar.

Texto propuesto para el informe:

> El tamizaje de título y resumen de los registros de Scopus y arXiv, el fallo de los casos dudosos
> y la extracción de los modelos los realizó una herramienta de inteligencia artificial (Claude, de
> Anthropic) a pedido de Camilo Molina, con criterios escritos antes de leer los registros y dos
> auditorías por muestra de las exclusiones. Ninguna de esas decisiones se basa en la lectura humana
> de cada registro.

## Pregunta que alimenta

¿Qué modelos de redes neuronales se usan como referencia para pronosticar series de tiempo
agroalimentarias de mercado (precio, demanda o abastecimiento)? El conteo de modelos se hace en la
etapa de texto completo, sobre los estudios incluidos.

## Criterios de inclusión

Un registro pasa el tamizaje de título y resumen si cumple los cuatro:

| Código | Criterio |
| --- | --- |
| I1 | La variable pronosticada es una magnitud de mercado o de cadena de abastecimiento agroalimentaria: precio (mayorista, minorista, al productor, futuros de productos agrícolas), demanda, ventas o consumo, abastecimiento, llegadas, despachos o volumen comercializado, producción agregada tratada como serie de oferta, exportaciones o importaciones, inventarios o reservas de alimentos |
| I2 | Usa al menos una red neuronal (MLP o ANN, ELM, RNN, LSTM, GRU, CNN, TCN, Transformer, o un híbrido que contenga una de ellas) |
| I3 | Pronostica valores futuros en el tiempo (horizonte posterior a la información usada) |
| I4 | Estudio empírico primario: artículo, ponencia o capítulo, con texto completo en inglés o español |

Que el estudio compare la red con al menos otro modelo, criterio I-1 de la respuesta a Lina, se
verifica en el texto completo, porque muchos resúmenes no lo dicen.

## Criterios de exclusión

| Código | Motivo |
| --- | --- |
| E1 | Tipo de documento: revisión, revisión de congreso (actas completas), fe de erratas, editorial, carta, nota, libro completo, artículo de datos o retractado |
| E2 | Idioma del texto completo distinto de inglés o español |
| E3 | La variable no es agroalimentaria de mercado: rendimiento o fenología de cultivos, enfermedades o plagas, calidad, inocuidad o frescura, clima, agua, suelo, energía, emisiones, productos no alimentarios (petróleo, metales, acciones, carbono), salud animal o humana, logística sin variable de mercado |
| E4 | No es pronóstico temporal: clasificación, detección, segmentación, estimación del estado actual, optimización o simulación sin pronóstico |
| E5 | No usa redes neuronales |
| E6 | Duplicado de otro registro (mismo DOI o mismo título normalizado) |

Se registra el primer criterio que falla, en el orden E6, E1, E2, E3, E4, E5.

## Procedimiento

1. **Duplicados.** Mismo DOI o mismo título normalizado (minúsculas, solo letras y números). Se
   conserva el registro de Scopus. Las actas de congreso que comparten título pero son volúmenes
   distintos no son duplicados; salen por E1.
2. **Metadatos.** E1 y E2 se aplican con los campos `Document Type` y `Language of Original
   Document` de Scopus.
3. **Títulos.** Se leen todos los títulos restantes. Se excluye solo cuando el título muestra sin
   ambigüedad un motivo E3 a E5. Ante la duda, el registro pasa.
4. **Resúmenes.** Se leen título, resumen y palabras clave de los que pasaron, y se decide
   `incluir`, `excluir` o `dudoso`. Los dudosos pasan a texto completo.
5. **Auditoría.** Se relee el resumen de una muestra aleatoria (semilla 42) de los excluidos por
   título para estimar cuántos se habrían incluido. Si aparece alguno, se revisa la regla que lo
   excluyó y se repite el paso 3.
6. **Revisiones.** Las revisiones excluidas por E1 se listan aparte. Sirven para buscar estudios
   primarios por referencias (bola de nieve), que se registran como una búsqueda nueva.

## Resultados del tamizaje de título y resumen (2026-11-07)

Las cifras salen de `tamizaje_conteos.json`.

| Paso | Registros | Salen | Motivo |
| --- | --- | --- | --- |
| Identificados | 2.225 (Scopus 2.196, arXiv 29) | | |
| 1. Duplicados | 2.225 | 6 | E6 (los 6 son de arXiv y ya estaban en Scopus) |
| 2. Metadatos | 2.219 | 230 | E1: 113, E2: 117 |
| 3. Títulos | 1.989 | 1.418 | E3: 1.361, E1: 46, E4: 9, E5: 2 |
| 4. Resúmenes | 571 | 46 | E3: 29, E4: 13, E5: 3, E1: 1 |
| Pasan a texto completo | 525 | | 446 incluidos y 79 dudosos |

**Auditoría.** En una muestra aleatoria de 100 exclusiones por título (semilla 42) apareció un falso
negativo: S1-1351, cuyo título no menciona precios. Se revisó la regla y se buscaron en los resúmenes
de todas las exclusiones por título las frases que unen *predict* o *forecast* con *price*, *demand*,
*sales*, *consumption*, *arrivals*, *exports* o *imports*. Se leyeron los 154 casos encontrados:
3 pasaron a incluidos y 13 a dudosos, casi todos plataformas cuyo título no menciona el pronóstico
de precios. Una segunda muestra de 60 exclusiones (semilla 7) no encontró falsos negativos.

**Coherencia.** Cuatro registros que pronostican rendimiento (S1-0695, S1-0926, S1-1808, S1-1958) se
habían marcado como dudosos en el resumen. Se excluyeron por E3, igual que los demás estudios de
rendimiento. Los de "grain yield" de China siguen como dudosos porque esa expresión suele traducir
producción total y no rendimiento por hectárea; el texto completo lo aclara.

## Fallo sobre los casos dudosos y el alcance del texto completo (2026-11-07)

Se fijaron reglas para los casos frontera y después se leyó el resumen completo de los 79 dudosos.

**Reglas.**

1. **Productos no alimentarios.** El algodón y la resina se excluyen por E3, porque I1 exige una
   variable *agroalimentaria*. Un estudio que mezcla un producto alimentario con uno no alimentario
   (S1-0077: algodón y cúrcuma) se mantiene.
2. **Servicios de comida.** La demanda de restaurantes, domicilios, eventos y alimentos procesados se
   excluye por E3. Las ventas de productos frescos en mercados, supermercados o bodegas sí entran.
3. **Producción.** Entra la producción agregada (regional, provincial o nacional) tratada como serie.
   Se excluyen por E3 la producción de una sola finca y el rendimiento por hectárea, y por E4 la
   producción estimada con insumos del mismo período (no es pronóstico).
4. **Plataformas.** Una plataforma entra solo si el resumen dice que el precio o la demanda se
   pronostica con una red. Si no lo dice, sale por E5.
5. **Red como referencia.** Un estudio cuyo modelo propuesto no es una red entra si compara contra una
   red (por ejemplo, LightGBM frente a ANN), porque esa red es justamente una referencia.

**Resultado.** De los 79 dudosos, 33 entran, 45 salen (E3: 18, E4: 14, E5: 11, E1: 2) y 1 (S1-0416)
pasa a texto completo porque el resumen no dice qué serie usa. Pasan 480 estudios.

**Alcance del texto completo.** Para cada estudio que pasa se contaron las familias de redes que
nombran el título, el resumen y las palabras clave (columna `familias_resumen`).

| Nivel | Estudios | Criterio | Lectura |
| --- | --- | --- | --- |
| A | 164 | Nombra dos o más familias de redes | Texto completo: modelos, referencias, métricas y horizonte |
| B | 268 | Nombra una familia | Extracción desde el resumen; texto completo solo si el resumen no dice contra qué compara |
| C | 48 | Dice "red neuronal" o "aprendizaje profundo" sin precisar | Texto completo para identificar la arquitectura |

El conteo de los modelos de referencia más usados se hace dos veces: con el nivel A solo y con todos
los niveles. Si los tres primeros coinciden, la selección es robusta; si no, se reporta la
diferencia. Este criterio se fija antes de leer los textos completos.

## Extracción en texto completo (2026-11-08)

**Obtención de los textos.** De los 212 estudios de los niveles A y C se consiguieron 90 textos
completos que entraron a la extracción:

- acceso abierto descargado directamente (OpenAlex, Europe PMC, arXiv);
- Springer, Elsevier y Taylor & Francis leídos con el acceso institucional de Uninorte.

No fueron accesibles:

- IEEE (45 estudios), que responde con un control antibots;
- Wiley y algunos capítulos de libro, que no están suscritos;
- MDPI, cuya página bloquea la extracción automática.

Esos estudios se extraen desde el resumen y quedan marcados en la columna `fuente_extraccion`. Los
textos completos no se guardan en el repositorio por derechos de autor; solo se guardan los campos
extraídos.

**Método.** Un script cuenta en cada texto las familias de redes, las referencias no neuronales, las
métricas, la frecuencia de los datos y el horizonte. Para no contar los modelos que el artículo solo
cita en su revisión de literatura, el conteo empieza en la sección de resultados o experimentos y
exige al menos dos menciones. Ese ajuste salió de revisar uno por uno una muestra de 8 textos
(semilla 42); la revisión la hizo la misma herramienta. Antes del ajuste, S1-1564 aparecía con MLP, ELM, RNN y CNN; después quedan sus referencias
reales, TDNN y ARIMA. Persisten dos limitaciones:

- el script no distingue el modelo propuesto de las referencias: cuenta las familias que el estudio
  usa o compara;
- el horizonte no se detecta en 56 de los 90 textos.

**Resultado** (`conteo_modelos.json`, `extraccion_texto_completo.csv`). Las tres familias más usadas
son las mismas en los tres conteos de sensibilidad:

| Conteo | 1.º | 2.º | 3.º | 4.º |
| --- | --- | --- | --- | --- |
| Todos los niveles (480) | LSTM (246) | MLP/ANN (167) | CNN (69) | Transformer (59) |
| Nivel A (164) | LSTM (122) | MLP/ANN (63) | CNN (61) | GRU (49) |
| Solo texto completo (90) | LSTM (63) | MLP/ANN (38) | CNN (31) | Transformer (27) |

La selección LSTM, MLP y CNN es robusta según el criterio fijado. El cuarto puesto varía entre
Transformer y GRU.

En los 90 textos completos:

- la referencia no neuronal más usada es ARIMA/SARIMA (42), seguida de SVR/SVM (23);
- solo 13 incluyen una referencia ingenua o de persistencia;
- 24 aplican la prueba de Diebold-Mariano;
- la mayoría usa datos diarios (49).

## Comparación con el detector de Lina (2026-11-09)

`scripts_camilo/comparar_detectores.py` aplica los dos detectores de familias al mismo texto: título,
resumen y palabras clave de los 480 estudios. El de Lina se lee de `contar_modelos_conjunto.py` en
su rama (commit 8035db1). Las cifras están en `comparacion_detectores.json` y el detalle por estudio
en `comparacion_detectores.csv`.

- **Resultado.** Los dos dan el mismo orden: LSTM, MLP/ANN y CNN. LSTM (240), CNN (56), GRU (55) y
  Transformer (48) tienen el mismo conteo en ambos. Difieren 47 estudios, y el nivel coincide en el
  94,6 %.
- **RNN simple (32 frente a 50).** La expresión de Lina incluye «recurrent neural», que también
  aparece al describir una LSTM o una GRU («gated recurrent neural network») y en las palabras clave
  de Scopus. De las 18 diferencias, unas 5 son RNN simples (S1-0251, S1-0670, S1-0736, S1-1357,
  S1-2015). Mi detector no halla ninguna de las cinco; en tres falla porque `\brnn\b` no acepta el
  plural «rnns».
- **MLP/ANN (145 frente a 154).** Lina cuenta «deep neural network», que en 8 de los 9 casos es un
  término genérico o una palabra clave. El noveno, S1-1947, nombra una «bp network» que mi
  expresión no reconoce.
- **Familias que solo tiene Lina.** Autoencoder/GAN (8), ANFIS (3), Reservorio (3) y DBN (2). Mi
  detector no las busca.
- **Otros casos.** Lina cuenta DLinear como N-BEATS (2), aunque es un modelo lineal, y BERT como
  Transformer en S1-0631, donde se usa para identificar temas y no para pronosticar. Mi detector
  halla TFT en S1-0420 y una red NAR escrita «auto-regressive» en S1-1472, y el de ella no.

Ninguna de estas diferencias toca las tres primeras familias, así que no se rehízo la extracción.
Para el informe conviene una sola expresión por familia que tome lo correcto de cada lado:

- de Lina: «rnns», «bp network» y las cuatro familias que solo ella busca;
- de este protocolo: TFT, «auto-regressive» y quitar «recurrent neural» y «deep neural network»
  como sinónimos de RNN y MLP;
- de ninguno: DLinear, que no es una red de la familia N-BEATS.

## Revisión de los 34 fallos de desacuerdo (2026-11-09)

Lina dejó en `resolucion_desacuerdos.csv` el fallo de los 34 desacuerdos entre los dos tamizajes.
A pedido de Camilo, la herramienta de la sección «Uso de inteligencia artificial» los revisó con el
título, el resumen completo de Scopus, los criterios y las cinco reglas de fallo. El juicio de cada
caso está en `revision_fallos_desacuerdos.csv`, y no se modificó el archivo de Lina.

**Confirmación humana.** El mismo 2026-11-09, Camilo leyó título y resumen de los 7 casos marcados
(los dos con cambio y los cinco con reserva) y decidió cada uno. Sus decisiones están en la columna
`decision_camilo` y coinciden con la revisión en los 7: incluir L0853, L0065, L0085 y L0109, y excluir
L0869, L0364 y L0959. Camilo informó que también revisó los otros 27, en los que la revisión coincide
con el fallo de Lina; para esos no quedó una decisión caso por caso.

| Juicio | Casos |
| --- | --- |
| De acuerdo | 27 |
| De acuerdo con reserva | 5 |
| Cambia el criterio | 1 |
| En desacuerdo | 1 |

- **En desacuerdo: L0853 (S1-0144).** El fallo lo excluye por E5 porque el precio lo pronostica un
  gradient boosting. Pero la red de grafos (GraphSAGE) pronostica la producción de 31 distritos con
  17 años de datos, y la regla 3 admite la producción regional como serie de oferta. Se propone
  incluirlo y confirmar en el texto completo que ese pronóstico es temporal.
- **Cambia el criterio: L0869 (S1-0858).** Sale igual, pero por E4 y no por E3: estima la
  producción con clima y suelo del mismo período, que la regla 3 manda a E4.
- **Verificar en el texto completo.** Tres incluidos dependen de un dato que el resumen no da:
  - L0065: la CNN clasifica imágenes para elegir el modelo de pronóstico; entra solo si algún
    candidato es una red.
  - L0085: «productivity» puede ser rendimiento por hectárea.
  - L0109: el modelo usa el clima del mismo mes; sin horizonte posterior saldría por E4.
- **Reservas sin cambio.** En L0364 y L0959 el fallo se sostiene con las reglas, pero el resumen
  es ambiguo.

Con esta revisión el conjunto pasaría de 490 a 491 estudios; L0853 aporta una GNN. No cambian las
tres primeras familias.

## Textos completos adicionales (2026-11-09)

Se buscó el texto completo de los cuatro casos de la revisión que dependían de él (L0853, L0065,
L0085 y L0109) y de los 30 estudios de niveles A y C que la extracción de Lina dejó desde el
resumen. Se usó el mismo detector, ejecutado dentro de la página del artículo o sobre el PDF de
acceso abierto; el texto no sale del navegador ni se guarda. Al patrón que marca el inicio de los
resultados se le añadieron los numerales romanos de IEEE («IV. Results»). El resultado está en
`texto_completo_adicional_camilo.csv`.

**Obtenidos (6).** L0065 (Scientific Reports), L0085 (Cogent Engineering), L0109 (IEEE Access, de
acceso abierto), L0511 (copia en el repositorio de Göttingen), L0328 (Emerald) y L0286 (Current
Science).

**No accesibles (28).**
- 26 de IEEE: por el proxy de Uninorte, IEEE respondió con una verificación contra bots («Max
  challenge attempts exceeded»), que no se intentó evitar. Entre ellos está L0853.
- L0029: su DOI no está registrado (doi.org responde 404).
- La página de Oxford de L0511 también pidió esa verificación, pero se encontró la copia abierta.

**Verificaciones.** Los tres incluidos que dependían del texto completo se mantienen:
- L0065: la CNN elige entre seis candidatos, y dos son redes (BPNN y ELM).
- L0085: la serie es la producción provincial en toneladas, no rendimiento por hectárea.
- L0109: el clima entra rezagado (t−1) y la validación respeta el orden temporal.

L0853 queda incluido por la revisión, sin confirmar en el texto completo.

**Efecto en el conteo.** En el corte de texto completo del conteo conjunto, los 90 estudios pasan a
96: LSTM 63 → 65, MLP/ANN 37 → 40 y CNN 32 → 33. El orden no cambia. Hay dos correcciones frente al
resumen: L0286 no usa una MLP como modelo (la ANN solo aparece en la introducción) y L0511 no usa una
RNN simple.
