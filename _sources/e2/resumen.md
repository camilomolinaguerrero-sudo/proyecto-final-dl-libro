# Entregable 2: benchmark del Top 10 de la literatura

Lina Margarita Buelvas y Camilo Molina Guerrero.

## Resumen

**Problema.** Pronóstico a una semana del abastecimiento de los corredores
de alimentos de Colombia (SIPSA-A), con el objetivo `y_reg = log1p(tasa_t1/base8)` y el MAE como
métrica primaria.
**Método.** Diez modelos elegidos por puntaje reproducible en la revisión sistemática, el MLP de una
búsqueda de 1.000 arquitecturas y los tres modelos base del Entregable 1, con 8 semillas cada uno,
un preregistro cerrado antes de entrenar y una sola evaluación en 2025 por corrida. Contraste de
decisión: Diebold–Mariano con corrección HLN y Holm; margen δ = 0,0032.
**Resultados.** El mejor es TFT (MAE 0,2729), con
-0,00099 frente a la CNN (IC 95 % -0,00151 a -0,00050): significativa, pero menor
que δ. 2 modelos quedan equivalentes a la CNN y 7 son peores por menos de δ. Los diez
superan a la mediana del entrenamiento. TFT es el único modelo del conjunto de confianza
(MCS, α = 0,10). Mincer–Zarnowitz rechaza la calibración en todos los modelos. El error en semanas de
interrupción es entre 1,20 y 1,26 veces el del resto. La distancia normalizada al techo empírico es γ = 0,03 frente a la mediana del entrenamiento.
**Conclusión.** Con estos datos, ninguna arquitectura reciente mejora a la CNN del Entregable 1 de
forma relevante; el límite parece estar en la información disponible y no en el modelo.

## Preguntas de investigación

Las tres preguntas reformulan las familias y criterios ya fijados en el preregistro v2
(`preregistro/preregistro_e2.md`, secciones 7 y 8); no se agregaron hipótesis después de ver la
prueba. α = 0,05 con Holm dentro de cada familia.

| RQ | Pregunta | H0 | H1 | Tipo |
| --- | --- | --- | --- | --- |
| RQ1 | ¿Algún modelo del Top 10 reduce el MAE de 2025 frente a la CNN del E1 en más de δ? | Δ ≥ −δ | Δ < −δ | Confirmatoria (F1, 10 contrastes) |
| RQ2 | ¿Cada modelo del Top 10 es adecuado frente a la mediana del entrenamiento? | Δ ≥ 0 | Δ < 0 | Confirmatoria (F2, 10 contrastes) |
| RQ3 | ¿La limitación del paper guía (error en semanas de interrupción) es propia del TFT o compartida? | razón ≤ 1 | razón > 1 | Piloto preregistrado |

Δ es el MAE del modelo menos el de la referencia, sobre las 52 semanas de 2025. δ = 0,0032 se
hereda del Entregable 1: el 1 % del MAE de la mejor referencia ingenua en validación.
