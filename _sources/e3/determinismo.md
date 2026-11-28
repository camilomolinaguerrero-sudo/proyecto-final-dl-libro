# Determinismo y semillas

Esta página responde a lo que pide el bloque A sobre determinismo y semillas: que estén fijadas, que
la configuración esté documentada y que se declare lo que no es determinista y cuánto varía.

## Lo que fija el código

`src/entrenamiento.py`, `fijar_semillas(semilla, estricto=True)`, se llama antes de crear cada
modelo:

| Qué | Cómo |
| --- | --- |
| Python | `random.seed(semilla)` y `PYTHONHASHSEED` |
| NumPy | `np.random.seed(semilla)` |
| PyTorch, CPU y GPU | `torch.manual_seed(semilla)` |
| Algoritmos | `torch.use_deterministic_algorithms(True, warn_only=True)`: si una operación no tiene versión determinista, PyTorch avisa y sigue |
| cuDNN | `torch.backends.cudnn.benchmark = False` |
| cuBLAS | `CUBLAS_WORKSPACE_CONFIG=":4096:8"`, que exigen las RNN deterministas en GPU |
| Orden de los lotes | un `torch.Generator` propio sembrado con la semilla; su estado se guarda en el checkpoint |

El checkpoint de cada época guarda pesos, optimizador, planificador y el estado de todos los
generadores. Si Colab corta la sesión, la corrida sigue donde iba, y en el mismo hardware el resultado
es igual al de una corrida sin cortes. `src/prueba_humo.py` lo verifica: entrena una vez sin cortes y
otra con un corte simulado, y compara los pesos finales.

## Lo que no es determinista, declarado

- **`warn_only=True`:** una operación sin versión determinista no detiene la corrida. Por eso se
  reportan siempre varias semillas y no una corrida.
- **Desarrollo del modelo original en MPS:** las 45 corridas de k1 a k3 se hicieron en la GPU de un
  Apple M5 (MPS), cuyas operaciones no son reproducibles bit a bit. `elegir_dispositivo` no usa MPS por
  esa razón, y aquí se usó a propósito y se declara. Esas corridas solo deciden λ y las variantes; la
  corrida definitiva va en CUDA.
- **Réplica del TFT en Electricity:** `src/replica_electricity.py` entrena con PyTorch Lightning y
  `deterministic=False`. Esa réplica no entra en ninguna comparación confirmatoria.
- **Cambio de GPU entre sesiones:** Colab puede asignar otra GPU al retomar. Cada registro guarda el
  hardware de cada sesión (`sesiones` en `resultados/corridas/*.json`).

## Cuánto varía entre semillas

La desviación estándar del MAE en 2025 entre las 8 semillas de la definitiva del E2 va de 0,00028
(DLinear) a 0,00147 (N-HiTS), con un coeficiente de variación de 0,10 % a 0,53 %
(`estadistica/resultados_e2_complementos/sensibilidad_semilla.csv`). En los 15 modelos, la prueba de
Friedman por semilla, con Holm, rechaza que la semilla no importe. Por eso el protocolo del E3 exige
12 semillas y la condición 6 pide que las semillas 1 a 8 lleguen a la misma conclusión.

## Reproducción en un entorno limpio

- **2026-11-23:** venv nuevo con Python 3.11.16, `pip install -r requirements.txt pytest` y las tres
  carpetas de pruebas, en macOS con CPU. Pasaron 215, se omitieron 13 (las que necesitan el panel) y
  no falló ninguna (`estadistica/resultados_e2_complementos/entorno_limpio.json`).
- **Tablas del E2:** recalculadas desde las 123 corridas, coinciden con las publicadas con diferencia
  máxima de 5,6e-17 (`reproduccion.json`, en la misma carpeta).
- **CI:** el flujo `.github/workflows/pruebas.yml` del PR #24 repite las pruebas en Ubuntu con Python
  3.13, construye el libro y corre las pruebas dentro de la imagen de Docker.
