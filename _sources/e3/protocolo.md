# Protocolo experimental del Entregable 3

**Borrador de Camilo del 2026-11-24, pendiente de aprobación de Lina.** Se congela con la etiqueta
`protocolo-e3` antes de la primera corrida confirmatoria del modelo original. Lo marcado *(por
acordar)* se cierra antes de esa etiqueta. Lo que no cambia frente al E2 se hereda de
`preregistro/preregistro_e2.md` y aquí solo se cita.

## 1. Estándar

El de `experiments/estandar.md` (profundidad teórica), con 12 semillas por modelo.

## 2. Problema, métricas y partición (heredado)

- Tarea, objetivo, ventana, covariables y unidad de independencia: los del preregistro v2, sección 1.
- **Métrica primaria:** MAE. **Secundarias:** RMSE, MASE, AUC de interrupción, pendiente de
  Mincer–Zarnowitz y razón de error en semanas de interrupción frente al resto. Las secundarias no
  sostienen R3.
- **δ = 0,0032** (heredado del E1). **δg = 0,032** (heredado del preregistro v2).
- **Prueba:** 2025, hash `988ab7ac…2c78753`. Se evalúa una sola vez por corrida, con todo fijado antes.
- **Partición de desarrollo y de confirmación (sección 7.2 del enunciado).** El diseño del modelo
  original se decide solo con los bloques de validación k1 a k3 de la validación de origen móvil. El
  bloque k4 (parada temprana y tasa) y la prueba 2025 son de confirmación y no intervienen en ninguna
  decisión de diseño.

## 3. Modelo original y mecanismo

- **Mecanismo:** cabeza de mezcla por régimen sobre la CNN del E1. Una compuerta g(x) ∈ (0, 1) estima
  la probabilidad de que la semana objetivo sea de interrupción, y la salida es
  ŷ = g(x)·h_int(x) + (1 − g(x))·h_norm(x), con dos cabezas sobre la misma representación.
- **Fuente (transferido):** mezcla de expertos con compuerta, de Jacobs, R. A., Jordan, M. I.,
  Nowlan, S. J. y Hinton, G. E. (1991). Adaptive mixtures of local experts. *Neural Computation*,
  3(1), 79-87. https://doi.org/10.1162/neco.1991.3.1.79. Las modificaciones algorítmicas se detallan
  en `experiments/procedencia.md` (borrador de Camilo, por revisar con Lina). La principal es que la compuerta recibe
  supervisión del régimen observado (`y_reg < ln 1,5`) además de la pérdida de pronóstico.
- **Limitación que ataca (L):** la del paper guía, cuantificada en el piloto del E2: el error en semanas
  de interrupción es de 1,20 a 1,26 veces el del resto en TFT, PatchTST y N-BEATS. A eso se suman los
  pronósticos comprimidos hacia la media, con pendientes de Mincer–Zarnowitz entre 0,52 y 0,71 en
  todos los modelos.
- **Hipótesis del mecanismo (D3):** la compuerta M corrige L porque separa los dos regímenes y evita
  que una sola cabeza promedie semanas normales y de interrupción. Por tanto, el modelo original reduce
  el MAE de las semanas de interrupción frente a la CNN en al menos δ.
- **Predicciones secundarias (D4), declaradas a priori:**
  1. La razón de error interrupción/resto baja frente a la de la CNN.
  2. La pendiente de Mincer–Zarnowitz se acerca a 1.
  3. Con la compuerta fija en 0,5 (sin régimen), la ventaja desaparece. Es la ablación principal.
  4. La compuerta discrimina las semanas de interrupción mejor que el azar (AUC > 0,5 con IC).
- **Propiedad demostrada:** cota de Lipschitz del modelo respecto de la entrada, con sus supuestos y
  su verificación numérica (cociente máximo sobre pares de entradas), en `experiments/lipschitz.md`.
- **Implementación:** `src/modelo_original.py` y corridas en `src/original_e3.py`. El peso λ de la
  supervisión de la compuerta quedó en 0,5 el 2026-11-28, con el desarrollo en k1 a k3
  (`experiments/design_log.md`).
- **Disciplina de diseño:** bitácora `experiments/design_log.md` con cada variante, su MAE en el
  desarrollo y la decisión. Tope de variantes: **Nd ≤ 10**. El número real se declara.
- **Criterio de éxito y fracaso (D5):** el de R3 de la sección 7. Si no se cumple, el veredicto es «no
  superior», y se informa con la diferencia mínima detectable.

## 4. Conjuntos de datos adicionales *(por acordar)*

Tres conjuntos públicos de pronóstico de series de tiempo, con la misma tarea (un paso adelante,
modelo global) y la misma métrica primaria. Cada uno debe diferir del principal en al menos dos ejes
(tamaño, dominio, ruido o estructura temporal), y de ellos se registra la fuente, la licencia, la
versión y el hash. Deben ser pequeños, para que la familia completa con 12 semillas quepa en el
presupuesto de GPU. Se eligen y se fijan aquí antes de correr cualquier modelo en ellos, y no se usan
para decisiones de diseño. Se les aplica la misma auditoría de fuga.

**Propuesta de Lina del 2026-11-25** (`preregistro/conjuntos_adicionales.md`, rama `lina/semillas-e3`,
commit 546dbe6), elegida antes de correr ningún modelo: M4 semanal (Monash, 286 series), Dominick
(Monash, muestra de 2.000 series) e ILINet por estado (CDC vía Delphi, 50 series). Fuente, licencia,
versión, hash, selección, adaptador, partición y análisis están en ese archivo y se adoptan aquí por
referencia. Antes de la etiqueta quedan por cerrar tres puntos:

