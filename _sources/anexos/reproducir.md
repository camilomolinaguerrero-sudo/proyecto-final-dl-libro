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
