# Proyecto guiado: ¿cuánto cuesta la vista al mar?

En el taller encontramos dos factores que se relacionan con el valor de la vivienda: el **ingreso**
de la zona y la **cercanía al océano**. Pero quedó una duda abierta:

> En la costa vive gente con más ingresos. Entonces, ¿la costa es más cara **por estar en la costa**,
> o solo porque allí vive gente con más dinero?

En este proyecto vamos a responderla siguiendo el flujo completo, con algunas herramientas del
material complementario. Abre un notebook nuevo en la carpeta `notebooks/` y ve copiando los
bloques de código. Cada paso explica **por qué** se hace, que es lo más importante.

---

## Paso 1. Plantear la pregunta y el plan

Antes de tocar el código, escribe qué vas a hacer. Un plan sencillo:

1. Cargar los datos y limpiar lo que pueda confundirnos.
2. Crear las variables que necesitamos (zona costera o interior, nivel de ingreso).
3. Comparar costa e interior **sin** tener en cuenta el ingreso.
4. Comparar costa e interior **dentro de cada nivel de ingreso** (una comparación justa).
5. Visualizar el resultado.
6. Sacar conclusiones y reconocer las limitaciones.

---

## Paso 2. Cargar y limpiar

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

sns.set_theme(style="whitegrid")
df = pd.read_csv("../data/housing.csv")
print("Filas originales:", len(df))
```

Tomamos tres decisiones de limpieza, cada una con su razón:

| Decisión | Por qué |
|----------|---------|
| Quitar las filas sin `total_bedrooms` | Son solo 207 (1%) y así evitamos sorpresas con los nulos. |
| Quitar los distritos con valor = 500.001 | Es un **tope**: el valor real puede ser 600.000 o 2 millones. Mantenerlos subestimaría las zonas caras. |
| Quitar `ISLAND` | Solo 5 distritos: cualquier conclusión sobre ellos sería poco confiable. |

```python
datos = (
    df
    .dropna(subset=["total_bedrooms"])
    .query("median_house_value < 500001 and ocean_proximity != 'ISLAND'")
    .copy()
)
print("Filas después de limpiar:", len(datos))   # 19.470
```

> **Importante:** deja **por escrito** cada decisión de limpieza. Otra persona podría tomar
> decisiones distintas, y es justo que sepa cuáles tomaste tú.

---

## Paso 3. Crear las variables

Simplificamos `ocean_proximity` en dos grupos y dividimos el ingreso en cinco niveles con la misma
cantidad de distritos cada uno (quintiles):

```python
datos["zona"] = np.where(datos["ocean_proximity"] == "INLAND", "Interior", "Costa")

datos["nivel_ingreso"] = pd.qcut(datos["median_income"], q=5,
                                 labels=["Muy bajo", "Bajo", "Medio", "Alto", "Muy alto"])

datos[["ocean_proximity", "zona", "median_income", "nivel_ingreso"]].head()
```

---

## Paso 4. La comparación ingenua

Primero, comparemos costa e interior directamente:

```python
datos.groupby("zona").agg(
    valor_mediano=("median_house_value", "median"),
    ingreso_mediano=("median_income", "median"),
    distritos=("zona", "size"),
)
```

| zona | valor_mediano | ingreso_mediano | distritos |
|------|---------------|-----------------|-----------|
| Costa | 211.300 | 3,71 | 13.001 |
| Interior | 108.300 | 2,98 | 6.469 |

La costa vale casi **el doble**. Pero fíjate en la columna del ingreso: en la costa también se gana
más. Con esta tabla **no podemos separar** los dos efectos. Por eso se llama comparación ingenua.

---

## Paso 5. La comparación justa

La idea clave del proyecto: comparar costa e interior **entre distritos con un ingreso parecido**.
Así, si la costa sigue siendo más cara, la diferencia ya no se puede atribuir al ingreso.

```python
tabla = pd.pivot_table(datos, values="median_house_value",
                       index="nivel_ingreso", columns="zona",
                       aggfunc="median", observed=True)

tabla["sobrecosto_costa_%"] = ((tabla["Costa"] / tabla["Interior"] - 1) * 100).round(0)
tabla
```

| nivel_ingreso | Costa | Interior | sobrecosto_costa_% |
|---------------|-------|----------|--------------------|
| Muy bajo | 134.200 | 70.400 | 91 |
| Bajo | 169.600 | 97.400 | 74 |
| Medio | 195.450 | 117.200 | 67 |
| Alto | 222.400 | 142.500 | 56 |
| Muy alto | 293.900 | 204.950 | 43 |

**Lectura:** en **todos** los niveles de ingreso, la costa es más cara: entre un 43% y un 91%. La
cercanía al océano tiene un efecto propio, que no se explica solo por el ingreso.

Un detalle interesante: el sobrecosto **disminuye** a medida que sube el ingreso. ¿Se te ocurre
alguna explicación? (No hay una única respuesta; plantear hipótesis es parte del trabajo.)

---

## Paso 6. Visualizar

Una sola gráfica que cuente toda la historia:

```python
fig, ax = plt.subplots(figsize=(9, 5))
sns.barplot(data=datos, x="nivel_ingreso", y="median_house_value", hue="zona",
            estimator="median", errorbar=None, palette=["#1f77b4", "#d98c3a"], ax=ax)
