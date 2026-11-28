# Proyecto final: pronóstico del abastecimiento de alimentos con Deep Learning

Lina Margarita Buelvas y Camilo Molina Guerrero. Proyecto final de Deep Learning, doctorado en
ingeniería.

## El problema

Pronosticamos el abastecimiento semanal de los corredores de alimentos de Colombia: cada corredor
une un municipio de origen con una central mayorista. Los datos son los microdatos SIPSA-A del DANE
de 2018 a 2025. El objetivo es `y_reg = log1p(tasa_t1 / base8)`: el abastecimiento de la semana
siguiente frente a su nivel de las últimas ocho semanas. Una interrupción es `y_reg < ln(1,5)`.

El libro reúne los tres entregables. El Entregable 1 fija el nivel mínimo de desempeño (el *baseline*) que cualquier modelo más complejo
deberá superar. Para eso, el libro:

1. define el problema y analiza los datos (capítulos 1 y 2);
2. revisa la literatura y extrae de ella los tres modelos de Deep Learning más usados como
   referencia: LSTM, MLP y CNN 1D (capítulo 3);
3. fija el diseño experimental antes de entrenar: partición, métrica primaria, margen δ y unidad de
   independencia (capítulo 4);
4. compara los tres modelos con inferencia estadística (capítulo 5).

## Estado del informe

| Sección del enunciado | Estado |
| --- | --- |
| 1-2. Problema, datos y EDA | Cerrada |
| 3.1 Estrategia de búsqueda | Cerrada: 491 estudios incluidos y diagrama de flujo |
| 3.2 Selección de los tres modelos | Acordada (LSTM, MLP, CNN 1D) |
| 3.3 Análisis por modelo | Cerrada |
| 3.4 Tabla de síntesis y equivalencia | Cerrada |
| 4. Diseño experimental | Preregistro cerrado el 2026-11-13, antes de evaluar 2025 |
| 5. Resultados e inferencia | Cerrada: corrida definitiva (8 semillas) y comparación preregistrada sobre 2025 |
| Entregable 2 | Benchmark del Top 10 de la literatura con preregistro v2 (parte «Entregable 2», estructura A.4 del enunciado) |
| Entregable 3 | Modelo original (mezcla de expertos con compuerta supervisada), validación con la regla R3, conjuntos adicionales, artefactos ONNX y dashboard. El estado de cada parte está en «Entregable 3: resumen y estado» |

**Resultado en una línea.** Las tres redes son equivalentes entre sí y superan a todas las referencias. El
baseline es la CNN, y un modelo avanzado debe bajar su MAE al menos en el margen preregistrado δ (capítulo 5).
El Entregable 2 pone a prueba esa cota con diez arquitecturas de la literatura; su resumen abre la parte
«Entregable 2». El Entregable 3 propone un modelo original que ataca la limitación encontrada en el E2:
el error es mayor en las semanas de interrupción. Lo somete a la regla R3 contra los 15 rivales; sus
cuatro partes siguen los bloques del enunciado.

Ninguna cifra del libro se escribe a mano: cada una sale de un archivo de resultados del
repositorio, y el texto dice cuál.

## Uso de inteligencia artificial

Los dos autores usamos Claude (Anthropic), a través de Claude Code, como herramienta de apoyo en el
tamizaje de la literatura, el análisis y la redacción. Los protocolos de tamizaje (anexos) detallan
qué hizo la herramienta, qué revisó cada autor y qué se decidió de común acuerdo.

## Procedencia de esta versión

Construida desde la rama `camilo/libro-completo` @ 7bf4368.

| Archivo | Tomado de |
| --- | --- |
| `eda/01_problema_dataset.ipynb` | `camilo/libro-completo` @ c3f9825 |
| `eda/02_eda_general.ipynb` | `camilo/libro-completo` @ 758a7e8 |
| `eda/03_eda_series.ipynb` | `camilo/libro-completo` @ 6648862 |
| `eda/decisiones_para_modelos.md` | `camilo/libro-completo` @ 758a7e8 |
| `literatura/modelos_seleccionados.md` | `camilo/libro-completo` @ 3c5ed17 |
| `literatura/sintesis_y_equivalencia_camilo.md` | `camilo/libro-completo` @ 62778fc |
| `literatura/analisis_por_modelo_lina.md` | `camilo/libro-completo` @ c8913c0 |
| `resultados/capitulo5_resultados_definitivos.md` | `camilo/libro-completo` @ f4a6d7f |
| `estadistica/baseline_y_margen.md` | `camilo/libro-completo` @ 59a1891 |
| `DECISIONES.md` | `camilo/libro-completo` @ fb3826a |
| `preregistro/borrador_preregistro.md` | `camilo/libro-completo` @ e56750d |
| `estadistica/README.md` | `camilo/libro-completo` @ cedf4c8 |
| `literatura/protocolo_tamizaje_camilo.md` | `camilo/libro-completo` @ 921178a |
| `literatura/protocolo_tamizaje_lina.md` | `camilo/libro-completo` @ 7add5ad |
| `literatura/diagrama_prisma.png` | `camilo/libro-completo` @ 9d45b50 |
| `eda/resultados/trazabilidad_hallazgo_decision.md` | `camilo/libro-completo` @ a2b6f46 |
| `literatura/top10_e2.md` | `camilo/libro-completo` @ cced659 |
| `preregistro/preregistro_e2.md` | `camilo/libro-completo` @ f19edce |
| `experiments/estandar.md` | `camilo/libro-completo` @ 1e7a51b |
| `experiments/protocolo.md` | `camilo/libro-completo` @ 49e1688 |
| `experiments/procedencia.md` | `camilo/libro-completo` @ 1ce88f9 |
| `experiments/design_log.md` | `camilo/libro-completo` @ 7443ec7 |
| `experiments/lipschitz.md` | `camilo/libro-completo` @ 1ce88f9 |
| `experiments/novedad.md` | `camilo/libro-completo` @ 9fe26c9 |
| `experiments/tabla_maestra.md` | `camilo/libro-completo` @ 5cdc613 |
| `experiments/determinismo.md` | `camilo/libro-completo` @ 6437ac7 |
| `preregistro/conjuntos_adicionales.md` | `camilo/libro-completo` @ 31123c3 |
| `resultados/semillas_e3/comparacion_10/resumen.md` | `camilo/libro-completo` @ 700e409 |
| `MODEL_CARD.md` | `camilo/libro-completo` @ fbd6af3 |
| `DATA_STATEMENT.md` | `camilo/libro-completo` @ fbd6af3 |
| `artifacts/README.md` | `camilo/libro-completo` @ 970a4de |
| `dashboard/README.md` | `camilo/libro-completo` @ b42edb4 |
| `resultados/referencias_validacion.csv` | `camilo/libro-completo` @ bdf5ffe |
| `resultados/piloto_metricas_por_corrida.csv` | `camilo/libro-completo` @ 27c8c36 |
| `resultados/busqueda_elegidas.csv` | `camilo/libro-completo` @ 27c8c36 |
