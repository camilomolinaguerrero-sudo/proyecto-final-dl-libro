# Métodos

El preregistro v2 (página siguiente) fija la partición, el ajuste, el entrenamiento, las semillas,
la potencia, la regla de exclusión y el plan de contrastes. Aquí se resume lo que no está allí: el
resultado de la búsqueda de arquitectura de la sección 14 y los desvíos que se declaran.

## Búsqueda de arquitectura del MLP (sección 14)

- **Fase 1:** 1.000 arquitecturas de tres capas (anchos de 16 a 256, abandono 0,2 en 8 patrones),
  semilla 0, pliegue k4. MAE de parada entre 0,2862 y 0,2884.
- **Fase 2:** 21 configuraciones (finalistas más la referencia) con 5 semillas. La mejor por MAE
  es `w256-016-128_b000` (0,2866).
- **Regla de parsimonia:** entre las 21 que quedan a menos de δ de la mejor, la de
  menos parámetros: `w032-064-032_b000` (31.758 parámetros, MAE 0,2869).
  La ganadora no está en el borde del espacio de anchos.
- **Sesgo del ganador:** la correlación de Spearman entre el orden de la fase 1 y el de la fase 2 es
  0,19. Con una semilla, la fase 1 ordena poco a los finalistas, y por eso la elección se hizo con la fase 2.

## Desvíos declarados

1. La búsqueda de la sección 14 no se repitió para Transformer, TCN, GRU, RNN simple ni para la LSTM y
   la CNN del E1, por costo (sección 4 del preregistro). Usan la arquitectura de referencia.
2. La cabeza común de los modelos del Top 10 va sin abandono.
3. **Vacío de la regla de decisión.** La regla preregistrada no tiene categoría para una diferencia
   significativa que cabe dentro de ±δ; esos casos salen como «no concluyente». La decisión
   preregistrada no se cambió. Al lado se informa una lectura descriptiva con las categorías de la
   plantilla A.3 («significativa sin relevancia»). Lo acordamos los dos después de ver los
   resultados, y por eso se declara aquí.
4. El baseline clásico fuerte es la referencia lineal del E1; no se ajustó ARIMA/ETS ni boosting.
