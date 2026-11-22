# Proyecto final: pronóstico del abastecimiento de alimentos con Deep Learning

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

**Resultado en una línea.** Las tres redes son equivalentes entre sí y superan a todas las referencias. El
baseline es la CNN, y un modelo avanzado debe bajar su MAE al menos en el margen preregistrado δ (capítulo 5).
El Entregable 2 pone a prueba esa cota con diez arquitecturas de la literatura; su resumen abre la parte
«Entregable 2».

Ninguna cifra del libro se escribe a mano: cada una sale de un archivo de resultados del
repositorio, y el texto dice cuál.

## Uso de inteligencia artificial

Los dos autores usamos Claude (Anthropic), a través de Claude Code, como herramienta de apoyo en el
tamizaje de la literatura, el análisis y la redacción. Los protocolos de tamizaje (anexos) detallan
qué hizo la herramienta, qué revisó cada autor y qué se decidió de común acuerdo.

## Procedencia de esta versión

Construida desde la rama `main` @ 30fec09.

| Archivo | Tomado de |
| --- | --- |
| `eda/01_problema_dataset.ipynb` | `main` @ c3f9825 |
| `eda/02_eda_general.ipynb` | `main` @ 758a7e8 |
| `eda/03_eda_series.ipynb` | `main` @ 6648862 |
| `eda/decisiones_para_modelos.md` | `main` @ 758a7e8 |
| `literatura/modelos_seleccionados.md` | `main` @ 3c5ed17 |
| `literatura/sintesis_y_equivalencia_camilo.md` | `main` @ 62778fc |
| `literatura/analisis_por_modelo_lina.md` | `main` @ c8913c0 |
| `resultados/capitulo5_resultados_definitivos.md` | `main` @ f4a6d7f |
| `estadistica/baseline_y_margen.md` | `main` @ 59a1891 |
| `DECISIONES.md` | `main` @ fb3826a |
| `preregistro/borrador_preregistro.md` | `main` @ e56750d |
| `estadistica/README.md` | `main` @ 2128cce |
| `literatura/protocolo_tamizaje_camilo.md` | `main` @ 921178a |
| `literatura/protocolo_tamizaje_lina.md` | `main` @ 7add5ad |
| `literatura/diagrama_prisma.png` | `main` @ 9d45b50 |
| `eda/resultados/trazabilidad_hallazgo_decision.md` | `main` @ a2b6f46 |
| `literatura/top10_e2.md` | `main` @ cced659 |
| `preregistro/preregistro_e2.md` | `main` @ f19edce |
| `resultados/referencias_validacion.csv` | `main` @ bdf5ffe |
| `resultados/piloto_metricas_por_corrida.csv` | `main` @ 27c8c36 |
| `resultados/busqueda_elegidas.csv` | `main` @ 27c8c36 |
