# Modelo original: diseño

Capítulo 5 del cuadro 7. La declaración completa del mecanismo está en el [protocolo](protocolo.md), sección 3; la demostración de la propiedad, en [Cota de Lipschitz](lipschitz.md); y la bitácora de variantes, en [Bitácora de diseño](design_log.md).

## 5.1 Limitación que se ataca

El piloto del E2 (`resultados/piloto_limitacion_e2.json`) midió el error de tres modelos del Top 10 en las semanas de interrupción (razón tasa_t1/base8 < 0,5; 26,9 % de las filas) y en el resto, con un IC por bootstrap de corredores:

| Modelo | MAE en interrupción | MAE en el resto | Razón | IC 95 % |
| --- | --- | --- | --- | --- |
| TFT | 0,3294 | 0,2616 | 1,26 | 1,22 a 1,30 |
| PatchTST | 0,3317 | 0,2635 | 1,26 | 1,22 a 1,30 |
| N-BEATS | 0,3220 | 0,2688 | 1,20 | 1,16 a 1,24 |

En los tres, el IC 95 % de la razón queda por encima de 1. Los modelos fallan más justo donde el pronóstico importa: cuando el abastecimiento se cae.

La segunda cara del mismo problema está en la regresión de Mincer–Zarnowitz del E2 (`estadistica/resultados_e2/adecuacion.csv`). La pendiente de y sobre ŷ va de 0,52 a 0,71 en los 14 modelos, siempre por debajo de 1, y la prueba conjunta de a = 0 y b = 1 rechaza en todos (p máximo 5,1e-17). Los pronósticos están comprimidos hacia la media, que es lo que se espera de una sola cabeza que promedia semanas normales y de interrupción.

## 5.2 Fuente, transferencia e hipótesis

El mecanismo es la mezcla de expertos con compuerta de Jacobs, Jordan, Nowlan y Hinton (1991), transferido de clasificación y regresión genéricas al pronóstico de abastecimiento. La propiedad compartida que justifica la transferencia es que los datos vienen de regímenes distintos: en Jacobs et al., subtareas; aquí, semanas normales y semanas de interrupción. La procedencia completa y las modificaciones están en [Procedencia](procedencia.md).

- **Hipótesis del mecanismo (D3).** La compuerta separa los dos regímenes, así que el modelo reduce el MAE de las semanas de interrupción frente a la CNN del E1 en al menos δ.
- **Predicciones secundarias (D4), a priori.** (1) Baja la razón de error interrupción/resto. (2) La pendiente de Mincer–Zarnowitz se acerca a 1. (3) Con la compuerta fija en 0,5, la ventaja desaparece. (4) La compuerta discrimina las interrupciones mejor que el azar (AUC > 0,5 con IC).

## 5.3 Formulación

Sea $x \in \mathbb{R}^{T \times C}$ la ventana (T = 52, C = 3), $c$ las covariables y $e(\kappa)$ los *embeddings* de las categóricas. La CNN del E1 produce la representación $r(x)$ (promedio global y última semana de sus mapas), y la entrada de la cabeza es $z = [r(x), c, e(\kappa)]$. Con el panel, $z \in \mathbb{R}^{342}$.

$$
g(z) = \sigma(u^\top z + c_0)
$$ (eq-compuerta)

$$
h_i(z) = a_i^\top \operatorname{ReLU}(A_i z + b_i) + \beta_i, \quad i \in \{\text{int}, \text{norm}\}
$$ (eq-expertos)

$$
\hat y = g(z)\, h_{\text{int}}(z) + \bigl(1 - g(z)\bigr)\, h_{\text{norm}}(z)
$$ (eq-salida)

El régimen observado es $s = \mathbb{1}[y < \ln 1{,}5]$, porque $y = \log(1 + \text{tasa}_{t+1}/\text{base}_8)$ y la interrupción es una razón menor que 0,5. La pérdida de entrenamiento es

$$
\mathcal{L} = \frac{1}{B}\sum_{j=1}^{B} |\hat y_j - y_j| + \lambda \cdot \frac{1}{B}\sum_{j=1}^{B} \operatorname{BCE}\bigl(g(z_j), s_j\bigr)
$$ (eq-perdida)

con λ = 0,5 provisional, que se fija en el desarrollo con k1 a k3. La validación, la parada temprana y la métrica son solo el MAE.

**Gradiente respecto del logit de la compuerta.** Con $a = u^\top z + c_0$ y $g = \sigma(a)$, para una observación,

$$
\frac{\partial \mathcal{L}}{\partial a} = \operatorname{sign}(\hat y - y)\, g(1-g)\, \bigl(h_{\text{int}} - h_{\text{norm}}\bigr) + \lambda\,(g - s)
$$ (eq-gradiente)

El primer término es el de la mezcla de Jacobs et al.: se anula cuando los dos expertos coinciden, y entonces la compuerta no aprende nada. Al inicio del entrenamiento los expertos son casi iguales, así que sin supervisión la compuerta puede quedarse sin separar los regímenes. El segundo término no depende de los expertos y empuja a g hacia el régimen observado desde la primera época. Esa es la razón de la modificación principal, y la ablación λ = 0 la pone a prueba.

## 5.4 Pseudocódigo

```text
Entrada: entrenamiento D = {(x, c, κ, y)}, bloque de parada P, tasa η, λ, lote B, paciencia 10
Salida: parámetros θ de la época con menor MAE en P
1. Inicializar θ con la semilla; mejor ← ∞
2. Para cada época hasta 100:
3.   Para cada lote {(x, c, κ, y)} de D, barajado con la semilla:
4.     z ← [r(x), c, e(κ)]                         # cuerpo de la CNN del E1
5.     g ← σ(uᵀz + c₀); ŷ ← g·h_int(z) + (1 − g)·h_norm(z)   # (eq-salida)
6.     s ← 1[y < ln 1,5]
7.     L ← MAE(ŷ, y) + λ·BCE(g, s)                 # (eq-perdida)
8.     θ ← Adam(θ, ∇θ L, η)
9.   m ← MAE en P (sin la BCE)
10.  Si m < mejor: mejor ← m; guardar θ. Si no mejora en 10 épocas: parar
11. Devolver el θ guardado
```

La corrida definitiva repite esto en dos etapas, como todos los rivales: la etapa 1 fija la mejor época E, y la etapa 2 reentrena con todo el entrenamiento durante E épocas.

## 5.5 Complejidad

Con las dimensiones del panel, el modelo original tiene 159.894 parámetros y la CNN del E1 137.534, es decir, 22.360 más (16,3 %). Son un experto más (22.017) y la compuerta (343). El cuerpo convolucional, con 99.840, es el mismo.

Por observación, el costo lo domina el cuerpo: $O(L\,T\,U^2 k)$ operaciones, con L = 3 bloques, T = 52, U = 128 y k = 3. Las cabezas agregan $O(d_z \cdot 64)$ cada una, así que la mezcla cuesta una fracción pequeña del total. Los tiempos medidos se informan con la definitiva.

## 5.6 Propiedad demostrada

El teorema 1 de [Cota de Lipschitz](lipschitz.md) acota la sensibilidad del pronóstico a la entrada en un dominio acotado, con las categorías fijas. Su corolario dice que la salida siempre queda entre los dos expertos. La verificación numérica está en las pruebas del capítulo 6. La cota es holgada por construcción (producto de normas espectrales); la sensibilidad real se informa con el cociente empírico de los modelos entrenados.
