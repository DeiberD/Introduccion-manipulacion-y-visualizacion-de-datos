# Pandas más allá del taller

En el taller usamos Pandas para cargar, explorar, filtrar, ordenar y agrupar. Aquí tienes temas
para profundizar. Los ejemplos asumen que ya cargaste el dataset:

```python
import pandas as pd
df = pd.read_csv("../data/housing.csv")
```

---

## 1. Seleccionar con `loc` e `iloc`

Son las formas "profesionales" de seleccionar filas y columnas:

- **`loc`** → selecciona por **etiqueta** (nombre de índice o columna).
- **`iloc`** → selecciona por **posición** (número entero).

```python
df.loc[0, "median_house_value"]     # valor de la fila con índice 0, columna dada
df.loc[0:4, ["median_income", "median_house_value"]]   # filas 0 a 4, dos columnas
df.iloc[0, 8]                       # fila 0, columna en la posición 8
df.iloc[:5, :3]                     # primeras 5 filas, primeras 3 columnas
```

---

## 2. Crear columnas nuevas (variables derivadas)

Muchas veces la información útil no está en el dataset, sino en una **combinación** de columnas:

```python
# Habitaciones por hogar
df["rooms_per_household"] = df["total_rooms"] / df["households"]

# Proporción de dormitorios sobre habitaciones
df["bedroom_ratio"] = df["total_bedrooms"] / df["total_rooms"]

# Personas por hogar
df["people_per_household"] = df["population"] / df["households"]
```

---

## 3. Tratar valores nulos

Detectarlos, eliminarlos o rellenarlos:

```python
df.isnull().sum()                 # contar nulos por columna

df.dropna()                       # elimina las filas con algún nulo
df.dropna(subset=["total_bedrooms"])   # solo si el nulo está en esa columna

mediana = df["total_bedrooms"].median()
df["total_bedrooms"] = df["total_bedrooms"].fillna(mediana)   # rellenar con la mediana
```

> 💡 No hay una única respuesta correcta: eliminar es más simple pero pierde datos; rellenar
> conserva filas pero introduce valores "inventados". La decisión depende del problema.

---

## 4. `value_counts`: contar categorías

Para variables categóricas, cuenta cuántas veces aparece cada valor:

```python
df["ocean_proximity"].value_counts()               # conteo por categoría
df["ocean_proximity"].value_counts(normalize=True) # como proporción (0 a 1)
```

---

## 5. `groupby` con varias medidas

Ya vimos `groupby` con `.mean()`. Puedes calcular varias medidas a la vez con `.agg()`:

```python
df.groupby("ocean_proximity")["median_house_value"].agg(["mean", "median", "min", "max", "count"])
```

Y agrupar por **varias** columnas:

```python
# Primero creamos una categoría de ingreso
df["nivel_ingreso"] = pd.cut(df["median_income"],
                             bins=[0, 3, 6, 100],
                             labels=["bajo", "medio", "alto"])

df.groupby(["ocean_proximity", "nivel_ingreso"], observed=True)["median_house_value"].mean()
```

---

## 6. Ordenar y quedarse con los "top"

```python
df.nlargest(10, "median_house_value")    # las 10 viviendas más caras
df.nsmallest(10, "median_income")        # las 10 zonas de menor ingreso
```

---

## 7. `apply`: aplicar una función personalizada

Cuando necesitas una transformación que no existe de fábrica:

```python
def categoria_valor(valor):
    if valor < 150000:
        return "económica"
    elif valor < 350000:
        return "media"
    else:
        return "cara"

df["categoria"] = df["median_house_value"].apply(categoria_valor)
df["categoria"].value_counts()
```

---

## 8. Guardar tus resultados

```python
df.to_csv("resultado.csv", index=False)     # a CSV
df.to_excel("resultado.xlsx", index=False)  # a Excel (requiere openpyxl)
```

---

## Chuleta rápida (cheat sheet)

| Necesito... | Uso... |
|-------------|--------|
| Cargar un CSV | `pd.read_csv("archivo.csv")` |
| Ver las primeras filas | `df.head()` |
| Ver tipos y nulos | `df.info()` |
| Estadísticas | `df.describe()` |
| Una columna | `df["columna"]` |
| Varias columnas | `df[["col1", "col2"]]` |
| Filtrar | `df[df["col"] > valor]` |
| Ordenar | `df.sort_values("col")` |
| Agrupar | `df.groupby("col")["otra"].mean()` |
| Contar categorías | `df["col"].value_counts()` |
| Nulos por columna | `df.isnull().sum()` |
| Rellenar nulos | `df["col"].fillna(valor)` |
| Columna nueva | `df["nueva"] = ...` |

---

## Para seguir

- Documentación oficial: https://pandas.pydata.org/docs/
- "10 minutes to pandas": https://pandas.pydata.org/docs/user_guide/10min.html
- Guía de usuario completa: https://pandas.pydata.org/docs/user_guide/index.html