1. **Semillas:** el preregistro de los conjuntos dice 10 (1 a 10) y este protocolo 12. Se alinea a 12.
2. **Familia en los conjuntos:** las tres referencias triviales (mediana, persistencia, ingenuo
   estacional) se corren y se informan, pero no entran a la familia confirmatoria de la sección 7.
3. **Filtro de negativos y tamaño de Dominick:** el filtro mira la serie completa, incluida la parte de
   prueba; debe mirar solo el entrenamiento. Y Dominick, con 729.810 semanas, es más grande que el
   entrenamiento del panel (622.244 filas); con 12 semillas su costo excede el presupuesto (ver la
   estimación en el correo del 2026-11-25). Propuesta: bajar la muestra antes de correr nada.

## 5. Ejecuciones, semillas y determinismo

- **12 semillas** (1 a 12) para todos los modelos de la familia y el modelo original. Las semillas 1 a
  10 ya existen (las 9 y 10, de Lina, en `resultados/semillas_e3/`); faltan la 11 y la 12 de cada
  rival (`src/semillas_e3.py`). Con las diez semillas, el σd máximo entre pares es 0,00192 (N-HiTS
  frente a TFT): con 10 semillas la potencia por contraste sería 0,85, por debajo del 0,90 del
  estándar; con 11 llega a 0,91 y con 12 a 0,95 (`experiments/potencia_e3.json`). Si el piloto del
  modelo original (al menos 5 semillas en el desarrollo) da un σd mayor que 0,0019 frente a algún
  rival, se vuelve a correr `experiments/potencia_e3.py --sd` y se aumentan las semillas antes de la
  etiqueta.
- Determinismo estricto y procedimiento en dos etapas, como en el E2 (`src/definitiva.py`).

## 6. Presupuesto de ajuste (sección 8.2)

El mismo para todos: tres tasas {1e-4, 3e-4, 1e-3} con la semilla 0 en k4, lote 512, hasta 100 épocas
y paciencia 10. El presupuesto se mide en número de configuraciones y también se informa el tiempo de
GPU de cada modelo. Se informan parámetros, FLOPs, memoria pico y latencia de todos.

## 7. Familia, pruebas y decisión (secciones 8.4 a 8.7)

- **Familia confirmatoria principal (m = 15):** el modelo original frente a cada rival de
  `experiments/estandar.md`, con Holm sobre los 15. «Supera a todos» exige rechazar los 15
  contrastes (unión-intersección). No se excluye ningún rival.
- **Tres pruebas por contraste:**
  1. t pareada sobre las 12 semillas (MAE de 2025 por semilla), con verificación de normalidad
     (Shapiro–Wilk). Es la prueba sobre la que se calculó la potencia.
  2. Diebold–Mariano con corrección HLN sobre la pérdida absoluta de las 52 semanas (promedio de
     semillas), con varianza de largo plazo.
  3. Bootstrap por bloques de 4 semanas de la diferencia semanal (B = 10.000), con su p y su IC 95 %.
- **Procedimientos globales:** Model Confidence Set (α = 0,10) sobre las pérdidas semanales, y
  Friedman con Iman–Davenport y Nemenyi con las semillas como bloques.
- **Decisión de cada contraste:** el máximo de los tres p de Holm debe ser menor que 0,05, y la cota
  superior del IC 95 % de la diferencia (original − rival) debe quedar por debajo de −δ.
- **R3, las seis condiciones de la sección 8.4:**
  1. MAE medio menor que el de cada rival.
  2. Los 15 contrastes rechazan con las tres pruebas.
  3. |δ de Cliff| ≥ 0,147 o |dz| ≥ 0,2.
  4. Mejor MAE medio en al menos dos de los tres conjuntos adicionales, sin ser significativamente
     peor que ningún rival en ninguno.
  5. Adecuación según la sección 7.2 del E2: condiciones (i) a (iv), con Mincer–Zarnowitz y la tabla B.
  6. La conclusión se mantiene con Wilcoxon por semillas, con la t con corrección de Nadeau–Bengio y
     con el subconjunto de semillas 1 a 8.
- **Familias secundarias, cada una con su propio Holm:** conjuntos adicionales (Friedman con
  Iman–Davenport y Wilcoxon-Holm por pares), ablación (compuerta fija, sin supervisión de régimen) y
  semanas de interrupción.
- **Potencia:** se informa la potencia por contraste y la conjunta. Con 12 semillas y el σd del peor
  caso de las diez semillas (0,00192), la potencia por contraste es de 0,95 y la conjunta de 0,48
  (15 contrastes independientes); la diferencia mínima detectable con potencia 0,90 es 0,0029. Un resultado no concluyente no se lee como equivalencia; para eso están TOST y
  ROPE con ±δ.
- **Lenguaje:** no se habla de «tendencia a la significancia».

## 8. Robustez y calidad probabilística

La robustez con tres severidades, la incertidumbre conforme por bloques y la calibración, con el mismo
procedimiento del E2 aplicado a toda la familia.

## 9. Congelamiento

- Prueba: hash `988ab7ac22cbfc0e4bac4b0c600959a860e640029748014048abe9a852c78753`.
- Panel: el mismo SHA-256 del E1.
- Código del modelo original: el commit de la etiqueta `protocolo-e3`. Después de esa etiqueta no
  cambian la arquitectura, la pérdida ni el espacio de hiperparámetros; un cambio reinicia el
  protocolo con una etiqueta nueva.
