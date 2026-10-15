# Visualización más allá del taller

En el taller vimos histogramas, dispersión, boxplots, el mapa de California y el mapa de calor de
correlaciones. Aquí tienes más tipos de gráficas, la forma "profesional" de usar Matplotlib y
consejos para comunicar mejor. Los ejemplos asumen:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

sns.set_theme(style="whitegrid")
df = pd.read_csv("../data/housing.csv")
```

---

## 1. Elegir el gráfico correcto

Antes de escribir código, pregúntate **qué quieres mostrar**:

| ¿Qué quiero mostrar? | Gráfico recomendado | Función |
|----------------------|---------------------|---------|
| Cómo se distribuye **una** variable numérica | Histograma / densidad | `histplot`, `kdeplot` |
| Relación entre **dos** variables numéricas | Dispersión | `scatterplot`, `regplot` |
| Comparar una variable numérica entre **grupos** | Caja / violín | `boxplot`, `violinplot` |
| Comparar una cantidad entre **categorías** | Barras | `barplot` |
| Contar cuántos hay en cada **categoría** | Barras de conteo | `countplot` |
| **Evolución** a lo largo de algo ordenado (tiempo) | Líneas | `lineplot` |
| Relación entre **muchas** variables | Mapa de calor / matriz | `heatmap`, `pairplot` |
| La misma gráfica para **cada grupo** | Facetas | `displot`, `relplot`, `catplot` con `col=` |

---

## 2. La forma "profesional": `fig, ax`

En el taller usamos el estilo `plt.title(...)`, `plt.xlabel(...)`. Funciona, pero cuando tienes
varias gráficas en una misma figura se vuelve confuso. El estilo **orientado a objetos** es más
claro y es el que verás en la mayoría de ejemplos en internet:

```python
fig, ax = plt.subplots(figsize=(8, 5))       # fig = el lienzo; ax = el área del gráfico

ax.hist(df["median_income"], bins=40, color="steelblue", edgecolor="white")
ax.set_title("Distribución del ingreso medio")
ax.set_xlabel("Ingreso medio (decenas de miles de USD)")
ax.set_ylabel("Número de distritos")

plt.show()
```

| Estilo `plt` | Estilo `ax` |
|--------------|-------------|
| `plt.title(...)` | `ax.set_title(...)` |
| `plt.xlabel(...)` | `ax.set_xlabel(...)` |
| `plt.xlim(0, 10)` | `ax.set_xlim(0, 10)` |
| `plt.legend()` | `ax.legend()` |

Las funciones de Seaborn aceptan el parámetro `ax=` para dibujar sobre un área concreta.

---

## 3. Varias gráficas en una figura (`subplots`)

```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))   # 1 fila, 2 columnas

sns.histplot(data=df, x="median_income", ax=axes[0])
axes[0].set_title("Ingreso medio")

sns.histplot(data=df, x="median_house_value", ax=axes[1])
axes[1].set_title("Valor de vivienda")

plt.tight_layout()   # evita que los títulos y ejes se monten unos sobre otros
plt.show()
```

Con una cuadrícula de 2x2, `axes` es una matriz y se accede con `axes[fila, columna]`.

---

## 4. Más tipos de gráficas útiles

### Barras (comparar categorías)

```python
fig, ax = plt.subplots(figsize=(8, 5))
sns.barplot(data=df, x="ocean_proximity", y="median_house_value",
            estimator="median", errorbar=None, ax=ax,
            order=["INLAND", "<1H OCEAN", "NEAR OCEAN", "NEAR BAY", "ISLAND"])
ax.set_title("Valor mediano de la vivienda por cercanía al océano")
ax.set_xlabel("")
ax.set_ylabel("USD")
plt.show()
```

Pandas también puede graficar directo desde un resultado de `groupby`:

```python
df.groupby("ocean_proximity")["median_house_value"].median().sort_values().plot(kind="barh")
plt.title("Valor mediano por zona")
plt.show()
```

### Conteo de categorías

```python
sns.countplot(data=df, y="ocean_proximity", order=df["ocean_proximity"].value_counts().index)
plt.title("Número de distritos por zona")
plt.show()
```

### Violín (como un boxplot, pero mostrando la forma)

El boxplot resume con 5 números; el violín muestra **toda** la forma de la distribución:

```python
plt.figure(figsize=(9, 5))
sns.violinplot(data=df, x="ocean_proximity", y="median_income",
               order=["INLAND", "<1H OCEAN", "NEAR OCEAN", "NEAR BAY"])
plt.title("Distribución del ingreso por zona")
plt.show()
```

### Densidad (un histograma "suavizado")

Ideal para comparar distribuciones superpuestas sin que las barras se tapen:

```python
plt.figure(figsize=(9, 5))
sns.kdeplot(data=df[df["ocean_proximity"] != "ISLAND"],
            x="median_house_value", hue="ocean_proximity", common_norm=False)
