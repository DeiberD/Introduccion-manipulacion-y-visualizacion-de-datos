# Pandas más allá del taller

En el taller usamos Pandas para cargar, explorar, filtrar, ordenar y agrupar. Aquí tienes temas
para profundizar, en el orden en que suelen aparecer en un análisis real. Los ejemplos asumen que
ya cargaste el dataset:

```python
import pandas as pd
df = pd.read_csv("../data/housing.csv")
```

---

## 1. Cargar archivos que no son "perfectos"

`read_csv` tiene muchos parámetros. Los que más vas a necesitar:

```python
pd.read_csv("datos.csv", sep=";")             # separador punto y coma (común en Excel en español)
pd.read_csv("datos.csv", decimal=",")         # decimales con coma: 3,5 en lugar de 3.5
pd.read_csv("datos.csv", encoding="latin-1")  # si las tildes y la ñ salen como símbolos raros
pd.read_csv("datos.csv", usecols=["a", "b"])  # cargar solo algunas columnas
pd.read_csv("datos.csv", nrows=1000)          # cargar solo las primeras 1000 filas

pd.read_excel("datos.xlsx", sheet_name="Hoja1")   # Excel (requiere: pip install openpyxl)
pd.read_csv("https://...")                         # también funciona con una URL
```

---

## 2. Seleccionar con `loc` e `iloc`

Son las formas "profesionales" de seleccionar filas y columnas a la vez:

- **`loc`** → selecciona por **etiqueta** (nombre del índice o de la columna).
- **`iloc`** → selecciona por **posición** (número entero, como en una lista).

```python
df.loc[0, "median_house_value"]                          # fila con índice 0, una columna
df.loc[0:4, ["median_income", "median_house_value"]]     # filas 0 a 4 (¡incluye el 4!), dos columnas
df.loc[df["median_income"] > 10, "ocean_proximity"]      # filtro + columna en un solo paso

df.iloc[0, 8]       # fila 0, columna en la posición 8
df.iloc[:5, :3]     # primeras 5 filas, primeras 3 columnas
df.iloc[-1]         # última fila
```

> **Ojo:** con `loc` el final del rango **se incluye** (`0:4` son 5 filas); con `iloc` **no**
> (`:5` son las posiciones 0 a 4).

---

## 3. Filtros más expresivos

```python
# Varias condiciones: & (y), | (o), ~ (no). Cada condición entre paréntesis.
df[(df["median_income"] > 8) & (df["ocean_proximity"] == "INLAND")]

# isin: "que esté en esta lista"
df[df["ocean_proximity"].isin(["NEAR BAY", "NEAR OCEAN"])]

# between: "entre estos dos valores" (incluye los extremos)
df[df["housing_median_age"].between(10, 20)]

# query: lo mismo, escrito casi como una frase
df.query("median_income > 8 and ocean_proximity == 'INLAND'")
```

---

## 4. Crear columnas nuevas (variables derivadas)

Muchas veces la información útil no está en el dataset, sino en una **combinación** de columnas.
Gracias a NumPy, la operación se aplica a todas las filas a la vez:

```python
df["rooms_per_household"] = df["total_rooms"] / df["households"]       # habitaciones por hogar
df["bedroom_ratio"] = df["total_bedrooms"] / df["total_rooms"]         # proporción de dormitorios
df["people_per_household"] = df["population"] / df["households"]       # personas por hogar
df["valor_miles"] = df["median_house_value"] / 1000                    # cambiar de unidades
```

Y para convertir una variable numérica en **categorías** (por rangos):

```python
df["nivel_ingreso"] = pd.cut(df["median_income"],
                             bins=[0, 3, 6, 100],
                             labels=["bajo", "medio", "alto"])

df["cuartil_valor"] = pd.qcut(df["median_house_value"], q=4,
                              labels=["Q1", "Q2", "Q3", "Q4"])   # 4 grupos del mismo tamaño
```

`cut` usa los límites que tú le das; `qcut` los calcula para que cada grupo tenga la misma cantidad
de filas.

---

## 5. Limpieza de datos

En la vida real los datos llegan "sucios". Esta es la parte del trabajo que más tiempo consume.

### Valores nulos

```python
df.isnull().sum()                          # contar nulos por columna
df.isnull().mean() * 100                   # porcentaje de nulos por columna

df.dropna()                                # elimina las filas con algún nulo
df.dropna(subset=["total_bedrooms"])       # solo si el nulo está en esa columna

mediana = df["total_bedrooms"].median()
df["total_bedrooms"] = df["total_bedrooms"].fillna(mediana)   # rellenar con la mediana
```

> **Nota:** No hay una única respuesta correcta: eliminar es más simple pero pierde datos; rellenar
> conserva filas pero introduce valores "inventados". La decisión depende del problema. Si los
> nulos son pocos (aquí ~1%), cualquiera de las dos es razonable. Si son muchos, investiga **por
> qué** faltan antes de decidir.

