# Declaración de datos: panel de corredores SIPSA-A 2018-2025

Sigue las secciones de Bender y Friedman (2018), adaptadas a datos tabulares. Cada cifra sale de un
archivo del repositorio, citado al lado.

## 1. Motivación

El panel se armó para pronosticar, con una semana de anticipación, el abastecimiento de cada corredor
de alimentos (municipio de origen → central mayorista) y detectar las semanas en que cae a menos de la
mitad de lo habitual. Lo armaron Lina Margarita Buelvas y Camilo Molina Guerrero para el Entregable 1
del curso de Machine Learning (repositorio `ml-sipsa-abastecimiento`, commit `7478ac9`), y aquí se
reutiliza sin cambios.

## 2. Fuente

- **Microdatos:** Departamento Administrativo Nacional de Estadística (DANE), Sistema de Información
  de Precios y Abastecimiento del Sector Agropecuario, componente de abastecimiento (SIPSA-A),
  catálogo de microdatos 697 (<https://microdatos.dane.gov.co/index.php/catalog/697>): 18 ZIP
  semestrales de 2018 a 2025, 1,81 GB (`src/armado_panel/README.md`).
- **Registros usados:** 12.675.912 envíos (`eda/resultados/aux_cobertura_envios.json`).
- **Auxiliares:** DIVIPOLA con coordenadas y municipios PDET, de datos.gov.co, con licencia
  CC BY-SA 4.0 (`src/armado_panel/raw/`).

El DANE recoge SIPSA-A con encuestas en las centrales mayoristas: registra el origen, el producto y
los kilos de cada carga que entra. El proyecto no controla ni el diseño de la encuesta ni los días en
que se encuesta cada mercado.

## 3. Curaduría

`src/armado_panel/01_descarga_consolidacion.py` consolida los ZIP en un registro por envío y
`02_construir_panel.py` arma el panel:

- **Unidad:** el corredor (mercado destino, municipio de origen) en la semana t, de lunes a domingo.
  Se excluyen las importaciones.
- **Tasa:** kilos por día encuestado del mercado. `base8` es el promedio de las 8 semanas previas.
- **Filas elegibles:**
  - el mercado se encuestó en t+1;
  - el corredor tuvo abastecimiento en al menos 4 de las 8 semanas previas;
  - `base8 > 0`.
- **Objetivo:** `y_reg = log1p(tasa_t1 / base8)`. La interrupción es `y_reg < ln 1,5`, es decir, una
  razón menor que 0,5.
- **Variables:** solo usan información disponible al cierre de la semana t. La auditoría de fuga está
  en `auditoria/`.

Las 16 reglas de consistencia de `eda/resultados/general_resultados.json` (rangos, definiciones del
objetivo, fechas y participaciones) tienen 0 violaciones. Tampoco hay claves ni filas duplicadas.

## 4. Lo que contiene

Fuente de este apartado: `eda/resultados/problema_descripcion.json` y `general_resultados.json`.

| Aspecto | Valor |
| --- | --- |
| Filas | 744.921 |
| Columnas | 44 |
| Corredores | 4.512 |
| Mercados | 32 |
| Municipios de origen | 925, en 30 departamentos |
| Semanas objetivo | 414, de 2018-01-29 a 2025-12-29 |
| Filas por corredor | mediana de 93 |
| SHA-256 del panel | `30ead68472fc5718bf4c2a308db8e854a3f1329923c6f55adb6194da6b54b93d` |

| Partición | Semanas objetivo | Filas | Corredores |
| --- | --- | --- | --- |
| Entrenamiento | 2018-01-29 a 2024-10-21 | 622.244 | 4.305 |
| Hueco | 2024-10-28 a 2024-12-30 | 19.211 | 2.258 |
| Prueba | 2025-01-06 a 2025-12-29 | 103.466 | 3.009 |

En el entrenamiento, el 28,2 % de las filas son interrupciones y el 17,6 % tienen `y_reg = 0`, es
decir, nada de abastecimiento en t+1.

## 5. Representación y sesgos conocidos

- **Concentración en pocos mercados:** Corabastos (Bogotá) aporta el 14,8 % de las filas y la Central
  Mayorista de Antioquia, el 8,9 %. El error medio del modelo pesa más en esos mercados.
- **Población condicionada a la regularidad:** el filtro de 4 de 8 semanas deja fuera los corredores
  esporádicos. En los envíos hay 750.687 celdas corredor-semana con dato que no entran al panel
  (`aux_cobertura_envios.json`). Los pronósticos no dicen nada de esos corredores.
- **Días encuestados:** con 1 día encuestado en t+1, el 80 % de las filas son interrupciones. La
  medición es más ruidosa cuando la central se encuestó pocos días.
- **Dependencia espacial:** el I de Moran entre municipios es 0,185 (p de permutación 0,001; 779
  municipios). Los corredores vecinos no son independientes, y por eso la inferencia usa el corredor
  y la semana como bloques.
- **Cambio en el tiempo:**
  - el inicio del entrenamiento y sus últimas 52 semanas se distinguen casi perfectamente (AUC 0,9999);
  - las variables con más desplazamiento son `antig_sem` (PSI 4,03) y `frac_caida_nacional_t` (PSI 3,80);
  - en la prueba, el 6,4 % de los corredores (el 1,6 % de las filas) no aparece en el entrenamiento.
- **Reuso de la prueba:** 2025 ya se usó como prueba en el curso de Machine Learning (`DECISIONES.md`).

## 6. Datos personales y uso

El panel no tiene identificadores de personas, de vehículos ni de comerciantes: agrega por corredor y
semana. La variable más fina es el municipio de origen.

Los microdatos y el panel no están en el repositorio. Cualquiera puede reconstruirlos desde el
catálogo 697 con `python3 src/descargar_datos.py --dane`; el 2026-11-11 el panel salió idéntico byte
a byte.

Los microdatos se rigen por las condiciones de uso del DANE, y las tablas auxiliares por CC BY-SA 4.0.
La licencia MIT del repositorio cubre solo el código.

## Referencia

Bender, E. M. y Friedman, B. (2018). Data Statements for Natural Language Processing: Toward
Mitigating System Bias and Enabling Better Science. *Transactions of the Association for
Computational Linguistics*, 6, 587-604. <https://doi.org/10.1162/tacl_a_00041>
