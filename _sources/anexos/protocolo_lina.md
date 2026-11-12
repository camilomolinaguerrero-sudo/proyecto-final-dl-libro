# Protocolo de tamizaje (búsquedas W1 e I1)

Aplica a `registros/wos_2026-11-07_W1.txt` y `registros/ieee_2026-11-07_I1.csv`. Las decisiones quedan en `tamizaje_lina.csv` y las cifras para el diagrama PRISMA en `tamizaje_lina_conteos.json`.

## Declaración sobre cómo se tomaron las decisiones

**Nadie leyó cada registro.** El tamizaje de título y resumen de estas dos búsquedas se hizo con reglas automáticas:

1. Claude escribió las reglas de `sugerir_tamizaje.py`, que buscan palabras en el título y el resumen y usan el tipo de documento y el idioma que da cada base.
2. Lina decidió el 2026-11-08 adoptar en bloque el resultado de esas reglas como decisión de tamizaje, sin leer ni revisar los registros uno por uno.
3. Cada decisión lleva en `nota` el motivo que dio la regla. Las columnas `sugerencia_claude`, `criterio_sugerido` y `motivo_sugerencia` conservan la salida original.
4. Los 194 registros que las reglas dejaron como dudosos los falló Claude el 2026-11-08, por encargo de Lina. Leyó el título y extractos del resumen (el inicio y las frases sobre pronóstico o modelos, hasta 620 caracteres), y el final del resumen en 16 casos. Cada fallo y su razón están en `fallos_dudosos_lina.csv`.

**El tamizaje de Scopus y arXiv tampoco es lectura humana registro por registro.** Camilo informó por correo el 2026-11-09 que su tamizaje, el fallo de sus dudosos y su extracción los hizo una herramienta de IA a pedido suyo, con criterios escritos antes y auditoría por muestra. `protocolo_tamizaje_camilo.md` lo declara desde ese día, en su sección «Uso de inteligencia artificial»: la herramienta es Claude (Anthropic), usada en Claude Code, la misma de este lado. Los dos tamizajes no son independientes ni siquiera en la herramienta.

Hasta el 2026-11-09, este protocolo y `consolidar_tamizaje.py` decían que el criterio de Camilo venía de «lectura humana». Era una suposición de Claude, no un dato, y se corrigió ese día.

Texto propuesto para el informe:

> El tamizaje de título y resumen de los registros de Web of Science e IEEE Xplore se realizó con reglas automáticas basadas en palabras clave, escritas con asistencia de una herramienta de inteligencia artificial (Claude) y aplicadas sobre los criterios I1 a I4 y E1 a E6. Lina Buelvas adoptó en bloque sus resultados, sin lectura individual de los registros. Los 194 casos dudosos los resolvió la misma herramienta a partir del título y de extractos del resumen, por encargo de la autora. Una revisión por muestreo de títulos estimó que cerca del 5 % de las exclusiones por variable no agroalimentaria podría ser incorrecta (2 de 40; intervalo de Wilson al 95 %: 1 % a 17 %). En los 879 estudios de 2018 a 2026 presentes también en las búsquedas de Scopus y arXiv, el acuerdo con el tamizaje de esas bases, a cargo de Camilo Molina y hecho con la misma herramienta, fue de 96,1 % (κ de Cohen = 0,91; IC 95 % aproximado: 0,88 a 0,94). Ese valor compara dos procedimientos hechos con la misma herramienta de inteligencia artificial y con los mismos criterios, no dos revisores humanos independientes. Los 34 desacuerdos los resolvió la herramienta del lado de Lina Buelvas a partir del resumen completo y de las reglas de fallo del protocolo de Scopus y arXiv. Lina Buelvas confirmó esos fallos tras revisar el título y la razón de cada uno. Una segunda revisión, hecha por la herramienta a pedido de Camilo Molina, coincidió en la decisión de 33 de los 34; los dos autores leyeron el título y el resumen de los 7 casos que esa revisión marcó, y con su acuerdo un estudio pasó de excluido a incluido y otro cambió de criterio de exclusión.

La frase sobre Scopus y arXiv sigue la declaración de `protocolo_tamizaje_camilo.md`. Camilo debe confirmar el modelo exacto de Claude antes del informe.

## Criterios

