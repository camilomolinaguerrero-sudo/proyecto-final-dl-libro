# Artefactos del modelo original

Cada carpeta `original_s<semilla>/` sale de `src/exportar.py` después de la corrida definitiva y trae:

| Archivo | Contenido |
| --- | --- |
| `pesos.pt` | `state_dict` del modelo que evaluó 2025 (segunda etapa, todo el entrenamiento); se carga con `weights_only=True` |
| `transformador.json` | medianas, medias, desvíos y vocabularios ajustados con las filas hasta FIN_TRAIN |
| `metadata.json` | constructor y argumentos, dimensiones, entradas y salida, configuración y entorno del entrenamiento, hashes SHA-256 de los dos archivos, del checkpoint y de las predicciones de referencia, hash de la prueba y resultado de la ida y vuelta |

Para pronosticar con un artefacto:

```python
from exportar import cargar
art = cargar("artifacts/original_s1")          # verifica los hashes
pred = art.predecir(panel, filas)              # y_pred, razon_pred = expm1(y_pred), interrupcion_pred
```

La ida y vuelta (cargar y volver a pronosticar 2025) debe dar las predicciones registradas por la
corrida con diferencia máxima de `exportar.TOLERANCIA` en y_reg; si no, la carpeta no se escribe.
Las pruebas con datos sintéticos están en `src/pruebas/test_exportar.py`.

Todavía no hay carpetas: la corrida definitiva del modelo original no se ha hecho.

## ONNX, latencia y FLOPs

`python src/exportar_onnx.py artifacts/original_s<semilla>` agrega `modelo.onnx` a la carpeta, con el
lote como eje dinámico y opset 18. Las entradas son `x`, `covariables` y `categoricas`; la salida es
`y_reg`. Antes de guardarlo compara ONNX Runtime con PyTorch, con tolerancia de 1e-4. Después escribe
en `metadata.json`, en la clave `onnx`:

- el hash del ONNX y la verificación;
- los FLOPs por observación, que en el modelo de tamaño real (128 canales, ventana de 52) son unos
  10,4 millones;
- la latencia en CPU con un hilo, de PyTorch y de ONNX Runtime, con lotes de 1 y 512.

Las pruebas están en `src/pruebas/test_exportar_onnx.py`.

## Los 15 rivales

`src/exportar.py` exporta cualquier modelo de la definitiva con la misma fábrica que la entrenó
(`fabricas_proyecto`), con la semilla 1 fijada de antemano. El 2026-11-28 se exportaron los 15 desde los
checkpoints de Drive. Todos pasan la ida y vuelta: la diferencia máxima con las predicciones
registradas es de 1,6e-5 en TFT y menor en el resto. ONNX Runtime coincide con PyTorch en todos.

El costo de inferencia de los 15, medido en las mismas condiciones (CPU Apple M5, un hilo de ONNX
Runtime), está en `resultados/costo_inferencia.csv`.

Solo N-BEATS no cabe en un archivo de GitHub: tiene 28,5 millones de parámetros y su ONNX ocupa 110 MB.
`src/cuantizar_onnx.py` lo cuantiza a INT8 y queda en 28 MB. El MAE en 2025 pasa de 0,27541 a
0,27539, y el TOST semanal con ±δ da equivalencia (p = 7,8e-77).

Falta acordar qué archivos van en el repositorio. La propuesta es esta:

- **en el repositorio:** el ONNX (el INT8 en N-BEATS), `transformador.json` y `metadata.json` de cada
  modelo;
- **fuera del repositorio:** los `pesos.pt` y los checkpoints completos se quedan en Drive, y su
  SHA-256 queda en `metadata.json`.
