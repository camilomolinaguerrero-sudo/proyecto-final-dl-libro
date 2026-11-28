# Ficha del modelo original: CNN con mezcla por régimen

Sigue las secciones de Mitchell et al. (2019). Lo que todavía no tiene corridas dice **pendiente** y se
llena con los archivos de resultados cuando existan, no a mano.

## 1. Detalles del modelo

- **Autores:** Lina Margarita Buelvas y Camilo Molina Guerrero, Entregable 3 del curso de Deep
  Learning del doctorado en ingeniería, 2026.
- **Código:**
  - `src/modelo_original.py`, con la clase `CNNMezcla` y la fábrica `construir_original`;
  - la demostración de la cota de Lipschitz está en `experiments/lipschitz.md`.
- **Arquitectura:**
  - el cuerpo es la CNN causal del E1: 3 bloques de convolución con kernel 3, 128 canales, ReLU y
    *dropout* 0,1;
  - z = [promedio global, última semana, covariables, embeddings de mercado, departamento, municipio
    y grupo dominante];
  - sobre z hay una compuerta g(z) = σ(uᵀz + c) y dos expertos, h_int y h_norm, cada uno con una capa
    oculta de 64 unidades;
  - la salida es ŷ = g·h_int + (1 − g)·h_norm.
- **Tamaño:** 159.894 parámetros, frente a 137.534 de la CNN del E1 (+16,3 %).
- **Fuente:** la mezcla de expertos de Jacobs, Jordan, Nowlan y Hinton (1991). La modificación es que
  la compuerta se supervisa con el régimen observado: a la pérdida de pronóstico se le suma
  λ·BCE(g, 1[y < ln 1,5]).
- **Entrenamiento:**
  - optimizador Adam, pérdida MAE + λ·BCE, lote de 512, hasta 100 épocas, paciencia de 10 en el MAE
    del bloque de parada;
  - la tasa se elige entre {1e-4, 3e-4, 1e-3} con la semilla 0 en k4;
  - la corrida definitiva tiene dos etapas: la primera fija la mejor época E y la segunda reentrena
    E épocas con todo el entrenamiento hasta 2024-10-21;
  - va con 12 semillas.
- **λ:** 0,5 provisional. Se fija con el desarrollo en k1 a k3 (`experiments/design_log.md`):
  **pendiente**.
- **Desviación declarada:** el desarrollo (k1 a k3, semillas 1 a 5) corrió en la GPU de un Apple M5
  (MPS), cuyas operaciones no son reproducibles bit a bit. Solo decide λ y las variantes; la corrida
  definitiva va en CUDA.
- **Artefactos:** `artifacts/original_s<semilla>/` (pesos, transformador y `metadata.json`), escritos
  por `src/exportar.py`: **pendiente**, a la espera de la corrida definitiva.
- **Licencia:** MIT para el código.

## 2. Uso previsto

- **Para qué:** pronosticar, con una semana de anticipación, la razón entre el abastecimiento de un
  corredor (municipio de origen → central mayorista) y su promedio de las 8 semanas previas, y marcar
  las semanas en que esa razón caería por debajo de 0,5.
- **Usuarios:** investigación y análisis del abastecimiento con datos públicos de SIPSA-A. Es un
  trabajo de curso, no un sistema operativo.
- **Fuera de alcance:**
  - corredores que no cumplen la regla de 4 de 8 semanas;
  - horizontes de más de una semana;
  - mercados que no están en SIPSA-A;
  - decisiones de política o de mercado sin validación aparte.

## 3. Factores

Los factores por los que se desagrega el error son:

- el régimen (semanas de interrupción frente al resto);
- el mercado;
- los corredores nuevos en 2025;
- el número de días encuestados.

La motivación del modelo es el régimen. En el E2, el MAE en semanas de interrupción fue 1,20 a 1,26
veces el del resto en los tres mejores modelos publicados (`resultados/piloto_limitacion_e2.json`):

