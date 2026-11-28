# Bloque A: correcciones del Entregable 2

El docente revisa el proyecto completo al final, así que no hubo retroalimentación del E2 antes de esta entrega. Esta tabla registra las correcciones que identificamos nosotros al revisar el E2, con las columnas que pide el enunciado. Si llegan observaciones del docente, cada una entra como una fila más.

## Trazabilidad

| Observación | Acción | Ubicación en el libro | Evidencia | Impacto en los resultados |
| --- | --- | --- | --- | --- |
| Inferencia (tabla C): el E2 usó 8 semillas por modelo, y el análisis de potencia del E3 (`experiments/potencia_e3.json`) pide más. | Se corrieron las semillas 9 y 10 de los 15 modelos y se repitió el análisis del E2 con 10. | Esta página, tabla de antes y después | `resultados/semillas_e3/comparacion_10/` | Ver la tabla de antes y después: MAE, posición y decisión de cada contraste. |
| Inferencia: el modelo mixto no anidaba la semilla en el modelo. | Semilla anidada en el modelo y lectura de la condición (iv) sin atribuirla solo al año (PR #19). | Capítulo del E2, efectos mixtos | `estadistica/resultados_e2_complementos/mixto_varianza.csv` | Contrastes recalculados en `mixto_contrastes.csv`, en la misma carpeta. |
| Inferencia: la t corregida de Nadeau y Bengio era opcional y podía faltar sin aviso. | Es obligatoria en el análisis del E3, que también exige el mismo conjunto de semillas en todos los modelos (PR #21). | Validación (e3/validacion) | `estadistica/ejecutar_e3.py`, `exigir_semillas` | Solo afecta al E3; si falta una alternativa, la condición 6 queda como no evaluada. |
| Inferencia: el p del bootstrap se calculaba como k/B y podía dar 0. | Se usa (k + 1)/(B + 1) (PR #21). | Validación (e3/validacion) | `estadistica/ejecutar_e3.py` | Cada p cambia en menos de 1/(B + 1) y ya no puede ser 0. |
| Fuga de información: había que repetir la auditoría antes de congelar. | Se reutiliza la batería de la sección 10 del E2 sobre el mismo panel. | Anexo de auditoría | `auditoria/resultados/auditoria_fuga.json` | Aprueban 8 de 9; no aprueba: validacion adversarial, declarado en el anexo. |
| Determinismo: no estaba documentado qué se fija y qué no es determinista. | Página de determinismo y semillas, con la variación entre semillas (PR #29). | e3/determinismo | `experiments/determinismo.md` | Documental; no cambia cifras. |
| Calibración e incertidumbre: los intervalos solo cubrían los tres mejores modelos. | Intervalos conformales en línea de los 15 modelos, en versión de partición y adaptativa, y su curva de calibración (PR #27). | Evaluación ampliada | `estadistica/resultados_e3_incertidumbre/` | Cobertura al 90 % entre 0,902 y 0,903 en los 15 modelos. |

## Antes y después: 8 frente a 10 semillas

Antes: `estadistica/resultados_e2`. Después: `resultados/semillas_e3/comparacion_10`. Solo redes; las referencias ingenuas son deterministas y no cambian.

| Modelo | MAE, 8 semillas | MAE, 10 semillas | Diferencia | Posición antes | Posición después |
| --- | --- | --- | --- | --- | --- |
| TFT | 0,2729 | 0,2730 | +0,00008 | 1 | 1 |
| Transformer | 0,2737 | 0,2737 | -0,00003 | 2 | 2 |
| CNN (E1) | 0,2739 | 0,2740 | +0,00004 | 3 | 3 |
| MLP (E1) | 0,2741 | 0,2741 | -0,00002 | 5 | 4 |
| PatchTST | 0,2741 | 0,2742 | +0,00015 | 4 | 5 |
| RNN simple | 0,2743 | 0,2745 | +0,00017 | 7 | 6 |
| LSTM (E1) | 0,2743 | 0,2745 | +0,00021 | 6 | 7 |
| GRU | 0,2747 | 0,2747 | -0,00001 | 8 | 8 |
| MLP (E2) | 0,2748 | 0,2748 | +0,00003 | 9 | 9 |
| DLinear | 0,2751 | 0,2751 | +0,00003 | 10 | 10 |
| N-BEATS | 0,2753 | 0,2753 | +0,00003 | 11 | 11 |
| TCN | 0,2756 | 0,2755 | -0,00006 | 12 | 12 |
| N-HiTS | 0,2757 | 0,2756 | -0,00012 | 13 | 13 |
| iTransformer | 0,2758 | 0,2757 | -0,00001 | 14 | 14 |
| Referencia lineal | 0,2829 | 0,2830 | +0,00013 | 15 | 15 |

Cambian de posición 4 de 15 modelos: MLP (E1) (5 → 4), PatchTST (4 → 5), RNN simple (7 → 6), LSTM (E1) (6 → 7). La mayor diferencia absoluta de MAE es 0,00021, frente a δ = 0,0032.

- **F1, frente a la CNN:** cambia la decisión en 0 de 10 contrastes.
- **F2, frente a la mediana:** cambia la decisión en 0 de 10 contrastes.

## Congelamiento de los benchmarks

Al cerrar el bloque A, los resultados de los rivales y de las referencias se congelan con el hash de sus archivos y la etiqueta `e3-benchmarks`. *Pendiente: la etiqueta `e3-benchmarks` todavía no existe.*
