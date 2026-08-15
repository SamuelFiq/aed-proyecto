# AED Proyecto

Análisis Exploratorio de Datos (AED) en R sobre esperanza de vida y factores
socioeconómicos y de salud.

## Sobre los datos

La base es **Life Expectancy Data (Updated)**, publicada en Kaggle:
https://www.kaggle.com/datasets/lashagoch/life-expectancy-who-updated

Contiene 21 variables con indicadores de salud, inmunización, economía y
demografía para 179 países, entre 2000 y 2015 (2864 filas), construida a
partir de datos de la OMS (WHO) y el Banco Mundial.

## Estructura

- `data/Life-Expectancy-Data-Updated.csv` — dataset original, tal como se
  descarga de Kaggle.
- `data/Life-Expectancy-Data-Clean.csv` — dataset limpio, generado al correr
  `proyecto_aed.R` (no se versiona a mano, se recrea corriendo el script).
- `proyecto_aed.R` — script de limpieza y exploración en R.

## Qué hace `proyecto_aed.R`

El script parte del csv original y lo deja listo para análisis posterior.
En orden:

1. **Carga y exploración inicial**: lee el csv, revisa estructura
   (`str`, `summary`), valores faltantes por columna, y cobertura de países
   y años.
2. **Unifica el status económico**: junta las columnas dummy
   `Economy_status_Developed` / `Economy_status_Developing` en una sola
   columna categórica `Status` (`Developed` / `Developing`), ubicada
   después de `Region`.
3. **Ordena la base** por país y año.
4. **Corrige el error del "cero perdido"** en `Thinness_ten_nineteen_years`
   y `Thinness_five_nine_years`: algunos valores de dos dígitos quedaron
   guardados como si fueran de un dígito (ej. 20.3 guardado como 2.3). El
   script detecta esto automáticamente comparando cada valor con los de su
   mismo país en años cercanos, y lo corrige cuando corresponde.
5. **Corrige puntualmente Eritrea 2015**: `GDP_per_capita` y
   `Population_mln` tenían un salto sin explicación lógica para ese
   país/año; se reemplazan por los valores reales reportados por el Banco
   Mundial.
6. **Crea variables cualitativas nuevas por terciles**: convierte
   `GDP_per_capita`, `Schooling` y `Adult_mortality` en columnas
   `_cat` (`Bajo` / `Medio` / `Alto`), sin borrar las columnas numéricas
   originales.
7. **Exporta** la base resultante a
   `data/Life-Expectancy-Data-Clean.csv`.

## Diccionario de variables

Descripciones conceptuales según la documentación original del dataset en
Kaggle.

| Variable | Descripción | Tipo de dato | Escala de medición |
|---|---|---|---|
| `Country` | Nombre del país (179 países). | Texto (character) | Nominal |
| `Region` | Región geográfica del país (9 regiones: Africa, Asia, European Union, Rest of Europe, Middle East, North America, South America, Central America and Caribbean, Oceania). | Texto (character) | Nominal |
| `Status` | Clasificación económica del país: `Developed` o `Developing`. Unifica las dummies originales `Economy_status_Developed` / `Economy_status_Developing`. | Texto (character) | Nominal |
| `Year` | Año de la observación (2000–2015). | Numérico entero (integer) | Intervalo |
| `Infant_deaths` | Muertes de infantes (menores de 1 año) por cada 1000 habitantes. | Numérico (double) | Razón |
| `Under_five_deaths` | Muertes de menores de 5 años por cada 1000 habitantes. | Numérico (double) | Razón |
| `Adult_mortality` | Probabilidad de morir entre los 15 y 60 años, por cada 1000 habitantes. | Numérico (double) | Razón |
| `Adult_mortality_cat` | Tercil de `Adult_mortality`: `Bajo` / `Medio` / `Alto`. | Factor ordenado | Ordinal |
| `Alcohol_consumption` | Consumo de alcohol puro registrado, en litros per cápita, en personas de 15 años o más. | Numérico (double) | Razón |
| `Hepatitis_B` | % de cobertura de inmunización contra Hepatitis B (HepB3) en niños de 1 año. | Numérico (double) | Razón |
| `Measles` | % de cobertura de inmunización contra sarampión (MCV1) en niños de 1 año. | Numérico (double) | Razón |
| `BMI` | Índice de masa corporal promedio de la población adulta (kg/m²). | Numérico (double) | Razón |
| `Polio` | % de cobertura de inmunización contra polio (Pol3) en niños de 1 año. | Numérico (double) | Razón |
| `Diphtheria` | % de cobertura de inmunización DTP3 (difteria, tétanos, tos ferina) en niños de 1 año. | Numérico (double) | Razón |
| `Incidents_HIV` | Incidencia de VIH por cada 1000 habitantes de 15 a 49 años. | Numérico (double) | Razón |
| `GDP_per_capita` | PIB per cápita, en dólares estadounidenses corrientes. | Numérico (double) | Razón |
| `GDP_per_capita_cat` | Tercil de `GDP_per_capita`: `Bajo` / `Medio` / `Alto`. | Factor ordenado | Ordinal |
| `Population_mln` | Población total del país, en millones. | Numérico (double) | Razón |
| `Thinness_ten_nineteen_years` | Prevalencia de delgadez (BMI < -2 desviaciones estándar de la mediana, referencia OMS) en adolescentes de 10 a 19 años, en %. | Numérico (double) | Razón |
| `Thinness_five_nine_years` | Prevalencia de delgadez en niños de 5 a 9 años, en %. | Numérico (double) | Razón |
| `Schooling` | Años promedio de escolaridad formal en personas de 25 años o más. | Numérico (double) | Razón |
| `Schooling_cat` | Tercil de `Schooling`: `Bajo` / `Medio` / `Alto`. | Factor ordenado | Ordinal |
| `Life_expectancy` | Esperanza de vida promedio al nacer (ambos sexos), en años. Variable objetivo del análisis. | Numérico (double) | Razón |

`Economy_status_Developed` y `Economy_status_Developing` (dummies 0/1 del
dataset original) se eliminan del resultado final: quedan representadas por
`Status`.
