# Protocolo y validación estadística

Capítulo 7 del cuadro 7. El estándar está en [Estándar](estandar.md) y el protocolo completo en [Protocolo](protocolo.md).

**Etiqueta del protocolo:** `protocolo-e3` existe.

## 7.1 Potencia

Con las 10 semillas de los rivales (`resultados/semillas_e3/diez_semillas_por_corrida.csv`), el mayor σd entre pares es 0,00192 (N-HiTS frente a TFT) y la mediana 0,00097. Con α/m = 0,00333 (m = 15) y δ = 0,0032:

| Caso | σd | Semillas para 0,90 | Potencia con 10 | Potencia con 12 | Conjunta con 12 | MDE con 12 |
| --- | --- | --- | --- | --- | --- | --- |
| Peor caso | 0,00192 | 11 | 0,85 | 0,95 | 0,48 | 0,0029 |
| Mediana | 0,00097 | 6 | 1,00 | 1,00 | 1,00 | 0,0015 |

Fuente: `experiments/potencia_e3.json`. En el peor caso, 10 semillas dan 0,85 por contraste, por debajo del 0,90 del estándar. Por eso el protocolo pide 12. La potencia conjunta de los 15 contrastes es baja aun con 12, y se informa así: un resultado no concluyente no es equivalencia, y para eso están TOST y ROPE con ±δ.

## 7.2 Contrastes confirmatorios (tabla A.4)

*Pendiente: `estadistica/resultados_e3/familia_principal.csv` todavía no existe en esta rama.* Se genera con `estadistica/ejecutar_e3.py` cuando el modelo original tenga sus 12 semillas en 2025.

## 7.3 Regla de decisión

Cada contraste decide con la prueba que menos rechaza (el mayor de los tres p de Holm), exige que la cota superior del IC 95 % quede por debajo de −δ y un tamaño de efecto no despreciable (|Cliff| ≥ 0,147 o |dz| ≥ 0,2). La condición 6 repite la decisión con Wilcoxon por semillas, con Nadeau–Bengio y con las semillas 1 a 8. El análisis falla si los modelos no tienen las mismas semillas. Código: `estadistica/ejecutar_e3.py`.
