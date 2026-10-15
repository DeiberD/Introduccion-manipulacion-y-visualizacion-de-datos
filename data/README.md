# Conjunto de datos — California Housing Prices

Este es el dataset que usamos durante todo el taller.

## Descripción general

Contiene información de **distritos residenciales de California** basada en el **censo de Estados
Unidos de 1990**. Cada fila representa un *block group* (la unidad geográfica más pequeña del censo,
que agrupa típicamente entre 600 y 3.000 personas) al que aquí llamamos, de forma simplificada, un
"distrito".

- **Filas (registros):** 20.640
- **Columnas (variables):** 10
- **Formato:** CSV (valores separados por comas)
- **Archivo:** [`housing.csv`](housing.csv)

## Diccionario de datos

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `longitude` | numérica | Longitud geográfica del distrito (más negativo = más al oeste). |
| `latitude` | numérica | Latitud geográfica del distrito (más alto = más al norte). |
| `housing_median_age` | numérica | Antigüedad **media** de las viviendas del distrito, en años. |
| `total_rooms` | numérica | Número total de habitaciones en el distrito. |
| `total_bedrooms` | numérica | Número total de dormitorios. **Contiene 207 valores nulos.** |
| `population` | numérica | Población total del distrito. |
| `households` | numérica | Número de hogares (grupos de personas que viven en una misma vivienda). |
| `median_income` | numérica | Ingreso **medio** de los hogares del distrito, expresado en **decenas de miles de dólares** (p. ej. `8.3252` ≈ 83.252 USD). |
| `median_house_value` | numérica | **Valor medio de las viviendas** del distrito, en dólares. Es nuestra variable de interés. Está **recortada a 500.001** (todos los valores mayores aparecen como ese tope). |
| `ocean_proximity` | categórica | Cercanía al océano. Categorías: `<1H OCEAN`, `INLAND`, `NEAR OCEAN`, `NEAR BAY`, `ISLAND`. |

## Detalles útiles para el análisis

- **Valores faltantes:** solo `total_bedrooms` los tiene (207). Es un buen caso para practicar su
  detección (`isnull()`) y tratamiento (`fillna()`).
- **Variable categórica:** `ocean_proximity` es perfecta para agrupar (`groupby`) y para comparar
  grupos con un `boxplot`.
- **Tope artificial:** `median_house_value` está recortada en 500.001. Esto se nota como un "pico"
  en el extremo derecho de su histograma; es un buen ejemplo de por qué conviene **graficar** los datos.
- **Correlación notable:** `median_income` es la variable que mejor se correlaciona con
  `median_house_value` (≈ 0.69).

## Origen y descarga

El dataset proviene originalmente del artículo:

> Pace, R. Kelley & Ronald Barry (1997). *Sparse Spatial Autoregressions*.
> Statistics & Probability Letters, 33(3), 291–297.

Se popularizó a través del libro *Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow*
de Aurélien Géron, y hoy es un dataset de referencia para aprender análisis de datos.

- **Enlace de descarga directa (CSV):**
  https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv

- **También disponible en Kaggle:**
  https://www.kaggle.com/datasets/camnugent/california-housing-prices

> **Nota:** El archivo `housing.csv` ya está incluido en este repositorio, así que **no necesitas
> descargarlo aparte** para hacer el taller. Los enlaces son solo por transparencia y para quien
> quiera la fuente original.

## Cargarlo en Python

```python
import pandas as pd

# Si trabajas desde la carpeta notebooks/:
df = pd.read_csv("../data/housing.csv")

# O directamente desde la URL (requiere internet):
url = "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
df = pd.read_csv(url)

print(df.shape)   # (20640, 10)
df.head()
```
