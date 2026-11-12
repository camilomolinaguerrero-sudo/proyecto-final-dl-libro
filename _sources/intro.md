# Entregable 1: estado del arte, EDA y tres modelos base

Lina Margarita Buelvas y Camilo Molina Guerrero. Proyecto final de Deep Learning, doctorado en
ingeniería.

## El problema

Pronosticamos el abastecimiento semanal de los corredores de alimentos de Colombia: cada corredor
une un municipio de origen con una central mayorista. Los datos son los microdatos SIPSA-A del DANE
de 2018 a 2025. El objetivo es `y_reg = log1p(tasa_t1 / base8)`: el abastecimiento de la semana
siguiente frente a su nivel de las últimas ocho semanas. Una interrupción es `y_reg < ln(1,5)`.

El Entregable 1 fija el nivel mínimo de desempeño (el *baseline*) que cualquier modelo más complejo
deberá superar. Para eso, el libro:

1. define el problema y analiza los datos (capítulos 1 y 2);
2. revisa la literatura y extrae de ella los tres modelos de Deep Learning más usados como
   referencia: LSTM, MLP y CNN 1D (capítulo 3);
3. fija el diseño experimental antes de entrenar: partición, métrica primaria, margen δ y unidad de
   independencia (capítulo 4);
4. compara los tres modelos con inferencia estadística (capítulo 5).

## Estado del informe

Este libro se publica mientras el trabajo avanza. Cada capítulo dice qué está cerrado y qué falta.

| Sección del enunciado | Estado |
| --- | --- |
| 1-2. Problema, datos y EDA | Cerrada |
| 3.1 Estrategia de búsqueda | Cerrada: 491 estudios incluidos y diagrama de flujo |
| 3.2 Selección de los tres modelos | Acordada (LSTM, MLP, CNN 1D) |
| 3.3 Análisis por modelo | Pendiente (Lina); las fórmulas de parámetros ya están en el capítulo 3 |
| 3.4 Tabla de síntesis y equivalencia | Borrador para revisión |
| 4. Diseño experimental | Decisiones cerradas; el preregistro sigue en borrador hasta el piloto |
| 5. Resultados e inferencia | Referencias ingenuas calculadas; los tres modelos aún no se entrenan |

Ninguna cifra del libro se escribe a mano: cada una sale de un archivo de resultados del
repositorio, y el texto dice cuál.

## Uso de inteligencia artificial

Los dos autores usamos Claude (Anthropic), a través de Claude Code, como herramienta de apoyo en el
tamizaje de la literatura, el análisis y la redacción. Los protocolos de tamizaje (anexos) detallan
qué hizo la herramienta, qué revisó cada autor y qué se decidió de común acuerdo.

## Procedencia de esta versión

Construida desde la rama `camilo/libro` @ 9d45b50.

| Archivo | Tomado de |
| --- | --- |
| `eda/01_problema_dataset.ipynb` | `camilo/libro` (árbol de trabajo) |
| `eda/02_eda_general.ipynb` | `camilo/libro` (árbol de trabajo) |
| `eda/03_eda_series.ipynb` | `camilo/libro` (árbol de trabajo) |
| `eda/decisiones_para_modelos.md` | `camilo/libro` (árbol de trabajo) |
| `literatura/modelos_seleccionados.md` | `camilo/libro` (árbol de trabajo) |
| `literatura/sintesis_y_equivalencia_camilo.md` | `camilo/libro` (árbol de trabajo) |
| `DECISIONES.md` | `lina/pipeline` @ f6e260c |
| `preregistro/borrador_preregistro.md` | `lina/pipeline` @ f6e260c |
| `estadistica/README.md` | `camilo/libro` (árbol de trabajo) |
| `literatura/protocolo_tamizaje_camilo.md` | `camilo/libro` (árbol de trabajo) |
| `literatura/protocolo_tamizaje_lina.md` | `lina/pipeline` @ f6e260c |
| `literatura/diagrama_prisma.png` | `camilo/libro` (árbol de trabajo) |
| `resultados/referencias_validacion.csv` | `lina/pipeline` @ f6e260c |