plt.title("Distribución del valor de vivienda por zona")
plt.show()
```

### Dispersión con línea de tendencia

`regplot` dibuja los puntos y una recta que resume la tendencia:

```python
plt.figure(figsize=(8, 5))
sns.regplot(data=df.sample(2000, random_state=0), x="median_income", y="median_house_value",
            scatter_kws={"alpha": 0.2, "s": 10}, line_kws={"color": "red"})
plt.title("Ingreso vs. valor, con línea de tendencia")
plt.show()
```

### Líneas (evolución o tendencia)

Ideal cuando el eje X tiene un orden natural (tiempo, edad...):

```python
tendencia = df.groupby("housing_median_age")["median_house_value"].median()

fig, ax = plt.subplots(figsize=(9, 5))
ax.plot(tendencia.index, tendencia.values, marker="o", markersize=3)
ax.set_title("Valor mediano según la antigüedad de la vivienda")
ax.set_xlabel("Antigüedad media (años)")
ax.set_ylabel("Valor mediano (USD)")
plt.show()
```

### `pairplot` (todas las relaciones de un vistazo)

Dibuja la relación entre cada par de variables. Útil para explorar, aunque puede ser lento:

```python
variables = ["median_income", "median_house_value", "housing_median_age"]
sns.pairplot(df[variables].sample(1000, random_state=0))   # una muestra de 1000 filas para que sea rápido
plt.show()
```

---

## 5. Facetas: la misma gráfica para cada grupo

Las funciones `displot`, `relplot` y `catplot` de Seaborn crean una cuadrícula de gráficas con un
solo parámetro (`col=` o `row=`). Es una de las herramientas más poderosas para comparar grupos:

```python
sns.relplot(data=df[df["ocean_proximity"] != "ISLAND"],
            x="median_income", y="median_house_value",
            col="ocean_proximity", col_wrap=2,
            alpha=0.2, s=10, height=3.5)
plt.show()
```

Con una línea obtuviste cuatro gráficas, una por zona, con los mismos ejes para compararlas.

---

## 6. Un mapa más completo con las coordenadas

En el taller dibujamos California coloreando por valor. Podemos añadir el **tamaño** de los puntos
según la población:

```python
plt.figure(figsize=(9, 7))
sns.scatterplot(data=df, x="longitude", y="latitude",
                hue="median_house_value", palette="viridis",
                size="population", sizes=(5, 200), alpha=0.5)
plt.title("Precio de la vivienda y población por ubicación")
plt.xlabel("Longitud")
plt.ylabel("Latitud")
plt.legend(bbox_to_anchor=(1.05, 1), loc="upper left")   # leyenda fuera del gráfico
plt.show()
```

---

## 7. Detalles que marcan la diferencia

### Anotar un punto importante

```python
fig, ax = plt.subplots(figsize=(8, 5))
ax.hist(df["median_house_value"], bins=50, color="steelblue", edgecolor="white")
ax.annotate("Tope artificial\nen 500.001 USD",
            xy=(495000, 1000), xytext=(300000, 1100),
            arrowprops=dict(arrowstyle="->"))
ax.set_title("Distribución del valor de las viviendas")
plt.show()
```

### Números legibles en los ejes

`500000` se lee peor que `500k`. Puedes formatear los ejes:

```python
from matplotlib.ticker import FuncFormatter

