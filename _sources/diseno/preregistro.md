# Preregistro del Entregable 1

**Estado:** cerrado, antes de la corrida definitiva y sin haber evaluado la prueba. Lo cerró Lina después de fusionar los PR #1 y #2 en `main`, con el acuerdo de Camilo de cerrarlo en ese momento (correo del 2026-11-13). Contiene los resultados del piloto (sección 7), que solo usa el bloque externo de k4, y ninguno de la prueba. Cualquier cambio posterior se declara aparte, con su fecha y su razón.

**Fecha de cierre:** 2026-11-13
**Commit del código:** `7d392b8` (`main`, fusión de los PR #1 y #2). El commit que cierra este archivo solo cambia el preregistro y la rama que clona `src/colab_definitiva.ipynb`.

## 1. Problema

- **Tipo:** series de tiempo, modelo global con covariables, horizonte de 1 semana.
- **Objetivo:** `y_reg = log1p(tasa_t1 / base8)`. Una interrupción es `y_reg < ln(1,5)`.
- **Unidad de independencia:** el corredor (`mercado` + `cod_mpio`); en la inferencia, la semana como bloque.

## 2. Datos y partición

- **Panel:** `panel_corredores.parquet`, SHA-256 `30ead68472fc5718bf4c2a308db8e854a3f1329923c6f55adb6194da6b54b93d`.
- **Origen:** microdatos públicos de SIPSA-A 2018-2025, del catálogo de microdatos del DANE. Fuente: Departamento Administrativo Nacional de Estadística: www.dane.gov.co.
- **Entrenamiento:** semanas objetivo hasta 2024-10-21.
- **Validación:** origen móvil, cuatro bloques de 52 semanas con hueco de 8.
- **Parada temprana:** último bloque interno de cada entrenamiento, con hueco de 8.
- **Prueba:** de 2025-01-06 a 2025-12-29, congelada en `preregistro/hash_prueba.json`. Se evalúa una vez por corrida, con el modelo y los hiperparámetros ya fijados.

## 3. Entradas

- **Ventana:** 52 semanas, con los canales `nivel`, `observado` e `iniciado`.
- **Covariables:** lista cerrada de `src/config.py`, sin `antig_sem`. Imputación con mediana de entrenamiento e indicadores de ausencia.
- **Ajuste:** escalado y vocabularios solo con las filas que entrenan los pesos de cada corrida.

## 4. Métricas y margen

- **Primaria:** MAE. **Secundarias:** RMSE, MASE y AUC de interrupción.
- **Referencias ingenuas:** persistencia e ingenuo estacional, más la mediana del entrenamiento como contexto (`resultados/referencias_validacion.csv`).
- **Margen δ:** δ = 0,0032, el 1 % del MAE de la mejor referencia ingenua en validación, que es la mediana del entrenamiento (0,3188). Acordado por Lina y Camilo el 2026-11-08 y registrado en `DECISIONES.md`.
- **Lo que el diseño detecta frente a δ:** en validación, la diferencia semanal de MAE entre la persistencia y la mediana tiene una desviación de 0,0165 (`resultados/referencias_variabilidad_semanal.csv`). Con las 52 semanas de la prueba, una comparación pareada por semanas detectaría diferencias desde 0,0064, el doble de δ; con las 208 de validación, desde 0,0032. El cálculo supone semanas independientes y la autocorrelación de orden 1 es 0,33, así que la cifra real es mayor. Es un cálculo entre referencias ingenuas, que Camilo planteó el 2026-11-09: la variabilidad entre los tres modelos la mide el piloto. δ no se cambia por esto. Si el diseño definitivo no alcanza a detectarlo, se declara como limitación (sección 6).

## 5. Modelos base

Los tres modelos, su configuración y las referencias que la respaldan están en `literatura/modelos_seleccionados.md`, acordado el 2026-11-11. La implementación está en `src/modelos.py`, y su número de parámetros coincide con el de ese archivo.

- **Modelos:** LSTM, MLP y CNN 1D, con la misma entrada y una cabeza común con una capa oculta de 64 unidades. Los *embeddings* tienen el mismo tamaño en todos los pliegues (16, 16, 16 y 5), y la CNN usa relleno causal.
- **Pérdida, optimizador, tamaño de lote, épocas y paciencia:** MAE; Adam sin planificador, sin decaimiento de pesos y sin recorte de gradiente; lote de 512; máximo de 100 épocas con paciencia 10.
- **Espacio de búsqueda:** unidades o filtros en {32, 64, 128}; 2 o 3 capas en el MLP y la CNN y 1 o 2 en la LSTM; *dropout* en {0; 0,1; 0,3}; tasa de aprendizaje en {1e-4, 3e-4, 1e-3}; *kernel* de la CNN fijo en 3. Son 54 combinaciones por modelo.
- **Método y presupuesto:** búsqueda aleatoria de 20 configuraciones por modelo, con una semilla. Cada configuración se elige con el bloque interno de parada temprana del pliegue k4. La lista sorteada (semilla 0, con `src/busqueda.py --sortear`) quedó fija en `resultados/busqueda_configuraciones.csv` el 2026-11-12, antes de entrenar. Gana la de menor MAE en ese bloque; un empate lo gana la de menos parámetros.
- **Corrida definitiva en dos etapas:** cada semilla entrena primero como en la búsqueda y el piloto, con los pesos ajustados hasta 2022-12-19 y parada temprana en el último bloque interno (2023-02-20 a 2024-10-21), y guarda la mejor época E. Ese modelo no evalúa nada. Después se reentrena desde cero, con la misma semilla y la misma configuración, sobre todo el entrenamiento hasta 2024-10-21, durante exactamente E épocas y sin parada temprana; el escalado y los vocabularios se ajustan con esas mismas filas. Ese segundo modelo es el que evalúa 2025. La razón: sin el reentrenamiento, el 26 % más reciente del entrenamiento (88 semanas, 164.539 de 622.244 filas) nunca ajustaría los pesos, mientras que las referencias ingenuas sí usan esa información. E se fija en épocas, no en pasos: la segunda etapa tiene unas 1,4 veces más filas (622.244 frente a 444.064), así que hace más pasos de gradiente que la primera. Surgió de la revisión de código de Camilo, hecha con Claude, el 2026-11-13; está en `src/correr.py` (`correr_prueba`) y lo verifica `src/pruebas/test_piloto_y_prueba.py`.
- **Referencia lineal de contexto:** `lineal` en `src/modelos.py`, una sola capa densa sobre la ventana aplanada, las covariables y los *embeddings*, sin capas ocultas ni *dropout*. Solo se busca la tasa de aprendizaje, con sus 3 valores, en el mismo bloque. Entra en la comparación como referencia, igual que las ingenuas, no como cuarto modelo base.
- **Por qué 20:** el 2026-11-11 se midió el tiempo por época en una GPU Tesla T4, con datos sintéticos del tamaño de k4 (`resultados/tiempos_por_epoca.csv`, de `src/medir_tiempo.py`). Una época tarda de 2,3 a 2,8 s en el MLP, de 3,1 a 9,1 s en la CNN y de 2,8 a 22,6 s en la LSTM, según la configuración. Si las 20 configuraciones tardaran lo que la más grande y llegaran a las 100 épocas, contando el guardado del checkpoint, la búsqueda tomaría 1,6 h en el MLP, 5,1 h en la CNN y 12,6 h en la LSTM. Los checkpoints permiten repartirla en varias sesiones de Colab.

## 6. Piloto

- **Diseño:** los tres modelos base y la referencia lineal, cada uno con la configuración que eligió la búsqueda (`resultados/busqueda_elegidas.csv`), se entrenan con las mismas semillas y se evalúan en el bloque externo del pliegue k4, que la búsqueda no tocó. Se corre con `src/piloto.py correr`.
- **Semillas:** 1, 2, 3, 4 y 5, distintas de la semilla 0 de la búsqueda. Declaradas el 2026-11-13, antes de correr el piloto.
- **Reporte:** media, desviación estándar y coeficiente de variación del MAE; `σ_d` de cada par y `δ/σ_d`.
- **Número de ejecuciones:** `R ≈ (z_{1-α/2} + z_{1-β})² σ_d² / δ²`, con `α = 0,05` y potencia 0,80. Nunca menos de 8: el Wilcoxon exacto bilateral no baja de `p = 2/2^n`, y con Holm sobre los 3 pares el menor `p` ajustado es 0,094 con 6 semillas, 0,047 con 7 y 0,023 con 8. Camilo lo señaló el 2026-11-13, antes de la corrida definitiva; el mínimo anterior era 6.
- **Prueba por semillas:** el Wilcoxon de rangos con signo es la prueba principal, sin depender de un pretest de normalidad, porque con 5 diferencias el Shapiro-Wilk tiene muy poca potencia. La t pareada se reporta como sensibilidad. Acordado el 2026-11-13.
- **Si la diferencia mínima detectable supera δ:** se amplía el diseño o se declara como limitación.

## 7. Resultado del piloto

Corrido el 2026-11-13 en una Tesla T4 con el commit `9ae367b`, semillas 1 a 5, bloque externo de k4. Las configuraciones son las de `resultados/busqueda_elegidas.csv`: CNN c20, MLP c19, LSTM c20 y lineal c02. Las 20 corridas están en `resultados/piloto_metricas_por_corrida.csv`, y el resumen, con δ = 0,0032, en `resultados/piloto_por_modelo.csv` y `resultados/piloto_por_par.csv`.

- **MAE por modelo (media ± desviación estándar, CV):** CNN 0,28175 ± 0,00016 (0,06 %); MLP 0,28194 ± 0,00038 (0,13 %); LSTM 0,28346 ± 0,00108 (0,38 %). La referencia lineal tiene 0,28790 ± 0,00019.
- **Pares, diferencias emparejadas por semilla:** MLP − LSTM: −0,00151, `σ_d` = 0,00091, `δ/σ_d` = 3,53. MLP − CNN: 0,00019, `σ_d` = 0,00053, `δ/σ_d` = 6,05. LSTM − CNN: 0,00170, `σ_d` = 0,00122, `δ/σ_d` = 2,63.
- **Número de ejecuciones:** la fórmula da 0,63, 0,21 y 1,14 ejecuciones, todas por debajo del mínimo, así que se toma el mínimo de 8 (sección 6). Con las 5 semillas del piloto, la diferencia mínima detectable ya es menor que δ en los tres pares (0,00113, 0,00066 y 0,00153).
- **Normalidad:** Shapiro-Wilk da p = 0,027 en MLP − LSTM, 0,106 en MLP − CNN y 0,428 en LSTM − CNN. No decide la prueba: el Wilcoxon es la principal en los tres pares (sección 6).
- **Espacio de búsqueda:** en las tres redes ganó la configuración más grande del espacio, con 128 unidades o filtros y el máximo de capas. El espacio quedó corto por arriba. No se cambia, porque está fijado desde antes de entrenar; se declara como limitación y se amplía en el Entregable 2.
- **Frente a la lineal:** las tres redes tienen un MAE menor que la lineal en 0,0044 a 0,0061, más que δ, y entre ellas difieren menos que δ.

**Número de ejecuciones definitivas:** 8 por modelo, con las semillas 1 a 8.

## 8. Análisis confirmatorio

Según `estadistica/README.md`: comparaciones pareadas por semilla con corrección de Holm, Friedman con Nemenyi, Diebold-Mariano con la corrección HLN sobre la pérdida semanal, Ljung-Box y BDS sobre los residuos, intervalos por bootstrap de bloques, y TOST o ROPE con δ si se quiere afirmar equivalencia.

## 9. Lo que se declara desde ya

- 2025 ya se usó como prueba en el curso de Machine Learning, así que no es una prueba ciega para los autores.
- El EDA comparó las covariables de 2025 con el entrenamiento, como pide el enunciado.
- El EDA también comparó el objetivo de 2025 con el del entrenamiento (PSI de `y_reg`, media, ceros y prueba KS). Camilo lo declaró en `eda/decisiones_para_modelos.md`, sección «Uso de 2025 en el EDA»: ninguna decisión usa esas cifras, pero los autores las conocen.
- La ventana de 52 semanas se eligió midiendo en las últimas 52 semanas del entrenamiento, el mismo bloque k4 de validación.
- La definición de persistencia se fijó después de comparar variantes en validación; todas están en `resultados/referencias_validacion.csv`.
- El tamizaje de título y resumen se hizo con asistencia de IA en las cuatro bases, sin lectura humana registro por registro. En Web of Science e IEEE Xplore, a cargo de Lina, fueron reglas automáticas escritas con asistencia de IA y adoptadas en bloque, y esa herramienta falló los casos dudosos. En Scopus y arXiv, a cargo de Camilo, la misma herramienta, Claude (Anthropic), hizo el tamizaje, el fallo de dudosos y la extracción a pedido suyo, con criterios escritos antes y auditoría por muestra.
- La herramienta del lado de Lina también propuso cómo resolver los 34 desacuerdos dentro del período y extrajo los modelos de los 61 estudios que faltaban. Lina confirmó esos 34 fallos el 2026-11-09 a partir del título y la razón de cada uno. Una segunda revisión con la herramienta de Camilo marcó 7 casos, que los dos autores leyeron con título y resumen; con su acuerdo, L0853 pasó a incluido y L0869 sigue excluido, pero por E4 en lugar de E3 (2026-11-10). El acuerdo entre los dos tamizajes (κ = 0,91) compara dos procedimientos hechos con la misma herramienta de IA, no dos revisores humanos. El detalle está en `literatura/protocolo_tamizaje_lina.md` y en `literatura/protocolo_tamizaje_camilo.md`.
- El filtro 2018-2026 se acordó después de los dos tamizajes y de ver que no cambiaba las tres familias más nombradas en los resúmenes de los estudios de Camilo, cuya propuesta escrita inicial era no limitar los años. Se mantiene por dos razones. El período compara las familias en años en que todas se usaban: los 52 estudios que el filtro deja fuera nombran sobre todo MLP/ANN (40), y LSTM, CNN y GRU casi no aparecen (2, 2 y 1). Y no altera la selección: sin el filtro, las tres familias más usadas son las mismas y en el mismo orden, LSTM (274), MLP/ANN (182) y CNN (76) en 543 estudios (`literatura/conteo_modelos_conjunto.json`). Otros 5 estudios fuera del período los incluye solo uno de los dos tamizajes y no tienen fallo; con ellos el resultado tampoco cambia.
- El 2026-11-09, antes de correr cualquier modelo, se vio que las 52 semanas de la prueba no alcanzan para detectar δ en una comparación por semanas entre referencias ingenuas (sección 4). δ se mantuvo en el 1 %.
- El número de configuraciones de la búsqueda se fijó el 2026-11-11 con el tiempo por época medido sobre datos sintéticos, sin entrenar ningún modelo con datos reales.
- El margen δ se ancló primero a la persistencia y se cambió a la mediana del entrenamiento después de ver los MAE de las referencias en validación, antes de correr cualquiera de los tres modelos.
