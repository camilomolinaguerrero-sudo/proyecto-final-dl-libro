# Propiedad demostrada: cota de Lipschitz del modelo original

**Borrador de Camilo del 2026-11-26, pendiente de revisión de Lina.** Sección 3 de
`experiments/protocolo.md`. El código está en `src/modelo_original.py` (`cota_lipschitz`,
`cociente_empirico`), y la verificación numérica en `src/pruebas/test_modelo_original.py`.

## 1. El modelo en modo de evaluación

Con el modelo en evaluación, el *dropout* es la identidad. Para una observación con ventana
x ∈ ℝ^{T×C} (T = 52 semanas, C = 3 canales), covariables c ∈ ℝ^p y categorías κ:

1. **Cuerpo.** Son L bloques. El bloque ℓ hace relleno causal de k − 1 ceros, una convolución con
   pesos W_ℓ ∈ ℝ^{U×C_ℓ×k} y sesgo, y una ReLU. La salida es M(x) ∈ ℝ^{U×T}. En el modelo,
   k = 3, U = 128 y L = 3.
2. **Representación.** r(x) = [ (1/T) Σ_t M_{·t}(x) , M_{·T}(x) ] ∈ ℝ^{2U}, es decir, el promedio
   global junto con la última semana.
3. **Entrada de la cabeza.** z = [r(x), c, e(κ)], donde e(κ) es la concatenación de los
   *embeddings* de las cuatro categóricas.
4. **Expertos.** h_i(z) = a_iᵀ ReLU(A_i z + b_i) + β_i, para i ∈ {int, norm}.
5. **Compuerta.** g(z) = σ(uᵀz + c₀), con σ la logística.
6. **Salida.** f = g·h_int + (1 − g)·h_norm.

Normas: ‖·‖ es la euclídea en vectores y la de Frobenius en matrices de datos, y ‖·‖₂ es la norma
espectral de una matriz de pesos. Los canales «observado» e «iniciado» son 0/1 en los datos, pero la
cota vale para cualquier valor real de la entrada.

## 2. Lemas

**Lema 1 (convolución causal).** Sea K(x) la convolución con relleno causal de k − 1 ceros y pesos
W, con W̃ ∈ ℝ^{U×(C·k)} su matriz aplanada. Entonces ‖K(x) − K(x′)‖ ≤ √k ‖W̃‖₂ ‖x − x′‖.

*Demostración.* El sesgo se cancela en la diferencia. Para cada t, la salida es
K(x)_{·t} = W̃ w_t(x), donde w_t(x) ∈ ℝ^{C·k} es la ventana de x en las semanas t − k + 1, …, t,
con ceros antes de la primera semana. Por linealidad, w_t(x) − w_t(x′) = w_t(x − x′), así que

‖K(x) − K(x′)‖² = Σ_t ‖W̃ w_t(x − x′)‖² ≤ ‖W̃‖₂² Σ_t ‖w_t(x − x′)‖² ≤ k ‖W̃‖₂² ‖x − x′‖².

La última desigualdad vale porque cada semana s de x − x′ aparece en a lo sumo k ventanas, y las
semanas del relleno valen cero en la diferencia. Permutar columnas de W̃ no cambia su norma
espectral, así que el orden del aplanado no importa. ∎

**Lema 2 (representación).** Sea L_M = Π_ℓ √k ‖W̃_ℓ‖₂. Entonces
‖r(x) − r(x′)‖ ≤ L_r ‖x − x′‖, con L_r = L_M · √(1 + 1/T).

*Demostración.* La ReLU es 1-Lipschitz componente a componente, y la composición multiplica las
constantes. Con el lema 1 queda ‖M(x) − M(x′)‖ ≤ L_M ‖x − x′‖. Sea Δ = M(x) − M(x′).

- Por Cauchy–Schwarz, ‖(1/T) Σ_t Δ_{·t}‖² ≤ (1/T) Σ_t ‖Δ_{·t}‖² = ‖Δ‖²/T.
- Además, ‖Δ_{·T}‖² ≤ ‖Δ‖².

Al sumar las dos partes, ‖r(x) − r(x′)‖² ≤ (1 + 1/T)‖Δ‖². ∎

**Lema 3 (piezas de la cabeza).**

- h_i es L_i-Lipschitz en z, con L_i = ‖a_i‖ ‖A_i‖₂.
- g es L_g-Lipschitz, con L_g = ‖u‖/4.
- Con las categorías fijas, ‖z − z′‖ ≤ max(L_r, 1) ‖(x, c) − (x′, c′)‖.

*Demostración.*

- Para h_i: la ReLU es 1-Lipschitz, y |aᵀv − aᵀv′| ≤ ‖a‖ ‖v − v′‖.
- Para g: σ′ ≤ 1/4, y |uᵀz − uᵀz′| ≤ ‖u‖ ‖z − z′‖.
- Para z: con e fijo, ‖z − z′‖² = ‖r − r′‖² + ‖c − c′‖² ≤ L_r²‖x − x′‖² + ‖c − c′‖², y esto es a lo
  sumo max(L_r, 1)² (‖x − x′‖² + ‖c − c′‖²). ∎

