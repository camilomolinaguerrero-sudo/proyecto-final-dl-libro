# Estadística (Camilo)

Secciones 5.1 a 5.4 y 7 del enunciado, para un problema de series de tiempo con MAE como métrica
primaria.

| Archivo | Contenido |
| --- | --- |
| `comparacion.py` | Funciones: métricas, IC (t y bootstrap de bloques de semanas), Diebold–Mariano con la corrección HLN, Ljung–Box, BDS, Friedman con Nemenyi, ANOVA de medidas repetidas, Holm, tamaños de efecto, TOST, ROPE, Nadeau–Bengio, potencia y MDE, tabla comparativa y definición del baseline |
| `ejecutar_comparacion.py` | Corre todo el análisis sobre una carpeta de predicciones y escribe `resultados/` |
| `pruebas/test_comparacion.py` | 14 pruebas contra referencias (scipy, statsmodels y tabla de Demšar) |

## Uso

```bash
python estadistica/ejecutar_comparacion.py RUTA/resultados/predicciones --delta <δ> \
    --referencias persistencia,ingenuo_estacional --costo resultados/costo.csv
python -m pytest estadistica/pruebas -q
```

Las predicciones siguen el formato de DECISIONES.md: `<modelo>_s<semilla>.parquet` con `fila`,
`semana_t1`, `y_real` y `y_pred`. Todas las corridas deben predecir las mismas filas, y el script lo
verifica antes de calcular.

**Escala del MASE.** Por defecto es el MAE de la persistencia en todo el entrenamiento: 0,3463.
Para la prueba final coincide con la de `src/correr.py`; el 2026-11-08 ambas dieron 0,346341 sobre
las mismas 622.244 filas. En validación, `src/` usa una escala por pliegue, calculada con las filas
que entrenan cada bloque. Para reproducir sus cifras, pásala con `--escala-mase <valor>`; está en
el registro de cada corrida, en `resultados/corridas/`.

## Diseño de la inferencia

**Entre semillas.** Cada semilla es un par para todos los modelos. Con esos pares se calculan:

- la media ± DE y el IC t;
- Shapiro–Wilk, t pareada y Wilcoxon exacto, con Holm;
- Friedman con Nemenyi, usando las semillas como bloques;
- TOST y ROPE con δ, y la MDE con la potencia del diseño.

Con 5 semillas, el Wilcoxon bilateral no puede bajar de p = 0,0625 (sección 7). Se recomiendan 6 o
más.

**Dentro de la prueba.** El análisis trabaja con la pérdida absoluta media por semana, coherente con
el MAE. Agregar por semana absorbe la dependencia entre los corredores de una misma semana, que es el
bloque según DECISIONES.md. Sobre esas pérdidas se calculan:

- Diebold–Mariano con la corrección HLN, con h = 1 y T − 1 grados de libertad;
- IC por bootstrap de bloques circulares de 4 semanas.

Friedman con las semanas como bloques se reporta como complemento.

**Residuos.** Ljung–Box en los rezagos 1, 4, 8 y 13, y BDS en las dimensiones 2 y 3. Se aplican al
residuo semanal medio de cada modelo, y Ljung–Box también por corredor, con Holm. A las redes se les
asignan 0 grados de libertad del modelo, porque no ajustan coeficientes ARMA sobre los residuos.

## Prueba de humo (2026-11-07)

Todavía no están las referencias ingenuas de Lina. El flujo completo se probó con predicciones
provisionales que no se guardan en el repositorio:

- referencias: persistencia, ingenuo estacional y mediana;
- tres ridge con 6 semillas cada una, en lugar de las redes;
- evaluación en las últimas 52 semanas del entrenamiento, sin tocar 2025.

Corrió sin errores. Falta repetirlo con los archivos de Lina.

## Ensayo con las referencias de Lina (2026-11-11)

Se repitió con los archivos reales de las referencias. `src/referencias.py` (rama `lina/pipeline`,
commit f6e260c) se corrió en local y escribió `persistencia_s0`, `ingenuo_estacional_s0` y
`mediana_entrenamiento_s0`. Cubren 374.740 filas en 208 semanas de validación (k1 a k4), y sus MAE
coinciden con `resultados/referencias_validacion.csv`.

