# NumPy más allá del taller

En el taller vimos lo esencial de NumPy: crear arreglos, consultar `shape`/`size`/`ndim`, hacer
operaciones básicas y comprobar en la "carrera" que es mucho más rápido que una lista. Aquí
encontrarás temas para dar el siguiente paso. Todos los ejemplos asumen:

```python
import numpy as np
```

---

## 1. Crear arreglos sin escribirlos a mano

Escribir listas largas es tedioso. NumPy tiene funciones para generar arreglos:

```python
np.zeros(5)            # [0. 0. 0. 0. 0.]
np.ones((2, 3))        # matriz 2x3 de unos
np.full(4, 7)          # [7 7 7 7]
np.arange(0, 10, 2)    # [0 2 4 6 8]  (inicio, fin sin incluir, paso)
np.linspace(0, 1, 5)   # [0.   0.25 0.5  0.75 1.  ]  (5 valores igualmente espaciados)
np.eye(3)              # matriz identidad 3x3
```

**¿`arange` o `linspace`?** Usa `arange` cuando conoces el **paso** y `linspace` cuando conoces
**cuántos puntos** quieres (muy común para graficar funciones).

---

## 2. Tipos de datos (`dtype`)

Todos los elementos de un arreglo tienen el **mismo tipo**. Eso es parte de lo que lo hace rápido:

```python
a = np.array([1, 2, 3])
a.dtype                      # int64  (enteros)

b = np.array([1.5, 2, 3])
b.dtype                      # float64 (basta un decimal para que todo sea decimal)

c = np.array([1, 2, 3], dtype=float)   # forzar el tipo al crearlo
d = a.astype(float)                    # convertir un arreglo existente
```

> **Cuidado:** si mezclas números y texto, `np.array([1, "dos", 3])` convierte **todo** a texto,
> y ya no podrás hacer operaciones matemáticas con él.

---

## 3. Indexación y *slicing* (rebanado)

Igual que en las listas de Python, pero más potente:

```python
a = np.array([10, 20, 30, 40, 50])

a[0]        # 10   -> primer elemento
a[-1]       # 50   -> último elemento
a[1:4]      # [20 30 40]  -> del índice 1 al 3 (el 4 no se incluye)
a[:3]       # [10 20 30]  -> los primeros 3
a[::2]      # [10 30 50]  -> uno de cada dos
a[::-1]     # [50 40 30 20 10]  -> al revés
```

En dos dimensiones se usa `[fila, columna]`:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])

m[0, 1]     # 2       -> fila 0, columna 1
m[:, 0]     # [1 4]   -> toda la primera columna
m[1, :]     # [4 5 6] -> toda la segunda fila
```

---

## 4. Máscaras booleanas (filtrar por condición)

Una de las herramientas más útiles: seleccionar los elementos que cumplen una condición. Es
exactamente lo que hace Pandas cuando escribes `df[df["col"] > valor]`.

```python
a = np.array([10, 25, 30, 5, 40])

a > 20            # [False  True  True False  True]
a[a > 20]         # [25 30 40]  -> solo los que cumplen
a[a > 20].mean()  # promedio de los que cumplen
(a > 20).sum()    # 3 -> cuántos cumplen (True cuenta como 1)

# Combinar condiciones: & (y), | (o), ~ (no). Cada condición entre paréntesis.
a[(a > 10) & (a < 40)]   # [25 30]
a[(a < 10) | (a > 35)]   # [ 5 40]
```

---

## 5. `np.where`: "si pasa esto, pon esto; si no, aquello"

```python
valores = np.array([452600, 91300, 358500, 67500])

np.where(valores > 200000, "cara", "accesible")
# ['cara' 'accesible' 'cara' 'accesible']

np.where(valores > 200000)   # sin los otros argumentos: devuelve las posiciones -> (array([0, 2]),)
```

Es la forma vectorizada (sin bucles) de escribir un `if/else` sobre todo un arreglo.

---

## 6. Estadística básica

```python
datos = np.array([3, 7, 7, 2, 9, 4, 7, 1])

np.mean(datos)               # promedio
np.median(datos)             # mediana: el valor del medio al ordenar
np.std(datos)                # desviación estándar: qué tan dispersos están los datos
np.percentile(datos, 90)     # percentil 90: el 90% de los datos está por debajo
np.argmax(datos)             # posición del valor máximo (4), no el valor
np.unique(datos, return_counts=True)   # valores distintos y cuántas veces aparece cada uno
np.cumsum(datos)             # suma acumulada
np.round(np.mean(datos), 2)  # redondear
```

**Media vs. mediana:** la media se deja arrastrar por valores extremos; la mediana no. Por eso los
precios de vivienda casi siempre se reportan con la **mediana** (de ahí `median_house_value`).

---

## 7. Valores faltantes: `np.nan`

En NumPy un dato faltante se representa con `np.nan` ("not a number"), y "contamina" los cálculos:

```python
x = np.array([1.0, 2.0, np.nan, 4.0])

