# NumPy más allá del taller

En el taller vimos lo esencial de NumPy: crear arreglos, consultar `shape`/`size`/`ndim` y hacer
operaciones básicas. Aquí encontrarás temas para dar el siguiente paso. Todos los ejemplos asumen:

```python
import numpy as np
```

---

## 1. Crear arreglos sin escribirlos a mano

Escribir listas largas es tedioso. NumPy tiene funciones para generar arreglos:

```python
np.zeros(5)            # [0. 0. 0. 0. 0.]
np.ones((2, 3))        # matriz 2x3 de unos
np.arange(0, 10, 2)    # [0 2 4 6 8]  (inicio, fin, paso)
np.linspace(0, 1, 5)   # [0.   0.25 0.5  0.75 1.  ]  (5 valores igualmente espaciados)
np.random.rand(3)      # 3 números aleatorios entre 0 y 1
```

---

## 2. Indexación y *slicing* (rebanado)

Igual que en las listas de Python, pero más potente:

```python
a = np.array([10, 20, 30, 40, 50])

a[0]        # 10   -> primer elemento
a[-1]       # 50   -> último elemento
a[1:4]      # [20 30 40]  -> del índice 1 al 3
a[:3]       # [10 20 30]  -> los primeros 3
a[::2]      # [10 30 50]  -> uno de cada dos
```

En dos dimensiones se usa `[fila, columna]`:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])

m[0, 1]     # 2    -> fila 0, columna 1
m[:, 0]     # [1 4] -> toda la primera columna
m[1, :]     # [4 5 6] -> toda la segunda fila
```

---

## 3. Máscaras booleanas (filtrar por condición)

Una de las herramientas más útiles: seleccionar elementos que cumplen una condición.

```python
a = np.array([10, 25, 30, 5, 40])

a > 20            # [False  True  True False  True]
a[a > 20]         # [25 30 40]  -> solo los que cumplen
a[a > 20].mean()  # promedio de los que cumplen

# Combinar condiciones con & (y), | (o)
a[(a > 10) & (a < 40)]   # [25 30]
```

---

## 4. El parámetro `axis`

En arreglos 2D, muchas operaciones aceptan `axis` para indicar la dirección:

- `axis=0` → opera **por columnas** (recorre las filas).
- `axis=1` → opera **por filas** (recorre las columnas).

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])

m.sum()          # 21  -> todo
m.sum(axis=0)    # [5 7 9]   -> suma de cada columna
m.sum(axis=1)    # [6 15]    -> suma de cada fila
```

---

## 5. *Broadcasting* (operar arreglos de distinto tamaño)

NumPy "estira" automáticamente los arreglos compatibles para operar entre ellos:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])

m + 100           # le suma 100 a cada elemento
m * np.array([1, 0, 1])   # multiplica cada fila por [1, 0, 1]
```

---

## 6. `reshape`: cambiar la forma

```python
a = np.arange(6)          # [0 1 2 3 4 5]
a.reshape(2, 3)           # [[0 1 2]
                          #  [3 4 5]]
a.reshape(3, 2)           # 3 filas, 2 columnas
```

---

## 7. ¿Por qué NumPy es tan rápido?

- Los arreglos guardan datos del **mismo tipo** en memoria contigua (a diferencia de las listas).
- Las operaciones están implementadas en **C**, no en Python puro.
- La **vectorización** evita los bucles lentos de Python.

Comparación rápida (sumar un millón de números):

```python
import numpy as np

datos = np.arange(1_000_000)

# Vectorizado (rápido): datos.sum()
# Con un bucle de Python sería decenas de veces más lento.
```

---

## Para seguir

- Documentación oficial: https://numpy.org/doc/stable/
- Tutorial "NumPy: the absolute basics for beginners":
  https://numpy.org/doc/stable/user/absolute_beginners.html