Son los de `protocolo_tamizaje_camilo.md`, con sus códigos y su orden: inclusión I1 a I4 y exclusión E1 a E6. Para escribir las reglas, Claude leyó solo las secciones de pregunta y criterios de ese protocolo. No abrió sus resultados, sus fallos sobre casos dudosos ni `tamizaje_camilo.csv`, de modo que las reglas no dependen de las decisiones de Camilo.

Las reglas no verifican todo lo que piden los criterios:

- **I4:** solo se comprueba el idioma y el tipo de documento que da la base.
- **E6:** los duplicados se quitaron antes, por DOI y por título normalizado.
- **Comparación contra otro modelo:** igual que en el protocolo de Camilo, se verifica en el texto completo.
- **Red neuronal:** un resumen que dice «machine learning» sin nombrar una red queda como `dudoso`.

## Resultado

| Etapa | Incluir | Dudoso | Excluir |
| --- | --- | --- | --- |
| Reglas | 273 | 194 | 903 |
| Fallo de los dudosos | +105 | -194 | +89 |
| **Final** | **378** | **0** | **992** |
| Final, solo 2018-2026 | 351 | 0 | 945 |

Exclusiones finales por criterio: E1, 75; E2, 8; E3, 729; E4, 135; E5, 45.

Para los dudosos se usó esta regla: si pronosticar una variable de mercado de alimentos es el objetivo y no se puede descartar una red, el registro pasa a texto completo; si el resumen nombra sus modelos y ninguno es una red, sale por E5. De los incluidos, 18 llevan en `nota` que falta verificar en el texto completo.

## Revisión por muestreo

Claude leyó solo los títulos de muestras al azar. No se revisaron resúmenes, así que el juicio también es aproximado.

| Ronda | Reglas | Grupo | Muestra | Posibles errores |
| --- | --- | --- | --- | --- |
| 1 | Primera versión; sirvió para ajustarlas | Seis grupos | 212 | Se usó para corregir las reglas |
| 2 | Tras la primera corrección | Incluir | 45 | 1 (L0326) |
| 2 | | Excluir E3 | 45 | Hasta 3 (L0733, L0619, L0278) |
| 2 | | Excluir E4 | 15 | 0 |
| 2 | | Excluir E5 | 12 | Al menos 1 (L0851) |
| 3 | Versión final | Excluir E3 | 40 | 2 (L0746, L0109) |

Después de la ronda 2 se agregaron «inflation» a las variables de mercado y más nombres de redes; L0733 y L0851 pasaron a incluir y L0619 a dudoso. Solo E3 se volvió a medir con la versión final. El grupo `dudoso` no se evaluó, porque es justo el que falta leer.

Con 2 errores en 40, la tasa de exclusión errónea en E3 sería cercana al 5 %, con intervalo de Wilson al 95 % de 1 % a 17 %. Sobre 681 registros eso significa entre unos 9 y 112. Es una estimación por títulos con una muestra pequeña.

L0326, L0746 y L0109 llevan la marca `SEÑALADO` en `nota`. Se dejó la decisión de la regla, para no mezclar criterios a mano.

## Comparación con el tamizaje de Camilo

Se hizo el 2026-11-08, después de cerrar estas decisiones. El SHA-256 del archivo antes de comparar está en `tamizaje_lina_conteos.json`. Las cifras salen de `comparacion_tamizaje.json` y el detalle por estudio de `comparacion_tamizaje.csv`, generados con `comparar_tamizajes.py`.

| Medida | Valor |
| --- | --- |
| Estudios en común | 928, de 1.370 de Lina y 2.219 de Camilo |
| Ambos incluyen | 297 |
| Ambos excluyen | 592 |
| Solo Lina incluye | 20 |
| Solo Camilo incluye | 19 |
| Acuerdo observado | 95,8 % |
| κ de Cohen | 0,91 (IC 95 % aproximado: 0,88 a 0,94) |

Un registro `dudoso` de Camilo cuenta como incluido, porque su protocolo lo pasa a texto completo.

Cómo leer este resultado:

