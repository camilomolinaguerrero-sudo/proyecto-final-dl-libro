# Dashboard

App multipágina en Dash (`use_pages=True`) con las tres secciones del bloque E: contexto, datos y
modelos.

| Archivo | Contenido |
| --- | --- |
| `app.py` (raíz) | Crea la app y expone `server` para gunicorn |
| `pages/contexto.py`, `pages/eda.py`, `pages/modelos.py` | Una página por sección |
| `dashboard/inference.py` | Carga los ONNX de `artifacts/`, verifica su hash y pronostica en CPU. Mantiene como máximo 2 sesiones a la vez |
| `dashboard/explain.py` | Explicación local por oclusión, igual para todos los modelos |
| `dashboard/data.py` | Lee y guarda en caché los resultados del repositorio. Si un archivo falta, devuelve None |
| `dashboard/plots.py` | Las figuras |
| `dashboard/preparar_datos.py` | Fuera de la app: arma `data/dashboard/` con el panel y las predicciones de Drive |
| `dashboard/pruebas/` | Pruebas: inferencia frente a PyTorch, hash alterado, oclusión y páginas |

La app no importa torch ni el código de `src/`, y sus dependencias son las de `requirements-app.txt`.

```bash
python dashboard/preparar_datos.py --drive "$DRIVE/proyecto_final_dl"   # una vez, con el panel
python app.py                                                           # http://127.0.0.1:8050
```

## Datos que usa

- **Predicción en vivo:** `data/dashboard/muestra_2025.*`. Son las entradas ya transformadas de 24 rutas
  de los 6 mercados con más filas, elegidas por número de semanas en 2025, no por ningún pronóstico.
  Ocupa menos de 1 MB.
- **Intervalos:** `data/dashboard/intervalos_2025.csv`. Para cada modelo y semana trae el cuantil
  conformal al 90 % de los residuos de las semanas anteriores (semilla 1).
- **Resto:** las cifras de `eda/resultados/`, `estadistica/resultados_e2/` (o la tabla de 10 o 12 semillas
  si está en la rama), `resultados/exploratorios_e2/` y `resultados/costo_inferencia.csv`.

## Pendiente de acordar

- **Artefactos:** qué binarios ONNX entran al repositorio (ver `artifacts/README.md`). Mientras no estén,
  la app marca cada modelo como «sin artefacto».
- **Muestra de 2025:** si `data/dashboard/` se puede publicar. Son entradas derivadas de los microdatos
  públicos del DANE, para 24 rutas, aunque el panel completo no se publica.
- **Estructura:** el enunciado pide `src/inference.py`, `training/`, etc. Aquí todo vive en `dashboard/`
  para no mover los archivos de `src/`. La reorganización final se hace de común acuerdo.
