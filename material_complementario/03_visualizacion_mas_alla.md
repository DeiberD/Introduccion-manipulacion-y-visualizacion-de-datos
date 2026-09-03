# Visualización más allá del taller

En el taller vimos histogramas, dispersión y boxplots. Aquí tienes más tipos de gráficas y consejos
para comunicar mejor. Los ejemplos asumen:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

sns.set_theme(style="whitegrid")
df = pd.read_csv("../data/housing.csv")
```

---

## 1. Más tipos de gráficas útiles

### Gráfico de barras (comparar categorías)
```python
valor_zona = df.groupby("ocean_proximity")["median_house_value"].mean().sort_values()

plt.figure(figsize=(8, 5))
valor_zona.plot(kind="bar")          # Pandas grafica directo desde una Series
plt.title("Valor medio de vivienda por cercanía al océano")
plt.ylabel("USD")
plt.show()
```

### Gráfico de líneas (evolución / tendencia)
Ideal cuando el eje X tiene un orden natural (tiempo, edad...):
```python
tendencia = df.groupby("housing_median_age")["median_house_value"].mean()

plt.figure(figsize=(9, 5))
plt.plot(tendencia.index, tendencia.values)
plt.title("Valor medio según la antigüedad de la vivienda")
plt.xlabel("Antigüedad media (años)")
plt.ylabel("Valor medio (USD)")
plt.show()
```

### Mapa de calor de correlaciones (`heatmap`)
Para ver de un vistazo qué variables se relacionan:
```python
corr = df.select_dtypes("number").corr()

plt.figure(figsize=(9, 7))
sns.heatmap(corr, annot=True, cmap="coolwarm", fmt=".2f")
plt.title("Correlación entre variables numéricas")
plt.show()
```

### `pairplot` (todas las relaciones de un vistazo)
Dibuja la relación entre cada par de variables. Útil para explorar, aunque puede ser pesado:
```python
variables = ["median_income", "median_house_value", "housing_median_age"]
sns.pairplot(df[variables].sample(1000))   # muestreamos 1000 filas para que sea rápido
plt.show()
```

---

## 2. Un mapa "de verdad" con las coordenadas

El dataset tiene `latitude` y `longitude`: ¡podemos dibujar California!

```python
plt.figure(figsize=(9, 7))
sns.scatterplot(data=df, x="longitude", y="latitude",
                hue="median_house_value", palette="viridis",
                size="population", sizes=(5, 200), alpha=0.5)
plt.title("Precio de la vivienda por ubicación geográfica")
plt.xlabel("Longitud")
plt.ylabel("Latitud")
plt.legend(bbox_to_anchor=(1.05, 1), loc="upper left")
plt.show()
```

Verás cómo las zonas más caras (colores claros) se concentran en la costa. La visualización revela
patrones geográficos que los números por sí solos no muestran.

---

## 3. Varias gráficas en una figura (`subplots`)

```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))   # 1 fila, 2 columnas

sns.histplot(data=df, x="median_income", ax=axes[0])
axes[0].set_title("Ingreso medio")

sns.histplot(data=df, x="median_house_value", ax=axes[1])
axes[1].set_title("Valor de vivienda")

plt.tight_layout()
plt.show()
```

---

## 4. Guardar una gráfica como imagen

```python
plt.figure(figsize=(8, 5))
sns.histplot(data=df, x="median_house_value", bins=50)
plt.title("Distribución del valor de vivienda")
plt.savefig("mi_grafica.png", dpi=300, bbox_inches="tight")   # antes de plt.show()
plt.show()
```

---

## 5. Elegir el gráfico correcto

| ¿Qué quiero mostrar? | Gráfico recomendado |
|----------------------|---------------------|
| Cómo se distribuye **una** variable numérica | Histograma / `histplot` |
| Relación entre **dos** variables numéricas | Dispersión / `scatterplot` |
| Comparar una variable numérica entre **grupos** | Boxplot / Violín |
| Comparar una cantidad entre **categorías** | Gráfico de barras |
| Contar cuántos hay en cada **categoría** | `countplot` |
| **Evolución** a lo largo de algo ordenado | Gráfico de líneas |
| Relación entre **muchas** variables | Heatmap / `pairplot` |

---

## 6. Buenas prácticas para gráficas claras

- **Siempre** título y ejes etiquetados (con unidades cuando aplique).
- Usa transparencia (`alpha`) cuando hay muchos puntos superpuestos.
- No uses más colores de los necesarios; el color debe **significar** algo.
- Piensa en tu audiencia: una gráfica se hace para que **otra persona** entienda.
- Evita gráficos 3D innecesarios y efectos decorativos que distraen.
- No exageres las escalas para hacer parecer una diferencia mayor de lo que es.

---

## Para seguir

- Galería de Matplotlib (ejemplos con código): https://matplotlib.org/stable/gallery/index.html
- Galería de Seaborn: https://seaborn.pydata.org/examples/index.html
- Tutorial oficial de Seaborn: https://seaborn.pydata.org/tutorial.html
