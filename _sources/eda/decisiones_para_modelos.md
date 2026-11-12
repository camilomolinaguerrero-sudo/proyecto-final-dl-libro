# Decisiones del EDA para los modelos

Responsable: Camilo. Destinataria: Lina (`src/`). Estado: 2026-11-07.

Las cifras salen de `eda/resultados/series_resultados.json` (cuaderno `eda/03_eda_series.ipynb`) y de
`eda/resultados/aux_cobertura_envios.json` (script `eda/fuentes/aux_cobertura_envios.py`). Todo se
calculó solo con el entrenamiento (semanas objetivo hasta el 2024-10-21).

Este archivo propone. Si algo cambia una decisión de `DECISIONES.md`, se acuerda entre los dos antes
de editar ese archivo.

## 1. Longitud de la ventana: L = 52 semanas

La ventana de la fila (corredor c, semana t) cubre las semanas t−51 a t. Nunca incluye t+1.

Evidencia (`series_resultados.json`, claves `ventana` y `dependencia`):

| L | MAE en las últimas 52 semanas de entrenamiento |
| --- | --- |
| 1 | 0,3059 |
| 13 | 0,2946 |
| 26 | 0,2933 |
| 52 | 0,2916 |
| 104 | 0,2912 |

- Regresión ridge sobre la ventana normalizada más la máscara. Se ajustó con semanas objetivo hasta
  60 semanas antes del final del entrenamiento y se evaluó en las últimas 52, con un hueco de 8.
- Pasar de 52 a 104 semanas mejora el MAE solo en 0,0004 y reduce del 85,7 % al 81,7 % la fracción de
  semanas con dato en la ventana.
- La ACF agrupada de los corredores vuelve a subir entre los rezagos 40 y 53 (0,091 en el 52). Por eso
  una ventana de 26 deja por fuera el ciclo anual.
- Referencias en el mismo bloque de evaluación: persistencia MAE 0,3457 y mediana constante MAE 0,3103.
  La persistencia pierde frente a la constante. Esto importa para fijar δ, que según `DECISIONES.md` se
  define con el MAE de persistencia en validación.

Consecuencias para cada arquitectura:

- **LSTM o GRU:** 52 pasos.
- **CNN-1D:** campo receptivo de al menos 52, con dilataciones o *pooling*.
- **MLP:** recibe la ventana aplanada.

## 2. Política de faltantes de la ventana

El panel no es rectangular. Desde la primera aparición de cada corredor, el 46,8 % de sus semanas no
tiene fila en el panel, por dos causas:

| Causa | Fracción de las semanas de una ventana de 52 | Tratamiento |
| --- | --- | --- |
| Hay dato en el panel | 79,4 % | valor observado |
| Corredor fuera de la población en riesgo | 10,3 % | nivel 0, `observado = 0` |
| Semana anterior a la primera aparición del corredor (o antes de 2018) | 10,3 % | nivel 0, `observado = 0`, `iniciado = 0` |
| Mercado sin encuesta | 0,03 % | nivel 0, `observado = 0` |

Estas ausencias no son aleatorias. El corredor sale del panel porque su propio abastecimiento cae
(MNAR por construcción): las filas cuya semana siguiente ya no aparece tienen un 99,2 % de `y_reg = 0`.

Los envíos de DANE muestran qué hay en esas semanas. Dentro de una ventana de 52, el 72,3 % son ceros
y el resto son envíos positivos que el panel no registra. Por eso no basta con rellenar con cero: el
modelo necesita la máscara para distinguir un cero observado (17 % de las semanas con dato) de una
semana sin dato.

### Reconstrucción solo con el panel

Una corredor-semana τ tiene dato si existe la fila (c, τ), que aporta `tasa_t`, o la fila (c, τ−1),
que aporta `tasa_t1`.

Este método reproduce exactamente la tasa de los envíos donde ambos tienen dato (diferencia relativa
máxima de 0) y recupera el 86,0 % de lo que los envíos tienen dentro de una ventana de 52. No hace
falta el archivo de envíos: alcanza con `datos/panel_corredores.parquet`. La función `matriz_tasa` de
`eda/comun.py` lo implementa y la puedes copiar a `src/`.

### Canales propuestos por paso τ de la ventana

1. **`nivel`:** `log1p(tasa_τ / base8_t)` si hay dato y 0 si no. La normalización usa la `base8` de la
   fila, que solo depende del pasado. Así no hace falta ningún escalador ajustado con datos.
2. **`observado`:** 1 si hay dato en el panel.
3. **`iniciado`:** 1 si τ es igual o posterior a la primera aparición del corredor y τ ≥ 0.

Opcionales, si quieres contexto agregado:

4. **Fracción de caída del mercado en τ:** `frac_caida_mercado_t` de cualquier fila del mercado en τ.
5. **Fracción de caída nacional en τ:** `frac_caida_nacional_t`.

### Qué no hacer

- No interpolar ni rellenar hacia adelante. El relleno hacia adelante usa solo el pasado, pero
  convierte una semana sin envíos en una semana normal.