| Modelo | Razón | IC 95 % |
| --- | --- | --- |
| TFT | 1,259 | 1,217 a 1,305 |
| PatchTST | 1,259 | 1,218 a 1,298 |
| N-BEATS | 1,198 | 1,158 a 1,238 |

Los IC salen de un bootstrap por corredor con B = 2000.

## 4. Métricas

- **Primaria:** MAE de `y_reg`.
- **Secundarias:** RMSE, MASE y AUC de interrupción.
- **Margen de equivalencia práctica:** δ = 0,0032.
- **Decisión:** la regla R3 de `experiments/protocolo.md`, con estas condiciones:
  - pruebas t pareada, Diebold-Mariano con la corrección HLN y bootstrap por bloques, con corrección
    de Holm;
  - el extremo superior del IC 95 % por debajo de −δ;
  - tamaño de efecto suficiente;
  - Wilcoxon, Nadeau-Bengio y las semillas 1 a 8 deben llegar a la misma conclusión.
- **Implementación:** `estadistica/ejecutar_e3.py`.

## 5. Datos

- **Entrenamiento:** el panel SIPSA-A hasta la semana objetivo 2024-10-21, con 622.244 filas y 4.305
  corredores.
- **Prueba:** 2025, con 103.466 filas y 3.009 corredores, congelada con su hash.
- **Detalle:** la fuente, la curaduría y los sesgos conocidos están en
  [DATA_STATEMENT.md](DATA_STATEMENT.md).
- **Conjuntos adicionales:** M4, ILINet y Dominick, en `resultados/conjuntos_adicionales/`: **pendiente**.

## 6. Resultados

| Análisis | Estado |
| --- | --- |
| Desarrollo k1 a k3 (3 variantes × 5 semillas) | en curso |
| Elección de la tasa en k4 | pendiente |
| 2025, 12 semillas, frente a los 15 rivales (tabla A.4) | pendiente |
| Error por régimen y por mercado | pendiente |
| Conjuntos adicionales | pendiente |
| Cota de Lipschitz con los pesos finales | pendiente |

Como referencia, en 2025 (8 semillas, `estadistica/resultados_e2/tabla_por_modelo.csv`):

| Modelo | MAE medio |
| --- | --- |
| TFT | 0,2729 |
| Transformer | 0,2737 |
| CNN del E1 | 0,2739 |

## 7. Consideraciones éticas

- Los datos son agregados por corredor y semana y no identifican personas (`DATA_STATEMENT.md`, §6).
- El error pesa más en los mercados con más filas: Corabastos aporta el 14,8 %. Un buen MAE global
  puede esconder un mal desempeño en mercados pequeños, y por eso el error se reporta por mercado.
- Una alerta falsa de interrupción, o una que falte, puede inducir decisiones de compra o de logística
  equivocadas si el modelo se usa fuera del alcance de la §2.

## 8. Advertencias

- 2025 ya se usó como prueba en el curso de Machine Learning (`DECISIONES.md`).
- La cota de Lipschitz es un producto de normas espectrales por capa, y ese tipo de cota suele quedar
  muy por encima del cociente empírico. Garantiza continuidad, no robustez práctica. Su valor con los
  pesos finales, junto al cociente empírico, queda en la tabla de la §6.
- El modelo depende de la regularidad del reporte del DANE. Con pocos días encuestados, la medición
  es ruidosa, y con 1 día el 80 % de las filas son interrupciones.

## Referencias

- Jacobs, R. A., Jordan, M. I., Nowlan, S. J. y Hinton, G. E. (1991). Adaptive Mixtures of Local
  Experts. *Neural Computation*, 3(1), 79-87. <https://doi.org/10.1162/neco.1991.3.1.79>
- Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji,
  I. D. y Gebru, T. (2019). Model Cards for Model Reporting. *Proceedings of the Conference on
  Fairness, Accountability, and Transparency*, 220-229. <https://doi.org/10.1145/3287560.3287596>
