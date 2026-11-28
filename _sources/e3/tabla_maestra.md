# Tabla maestra de experimentos

Generada por `experiments/tabla_maestra.py` con los archivos de resultados de la rama. Las cifras se cuentan de cada archivo, así que los modelos y las semillas incluyen las referencias ingenuas cuando el archivo las trae. Pendiente quiere decir que el archivo todavía no está en la rama.

| Id | Pregunta | Datos | Estado | Filas | Modelos | Semillas | GPU | Commits | Archivo |
|---|---|---|---|---|---|---|---|---|---|
| E1-1 | Búsqueda de hiperparámetros de MLP, LSTM y CNN | SIPSA-A | hecho | 63 | 4 |  | Tesla T4 | 27281cf | `resultados/busqueda_resultados.csv` |
| E1-2 | Arquitectura del MLP, fase 1 (anchos y profundidades) | SIPSA-A | hecho | 1000 |  | 1 | Tesla T4 | 1dfcf96 | `resultados/arquitectura_mlp_fase1.csv` |
| E1-3 | Arquitectura del MLP, fase 2 | SIPSA-A | hecho | 105 |  | 5 | Tesla T4 | 1dfcf96 | `resultados/arquitectura_mlp_fase2.csv` |
| E1-4 | Tasa de aprendizaje del MLP elegido | SIPSA-A | hecho | 3 |  | 1 | Tesla T4 | 1dfcf96 | `resultados/arquitectura_mlp_tasa.csv` |
| E1-5 | Piloto: varianza entre semillas y número de ejecuciones | SIPSA-A | hecho | 20 | 4 | 5 | Tesla T4 | 9ae367b | `resultados/piloto_metricas_por_corrida.csv` |
| E1-6 | Referencias ingenuas en validación | SIPSA-A | hecho | 25 | 5 |  |  |  | `resultados/referencias_validacion.csv` |
| E1-7 | Corrida definitiva en 2025 de los modelos del E1 | SIPSA-A | hecho | 32 | 4 | 8 | Tesla T4 | e56750d | `resultados/definitiva_metricas_por_corrida.csv` |
| E2-1 | Tasa de aprendizaje de los modelos publicados | SIPSA-A | hecho | 30 | 10 | 1 | Tesla T4 | bd7f0dc, ee59915 | `resultados/publicados_tasa.csv` |
| E2-2 | Corrida definitiva en 2025 de los 15 modelos | SIPSA-A | hecho | 123 | 18 | 9 |  |  | `estadistica/resultados_e2/metricas_por_corrida.csv` |
| E2-3 | Error por segmentos (mercado, régimen, corredores nuevos) | SIPSA-A | hecho | 684 | 18 |  |  |  | `resultados/exploratorios_e2/segmentos.csv` |
| E2-4 | Intervalos conformales por bloques semanales | SIPSA-A | hecho | 288 | 3 | 8 |  |  | `resultados/exploratorios_e2/conforme_por_corrida.csv` |
| E2-5 | Costo y frente de Pareto | SIPSA-A | hecho | 15 | 15 |  |  |  | `resultados/exploratorios_e2/costo_pareto.csv` |
| E2-6 | Robustez a perturbaciones e importancia de variables | SIPSA-A | hecho | 486 | 3 | 3 |  |  | `resultados/robustez_e2/robustez_interpretabilidad.csv` |
| E2-7 | Sensibilidad sin la variable nacional | SIPSA-A | hecho | 9 | 3 | 3 | Tesla T4 | 5ec4157 | `resultados/sensibilidad_sin_nacional/metricas_por_corrida.csv` |
| E2-8 | Réplica del TFT en Electricity | Electricity | hecho | 1 |  | 1 | Tesla T4 |  | `resultados/replica_electricity/replica_s1.json` |
| E2-9 | Limitación: error en semanas de interrupción frente al resto | SIPSA-A | hecho |  |  |  |  |  | `resultados/piloto_limitacion_e2.json` |
| E3-1 | Desarrollo del modelo original y ablaciones (k1 a k3) | SIPSA-A | hecho | 45 | 3 | 5 | Apple M5 (MPS) | 1ce88f9 | `resultados/original_desarrollo.csv` |
| E3-2 | Tasa de aprendizaje del modelo original (k4, semilla 0) | SIPSA-A | hecho | 3 | 1 | 1 | Apple M5 (MPS) | 1ce88f9 | `resultados/original_tasa.csv` |
| E3-3 | Corrida definitiva del modelo original en 2025 | SIPSA-A | pendiente |  |  |  |  |  | `estadistica/resultados_e3/metricas_por_corrida.csv` |
| E3-4 | Conjunto adicional ILINet | ILINet | hecho | 150 | 15 | 10 | Tesla T4 | 31123c3 | `resultados/conjuntos_adicionales/ilinet/metricas_por_corrida.csv` |
| E3-5 | Conjunto adicional M4 | M4 | pendiente |  |  |  |  |  | `resultados/conjuntos_adicionales/m4/metricas_por_corrida.csv` |
| E3-6 | Conjunto adicional Dominick | Dominick | pendiente |  |  |  |  |  | `resultados/conjuntos_adicionales/dominick/metricas_por_corrida.csv` |
| E3-7 | Conformal adaptativo y calibración de los intervalos | SIPSA-A | hecho | 240 | 15 | 8 |  |  | `estadistica/resultados_e3_incertidumbre/conformal_por_corrida.csv` |
