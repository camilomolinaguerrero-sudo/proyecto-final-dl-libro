# Revisión de literatura y matriz de novedad del modelo original

Sigue la sección 6.3 del enunciado. La búsqueda se hizo el **2026-11-28** y hay que repetirla antes de
la entrega. Los registros completos están en `literatura/novedad/`: las respuestas de cada base, los
resúmenes leídos, la búsqueda por citación y la de repositorios, junto con los scripts que las hacen.

## 1. Qué se busca

El modelo original es una mezcla de dos expertos con compuerta (Jacobs et al., 1991) sobre una CNN
causal. La compuerta se supervisa durante todo el entrenamiento con el régimen observado de la
semana que se pronostica: interrupción si la razón de abastecimiento cae por debajo de 0,5. Las
cadenas combinan:

- el mecanismo: mezcla de expertos, compuerta y compuerta supervisada;
- el problema: abastecimiento de alimentos, mercados mayoristas, interrupciones del suministro;
- el régimen;
- sinónimos en español;
- el mismo mecanismo en otras aplicaciones, para ubicar su origen.

## 2. Bases y cadenas

**Bases consultadas.** OpenAlex (búsqueda booleana en título y resumen), arXiv (campo `abs`) y
OpenAIRE (palabras clave).

**Bases que no se pudieron usar:**
- Scopus, Web of Science e IEEE Xplore piden acceso institucional desde el script. En el E1 se
  consultaron a mano para el problema (`literatura/registros/`).
- Semantic Scholar respondió 429 (límite de uso) a todas las consultas.
- DBLP pidió una verificación anti-bots, que no se intentó saltar.

| Cadena | Contenido (resumido) | OpenAlex | arXiv | OpenAIRE | Revisados | Relevantes | Observación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| C1 | "mixture of experts" y abastecimiento de alimentos o mercado mayorista | 1 | 6 | 9 (+1 con "wholesale market") | 17 | 1 | Solo una mezcla de expertos para precios de ajo; nada sobre abastecimiento |
| C2 | "mixture of experts", "time series" y régimen, disrupción o anomalía | 77 | 14 | 17 | 71 | 9 | Mezclas de expertos por régimen de 2025-2026; ninguna en alimentos |
| C3 | compuerta o mezcla de expertos y "supply disruption", con pronóstico | 0 | 0 | 0 | 0 | 0 | Sin resultados |
| C4 | "regime switching", redes neuronales y oferta agrícola o de alimentos | 0 | 0 | 2 | 2 | 0 | Sin relación |
| C5 | "mezcla de expertos" (español) | 17 | 0 | 84 | 67 | 0 | Variantes del mecanismo en otras áreas; ninguna en abastecimiento |
| C6 | "mixture of experts" y compuerta supervisada (otras aplicaciones) | 5 | 0 | 76 | 23 | 4 | Compuertas supervisadas o autosupervisadas en voz, recuperación de información y aprendizaje por refuerzo |
| C7 | SIPSA y aprendizaje profundo o pronóstico | 1 | 1 | 0 | 2 | 0 | Solo homónimos; los usos previos de SIPSA están en el E1 |

Notas de la tabla:
- **Revisados:** títulos leídos de los primeros resultados de cada base, hasta 50 por consulta.
- **Relevantes:** trabajos cuyo resumen se leyó y que entran en la matriz de la sección 4.

**Búsqueda por citación** (`bola_de_nieve_2026-11-28.json`): trabajos que citan a Jacobs et al.
(1991), a Lim et al. (2021), que es el paper guía, y al trabajo más cercano (Wang et al., 2026),
filtrados por abastecimiento o régimen. Se revisaron también las referencias de Wang et al.

| Semilla | Citas totales | Citan y tratan abastecimiento | Citan y tratan mezcla de expertos por régimen en pronóstico |
| --- | --- | --- | --- |
| Jacobs et al. (1991) | 7976 | 0 | 27 |
| Lim et al. (2021), paper guía | 5300 | 15 (cadenas de suministro y precios; ninguna mezcla de expertos) | 17 |
| Wang et al. (2026) | 5 | 0 | 2 |