Las tres redes se reemplazaron por predicciones **sintéticas**: mezclas de la mediana y la
persistencia con ruido, 6 semillas cada una. Sirven solo para probar el código, así que sus cifras no
se informan ni se guardan. El comando fue:

```bash
python estadistica/ejecutar_comparacion.py RUTA/resultados/predicciones --delta 0.0032 \
    --referencias persistencia,ingenuo_estacional,mediana_entrenamiento
```

Corrió sin errores en 5 segundos (Apple M5) y escribió los ocho archivos de salida. El formato de
`src/salidas.py` es el que lee este script, así que con el piloto solo hay que apuntarlo a la
carpeta de predicciones de Drive. Hay que recordar dos cosas:

- La mediana del entrenamiento tiene el menor MAE de las tres referencias, así que debe ir en
  `--referencias`.
- La referencia lineal de contexto (`literatura/modelos_seleccionados.md`, sección 4) también va en
  `--referencias`, con el nombre que le dé `src/`. No se cuenta como modelo base.
- En validación, el MASE comparable con `src/` necesita `--escala-mase` con la escala de cada
  pliegue.

## Comparación sobre el piloto (2026-11-13)

Primera corrida con predicciones reales de las redes: las 20 del piloto de Lina (CNN, MLP, LSTM y la
referencia lineal, semillas 1 a 5, bloque externo de k4: 98.874 filas en 52 semanas). Las referencias
ingenuas se recortaron a las mismas filas. Es exploratoria: sirve para revisar el diseño antes de las
ejecuciones definitivas, no es el análisis confirmatorio. Las salidas están en `resultados_piloto/`.

```bash
python estadistica/ejecutar_comparacion.py RUTA/predicciones --delta 0.0032 --escala-mase 0.345243 \
    --referencias persistencia,ingenuo_estacional,mediana_entrenamiento,lineal
```

La escala del MASE es la del pliegue k4 en `src/` (MAE/MASE de `piloto_metricas_por_corrida.csv`).
Las métricas coinciden con las que registró Lina.

Lo que dejó el piloto:

- **Las tres redes son equivalentes dentro de δ.** El TOST por semillas da p = 0,026 (CNN-LSTM),
  0,0001 (CNN-MLP) y 0,007 (LSTM-MLP), y la probabilidad dentro del ROPE va de 0,97 a 0,9998
  (`comparaciones_pareadas.csv`).
- **Aun así, la LSTM es algo peor.** El Diebold-Mariano sobre la pérdida semanal la separa de la CNN y
  del MLP (p ajustado < 0,0001), con 0,0017 y 0,0015 de MAE, menos que δ. Entre la CNN y el MLP no hay
  diferencia (p = 0,35).
- **Por semillas, nada es significativo.** Con 5 semillas, el menor p ajustado posible del Wilcoxon es
  0,1875, y es el que sale en los dos pares con la LSTM. Por eso se recomendaron 8 semillas para las
  ejecuciones definitivas.
- **Las tres redes superan a todas las referencias** (`frente_a_referencias.csv`): a la lineal por
  1,5 % a 2,1 % de MAE, a la mediana del entrenamiento por 8,7 % a 9,2 % y a la persistencia por
  18,0 % a 18,5 %, todas con p ajustado < 0,0001.
- **Baseline provisional: CNN**, MAE 0,2818 con IC 95 % de 0,2816 a 0,2820 (`baseline.json`).
- **Residuos.** Ljung-Box no rechaza en ningún rezago para las redes ni la lineal (`residuos.json`).
  El residuo semanal medio es positivo, de 0,050 a 0,057. Con pérdida MAE, los modelos aprenden la
  mediana condicional y no la media. En la CNN, semilla 1, el residuo por fila tiene mediana 0,005 y
  media 0,061: el centro está bien ubicado y la cola derecha sube la media. Es esperable y no indica
  una fuga ni un error de escala.
