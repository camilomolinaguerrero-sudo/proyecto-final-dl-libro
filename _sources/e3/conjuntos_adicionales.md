# Preregistro de los conjuntos adicionales (Entregable 3)

Redactado y aprobado por Lina el 2026-11-25; pendiente de aprobación por Camilo. Los tres conjuntos se eligieron
el 2026-11-25, antes de correr cualquier modelo en ellos. No se usan para decisiones de diseño: si
alguno llegara a usarse para ajustar un modelo, pasa a ser parte del desarrollo y se informa así.
Todo lo que no se dice aquí se hereda de `preregistro/preregistro_e2.md`.

Código: `src/conjuntos_adicionales.py` (descarga, verificación y selección). Cifras de esta página:
`resultados/conjuntos_adicionales/resumen_seleccion.json`.

## 1. Conjuntos, fuente, licencia, versión y hash

| Conjunto | Fuente | Licencia | Versión | SHA-256 |
|---|---|---|---|---|
| M4 semanal | Monash Time Series Forecasting Repository, Zenodo 10.5281/zenodo.4656522 | CC BY 4.0 | 3 | zip `5144147e…f45942`; tsf `1b6b9cd3…3b006` |
| Dominick | Monash, Zenodo 10.5281/zenodo.4654802 | CC BY 4.0 | 2 | zip `3025a5e6…f7bd96`; tsf `aba9ba78…a0c8dec` |
| ILINet por estado | CDC, servido por la API Epidata de Delphi (`fluview`) | «Publicly Accessible US Government» (declarada por Delphi) | publicaciones hasta la semana 2026w45 | csv `305df829…72ae2e` |

Los hashes completos están en `src/conjuntos_adicionales.py` y la descarga los verifica. ILINet se
revisa con el tiempo, así que la versión es el archivo descargado, guardado con su hash. Se usa el ILI
sin ponderar desde 2010w40, porque antes los valores por estado son imputados y no públicos.

Dominick describe la **ganancia** semanal por producto (SKU), no las ventas. Por eso se filtra a las
series sin valores negativos (sección 3), y la variable se declara como ganancia de productos que
nunca registraron pérdidas.

## 2. Por qué difieren del panel SIPSA (al menos dos ejes cada uno)

Cifras de las series incluidas; SIPSA solo con su entrenamiento.

| | SIPSA | M4 semanal | Dominick | ILINet |
|---|---|---|---|---|
| Dominio | abastecimiento de alimentos | varios (competencia M4) | ganancia minorista | epidemiología |
| Series | 4.305 | 286 | 2.000 | 50 |
| Semanas en total | 622.244 filas | 363.258 | 729.810 | 39.333 |
| Largo mediano por serie | 85 filas | 1.100 | 393 | 795 |
| Fracción de ceros | 0,176 (objetivo cero o faltante) | 0 | 0,368 | 0,023 |
| Calendario común | sí | no | no | sí |

Ejes: M4 difiere en dominio, tamaño y largo de las series (sin ceros, menos ruido intermitente);
Dominick en dominio y nivel de ceros (ruido intermitente); ILINet en dominio, tamaño (dos órdenes de
magnitud menor) y estructura temporal (estacionalidad epidémica fuerte). El desbalance (fracción de
semanas con razón menor que 0,5) se calculó con el adaptador de la sección 4, solo en entrenamiento
(`resultados/conjuntos_adicionales/resumen_paneles.json`): M4 0,0011, Dominick 0,176 e ILINet 0,118.
En M4 casi no hay interrupciones, así que su AUC de interrupción (secundaria) se informa pero no se
interpreta.

## 3. Selección de series (antes de ver resultados)

En este orden, en todos los conjuntos:
1. Al menos 300 semanas (52 de prueba, 8 de hueco, 52 de validación, 8 de hueco e historia).
2. Sin valores negativos, porque el objetivo es el log1p de una razón no negativa.
3. Solo Dominick: muestra simple de 2.000 de las 11.776 series elegibles, semilla 20261125.

Resultado: M4 286 de 359 (73 cortas); Dominick 2.000 (98.599 cortas, 5.329 con negativos, 9.776
fuera de la muestra); ILINet 50 de 51 (sale Florida, con 221 semanas). La lista de cada conjunto
queda en `resultados/conjuntos_adicionales/seleccion_{conjunto}.csv`. En ILINet una semana
intermedia sin reporte queda faltante (hay una, en DC); no se interpola.

## 4. Tarea, objetivo y adaptador

Misma tarea: pronóstico global a una semana, ventana de 52 semanas, objetivo
`y_reg = log1p(valor_{t+1} / base8_t)`, con `base8_t` la media de las 8 semanas hasta t y la misma
regla de elegibilidad del panel (al menos 4 semanas activas, `base8 > 0`, valor de t observado).
Mismos canales de la ventana: `nivel`, `observado` e `iniciado`.