- No descartar filas con la ventana incompleta. El 53,2 % de las filas tiene la ventana completa y el
  19,3 % tiene menos de 26 semanas con dato. Esas filas se pronostican igual en la prueba.
- No imputar con estadísticos que incluyan semanas futuras.

## 3. Tipo de modelo y objetivo

- **Modelo global** para los 4.305 corredores del entrenamiento. El 41,6 % tiene menos de 52 semanas
  en riesgo, así que un modelo local no es viable para ellos, aunque solo aportan el 4,8 % de las filas.
- La media de cada corredor explica apenas el 6,0 % de la varianza de `y_reg`. Los *embeddings* de
  mercado y municipio de origen son opcionales; la información principal está en la ventana.
- `y_reg` se modela sin diferenciar. El 94,7 % de los corredores es estacionario según ADF y KPSS, y la
  serie nacional no tiene quiebres en la media.
- **Pérdida sugerida: L1 o Huber.** La métrica primaria es el MAE. La varianza condicional cambia en el
  tiempo (ARCH significativo en el 22,0 % de los corredores tras Holm), y el 1 % de filas con mayor
  error aporta el 13,0 % del MSE pero solo el 4,6 % del MAE.
- **Atípicos:** no se recortan. Se concentran en bases pequeñas (7,1 % del decil con menor `base8`), así
  que conviene mantener `log_base8` como covariable.

## 4. Riesgos de fuga que conviene probar en `src/`

1. El último paso de la ventana es t: `nivel[-1] == log1p(ratio_t)`. El cuaderno 03 lo verifica con
   una aserción.
2. La matriz corredor × semana contiene `tasa_t1`, que es el objetivo de la fila. La ventana debe
   cortarse en la columna t.
3. Entre el ajuste y la evaluación de cada origen debe haber un hueco de 8 semanas. `base8` y las
   variables de 26 semanas comparten semanas entre filas vecinas.
4. Cualquier escalador de covariables se ajusta solo con las filas de ajuste de cada origen.
5. La parada temprana usa un bloque posterior al ajuste y anterior a la evaluación. Nunca la prueba.

## 5. Covariables tabulares

Fuente: `eda/resultados/general_resultados.json` (cuaderno `eda/02_eda_general.ipynb`).

### Faltantes en columnas

Todos tienen una causa estructural y son informativos. Cada columna con faltantes se imputa con la
mediana de las filas de ajuste de cada origen y lleva un indicador de ausencia.

| Columna | Faltante en el entrenamiento | Causa |
| --- | --- | --- |
| `frac_caida_mpio_t` | 15,7 % | el municipio tiene un solo corredor esa semana |
| `frac_caida_vec_t` | 1,0 % | los 8 municipios vecinos no tienen corredores activos |
| `tasa_int_vec26` | 0,35 % | ídem, en las 26 semanas |
| `ratio_t_1` | 0,03 % | la semana anterior del corredor no está en el panel |

### Columnas que no pueden ser entradas

- Funciones del objetivo: `tasa_t1`, `ratio_t1`, `interrupcion_t1` y `y_reg`.
- `fila`: está ordenada por semana objetivo.
- `anio_t1` y `mes_t1`: codifican el tiempo absoluto. La estacionalidad ya entra con `sem_sin` y
  `sem_cos`.

### `antig_sem`

- Crece con el calendario: tiene un PSI de 4,1 entre el entrenamiento y la prueba, y en 2025 toma
  valores que el ajuste nunca vio.
- Tiene información mutua de 0,000 con `y_reg`.
- Recomendación: excluirla o recortarla al rango del ajuste de cada origen.

### Corredores nuevos

- El 6,4 % de los corredores de la prueba (1,6 % de sus filas) no aparece en el entrenamiento.
- Los *embeddings* de mercado y municipio necesitan un índice reservado para valores desconocidos.

### Hash de la prueba

- El oficial es el de `preregistro/hash_prueba.json` (`988ab7ac…2c78753`), calculado por
  `src/congelar_prueba.py` sobre todas las columnas crudas de las filas de 2025.
- El cuaderno 02 lo recalcula con `comun.hash_prueba_oficial` y verifica que coincide.
- La huella que el EDA usaba antes queda en `eda/resultados/huellas.json` como auxiliar. No
  reemplaza al hash oficial.

### Uso de 2025 en el EDA

- La comparación entre particiones miró el objetivo de la prueba: PSI de `y_reg` (0,004), media,
  mediana, ceros, interrupción y KS.
- Ninguna decisión de esta lista usa esas cifras, pero los autores las conocen. Se declaran en el
  preregistro.

### Validación de origen móvil

- Con solo las variables propias del corredor, el desplazamiento adversarial es 0,727 entre el
  comienzo y las últimas 52 semanas del entrenamiento, y 0,717 entre el entrenamiento y la prueba.
- Un origen interno se parece a la prueba, así que la validación de origen móvil es representativa.