ax.set_title("A igual nivel de ingreso, la costa sigue siendo más cara")
ax.set_xlabel("Nivel de ingreso de la zona (quintiles)")
ax.set_ylabel("Valor mediano de la vivienda (USD)")
ax.legend(title="")
plt.show()
```

Fíjate en el título: no dice *"Valor por ingreso y zona"*, sino la **conclusión**. Quien vea la
gráfica entiende el mensaje en dos segundos.

---

## Paso 7 (bonus). Tu primer modelo de predicción

¿Y si quisiéramos **predecir** el valor de una vivienda a partir del ingreso? La forma más sencilla
es trazar una recta que pase lo más cerca posible de todos los puntos. NumPy lo hace con `polyfit`:

```python
pendiente, intercepto = np.polyfit(datos["median_income"], datos["median_house_value"], deg=1)
print(f"valor estimado = {pendiente:,.0f} * ingreso + {intercepto:,.0f}")
```

El resultado es aproximadamente `valor = 40.041 * ingreso + 45.167`. Es decir: **cada 10.000 USD
más de ingreso** en la zona (una unidad de `median_income`) se asocian con unos **40.000 USD más**
de valor de vivienda.

Ahora veamos **cuánto se equivoca** la recta en cada zona. A la diferencia entre el valor real y el
predicho se le llama **residuo**:

```python
datos["prediccion"] = pendiente * datos["median_income"] + intercepto
datos["residuo"] = datos["median_house_value"] - datos["prediccion"]

datos.groupby("zona")["residuo"].median().round(0)
```

| zona | residuo mediano |
|------|-----------------|
| Costa | +10.280 |
| Interior | -54.956 |

La recta, que solo conoce el ingreso, **se queda corta en la costa** y **se pasa en el interior**.
Esa es otra forma de ver lo mismo que en el paso 5: hay algo de la ubicación que el ingreso no explica.

Así empieza el *machine learning*: un modelo hace predicciones, miramos dónde se equivoca y
agregamos información (como la zona) para mejorarlo. Si quieres seguir por este camino, el
siguiente paso es la librería **scikit-learn** y el capítulo 2 del libro de Géron (ver
[`../referencias/bibliografia.md`](../referencias/bibliografia.md)).

---

## Paso 8. Conclusiones y limitaciones

**Conclusiones:**

1. La costa es más cara que el interior incluso cuando comparamos zonas con un ingreso similar.
2. El sobrecosto de la costa es mayor en las zonas de menor ingreso (91%) que en las de mayor ingreso (43%).
3. Un modelo que solo usa el ingreso subestima la costa y sobreestima el interior.

**Limitaciones** (siempre hay que decirlas):

- Los datos son del **censo de 1990**; los precios actuales son muy distintos.
- Cada fila es un **distrito**, no una casa: hablamos de medianas de zonas, no de viviendas concretas.
- Quitamos los distritos con valor en el tope; las conclusiones sobre las zonas más caras son parciales.
- "Costa" mezcla tres categorías distintas (`<1H OCEAN`, `NEAR OCEAN`, `NEAR BAY`).
- Encontramos **asociaciones**, no causas. Otras variables (clima, empleo, servicios) podrían
  explicar parte de la diferencia.

---

## Tu turno: ideas para un proyecto propio

Elige una de estas preguntas y repite el flujo (pregunta, limpieza, variables, comparación,
gráfica, conclusiones):

- ¿Las zonas con más **personas por hogar** (`population / households`) tienen viviendas más baratas?
- ¿Cómo cambia el valor de la vivienda de **norte a sur**? (usa `latitude` y `pd.cut`)
- ¿En qué zonas las viviendas son más **antiguas**? ¿Tiene eso que ver con el valor?
- ¿Hay distritos **"raros"** (valores extremos de habitaciones o población)? ¿Qué tienen en común?

O mejor aún: busca un dataset sobre un tema que te apasione en [datos.gov.co](https://www.datos.gov.co/)
o [Kaggle](https://www.kaggle.com/datasets) y hazle tus propias preguntas.
