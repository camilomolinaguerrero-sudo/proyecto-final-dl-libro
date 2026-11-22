# Preregistro del Entregable 2

Redactado por Camilo el 2026-11-17 y aprobado por Lina el 2026-11-17. Cerrado el 2026-11-18 con la
etiqueta de git `preregistro-e2`, antes de la primera corrida definitiva del Entregable 2;
`src/definitiva.py` no corre sin esa etiqueta. Sigue la plantilla de la
sección 9.1 del enunciado. Lo que no cambia respecto del Entregable 1 se hereda de `DECISIONES.md` y
de `preregistro/borrador_preregistro.md`, y aquí solo se cita.

## 1. Tarea y unidad de independencia (heredado)

Series de tiempo, modelo global con covariables, horizonte de una semana. Objetivo
`y_reg = log1p(tasa_t1/base8)`. Unidad de independencia: el corredor; en la inferencia, la semana como
bloque. Partición: entrenamiento hasta la semana objetivo 2024-10-21, hueco de 8 semanas, prueba
2025 (hash `988ab7ac…2c78753`), validación de origen móvil.

## 2. Métricas y márgenes

- **Primaria:** MAE. **Secundarias:** RMSE, MASE y AUC de interrupción. Las secundarias no sostienen
  conclusiones confirmatorias.
- **δ = 0,0032** (heredado).
- **δg = 0,032.** Brecha máxima aceptable entre el MAE de validación y el de entrenamiento, igual al
  10 % del MAE de la mediana del entrenamiento en validación (0,3188). Se aplica la prueba de la
  tabla B (Wilcoxon unilateral sobre las semillas).

## 3. Modelos

### 3.1 Top 10

Elegido con `literatura/top10_e2.py`, que puntúa 14 candidatos con cuatro criterios de pesos fijos
(representatividad en los 491 estudios 0,35; recencia 0,25; arquitectura definida en el artículo 0,20;
implementación disponible 0,20). Tabla completa en `literatura/top10_e2.md`. Cubre cinco familias.

| # | Modelo | Familia | Arquitectura | Tasa fijada |
| --- | --- | --- | --- | --- |
| 1 | PatchTST | atención | del artículo | 3e-4 |
| 2 | TFT | atención | del artículo | 3e-4 |
| 3 | iTransformer | atención | del artículo, con las elecciones de abajo | 1e-4 |
| 4 | DLinear | lineal | del artículo | 1e-3 |
| 5 | N-BEATS | descomposición | del artículo | 1e-3 |
| 6 | N-HiTS | descomposición | del artículo, con las elecciones de abajo | 3e-4 |
| 7 | Transformer (codificador) | atención | referencia de la sección 14.3 | 1e-3 |
| 8 | TCN | convolucional | referencia de la sección 14.3 | 1e-4 |
| 9 | GRU | recurrente | referencia de la sección 14.3 | 1e-3 |
| 10 | RNN simple (Elman) | recurrente | referencia de la sección 14.3 | 1e-4 |

Los diez están implementados y probados en `src/` (rama `lina/entregable2`, commits ee59915 y
378d690). Las tasas salen de `resultados/publicados_tasa.csv`.

**Elecciones que los artículos no fijan** (tomadas antes de ver la prueba):
- N-HiTS: pooling [8, 4, 1], de las opciones de la tabla A3 del artículo; 2 capas de 512 unidades.
- iTransformer: D = 256 y L = 2, los valores más pequeños del rango del artículo; 8 cabezas; capa
  densa de 256 con ReLU; dropout 0,1; una capa lineal para el embebido y otra para la proyección; sin
  normalización por instancia.

**Regla de caída:** un modelo del Top 10 cuyas 8 corridas definitivas no terminen antes del domingo
2026-11-22 sale del análisis confirmatorio y se declara. No lo reemplaza el siguiente de la lista
(DeepAR).

### 3.2 Referencias y modelos base