### Duplicados

```python
df.duplicated().sum()        # cuántas filas están repetidas
df = df.drop_duplicates()    # eliminarlas
```

### Tipos de datos

```python
df.dtypes                                          # tipo de cada columna
df["population"] = df["population"].astype(int)    # convertir de tipo
pd.to_numeric(pd.Series(["1", "2", "tres"]), errors="coerce")   # [1, 2, NaN]: lo que no pueda convertir queda nulo
pd.to_datetime(pd.Series(["2024-01-15", "2024-02-20"]))          # texto a fechas
```

### Texto

Las columnas de texto tienen métodos especiales en `.str`:

```python
df["ocean_proximity"].str.lower()                  # minúsculas
df["ocean_proximity"].str.replace("<1H", "MENOS DE 1H")
df["ocean_proximity"].str.contains("OCEAN")        # True/False, útil para filtrar
df["ocean_proximity"].str.strip()                  # quita espacios al inicio y al final
```

### Renombrar columnas

```python
df_es = df.rename(columns={"median_house_value": "valor_vivienda",
                           "median_income": "ingreso"})
df_es.columns
```

`rename` (como casi todos los métodos de Pandas) **no modifica** `df`: devuelve una tabla nueva.
Si quieres conservar el cambio, guárdalo en una variable. Aquí usamos `df_es` para que el resto
de ejemplos, que usan los nombres originales, sigan funcionando.

---

## 6. `value_counts`: contar categorías

```python
df["ocean_proximity"].value_counts()                # conteo por categoría
df["ocean_proximity"].value_counts(normalize=True)  # como proporción (0 a 1)
df["ocean_proximity"].nunique()                     # cuántas categorías distintas hay
```

---

## 7. `groupby` a fondo

Ya vimos `groupby` con `.mean()` y con `.agg(["mean", "count"])`. Algunas variantes útiles:

```python
# Varias medidas para una columna
df.groupby("ocean_proximity")["median_house_value"].agg(["mean", "median", "min", "max", "count"])

# Medidas distintas para columnas distintas, con nombres propios
df.groupby("ocean_proximity").agg(
    valor_mediano=("median_house_value", "median"),
    ingreso_promedio=("median_income", "mean"),
    distritos=("median_house_value", "count"),
)

# Agrupar por VARIAS columnas (usando el nivel_ingreso de la sección 4)
df.groupby(["ocean_proximity", "nivel_ingreso"], observed=True)["median_house_value"].mean()
```

**¿Cómo leer un `groupby`?** "Para cada `ocean_proximity`, toma `median_house_value` y calcula
la media". Si puedes decir la frase, puedes escribir el código.

---

## 8. Tablas dinámicas: `pivot_table` y `crosstab`

Si has usado tablas dinámicas en Excel, esto te va a gustar. Organizan un `groupby` de dos
variables en forma de **tabla**:

```python
pd.pivot_table(df,
               values="median_house_value",
               index="ocean_proximity",       # filas
               columns="nivel_ingreso",       # columnas
               aggfunc="median",
               observed=True).round(0)
```

Con esta tabla puedes responder una pregunta más fina que en el taller: **a igual nivel de ingreso**,
¿sigue siendo más cara la costa? (Spoiler: sí).

`crosstab` es parecido, pero **cuenta** cuántas filas caen en cada combinación:

```python
pd.crosstab(df["ocean_proximity"], df["nivel_ingreso"])
pd.crosstab(df["ocean_proximity"], df["nivel_ingreso"], normalize="index")   # proporción por fila
```

---

## 9. Ordenar y quedarse con los "top"

```python
df.nlargest(10, "median_house_value")              # las 10 viviendas más caras
df.nsmallest(10, "median_income")                  # las 10 zonas de menor ingreso
df.sort_values(["ocean_proximity", "median_income"], ascending=[True, False])   # por dos columnas
```

---

## 10. `apply`: aplicar una función propia

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

> **Ten en cuenta:** `apply` recorre las filas una por una, así que es más lento que las
> operaciones vectorizadas. Para este caso concreto, `pd.cut` haría lo mismo más rápido. Usa
> `apply` cuando la lógica sea demasiado compleja para otra herramienta.

---

## 11. Unir tablas: `concat` y `merge`

Los datos casi nunca vienen en un solo archivo.

```python
# concat: "pegar" tablas una debajo de la otra (mismas columnas)
parte1 = df.iloc[:100]
parte2 = df.iloc[100:200]
juntas = pd.concat([parte1, parte2])

# merge: combinar tablas que comparten una columna (como un BUSCARV de Excel)
traduccion = pd.DataFrame({
    "ocean_proximity": ["<1H OCEAN", "INLAND", "NEAR OCEAN", "NEAR BAY", "ISLAND"],
    "zona": ["A menos de 1 h del mar", "Tierra adentro", "Cerca del mar", "Cerca de la bahía", "Isla"],
})
df_traducido = df.merge(traduccion, on="ocean_proximity", how="left")
df_traducido[["ocean_proximity", "zona"]].head()
```