- **No es el acuerdo entre dos personas.** Ninguno de los dos tamizajes es lectura humana registro por registro: del lado de Lina decidieron reglas y Claude, y del lado de Camilo, también Claude, a pedido suyo. Además, las reglas se escribieron con los criterios de Camilo, así que ambos tamizajes comparten su base y su herramienta, y sus errores pueden ir en la misma dirección. El κ mide la coincidencia entre dos procedimientos asistidos por IA y puede sobrestimar el acuerdo entre revisores independientes.
- **Sirve para estimar el error de las reglas, con una reserva.** De los 564 registros comunes que las reglas excluyeron y el tamizaje de Camilo también evaluó, este incluyó 18 (3,2 %), un valor compatible con la estimación por muestreo. La referencia es otro tamizaje asistido por IA, no un patrón leído por personas. Algunos sí son errores claros de las reglas; por ejemplo, L0764 pronostica el precio del cardamomo y salió por contener «weather» en el título.
- **Parte de los desacuerdos viene de reglas distintas.** El protocolo de Camilo excluye el algodón, la resina y la demanda de restaurantes, y solo admite plataformas si el resumen dice que usan una red. Estas decisiones se tomaron sin conocer esas reglas.

## Acuerdos con Camilo

El 2026-11-08 Camilo respondió que sí a los tres puntos que Lina le planteó por correo: el filtro de años, δ = 0,0032 y los estudios de congresos. Lina lo informó en el chat; la respuesta no está en el hilo de correo. Los acuerdos están en `DECISIONES.md`.

- **Filtro 2018-2026.** Se aplicó después de los dos tamizajes, en las cuatro bases. Deja 351 de los 378 incluidos de Lina y 431 de los 480 de Camilo. En los estudios de Camilo no cambia las tres familias más nombradas en los resúmenes: LSTM, MLP/ANN y CNN. Se aplicó al pie de la letra, así que quedan fuera 14 registros con año 2027, de los cuales Camilo había incluido 3. En 13 estudios comunes las bases dan años distintos, pero ninguno cambia de lado del filtro.
- **Justificación del filtro.** El 2026-11-09 Camilo recordó por correo que su propuesta escrita era no limitar los años y pidió justificar el corte. Lina decidió mantenerlo. Sin el filtro entrarían 52 estudios más, de los cuales 40 nombran MLP/ANN, y las tres familias más usadas serían las mismas y en el mismo orden: LSTM (274), MLP/ANN (182) y CNN (76) en 543 estudios. Otros 5 estudios fuera del período los incluye solo un tamizaje y no tienen fallo; con ellos tampoco cambia. Las cifras están en `conteo_modelos_conjunto.json`, clave `sensibilidad_sin_filtro_de_años`.
- **Congresos.** Se admiten, y eso no cambió ninguna decisión. Los registros de congreso de IEEE Xplore que salieron por E1 son revisiones o retractaciones, salvo L1289, que salió por contener «Survey» en el título y que por su tema (avance de cultivos) saldría por E3. El E1 de Camilo excluye las actas completas, no las ponencias.

## Resolución de los desacuerdos

De los 39 desacuerdos, 5 quedaron fuera por el filtro. Los otros 34 los resolvió Claude el 2026-11-08, después de leer el resumen completo, con los criterios I1 a I4 y E1 a E5 y las reglas de fallo de `protocolo_tamizaje_camilo.md`. Cada fallo y su razón están en `resolucion_desacuerdos.csv`, y no se modificó `tamizaje_lina.csv`.

- **Base del fallo.** 20 con los criterios y 14 con las reglas de Camilo: productos no alimentarios 2, servicios de comida 4, producción 6 y plataformas 2. El correo a Camilo proponía usar las reglas de algodón, restaurantes y plataformas; la de producción se añadió aquí.
- **Quién decide en cada lado.** En las razones de `resolucion_desacuerdos.csv`, «Camilo» y «Lina» nombran el tamizaje de cada lado, no una lectura personal. Los 34 fallos son de Claude. El 2026-11-09 Lina revisó la lista con el título y la razón de cada uno, sin leer los resúmenes, y los confirmó sin cambios. El 2026-11-10 aceptó el cambio de L0853 que propuso Camilo (ver «Segunda revisión»).
- **Segunda revisión.** El 2026-11-09 la herramienta de Camilo revisó los 34 con el resumen completo (`revision_fallos_desacuerdos.csv`, en `camilo/eda`). Coincidió en la decisión de 33, 5 de ellos con reserva. Camilo leyó título y resumen de los 7 casos marcados y los decidió. Cambió uno: L0853 pasa de excluir por E5 a incluir, porque su red de grafos pronostica la producción de 31 distritos, que la regla 3 admite; el precio lo pronostica gradient boosting. Falta confirmar en el texto completo que ese pronóstico es temporal. En L0869 propuso E4 en lugar de E3, sin cambiar la decisión: la regla 3 manda a E4 la producción estimada con insumos del mismo período, y E3 se reserva para una sola finca o el rendimiento por hectárea. El 2026-11-10 Lina leyó título y resumen de los 7 casos marcados, confirmó las decisiones y pasó L0869 a E4.
- **Resultado.** 21 pasan a incluir y 13 a excluir. Prevaleció la decisión del lado de Camilo en 29 y la del lado de Lina en 5.
- **Errores del lado de Lina.** De los 541 estudios comunes dentro del período que las reglas excluyeron, 17 debían incluirse según estos fallos (3,1 %; intervalo de Wilson al 95 %: 2,0 % a 5,0 %). De esos 17, 12 salieron por una palabra del título, como «healthcare», «auction», «power», «weather» o «yield». En L0948 el fallo de Claude dijo que no había redes y el resumen nombra LSTM: el extracto leído no lo mostraba. Sale igual, por la regla de servicios de comida.
- **Errores del lado de Camilo.** Su tamizaje sacó L0188 y L0295 por el título, sin pasar al resumen, y uno pronostica producción nacional y el otro futuros agrícolas. Y su fallo de dudosos sacó L0853 por E5, que volvió con la segunda revisión.
- **Metadatos en conflicto.** L0012 tiene idioma coreano en Scopus e inglés en WoS; la página de la revista muestra el texto en coreano, así que sale por E2. L0286 es carta en Scopus y artículo en WoS; se incluye porque el resumen describe un estudio empírico.

