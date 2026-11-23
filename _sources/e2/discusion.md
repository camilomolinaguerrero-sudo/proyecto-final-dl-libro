# Discusión, conclusiones y trabajo futuro

## Respuesta a cada pregunta

**RQ1.** 0 de 10 modelos superan a la CNN por más de δ, así que no se rechaza H0 en ninguno. El mejor, TFT, cambia el MAE en -0,00099 (IC -0,00151 a -0,00050). La diferencia es significativa, pero la cota superior del intervalo queda lejos de −δ = -0,0032. La CNN del E1 sigue siendo un baseline suficiente, con muchos menos parámetros.

**RQ2.** 10 de 10 superan a la mediana del entrenamiento con diferencias de unas diez veces δ. Esa es la condición (i) de adecuación. La condición (ii) se cumple en 10 de 10: la brecha entre validación y entrenamiento no pasa de 0,0033, muy por debajo de δg = 0,032. La condición (iv) no se cumple en el sentido literal: el MAE de 2025 es menor que el de validación en todos, entre 0,0067 y 0,0085, lo que apunta a un año de prueba más fácil (cambio de distribución, como mostró la validación adversarial) y no a sobreajuste a la validación. La condición (iii) falla en todos: Mincer–Zarnowitz rechaza, con pendientes menores que 1. Por eso ningún modelo es adecuado en el sentido estricto de la sección 7.2.

**RQ3.** La razón de error en semanas de interrupción es mayor que 1 en TFT, PatchTST y N-BEATS, con intervalos que no se tocan con 1. La limitación es de la familia de modelos con estos datos.

## Techo de desempeño

Como techo empírico se usa la mediana de las predicciones de todos los modelos (MAE 0,2718). La distancia normalizada del mejor modelo es γ = 0,032 frente a la mediana del entrenamiento y γ = 0,52 frente a la CNN. El modelo está cerca de lo que se obtiene combinando todos (es un techo medido en 2025, así que solo describe), y la entropía de permutación cercana a la del ruido blanco apunta en la misma dirección: falta información, no capacidad.

## Costo

En la frontera de Pareto de MAE frente a parámetros, tiempo de entrenamiento e inferencia quedan TFT, Transformer, MLP (E1), MLP (E2), Referencia lineal. TFT usa 6.741.870 parámetros, 49 veces los de la CNN, y entrena en 32 minutos frente a 9. La mejora no paga ese costo.

## Amenazas a la validez

- **Prueba reutilizada.** 2025 ya se usó en el Entregable 1. En el E2 hubo 88 evaluaciones, una por corrida y con todo fijado antes; las del E1 se reutilizaron sin volver a evaluar.
- **Cambio de distribución.** La validación adversarial no aprueba (AUC 0,99). La corrida de sensibilidad sin la variable que más separa no cambia la lectura.
- **Arquitecturas de referencia.** Cuatro modelos y la CNN y la LSTM no tuvieron búsqueda propia; pueden estar subestimados. Los 21 finalistas del MLP quedaron dentro de δ, lo que sugiere un efecto pequeño.
- **Vacío de la regla de decisión**, declarado en Métodos.
- **Ablación sin reentrenar.** Mide la dependencia del modelo entrenado, no el aporte de cada grupo si se entrenara sin él.

## Limitaciones

- Un solo horizonte (una semana) y un solo conjunto de datos.
- El baseline clásico fuerte es lineal; no se ajustó ARIMA/ETS ni boosting.
- La réplica del TFT en Electricity es reducida (una semilla, otra implementación) y queda 16 % en P50 y 41 % en P90 por encima de lo publicado; no confirma ni refuta el resultado del artículo.

## Conclusiones y trabajo futuro

Diez arquitecturas recientes, entrenadas con el mismo presupuesto y evaluadas con un preregistro, no mejoran a la CNN del Entregable 1 en más del margen relevante. Todas son adecuadas frente a la mediana y todas comparten dos debilidades: pronósticos comprimidos hacia la media y más error en las semanas de interrupción. El trabajo futuro debería ir a los datos: variables externas (clima, vías, precios) y una pérdida o una cabeza que traten las interrupciones de forma explícita, antes que a modelos más grandes.

## Referencia del paper guía

Lim, B., Arık, S. Ö., Loeff, N. y Pfister, T. (2021). Temporal Fusion Transformers for interpretable multi-horizon time series forecasting. *International Journal of Forecasting*, 37(4), 1748-1764. https://doi.org/10.1016/j.ijforecast.2021.03.012

Las referencias de los demás modelos y de la revisión sistemática están en la parte 3 del libro.