Covariables: solo las que se pueden calcular desde la propia serie, con la definición de
`src/armado_panel/02_construir_panel.py`: `log_base8`, `cv8`, `pend8_rel`, `activas8`,
`activas26`, `ratio_t` y `ratio_t_1`; `sem_sin` y `sem_cos` en M4 e ILINet, que tienen fechas.
Categórica: el identificador de la serie. Las cabezas de los modelos esperan las cuatro categóricas
del panel; la serie ocupa la primera y las otras tres reciben una categoría constante, de modo que la
arquitectura no cambia. Las demás covariables del panel (composición por grupo,
geografía, festivos, fracciones de caída del mercado o del país) no existen en estos conjuntos y no
se reemplazan. El escalado se ajusta solo en entrenamiento, como en el panel.

## 5. Partición

Por posición en cada serie (M4 y Dominick no tienen calendario común): las últimas 52 semanas
objetivo son la prueba; antes, 8 semanas de hueco; el resto es entrenamiento. La validación (parada
temprana y elección de tasa) son las últimas 52 semanas del entrenamiento, separadas por 8 semanas
de hueco, como el bloque k4 del panel. Para eso las series se alinean por su última semana en un
calendario sintético; la fecha real solo da `sem_sin` y `sem_cos`. La prueba de cada conjunto quedó
congelada el 2026-11-25 en `preregistro/hash_prueba_adicionales.json` (M4 14.872 filas, Dominick
51.293, ILINet 2.600) y cada corrida la evalúa una sola vez (`src/panel_adicional.py`). El hash cubre
la identidad de cada fila (serie y semanas) y los valores observados (`tasa_t`, `tasa_t1`, `base8`).
Ese mismo día, antes de evaluar cualquier prueba, se cambió el hash inicial, que incluía todas las
columnas. La razón fue que `cv8` cambiaba en el último decimal entre el procesador del Mac y el de
Colab, aunque las versiones de pandas, numpy y pyarrow eran las mismas. Las covariables derivadas
salen de los valores observados y los archivos de origen tienen su propio hash. El hash inicial
queda registrado en el mismo archivo, bajo `anterior`. En Dominick, 607
de las 2.000 series no tienen filas elegibles en la prueba (ceros al final, productos que dejaron de
venderse) y 13 no tienen ninguna fila elegible; las demás reglas no cambian.

## 6. Modelos, ajuste y presupuesto

Toda la familia, sin excepciones: los diez del Top 10, el MLP del Entregable 2, las cuatro redes del
Entregable 1 y las tres referencias; el modelo original entra con su configuración congelada. Las
arquitecturas son las del preregistro del Entregable 2, sin búsqueda nueva. Mismo presupuesto en
cada conjunto: tasa en {1e-4, 3e-4, 1e-3} con la semilla 0 en la validación, lote 512, hasta 100
épocas, paciencia 10, pérdida MAE, dos etapas. Diez semillas (1 a 10) por modelo y conjunto.
Exclusión de corridas como en la sección 6 del Entregable 2.

## 7. Auditoría de fuga

La misma del panel en cada conjunto: `base8` y las covariables solo usan el pasado de t; la ventana
se corta en t; el escalado y la tasa se fijan sin la prueba; la prueba lleva hash y registro de
evaluaciones.

## 8. Análisis

Margen por conjunto: δ = 1 % del MAE de la mediana del entrenamiento en la validación de ese
conjunto, la misma regla que dio δ = 0,0032 en el panel. Métrica primaria: MAE; se informa por
separado en cada conjunto, sin promediar entre conjuntos.

- **En cada conjunto:** los contrastes de la sección 7 del Entregable 2 (Diebold–Mariano con HLN
  sobre las 52 semanas de prueba como decisión, Wilcoxon, IC por bootstrap de bloques, dz y δ de
  Cliff, TOST), cada familia con Holm propio.
- **Entre conjuntos:** Friedman con Iman–Davenport sobre el MAE medio de cada modelo en los cuatro
  conjuntos (SIPSA y los tres adicionales), con Wilcoxon por pares y Holm; además, la prueba
  bayesiana de rangos con signo con ROPE, usando el MAE relativo a la mediana de cada conjunto y
  ROPE de ±1 %. Con tan pocos conjuntos la potencia es baja, así que la conclusión se apoya también
  en la consistencia por conjunto.
- **Consistencia:** el modelo original cumple si tiene menor MAE medio en al menos dos de los tres
  conjuntos adicionales y no es significativamente peor que ningún modelo de la familia en ninguno.
