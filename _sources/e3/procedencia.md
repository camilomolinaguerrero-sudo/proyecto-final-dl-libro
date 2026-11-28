# Procedencia del modelo original

**Borrador de Camilo del 2026-11-26, pendiente de revisión de Lina.** Sección 3 de
`experiments/protocolo.md`.

## Fuente

Jacobs, R. A., Jordan, M. I., Nowlan, S. J. y Hinton, G. E. (1991). Adaptive mixtures of local
experts. *Neural Computation*, 3(1), 79-87. https://doi.org/10.1162/neco.1991.3.1.79

En la fuente, varios expertos y una red de compuerta se entrenan juntos, y la compuerta reparte cada
caso entre los expertos sin supervisión directa.

## Qué se transfiere

La salida como combinación convexa de expertos ponderada por una compuerta que depende de la
entrada.

## Qué se modifica (implementación propia, `src/modelo_original.py`)

1. **Compuerta supervisada por el régimen.** A la pérdida de pronóstico (MAE) se le suma
   λ·BCE(g, 1[y_reg < ln 1,5]). La compuerta aprende la probabilidad de que la semana objetivo sea de
   interrupción, que es la limitación L del protocolo.
2. **Dos expertos con un significado fijo** (interrupción y resto), en lugar de *K* expertos
   intercambiables.
3. **Representación compartida.** Los expertos y la compuerta leen la misma z de la CNN del E1, no la
   entrada cruda, y se entrenan de punta a punta.
4. **Salida de regresión con pérdida MAE**, que es la métrica primaria, y validación y parada
   temprana solo con el MAE.
5. **Propiedad demostrada** para esta composición: la cota de Lipschitz de `experiments/lipschitz.md`.

## Qué no se usó

No se copió código de ninguna implementación publicada. Todo está escrito sobre `src/modelos.py` y
`src/entrenamiento.py` del proyecto.