## Conjunto final de las cuatro bases

Las cifras salen de `tamizaje_conjunto_conteos.json` y el detalle por estudio de `tamizaje_conjunto.csv`, generados con `consolidar_tamizaje.py`.

| Etapa | Registros |
| --- | --- |
| Identificados: Scopus 2.196, arXiv 29, WoS 672, IEEE Xplore 730 | 3.627 |
| Duplicados: 32 dentro de WoS e IEEE Xplore, 6 dentro de Scopus y arXiv, 928 entre ambos pares | -966 |
| Únicos | 2.661 |
| Fuera de 2018-2026 | -195 |
| Evaluados dentro del período | 2.466 |
| Excluidos: E1 179, E2 102, E3 1.555, E4 89, E5 50 | -1.975 |
| **Incluidos** | **491** |

De los 491, 278 los incluyeron ambos tamizajes, 135 solo aparecen en Scopus o arXiv, 57 solo en WoS o IEEE Xplore y 21 salen de resolver desacuerdos. Por tipo: 281 de revistas y otros, 203 de congresos y 7 preprints de arXiv. Si ambos excluyen con criterios distintos, se conserva el de Camilo por convención. Eso solo cambia el reparto de las exclusiones por criterio, no qué estudios quedan incluidos. Ninguno de los dos criterios viene de lectura humana.

## Extracción y conteo de modelos

Se hizo el 2026-11-08 con `contar_modelos_conjunto.py`, siguiendo el método de `protocolo_tamizaje_camilo.md`. Las cifras están en `conteo_modelos_conjunto.json`. De los 491 incluidos, 430 ya estaban en la extracción de Camilo y se conservan tal cual; los otros 61 se extrajeron aquí y están en `extraccion_lina.csv`.

**Textos completos adicionales.** El 2026-11-10 se sumaron al conteo 6 textos completos que la herramienta de Camilo obtuvo y extrajo con su detector (`texto_completo_adicional_camilo.csv`, en `camilo/eda`): L0065, L0085, L0109, L0511, L0328 y L0286. En el conteo reemplazan la extracción desde el resumen; los archivos de extracción no cambian. Corrigen dos extracciones de aquí: L0286 no usa una MLP, que solo aparece en la introducción, y L0511 no usa una RNN simple.

**Detector.** El 2026-11-08 el de Camilo no estaba publicado, así que Claude lo reescribió y lo comparó con las extracciones de Camilo hechas desde el resumen. Esas extracciones también las produjo una herramienta de IA, de modo que la calibración mide la coincidencia entre dos detectores automáticos, no frente a una lectura humana. Camilo publicó el suyo el 2026-11-09 en `scripts_camilo/extraer_texto_completo.py`, y ese día comparó los dos sobre el título, el resumen y las palabras clave de sus 480 estudios (`comparacion_detectores.json`, en `camilo/eda`). Dan las mismas tres familias en el mismo orden y el mismo nivel en el 94,6 %. Según esa comparación, este detector cuenta de más RNN simple (50 frente a 32), porque «recurrent neural» también describe una LSTM o una GRU, y MLP/ANN (154 frente a 145), porque «deep neural network» suele ser un término genérico. También cuenta DLinear como N-BEATS y BERT como Transformer. En 384 estudios coinciden en 489 familias, con 15 que solo halla el detector y 14 que solo halló Camilo (precisión y exhaustividad de 97 %), y asignan el mismo nivel en el 95 %. En los 60 estudios extraídos el 2026-11-08, las palabras clave de IEEE y WoS solo añaden 4 menciones de LSTM.

