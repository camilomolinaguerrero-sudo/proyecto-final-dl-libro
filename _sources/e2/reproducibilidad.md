# Reproducibilidad (Apéndice B)

| Ítem | Estado | Evidencia |
| --- | --- | --- |
| Preregistro fechado y versionado antes del primer entrenamiento | sí | etiqueta `preregistro-e2`; `src/definitiva.py` no corre sin ella |
| Hash del dataset y de la prueba | sí | `preregistro/hash_prueba.json` |
| Auditoría de fuga ejecutada y anexa | sí, 8 de 9 aprueban | `auditoria/resultados/`; la adversarial se discute |
| Particiones y semillas regenerables | sí | semillas 1 a 8; `src/` |
| Mismo presupuesto de ajuste para todos | sí | tres tasas, lote 512, 100 épocas, paciencia 10 |
| Al menos 6 ejecuciones por modelo | sí | 8 por modelo; `estadistica/resultados_e2/metricas_por_corrida.csv` |
| Análisis de potencia | sí | preregistro v2, sección 5 |
| Pruebas con p exactos, ajustados, IC y tamaños de efecto | sí | `estadistica/resultados_e2/` |
| Familias y corrección múltiple a priori | sí | F1 y F2 con Holm |
| Techo y γ | sí | `resultados/exploratorios_e2/techo.csv` |
| Búsqueda de 1.000 arquitecturas con fase 2 | sí, en el MLP | `resultados/arquitectura_mlp_*.csv`; desvío para los demás |
| Robustez con tres severidades; fidelidad y estabilidad | sí | `resultados/robustez_e2/` |
| Incertidumbre en los tres mejores con cobertura | sí | `resultados/exploratorios_e2/` |
| Un solo acceso a la prueba por ejecución | sí | `resultados/definitiva_e2` en Drive, 88 evaluaciones |
| Reproducción independiente de la tabla principal | parcial: recálculo desde las predicciones con código separado, no por otro equipo; discrepancia máxima 6e-17 | `estadistica/resultados_e2_complementos/reproduccion.json` |
| Entorno limpio y pruebas | sí; 215 pruebas pasan y 13 se omiten por falta del panel | `estadistica/resultados_e2_complementos/entorno_limpio.json` |
| Brecha entrenamiento-validación (tabla B) y fichas de modelo | sí | páginas «Complementos» y «Fichas de modelo» |
| Replicación parcial del paper guía y limitación | sí; no cumple el criterio | `resultados/replica_electricity/`, `resultados/piloto_limitacion_e2.json` |

Para regenerar las tablas confirmatorias desde las predicciones en Drive:

```bash
python estadistica/ejecutar_e2.py <ruta a resultados/predicciones/prueba>
```