**Repositorios** (`github_2026-11-28.txt`):
- No hay repositorios de mezcla de expertos para abastecimiento de alimentos ni de compuerta
  supervisada en series de tiempo.
- Hay seis de mezclas de expertos por régimen en volatilidad financiera y energía, entre ellos el
  código de Ye y Borde (2026).
- Los repositorios sobre SIPSA tratan de precios, salvo los de los autores.

## 3. Trabajos más cercanos

Se leyeron el título y el resumen de cada trabajo, tomados de OpenAlex (`resumenes_cercanos.json`).
La diferencia se clasifica en las categorías del enunciado: mecanismo, función de pérdida, esquema de
entrenamiento o solo aplicación.

| Trabajo | Qué hace | Diferencia con el modelo original | Categoría |
| --- | --- | --- | --- |
| Jacobs, Jordan, Nowlan y Hinton (1991), *Neural Computation* | **Fuente del mecanismo.** Mezcla de expertos con compuerta, entrenada solo con el error de la salida | Aquí la compuerta se supervisa con el régimen observado del objetivo (λ·BCE), sobre una CNN causal, y hay una cota de Lipschitz demostrada | Función de pérdida |
| Jordan y Jacobs (1994), *Neural Computation* | Mezcla jerárquica de expertos entrenada con EM | Sin jerarquía ni EM; supervisión explícita del régimen | Función de pérdida y entrenamiento |
| Lim et al. (2021), *International Journal of Forecasting*, **paper guía** (TFT) | Atención interpretable y selección de variables, sin regímenes explícitos | Mecanismo de mezcla por régimen, motivado por el error mayor del TFT en las interrupciones (piloto del E2) | Mecanismo |
| Wang, Sanderson y Woo (2026), *Sensors* (WL-MoE) | Mezcla de expertos para extremos. Guía el enrutamiento con etiquetas débiles de *clustering* solo en una primera etapa y luego entrena solo con el pronóstico | Aquí la etiqueta es el régimen definido por el problema (razón < 0,5 en t+1), no un *cluster*, y la supervisión se mantiene en todo el entrenamiento. Además, la compuerta se lee como probabilidad de interrupción | Función de pérdida y esquema de entrenamiento |
| Ye y Borde (2026), ICAIF (RG-ResMoE) | El régimen, como información de entrada, solo decide el enrutamiento en volatilidad financiera | El régimen no es una entrada sino una etiqueta del objetivo que supervisa la compuerta | Función de pérdida |
| Gong et al. (2026), *Marine Energy Research* (RegimeMoE-PatchTST) | Compuerta condicionada por variables meteoceánicas, «sin etiquetas de régimen predefinidas» | Compuerta supervisada con el régimen observado | Función de pérdida |
| Li et al. (2026), IJCAI (NEST) | Regímenes obtenidos por *clustering* no supervisado del conjunto de datos | Régimen definido por el problema y supervisado | Función de pérdida y entrenamiento |
| Jin et al. (2025), Interspeech (S-MoE) | En otra aplicación, el enrutamiento es supervisado: tokens de tarea asignan cada ejemplo a su experto, sin compuerta entrenada | Aquí la compuerta sí se entrena y es suave; la supervisión es un término de pérdida, no una asignación fija | Mecanismo |
| Yu et al. (2026), *Sustainability* (GPF-EVMoLE) | Mezcla de expertos para precios de ajo | Precio, no abastecimiento; sin supervisión del régimen | Función de pérdida y aplicación |
| Molina Guerrero y Buelvas (2026), proyecto de Machine Learning (AJ-KL) | **Trabajo previo de los autores** sobre el mismo panel: clasificación de interrupciones con apilamiento jerárquico y encogimiento KL | Otra tarea (pronóstico de la razón, no clasificación) y otro mecanismo | Mecanismo |

## 4. Declaración de novedad

