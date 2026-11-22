# Tabla de trazabilidad hallazgo-decisión (Apéndice A.1)

Todas las cifras salen de `eda/resultados/general_resultados.json`, `series_resultados.json` y
`predictibilidad.json`, y de `auditoria/resultados/auditoria_fuga.json`. Solo entrenamiento, salvo la
comparación entre particiones.

| Hallazgo | Evidencia | Decisión de preprocesamiento | Impacto en arquitectura o validación | Riesgo y fuga |
| --- | --- | --- | --- | --- |
| Objetivo asimétrico (0,549) con 17,6 % de ceros | `general_resultados.json: objetivo` | `y_reg = log1p(tasa_t1/base8)`, ya fijado | MAE como métrica primaria; sin MAPE ni sMAPE por los ceros | `base8` usa solo las 8 semanas previas a t |
| Pérdida cuadrática dominada por pocas filas (el 1 % superior aporta el 13,0 % del MSE y el 4,6 % del MAE) | `series_resultados.json: atipicos` | Sin recorte de atípicos (2,1 % de las filas) | MAE primaria; RMSE solo secundaria | Ninguno adicional |
| Serie nacional estacionaria (ADF p = 7,7e-6; KPSS p = 0,10) | `series_resultados.json: estacionariedad` | Sin diferenciación | Los modelos reciben el nivel | Ninguno |
| Dependencia corta (ACF nacional: 0,37 en el rezago 1; −0,20 en el 8) | `series_resultados.json: dependencia` | Ventana de 52 semanas (`eda/decisiones_para_modelos.md`) | Cubre el ciclo anual; el hueco de 8 semanas iguala la base | Embargo de 8 semanas entre entrenamiento y validación |
| Estacionalidad moderada (fuerza 0,43) y tendencia casi nula (0,04) | `series_resultados.json: descomposicion` | `sem_sin`, `sem_cos` y `festivos_t1` como covariables de calendario | Naive estacional como referencia obligatoria | Calendario se conoce de antemano: sin fuga |
| No linealidad y heterocedasticidad (ARCH-LM p = 1,2e-8; BDS p ≈ 1e-7) | `series_resultados.json: no_linealidad` | Ninguna | Se justifican modelos no lineales; DM con varianza de largo plazo | Autocorrelación de errores: bloques por semana en la inferencia |
| Predictibilidad intrínseca baja (entropía de permutación de orden 4: mediana 0,978 en corredores y 0,986 en la serie nacional, frente a 0,989 del ruido blanco) | `predictibilidad.json` | Ninguna | El techo de desempeño es cercano a las referencias: se espera γ pequeño y diferencias de orden δ | Sobreinterpretar diferencias menores que δ = 0,0032 |
| `frac_caida_mpio_t` falta en el 15,7 % de las filas y la ausencia se asocia con el nivel (AUC de la ausencia 0,754) | `general_resultados.json: faltantes` | Imputación por mediana de train e indicador de ausencia | Indicadores de ausencia como entrada | Medianas ajustadas solo en train (verificado en la auditoría) |
| Autocorrelación espacial positiva (I de Moran 0,185; z = 11,2; p perm = 0,001) | `general_resultados.json: espacial` | Variables de vecindad con información hasta t | Embeddings por corredor y covariables de vecinos | Vecindad calculada solo con el pasado de cada fila |
| Separabilidad entre entrenamiento y prueba (AUC adversarial 0,99; con una sola covariable, `frac_caida_nacional_t`, 0,75; sin ella 0,71) | `auditoria_fuga.json: validacion_adversarial` | Se mantiene la covariable y se declara el desvío | Corrida de sensibilidad sin `frac_caida_nacional_t` (pendiente, a decidir con Lina) | Esa covariable es constante dentro de cada semana y puede actuar como huella de la semana |
| Cero duplicados exactos y cero casi duplicados | `general_resultados.json: duplicados`; `auditoria_fuga.json` | Ninguna | Ninguno | Aprobado |
| Corredores nuevos en prueba (194 de 3009; 26 % sin 52 semanas de historia) | `auditoria_fuga.json: interseccion_corredores` | Embedding con categoría 0 para corredores no vistos | Evaluar por segmento corredor con y sin historia | La intersección no es vacía por diseño: la partición es cronológica |