## 3. Teorema

**Teorema 1.** Fijemos un centro (x₀, c₀) y radios ρ_x y ρ_c, y sea
X = {(x, c) : ‖x − x₀‖ ≤ ρ_x, ‖c − c₀‖ ≤ ρ_c}. Definimos:

- E = (Σ_j max_m ‖E_j[m]‖²)^{1/2}, la mayor norma posible de e(κ);
- z₀ = [r(x₀), c₀, 0];
- R_z = ( (L_r ρ_x)² + ρ_c² + E² )^{1/2};
- D = |h_int(z₀) − h_norm(z₀)| + (L_int + L_norm) R_z.

Entonces, para cualquier categoría κ fija y cualesquiera (x, c) y (x′, c′) en X,

|f(x, c, κ) − f(x′, c′, κ)| ≤ max(L_r, 1) · [ max(L_int, L_norm) + L_g · D ] · ‖(x, c) − (x′, c′)‖.

Con la compuerta fija en a ∈ [0, 1] (ablación), la constante es
max(L_r, 1) · [ a L_int + (1 − a) L_norm ].

*Demostración.* Escribimos z = z(x, c, κ), z′ = z(x′, c′, κ) y Δh = h_int − h_norm. Sumando y restando,

f(z) − f(z′) = g(z)[h_int(z) − h_int(z′)] + (1 − g(z))[h_norm(z) − h_norm(z′)] + [g(z) − g(z′)] Δh(z′).

1. Los dos primeros términos son una combinación convexa, porque g ∈ (0, 1). Por el lema 3, su valor
   absoluto es a lo sumo max(L_int, L_norm) ‖z − z′‖.
2. Para el tercero, |g(z) − g(z′)| ≤ L_g ‖z − z′‖. Falta acotar |Δh(z′)| en la imagen de X. Para
   cualquier z′ de esa imagen, ‖z′ − z₀‖² = ‖r(x′) − r(x₀)‖² + ‖c′ − c₀‖² + ‖e(κ)‖² ≤ R_z², por el
   lema 2 y la definición de E. Como Δh es (L_int + L_norm)-Lipschitz,
   |Δh(z′)| ≤ |Δh(z₀)| + (L_int + L_norm) R_z = D.
3. Sumando, |f(z) − f(z′)| ≤ [max(L_int, L_norm) + L_g D] ‖z − z′‖. El lema 3 lleva ‖z − z′‖ a la
   entrada.

Con la compuerta fija, el tercer término es cero y los dos primeros se acotan con a y 1 − a. ∎

**Corolario 1 (la salida es una mezcla convexa).** Para toda entrada,
min(h_int, h_norm) ≤ f ≤ max(h_int, h_norm), porque g ∈ (0, 1). El modelo no extrapola fuera del
rango de sus dos expertos.

## 4. Supuestos y alcance

- La cota vale en modo de evaluación, que es el que pronostica. En entrenamiento, el *dropout* escala
  por 1/(1 − p) y la cota no aplica.
- Es local a X porque los expertos no son acotados en todo ℝ^d. Sin un dominio acotado, el término
  [g(z) − g(z′)]Δh(z′) no se controla. Con la compuerta fija, la cota es global.
- La constante es en las entradas continuas, con las categorías fijas. Las categorías son discretas, y
  solo entran en D a través de E, que cubre todas las combinaciones.
- Es una cota superior por producto de normas, y es holgada. En la prueba con pesos al azar
  (16 unidades, 2 bloques), la cota es varios órdenes de magnitud mayor que el cociente empírico.
  Sirve como garantía, no como estimación de la sensibilidad. La sensibilidad real se informa con el
  cociente empírico.

## 5. Verificación numérica

- `cota_lipschitz(modelo, centro, ρ_x, ρ_c)` calcula cada pieza a partir de los pesos entrenados.
- `cociente_empirico(modelo, a, b)` calcula max |f(a) − f(b)| / ‖a − b‖ sobre pares con las mismas
  categorías.
- `test_la_cota_de_lipschitz_acota_el_cociente_empirico` comprueba que el cociente empírico ≤ cota,
  con y sin compuerta fija.
- `test_lema_de_la_convolucion_causal` comprueba el lema 1 por separado.

Para el informe, con cada semilla de la definitiva (12), el procedimiento es:

1. Centro (x₀, c₀): la mediana del entrenamiento.
2. ρ_x y ρ_c: el máximo de las distancias de las ventanas del entrenamiento a ese centro.
3. Pares: 10.000 observaciones del entrenamiento, cada una con una perturbación suya que no salga de
   X. Se usa el entrenamiento para no tocar 2025 fuera de su evaluación única.
4. Se informan la cota, sus piezas y el cociente empírico.
