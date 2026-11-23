# Reproducir el proyecto

El código está en el repositorio del proyecto. Cada carpeta tiene su README con los comandos
exactos; este anexo resume el orden.

1. **Datos.** El panel no se publica, porque las condiciones del DANE no autorizan redistribuir los
   microdatos. `python3 src/descargar_datos.py --dane` lo arma desde los 18 archivos del catálogo
   697 del DANE; el 2026-11-11 el panel armado así salió idéntico byte a byte al que usamos. Los
   autores lo copian de su carpeta privada con `--desde`, que verifica el SHA-256.
2. **EDA.** Los tres cuadernos de `eda/` se generan desde `eda/fuentes/*.py` con `eda/py2nb.py`
   (ver `eda/README.md`).
3. **Literatura.** `literatura/contar_modelos_conjunto.py` produce el conteo de familias, y los
   scripts de `literatura/scripts_camilo/` producen la extracción, la comparación de detectores, el
   número de parámetros y el diagrama de flujo.
4. **Modelos.** `src/correr.py` entrena con semillas fijas y deja un registro por corrida
   (ver `src/README.md`).
5. **Estadística.** `estadistica/ejecutar_comparacion.py` corre la comparación sobre la carpeta de
   predicciones (capítulo 4).
6. **Libro.** `python book/armar_libro.py` reúne las páginas y construye el HTML en
   `book/_build/html`.

## Entregable 2

1. **Auditoría de fuga.** `python auditoria/auditar_fuga.py` escribe `auditoria/resultados/`.
2. **Top 10.** `python literatura/top10_e2.py` escribe `literatura/top10_e2.csv` y `.md`.
3. **Búsqueda de arquitectura del MLP.** `src/busqueda_arquitectura.py` (en Colab) escribe
   `resultados/arquitectura_mlp_*.csv`.
4. **Corrida definitiva.** `src/definitiva.py` no corre sin la etiqueta `preregistro-e2`; los cuadernos
   `src/colab_definitiva_e2_a.ipynb` y `_b.ipynb` la reparten en dos sesiones de Colab.
5. **Análisis confirmatorio.** `python estadistica/ejecutar_e2.py <carpeta de predicciones de prueba>`
   escribe `estadistica/resultados_e2/`.
6. **Piloto y exploratorios.** `python estadistica/piloto_limitacion.py`, `src/exploratorios_e2.py`,
   `src/robustez_interpretabilidad.py`, `src/sensibilidad_sin_nacional.py` y `src/replica_electricity.py`;
   cada script dice en su encabezado qué datos lee y dónde escribe. La réplica necesita además
   `pip install -r requirements-replica.txt` (agrega `pytorch-forecasting`).
7. **Complementos.** `python estadistica/complementos_e2.py --predicciones <carpeta> --corridas <carpetas de corridas>`
   escribe `estadistica/resultados_e2_complementos/` (tabla B, efectos mixtos, sección 14.6 y registro por corrida).
8. **Libro.** `book/capitulo_e2.py` genera las páginas del Entregable 2 desde esos archivos.