- **Modelos base del Entregable 1:** MLP, CNN y LSTM con su configuración del Entregable 1. Se usan
  sus 8 predicciones por modelo ya evaluadas en 2025 (commit e56750d); no se vuelven a correr ni a
  evaluar.
- **MLP del Entregable 2:** la ganadora de la búsqueda de la sección 14,
  `w032-064-032_b000` (31.758 parámetros), con la tasa 1e-3 fijada en la búsqueda.
- **Referencias:** mediana del entrenamiento, persistencia, ingenuo estacional y la referencia lineal
  (ya evaluadas en el Entregable 1).
- **Baseline a superar:** la CNN del Entregable 1 (MAE 0,2739). Umbral de relevancia: 0,2707.

## 4. Arquitectura (sección 14)

- **MLP:** búsqueda completa en dos fases, ya hecha en k4 (resultados en
  `resultados/arquitectura_mlp_*.csv`). Fase 1: 1.000 arquitecturas, semilla 0, tope de 25 épocas.
  Fase 2: 20 finalistas más la referencia, semillas 1 a 5. Ganadora por parsimonia entre las que
  quedan a menos de δ de la mejor.
- **Desvío declarado.** La búsqueda no se repite para los modelos sin diseño definido (Transformer,
  TCN, GRU y RNN simple) ni para la LSTM y la CNN del Entregable 1. La fase 1 de la LSTM costaría del
  orden de cien horas de T4, según los tiempos por época del Entregable 1, y no cabe antes del 22 de
  noviembre. Esos cuatro modelos usan la arquitectura de referencia de la sección 14.3: tres bloques
  de ancho 64 con dropout 0,2 solo después del segundo, `(64, 64, 64)` y `(0, 1, 0)`. El efecto
  posible es subestimar a esos modelos. La búsqueda del MLP sugiere que el efecto es pequeño, porque
  sus 21 finalistas quedaron dentro de δ.
- Adaptaciones de los modelos publicados, que Lina ya documentó: horizonte 1 y cabeza común; ventana
  de 52 semanas; en N-BEATS, θ y la base lineal en una sola capa; tasa de aprendizaje fijada por
  nosotros y no la del artículo.

## 5. Ajuste y entrenamiento (igual para todos)

- **Tasa:** {1e-4, 3e-4, 1e-3} con la semilla 0 en k4; queda la de menor MAE de parada. Se aplicó a
  los diez del Top 10; el MLP del Entregable 2 usa la tasa de su búsqueda (1e-3).
- **Entrenamiento:** lote 512, hasta 100 épocas, paciencia 10, pérdida MAE, igual que la búsqueda del
  Entregable 1. La corrida definitiva tiene dos etapas, como en el Entregable 1: parada temprana para
  fijar la época y reentrenamiento con todo el entrenamiento. **Desvío:** la cabeza común de los
  modelos del Top 10 va sin abandono.
- **Semillas:** 8 por modelo (1 a 8), las mismas del Entregable 1.

**Potencia.** Con la mayor desviación de las diferencias pareadas entre redes del Entregable 1,
σ̂d = 0,00075 (`resultados/comparacion_definitiva/comparaciones_pareadas.csv`), la t pareada necesita
3 semillas con α = 0,05 y 5 con α = 0,005 (Bonferroni con 10 contrastes) para detectar δ con
potencia 0,8. Con 8 semillas la potencia es mayor que 0,99 y la diferencia mínima detectable es
0,0014 con α = 0,005. Si los modelos nuevos varían más entre semillas, se recalcula y se informa la
potencia alcanzada.

## 6. Exclusión de corridas

Una corrida que diverge (pérdida NaN o infinita) se registra, se excluye y no se reemplaza. Si un
modelo pierde más de dos de sus 8 corridas, sale del análisis confirmatorio y se informa.

## 7. Análisis confirmatorio