Al mejor de nuestro conocimiento, y según la búsqueda descrita del 2026-11-28, no se encontraron
estudios que pronostiquen el abastecimiento de alimentos de corredores hacia centrales mayoristas con
una mezcla de expertos cuya compuerta se supervise con el régimen observado de interrupción del
objetivo.

- **Fuente del mecanismo:** la mezcla de expertos de Jacobs et al. (1991). La adaptación cambia la
  función de pérdida: agrega λ·BCE entre la compuerta y el régimen de la semana pronosticada.
- **Trabajo más cercano:** Wang et al. (2026). También guía el enrutamiento hacia los extremos, pero
  con etiquetas de *clustering* y solo en una primera etapa.
- **Clasificación:** la diferencia es de **función de pérdida y esquema de entrenamiento**, no solo de
  aplicación.
- **Lo que no es nuevo:** las mezclas de expertos por régimen en series de tiempo son un área muy
  activa en 2025-2026. La contribución es acotada, y así se presenta.

## 5. Limitaciones de la búsqueda

- **Bases no consultadas:** Scopus, Web of Science e IEEE Xplore no se pudieron consultar desde el
  script, y Semantic Scholar y DBLP tampoco. OpenAlex indexa buena parte de su contenido, pero no
  reemplaza la búsqueda en ellas.
- **Profundidad de la revisión:** se revisaron títulos y resúmenes, no textos completos. Cuatro de los
  trabajos cercanos no tienen resumen en OpenAlex: EAME, Deep Statistical Jump Models, Liu (2026) y
  Chen et al. (2026).
- **Pendiente:** repetir la búsqueda antes de la entrega y registrar la fecha.

## Referencias

- Gong, M., Wang, Y., Sun, M. et al. (2026). RegimeMoE-PatchTST: Metocean-Conditioned Mixture of PatchTST Experts for Multi-Step Offshore Wind Power Forecasting. *Marine Energy Research*. https://doi.org/10.70322/mer.2026.10022
- Jacobs, R. A., Jordan, M. I., Nowlan, S. J. y Hinton, G. E. (1991). Adaptive Mixtures of Local Experts. *Neural Computation*, 3(1), 79-87. https://doi.org/10.1162/neco.1991.3.1.79
- Jin, H., Hong, E., Hyung, Z. et al. (2025). Beyond Hard Sharing: Efficient Multi-Task Speech-to-Text Modeling with Supervised Mixture of Experts. *Interspeech 2025*. https://doi.org/10.21437/interspeech.2025-643
- Jordan, M. I. y Jacobs, R. A. (1994). Hierarchical Mixtures of Experts and the EM Algorithm. *Neural Computation*, 6(2), 181-214. https://doi.org/10.1162/neco.1994.6.2.181
- Li, L., Xie, B., Sun, L. G. et al. (2026). NEST: Tackling Dataset-Level Distribution Shifts via Regime-Oriented Mixture-of-Experts. *IJCAI 2026*. https://doi.org/10.24963/ijcai.2026/308
- Lim, B., Arık, S. Ö., Loeff, N. y Pfister, T. (2021). Temporal Fusion Transformers for interpretable multi-horizon time series forecasting. *International Journal of Forecasting*, 37(4), 1748-1764. https://doi.org/10.1016/j.ijforecast.2021.03.012
- Wang, J., Sanderson, J. y Woo, W. L. (2026). Extreme-Aware Time-Series Forecasting via Weak-Label-Guided Mixture of Experts. *Sensors*, 26(5), 1571. https://doi.org/10.3390/s26051571
- Ye, J. y Borde, G. V. (2026). Regime-Gated Residual Mixture-of-Experts for Cross-Sectional Volatility Forecasting. *ICAIF 2026*. https://doi.org/10.1145/3858063.3858798
- Yu, X., Zhu, K., Jiang, H. et al. (2026). GPF-EVMoLE: An ETS-Driven Variable Selection and Mixture-of-Experts Framework for Multi-Step Garlic Price Forecasting. *Sustainability*. https://doi.org/10.3390/su18094404
