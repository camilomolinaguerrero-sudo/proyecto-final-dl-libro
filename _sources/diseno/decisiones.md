# Decisiones del proyecto

Estas decisiones se heredan a los Entregables 2 y 3. Solo cambian de común acuerdo entre Lina y Camilo. Registradas el 2026-11-06.

## Problema

- **Tipo de problema:** series de tiempo, modelo global con covariables, horizonte de 1 semana.
- **Objetivo:** `y_reg = log1p(tasa_t1 / base8)`. La interrupción es `y_reg < ln(1,5)`.
- **Métricas:** primaria MAE; secundarias RMSE, MASE y AUC de interrupción.
- **Margen delta:** δ = 0,0032, el 1 % del MAE de la mediana del entrenamiento en validación (0,3188). Acordado el 2026-11-08; reemplaza el anclaje inicial a la persistencia.
- **Unidad de independencia:** el corredor (`mercado` + `cod_mpio`); en la inferencia, la semana como bloque.

## Partición

- **Entrenamiento:** hasta la semana objetivo 2024-10-21.
- **Prueba:** de 2025-01-06 a 2025-12-29, congelada con su hash.
- **Validación:** de origen móvil.
- **Hueco:** 8 semanas.
- **Por declarar en el informe:** 2025 ya se usó como prueba en el curso de Machine Learning.

## Literatura

Borrador de la cadena de búsqueda:

```text
("deep learning" OR "neural network*" OR LSTM OR GRU OR "temporal convolutional") AND (forecast* OR predict*) AND ("food supply" OR "food market*" OR "agri-food" OR "agricultural supply chain*" OR "wholesale market*")
```

Acuerdos del 2026-11-08 entre Lina y Camilo:

- **Período:** 2018-2026 en las cuatro bases (Web of Science, Scopus, IEEE Xplore y arXiv).
- **Estudios de congresos:** se admiten. E1 sigue excluyendo las revisiones y las actas completas.

## Entregas entre Lina y Camilo

| Archivo | Responsable | Contenido |
| --- | --- | --- |
| `eda/decisiones_para_modelos.md` | Camilo | Longitud de ventana y faltantes |
| `literatura/modelos_seleccionados.md` | Ambos | Los tres modelos |
| `resultados/predicciones/<modelo>_s<semilla>.parquet`, en Drive | Lina | `fila`, `semana_t1`, `y_real`, `y_pred` |
| `resultados/`, en el repositorio | Lina | Métricas por corrida |