`how="left"` conserva todas las filas de la tabla de la izquierda aunque no encuentren pareja.
Las otras opciones son `"inner"` (solo las que coinciden), `"right"` y `"outer"`.

---

## 12. Encadenar operaciones (*method chaining*)

En lugar de crear muchas variables intermedias, puedes encadenar los pasos. Se lee de arriba
abajo como una receta:

```python
resumen = (
    df
    .dropna(subset=["total_bedrooms"])
    .query("ocean_proximity != 'ISLAND'")
    .groupby("ocean_proximity")["median_house_value"]
    .median()
    .sort_values(ascending=False)
)
resumen
```

---

## 13. Guardar tus resultados

```python
df.to_csv("resultado.csv", index=False)     # a CSV
df.to_excel("resultado.xlsx", index=False)  # a Excel (requiere openpyxl)
```

`index=False` evita que se guarde una columna extra con el número de fila.

---

## Chuleta rápida

| Necesito... | Uso... |
|-------------|--------|
| Cargar un CSV | `pd.read_csv("archivo.csv")` |
| Ver las primeras filas | `df.head()` |
| Ver tipos y nulos | `df.info()` |
| Estadísticas | `df.describe()` |
| Una columna | `df["columna"]` |
| Varias columnas | `df[["col1", "col2"]]` |
| Filas y columnas por nombre | `df.loc[filas, columnas]` |
| Filas y columnas por posición | `df.iloc[filas, columnas]` |
| Filtrar | `df[df["col"] > valor]` |
| Filtrar por lista | `df[df["col"].isin([...])]` |
| Ordenar | `df.sort_values("col")` |
| Agrupar | `df.groupby("col")["otra"].mean()` |
| Tabla dinámica | `pd.pivot_table(df, values=..., index=..., columns=...)` |
| Contar categorías | `df["col"].value_counts()` |
| Nulos por columna | `df.isnull().sum()` |
| Rellenar nulos | `df["col"].fillna(valor)` |
| Quitar duplicados | `df.drop_duplicates()` |
| Columna nueva | `df["nueva"] = ...` |
| Numérica a categorías | `pd.cut(...)` / `pd.qcut(...)` |
| Renombrar | `df.rename(columns={...})` |
| Unir dos tablas | `df.merge(otra, on="col")` |
| Guardar | `df.to_csv("archivo.csv", index=False)` |

---

## Mini-retos

**1.** ¿Cuántos distritos `INLAND` tienen un ingreso medio mayor a 8 (80.000 USD)?

<details>
<summary>Ver solución</summary>

```python
len(df[(df["ocean_proximity"] == "INLAND") & (df["median_income"] > 8)])   # 56
```
</details>

**2.** ¿Cuál es el valor **mediano** de vivienda y el número de distritos por categoría de
`ocean_proximity`? Ordena de mayor a menor valor.

<details>
<summary>Ver solución</summary>

```python
(df.groupby("ocean_proximity")["median_house_value"]
   .agg(["median", "count"])
   .sort_values("median", ascending=False))
```
</details>

**3.** Crea la columna `people_per_household`. ¿Qué distrito tiene el valor más alto? ¿Te parece
un dato real o un error? (Pista: míralo con `nlargest`.)

<details>
<summary>Ver solución</summary>

```python
df["people_per_household"] = df["population"] / df["households"]
df.nlargest(3, "people_per_household")[["population", "households", "people_per_household"]]
```
Hay un distrito con más de **1.200 personas por hogar**: casi seguro es un error de captura o un
caso especial (por ejemplo, una institución). Detectar estos valores atípicos es parte del análisis.
</details>

**4.** Con una tabla dinámica, compara el valor mediano por `ocean_proximity` y `nivel_ingreso`.
¿La costa sigue siendo más cara cuando comparas zonas con el mismo nivel de ingreso?

<details>
<summary>Ver solución</summary>

```python
df["nivel_ingreso"] = pd.cut(df["median_income"], bins=[0, 3, 6, 100],
                             labels=["bajo", "medio", "alto"])
pd.pivot_table(df, values="median_house_value", index="ocean_proximity",
               columns="nivel_ingreso", aggfunc="median", observed=True).round(0)
```
Sí: en cada nivel de ingreso, `INLAND` sigue estando muy por debajo de las zonas costeras. La
cercanía al océano influye **además** del ingreso, no solo porque en la costa viva gente con más
dinero.
</details>

---

## Para seguir

- Documentación oficial: https://pandas.pydata.org/docs/
- "10 minutes to pandas": https://pandas.pydata.org/docs/user_guide/10min.html
- Guía de usuario completa: https://pandas.pydata.org/docs/user_guide/index.html
- Libro gratuito del creador de Pandas, *Python for Data Analysis*: https://wesmckinney.com/book/
