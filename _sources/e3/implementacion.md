# Modelo original: implementación

Capítulo 6 del cuadro 7.

## 6.1 Código

| Archivo | Qué hace |
| --- | --- |
| `src/modelo_original.py` | `CNNMezcla`: cuerpo de la CNN del E1, compuerta, dos expertos y pérdida auxiliar; `cota_lipschitz` y `cociente_empirico` |
| `src/entrenamiento.py` | El bucle común. Suma `perdida_auxiliar` solo si el modelo la define, así que para los modelos del E1 y el E2 no cambia |
| `src/original_e3.py` | Corridas: `desarrollo` (k1 a k3), `tasa` (k4) y `definitiva` (2025, exige la etiqueta `protocolo-e3`) |

El mecanismo central está escrito sobre `torch.nn` con capas básicas (`Conv1d`, `Linear`, `Embedding`). No se importa ninguna implementación de mezcla de expertos.

## 6.2 Pruebas unitarias

`src/pruebas/test_modelo_original.py` tiene 12 funciones de prueba (algunas parametrizadas):

- `test_salida_una_cifra_por_observacion_y_mismo_cuerpo_que_la_cnn`
- `test_la_salida_queda_entre_los_dos_expertos`
- `test_perdida_auxiliar_es_la_bce_con_el_umbral_de_interrupcion`
- `test_las_ablaciones_no_suman_perdida`
- `test_en_evaluacion_no_queda_logit_guardado`
- `test_compuerta_fija_es_el_promedio_de_los_expertos`
- `test_el_bucle_suma_la_perdida_auxiliar_solo_al_entrenar`
- `test_la_cota_de_lipschitz_acota_el_cociente_empirico`
- `test_lema_de_la_convolucion_causal`: ‖conv(x) − conv(x')‖ ≤ √k‖W‖₂‖x − x'‖ para la convolución con relleno causal.
- `test_cociente_exige_las_mismas_categorias`
- `test_variantes_y_eleccion_de_tasa`
- `test_el_desarrollo_no_admite_k4`

## 6.3 Adaptación frente a la fuente

| Aspecto | Jacobs et al. (1991) | Modelo original |
| --- | --- | --- |
| Expertos | K intercambiables | Dos, con significado fijo (interrupción y resto) |
| Compuerta | Sin supervisión directa | Supervisada con λ·BCE frente al régimen observado |
| Entrada de expertos y compuerta | La entrada cruda | La representación z de la CNN del E1 |
| Tarea y pérdida | Clasificación y regresión genéricas | Pronóstico un paso adelante, MAE |
| Propiedad | — | Cota de Lipschitz demostrada para esta composición |

## 6.4 Ablaciones e interpretabilidad

Preregistradas en la sección 7 del protocolo, con su propio Holm:

- `fija`: compuerta fija en 0,5. Si la ventaja se mantiene, no la explica el régimen (predicción 3).
- `sin_regimen`: λ = 0, la mezcla de la fuente sin supervisión.

La interpretabilidad viene de la propia compuerta: g es una probabilidad de interrupción por observación. Se informa su AUC frente al régimen observado (predicción 4) y su calibración.
