# Bitácora de diseño del modelo original

Sección 3 de `experiments/protocolo.md`. Aquí se anota cada variante que se corre en el desarrollo:
bloques k1 a k3, semillas 1 a 5, `python src/original_e3.py desarrollo`. Cada entrada lleva qué cambió,
el MAE medio por bloque, la decisión y la fecha, y se anota antes de correr la siguiente variante.
k4 y 2025 no intervienen en ninguna decisión. El tope es de Nd ≤ 10 variantes, y el número real se
declara en el informe.

## Desarrollo (2026-11-28, 45 corridas)

| # | Variante | Cambio frente a la anterior | MAE k1 | MAE k2 | MAE k3 | Decisión | Fecha |
|---|---|---|---|---|---|---|---|
| 1 | `original` | CNN del E1 (128 unidades, 3 bloques, *dropout* 0,1) con compuerta supervisada, λ = 0,5, tasa 1e-4 | 0,29331 | 0,28890 | 0,28487 | Se mantiene λ = 0,5 (regla de abajo) | 2026-11-28 |
| 2 | `fija` | Ablación: compuerta fija en 0,5 | 0,29400 | 0,28968 | 0,28572 | Referencia | 2026-11-28 |
| 3 | `sin_regimen` | Ablación: λ = 0 (mezcla de Jacobs et al. sin supervisión) | 0,29355 | 0,28907 | 0,28500 | Referencia | 2026-11-28 |

Las variantes 2 y 3 son las ablaciones preregistradas. No eligen el diseño, pero se corren en el mismo
desarrollo para tener la referencia. Si hace falta ajustar λ, las variantes nuevas (por ejemplo,
λ ∈ {0,1; 1}) cuentan dentro del tope.

## Regla para fijar λ (anotada el 2026-11-28 a las 04:00, con el desarrollo en curso)

Se anota antes de terminar las 45 corridas. A esa hora ya se habían visto las de las semillas 1 y 2,
y se declara. La regla solo usa k1 a k3:

1. Para cada bloque k y semilla s se calcula la diferencia pareada d = MAE(original) − MAE(ablación),
   frente a `fija` y frente a `sin_regimen`. Son 15 pares por ablación.
2. **Se mantiene λ = 0,5** si, frente a cada una de las dos ablaciones, la media de d es negativa en
   los 15 pares y en al menos 2 de los 3 bloques.
3. Si no se cumple, se corren dos variantes más, λ = 0,1 y λ = 1, con las mismas semillas y bloques
   (Nd pasa de 3 a 5). Se elige el λ de menor MAE medio en k1 a k3; si hay empate a 4 decimales, se
   queda λ = 0,5.

Es una regla de diseño, no una prueba confirmatoria: la confirmación es la regla R3 en 2025.

**Dispositivo.** El desarrollo corre en la GPU de un Apple M5 (MPS), que no es reproducible bit a bit
(`experiments/determinismo.md`). Solo decide λ.

`python experiments/bitacora_desarrollo.py RESULTADOS.csv` arma la tabla y aplica la regla, a partir del
CSV del desarrollo (una fila por variante, bloque y semilla, con la columna `mae`).

## Decisión (2026-11-28, 07:30)

Las 45 corridas terminaron. Con la regla de arriba, aplicada por `experiments/bitacora_desarrollo.py`
sobre `resultados/original_desarrollo.csv`:

- original − `fija`: media de d = −0,00077 en los 15 pares; el original es mejor en los 3 bloques;
- original − `sin_regimen`: media de d = −0,00018 en los 15 pares; el original es mejor en los 3
  bloques.

**Se mantiene λ = 0,5.** Nd = 3 (el original y las dos ablaciones); no se corrieron variantes nuevas.
Las diferencias son de diseño, no confirmatorias, y quedan por debajo de δ = 0,0032. La confirmación es
la regla R3 en 2025.

## Tasa (2026-11-28, 08:40)

Con λ = 0,5 fijo, `src/original_e3.py tasa` probó las tres tasas del presupuesto común en k4, con la
semilla 0 y parada temprana. Corrió en MPS, igual que el desarrollo (`resultados/original_tasa.csv`):

| Tasa | MAE de parada | Mejor época | Épocas |
| --- | --- | --- | --- |
| 1e-4 | 0,28522 | 45 | 55 |
| 3e-4 | 0,28530 | 17 | 27 |
| 1e-3 | 0,28559 | 7 | 17 |

**Tasa elegida: 1e-4** (menor MAE de parada, `elegir_tasa`). La configuración queda congelada para la
corrida definitiva: CNN del E1 (128 unidades, 3 bloques, *dropout* 0,1), compuerta supervisada con
λ = 0,5, tasa 1e-4, lote de 512, hasta 100 épocas, paciencia 10 y pérdida MAE + λ·BCE.