**Familia F1 (10 contrastes):** cada modelo del Top 10 frente a la CNN del Entregable 1. H1: el modelo
reduce el MAE en más de δ. Holm sobre los 10.

**Familia F2 (10 contrastes):** cada modelo del Top 10 frente a la mediana del entrenamiento (condición
(i) de adecuación). Holm sobre los 10.

Cada contraste usa tres pruebas de la tabla C:
1. **Diebold–Mariano con corrección HLN** sobre la pérdida absoluta media de cada una de las 52
   semanas de 2025 (promedio de las 8 semillas), con varianza de largo plazo. Es la prueba de
   decisión.
2. **Wilcoxon de rangos con signo** sobre las mismas 52 diferencias semanales (no paramétrica). Con 8
   semillas el p exacto mínimo de un Wilcoxon por semillas es 0,0078, mayor que el primer umbral de
   Holm (0,005), así que el Wilcoxon por semillas solo se informa como sensibilidad.
3. **Procedimiento global:** Friedman con la corrección de Iman–Davenport y post-hoc de Nemenyi, con
   las semillas como bloques y todos los modelos y referencias; diagrama de diferencia crítica. Además,
   Model Confidence Set con α = 0,10 sobre las pérdidas semanales.

Con cada contraste se informa la diferencia, su IC 95 % por bootstrap de bloques de 4 semanas
(B = 10.000), el p sin ajustar, el p de Holm, el tamaño del efecto (dz y δ de Cliff) y la decisión
frente a δ. **Equivalencia:** TOST con ±δ y ROPE bayesiana.

**Regla de decisión:** un modelo supera al baseline si el p de Holm del Diebold–Mariano es menor que
0,05 y la cota superior del IC de la diferencia queda por debajo de −δ. Si el p es mayor o igual a
0,05 y el TOST da equivalencia, se declara equivalente. Si no ocurre ninguna de las dos, no
concluyente.

**Adecuación (sección 7.2):** las condiciones (i) a (iv) con F2, la brecha de la tabla B con δg,
Mincer–Zarnowitz y Ljung–Box/ARCH-LM/BDS sobre los residuos semanales, y la diferencia entre
validación y prueba con IC por bootstrap.

## 8. Paper guía

TFT (Lim et al., 2021, *International Journal of Forecasting*; el DOI se verifica antes del informe).
Se elige por ser el de menor MAE de parada en k4 entre los publicados y porque su selección de
variables da interpretabilidad para la sección 4.6.

- **Replicación:** un resultado del artículo en Electricity con su configuración publicada.
  Criterio de éxito, fijado ahora: el valor publicado cae dentro del IC 95 % de 3 semillas, o la
  diferencia relativa es menor del 10 %.
- **Limitación a cuantificar:** el error en las semanas de interrupción (`y_reg < ln 1,5`) frente al
  resto, con IC 95 % por bootstrap por corredor, en la validación de k4. El piloto se hace con TFT,
  PatchTST y N-BEATS para ver si la limitación es del TFT o compartida.

## 9. Análisis exploratorios (no deciden nada)

- Corrida de sensibilidad sin `frac_caida_nacional_t` (auditoría de fuga: AUC adversarial 0,99).
- Desempeño por segmento: corredores con y sin 52 semanas de historia, por mercado y por semestre.
- Robustez con tres severidades, interpretabilidad en los tres mejores, ablación del mejor,
  incertidumbre conforme por bloques, techo de desempeño (γ) y frontera de Pareto.

Número de modelos y configuraciones examinados hasta hoy: 1.000 + 105 + 3 del MLP, y 30 corridas de
tasa del Top 10. Se actualiza en el informe.

## 10. Congelamiento

Prueba: hash `988ab7ac22cbfc0e4bac4b0c600959a860e640029748014048abe9a852c78753`, 103.466 filas.
Panel: SHA-256 `30ead684…` (el mismo del Entregable 1). Código: el commit de la etiqueta
`preregistro-e2`.