**Acceso a los textos.** De los 61, 29 son de nivel B y se extraen del resumen, como en el protocolo. Los 32 de niveles A y C debían leerse en texto completo:

| Resultado | Estudios |
| --- | --- |
| Leídos (L0724 en arXiv, L0440 en MDPI) | 2 |
| IEEE Xplore respondió con un control antibots en la primera prueba; no se reintentó ni se probaron los demás | 26 |
| Oxford respondió con una verificación de seguridad (L0511) | 1 |
| El proxy no abrió Emerald (L0328) | 1 |
| El DOI no resuelve (L0029) | 1 |
| Solo hay PDF y no se descargó (L0286) | 1 |

Los 30 no leídos se extrajeron desde el resumen y así constan en `fuente_extraccion` y `acceso`. De ellos, la herramienta de Camilo obtuvo después L0511, L0328 y L0286; siguen sin texto los 26 de IEEE Xplore y L0029. Los textos no se guardaron: en los dos leídos solo se registraron los campos extraídos.

| Corte | Estudios | 1.º | 2.º | 3.º | 4.º |
| --- | --- | --- | --- | --- | --- |
| Todos | 491 | LSTM (272) | MLP/ANN (142) | CNN (74) | Transformer (62) |
| Nivel A | 172 | LSTM (131) | CNN (66) | MLP/ANN (59) | GRU (55) |
| Texto completo | 96 | LSTM (65) | MLP/ANN (40) | CNN (33) | Transformer (27) |

Las tres familias son las mismas en los tres cortes, que es el criterio de robustez fijado en el protocolo. El orden sí cambia: en el nivel A, CNN pasa a MLP/ANN, y MLP/ANN supera a GRU por solo 4 estudios.

Cómo leer este resultado:

- 395 de los 491 estudios se extrajeron desde el resumen, y 40 no nombran ninguna familia concreta.
- El detector no distingue el modelo propuesto de las referencias: cuenta las familias que el estudio usa o compara.
- Los 96 textos completos son 88 de la extracción de Camilo, 6 que su herramienta obtuvo después y 2 de aquí. Ninguno consta como lectura humana: en los 94 de Camilo contó un script, según su protocolo, y los 2 de aquí los leyó Claude en la página del artículo. Entre ellos, las referencias no neuronales más usadas son ARIMA/SARIMA (47) y SVR/SVM (25), 14 incluyen una referencia ingenua y 24 aplican la prueba de Diebold-Mariano.

## Lo que falta

1. **Confirmar los 34 fallos.** Lina los confirmó el 2026-11-09 a partir del título y la razón de cada uno, y el 2026-11-10 aceptó el cambio de L0853. Los dos leyeron título y resumen de los 7 casos que marcó la revisión de Camilo y coinciden en todos; L0869 pasó a E4 el 2026-11-10. Los otros 27 los confirmó Lina con el título y la razón, y Camilo informa que también los revisó, sin decisión caso por caso.
2. **Exclusiones que nadie leyó.** 321 registros solo están en WoS o IEEE Xplore y los excluyeron las reglas. Con la tasa medida, se habrían perdido entre 6 y 16 estudios.
3. **Textos sin leer.** Quedan 27 de niveles A y C extraídos solo desde el resumen: los 26 de IEEE Xplore y L0029. L0853 entró sin confirmar en el texto completo que su pronóstico sea temporal, y también es de IEEE Xplore.
4. **Selección.** Con este conteo, `modelos_seleccionados.md` debe fijar las tres familias y justificar sus sesgos inductivos, de común acuerdo.
5. **Declaración del lado de Camilo.** Está en `protocolo_tamizaje_camilo.md` desde el 2026-11-09. Falta que Camilo confirme el modelo exacto de Claude antes del informe.
6. **Expresiones del detector.** Camilo propone para el informe una sola expresión por familia, que corrige los errores de los dos detectores. No cambia las tres familias.