x.mean()          # nan  -> un solo faltante arruina el promedio
np.nanmean(x)     # 2.333... -> ignora los faltantes
np.isnan(x)       # [False False  True False]
x[~np.isnan(x)]   # [1. 2. 4.] -> quedarse con los que no faltan
```

Pandas es más amable: `df["col"].mean()` ignora los nulos automáticamente.

---

## 8. El parámetro `axis`

En arreglos 2D, muchas operaciones aceptan `axis` para indicar la dirección:

- `axis=0` → opera **por columnas** (recorre las filas hacia abajo).
- `axis=1` → opera **por filas** (recorre las columnas hacia la derecha).

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])

m.sum()          # 21       -> todo
m.sum(axis=0)    # [5 7 9]  -> suma de cada columna
m.sum(axis=1)    # [ 6 15]  -> suma de cada fila
```

Truco para recordarlo: el `axis` que indicas es el que **desaparece**. Una matriz de forma `(2, 3)`
con `axis=0` queda de forma `(3,)`.

---

## 9. *Broadcasting* (operar arreglos de distinto tamaño)

NumPy "estira" automáticamente los arreglos compatibles para operar entre ellos:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])

m + 100                    # le suma 100 a cada elemento
m * np.array([1, 0, 1])    # multiplica cada fila por [1, 0, 1]
m - m.mean(axis=0)         # centra cada columna restándole su promedio
```

---

## 10. `reshape`: cambiar la forma

```python
a = np.arange(6)          # [0 1 2 3 4 5]
a.reshape(2, 3)           # [[0 1 2]
                          #  [3 4 5]]
a.reshape(3, -1)          # -1 = "calcula tú esta dimensión" -> 3 filas, 2 columnas
a.reshape(2, 3).T         # .T = transpuesta (filas <-> columnas)
```

---

## 11. Números aleatorios (con resultados repetibles)

Muy útil para simulaciones, pruebas y para sacar muestras:

```python
rng = np.random.default_rng(seed=42)   # la "semilla" hace que los resultados se repitan

rng.random(3)                          # 3 decimales entre 0 y 1
rng.integers(1, 7, size=10)            # 10 lanzamientos de un dado
rng.normal(loc=170, scale=8, size=5)   # 5 estaturas con distribución normal (media 170, desv. 8)
rng.choice(["cara", "sello"], size=4)  # elegir al azar de una lista
```

Si ejecutas el código con la misma semilla, obtendrás exactamente los mismos números. Eso hace que
tu análisis sea **reproducible**.

---

## 12. Copias y vistas: una trampa clásica

Un *slice* de NumPy **no es una copia**: es una "ventana" al arreglo original.

```python
a = np.array([1, 2, 3, 4])
b = a[:2]       # b es una VISTA de a
b[0] = 99
print(a)        # [99  2  3  4]  -> ¡a también cambió!

c = a[:2].copy()   # si quieres un arreglo independiente, usa .copy()
```

---

## 13. De Pandas a NumPy (y viceversa)

Cada columna de un DataFrame es, por debajo, un arreglo de NumPy:

```python
import pandas as pd

df = pd.read_csv("../data/housing.csv")
valores = df["median_house_value"].to_numpy()

type(valores)                  # numpy.ndarray
np.percentile(valores, [25, 50, 75])

# Las funciones de NumPy funcionan directamente sobre columnas de Pandas
df["log_valor"] = np.log(df["median_house_value"])
```

---

## 14. ¿Por qué NumPy es tan rápido?

- Los arreglos guardan datos del **mismo tipo** en memoria contigua (las listas guardan
  "referencias" a objetos dispersos en memoria).
- Las operaciones están implementadas en **C**, no en Python puro.
- La **vectorización** evita los bucles lentos de Python.

Regla práctica: si te encuentras escribiendo un `for` para recorrer un arreglo número por número,
probablemente existe una función de NumPy que lo hace en una línea.

---

## Mini-retos

Usa el arreglo `valores = df["median_house_value"].to_numpy()` (ver sección 13).

**1.** ¿Cuántos distritos tienen un valor de vivienda exactamente igual al tope de 500.001 USD?

<details>
<summary>Ver solución</summary>

```python
(valores == 500001).sum()   # 965
```
</details>

**2.** ¿Qué porcentaje de distritos tiene un valor menor a 100.000 USD?

<details>
<summary>Ver solución</summary>

```python
round((valores < 100000).mean() * 100, 1)   # 17.4 %
```
El promedio de un arreglo de `True`/`False` es la **proporción** de `True`.
</details>

**3.** Simula 10.000 lanzamientos de dos dados y calcula qué suma sale con más frecuencia.

<details>
<summary>Ver solución</summary>

```python
rng = np.random.default_rng(seed=0)
suma = rng.integers(1, 7, size=10000) + rng.integers(1, 7, size=10000)
vals, conteos = np.unique(suma, return_counts=True)
vals[np.argmax(conteos)]   # 7
```
</details>

---

## Para seguir

- Documentación oficial: https://numpy.org/doc/stable/
- Tutorial "NumPy: the absolute basics for beginners":
  https://numpy.org/doc/stable/user/absolute_beginners.html
- Capítulo 2 del *Python Data Science Handbook* (gratis):
  https://jakevdp.github.io/PythonDataScienceHandbook/02.00-introduction-to-numpy.html