fig, ax = plt.subplots(figsize=(8, 5))
ax.hist(df["median_house_value"], bins=50)
ax.xaxis.set_major_formatter(FuncFormatter(lambda x, pos: f"{x / 1000:.0f}k"))
ax.set_xlabel("Valor medio de la vivienda (USD)")
plt.show()
```

### Escala logarítmica

Cuando una variable tiene unos pocos valores enormes (como `population`), la escala logarítmica
permite ver el resto:

```python
fig, ax = plt.subplots(figsize=(8, 5))
ax.hist(df["population"], bins=50)
ax.set_xscale("log")
ax.set_title("Población por distrito (escala logarítmica)")
plt.show()
```

### Colores

- **Categorías** (sin orden): paletas como `"Set2"` o `"colorblind"`.
- **Valores numéricos** (de menos a más): `"viridis"`, `"Blues"`, `"rocket"`.
- **Valores con un centro** (negativo / positivo, como la correlación): `"coolwarm"`, `"RdBu"`.

```python
sns.set_palette("colorblind")   # paleta pensada para personas con daltonismo
```

Alrededor del 8% de los hombres tiene algún tipo de daltonismo. Evita depender solo de la
combinación rojo/verde para transmitir información.

---

## 8. Guardar una gráfica como imagen

```python
plt.figure(figsize=(8, 5))
sns.histplot(data=df, x="median_house_value", bins=50)
plt.title("Distribución del valor de vivienda")
plt.savefig("mi_grafica.png", dpi=300, bbox_inches="tight")   # antes de plt.show()
plt.show()
```

- `dpi=300` → buena resolución para informes y presentaciones.
- `bbox_inches="tight"` → recorta los bordes blancos sobrantes.
- Guarda como `.svg` o `.pdf` si la gráfica va a un documento (se ve nítida a cualquier tamaño).

---

## 9. Buenas prácticas para gráficas claras

- **Siempre** título y ejes etiquetados (con unidades cuando aplique).
- Un buen título dice la **conclusión**, no solo el tema: *"La costa es más cara que el interior"*
  comunica más que *"Valor por zona"*.
- Usa transparencia (`alpha`) cuando hay muchos puntos superpuestos.
- No uses más colores de los necesarios; el color debe **significar** algo.
- Ordena las categorías de forma lógica (de menor a mayor, o en su orden natural), no alfabéticamente.
- En gráficos de barras, el eje Y debe empezar en **cero**; si no, las diferencias parecen mayores.
- Evita gráficos 3D y de torta: son difíciles de leer con precisión.
- Piensa en tu audiencia: una gráfica se hace para que **otra persona** entienda.

---

## 10. Y si quieres gráficas interactivas...

**Plotly** permite hacer zoom, pasar el mouse por encima para ver valores y filtrar categorías.
Instálalo con `pip install plotly` y prueba:

```python
import plotly.express as px

fig = px.scatter(df.sample(3000, random_state=0),
                 x="median_income", y="median_house_value",
                 color="ocean_proximity", hover_data=["housing_median_age"])
fig.show()
```

---

## Mini-retos

**1.** Haz un boxplot del ingreso (`median_income`) por `ocean_proximity` con el estilo `fig, ax`.
¿Las zonas costeras tienen ingresos más altos?

<details>
<summary>Ver solución</summary>

```python
fig, ax = plt.subplots(figsize=(9, 5))
sns.boxplot(data=df, x="ocean_proximity", y="median_income", ax=ax,
            order=["INLAND", "NEAR OCEAN", "<1H OCEAN", "NEAR BAY", "ISLAND"])
ax.set_title("Ingreso medio según la cercanía al océano")
ax.set_xlabel("Cercanía al océano")
ax.set_ylabel("Ingreso medio (decenas de miles de USD)")
plt.show()
```
Sí, `INLAND` tiene el ingreso más bajo. Por eso es importante el reto 4 de Pandas: ¿la costa es
más cara por la ubicación o porque allí vive gente con más ingresos?
</details>

**2.** Crea una figura con 2x2 gráficas: histogramas de `median_income`, `housing_median_age`,
`median_house_value` y `population`.

<details>
<summary>Ver solución</summary>

```python
columnas = ["median_income", "housing_median_age", "median_house_value", "population"]
fig, axes = plt.subplots(2, 2, figsize=(12, 8))

for ax, col in zip(axes.flat, columnas):   # axes.flat recorre la cuadrícula como una lista
    sns.histplot(data=df, x=col, bins=40, ax=ax)
    ax.set_title(col)

plt.tight_layout()
plt.show()
```
</details>

**3.** Rehaz el mapa de California, pero solo con los distritos de valor mayor a 400.000 USD.
¿Dónde están?

<details>
<summary>Ver solución</summary>

```python
caros = df[df["median_house_value"] > 400000]

fig, ax = plt.subplots(figsize=(8, 7))
ax.scatter(df["longitude"], df["latitude"], s=3, color="lightgray")   # todo el estado, de fondo
ax.scatter(caros["longitude"], caros["latitude"], s=5, color="crimson")
ax.set_title("Distritos con viviendas de más de 400.000 USD")
ax.set_xlabel("Longitud")
ax.set_ylabel("Latitud")
plt.show()
```
Casi todos están en la costa, concentrados en el área de San Francisco y en Los Ángeles. Dibujar
todo el estado en gris de fondo ayuda a dar contexto.
</details>

---

## Para seguir

- Galería de Matplotlib (ejemplos con código): https://matplotlib.org/stable/gallery/index.html
- Galería de Seaborn: https://seaborn.pydata.org/examples/index.html
- Tutorial oficial de Seaborn: https://seaborn.pydata.org/tutorial.html
- *From Data to Viz* (qué gráfico usar según tus datos): https://www.data-to-viz.com/
- *Fundamentals of Data Visualization*, de Claus Wilke (libro gratuito): https://clauswilke.com/dataviz/
