# Buenas prácticas y siguientes pasos

Este documento reúne consejos generales para que crezcas como analista de datos, más allá de la
sintaxis de las librerías.

---

## 1. El flujo de trabajo del análisis de datos

Interioriza este ciclo. Casi todo proyecto de datos lo sigue:

```
   ┌─────────┐    ┌──────────┐    ┌────────────┐    ┌────────────┐    ┌──────────────┐
   │ CARGAR  │ →  │ EXPLORAR │ →  │ MANIPULAR  │ →  │ VISUALIZAR │ →  │ INTERPRETAR  │
   └─────────┘    └──────────┘    └────────────┘    └────────────┘    └──────────────┘
    read_csv       head/info       filtros           hist/scatter       sacar
                   describe        groupby            boxplot            conclusiones
```

A menudo no es lineal: explorar te lleva a manipular, manipular revela algo que quieres visualizar,
y una gráfica te sugiere una nueva pregunta. **Iterar es normal y deseable.**

---

## 2. Haz buenas preguntas

La técnica es solo una herramienta; el valor está en las **preguntas**. Antes de escribir código,
pregúntate:

- ¿Qué quiero averiguar exactamente?
- ¿Qué variables del dataset me pueden ayudar?
- ¿Cómo se vería una respuesta? (¿un número? ¿una comparación? ¿una gráfica?)

En el taller partimos de *"¿de qué depende el valor de una vivienda?"* y la dividimos en
sub-preguntas concretas. Ese hábito —**descomponer una pregunta grande en pequeñas**— es oro.

---

## 3. Correlación no es causalidad

Encontramos que a mayor ingreso, mayor valor de vivienda. Pero eso **no prueba** que una cosa
*cause* la otra. Podría haber un tercer factor (la ubicación, por ejemplo) que influya en ambas.

> Regla de oro: los datos muestran **asociaciones**. Afirmar causalidad requiere un diseño de estudio
> más cuidadoso (experimentos, controles, etc.).

---

## 4. Cuida la reproducibilidad

Que otra persona (¡o tú en el futuro!) pueda re-ejecutar tu análisis y obtener lo mismo:

- **Ejecuta las celdas en orden.** En Jupyter, `Kernel → Restart & Run All` es tu amigo para
  comprobar que el notebook corre de principio a fin sin errores.
- **Documenta** con celdas de Markdown: explica *qué* haces y *por qué*, no solo *cómo*.
- **No dependas de pasos manuales** externos: si descargaste o modificaste un archivo, déjalo escrito.
- Usa un **`requirements.txt`** para que otros instalen las mismas librerías.

---

## 5. Escribe código legible

- Nombres de variables **descriptivos**: `valor_por_zona` mejor que `x1`.
- Una idea por celda (en notebooks) facilita depurar.
- Comenta lo que **no es obvio**, no lo evidente.
- Prefiere la solución **clara** sobre la "ingeniosa" pero incomprensible.

---

## 6. Errores comunes al empezar (y cómo evitarlos)

| Error frecuente | Solución |
|-----------------|----------|
| `FileNotFoundError` al cargar el CSV | Revisa la **ruta**. Desde `notebooks/` es `../data/housing.csv`. |
| Olvidar los corchetes dobles al seleccionar varias columnas | Usa `df[["a", "b"]]`, no `df["a", "b"]`. |
| Combinar condiciones sin paréntesis | `df[(df.a > 1) & (df.b < 2)]`, cada condición entre `()`. |
| Modificar el DataFrame sin querer | Trabaja sobre copias con `.copy()` si vas a experimentar. |
| Gráfica sin título ni ejes | Añade siempre `title`, `xlabel`, `ylabel`. |
| Ejecutar celdas fuera de orden | `Restart & Run All` para verificar. |
| `KeyError: 'columna'` | El nombre no existe tal cual. Revisa con `df.columns` (mayúsculas, espacios, tildes). |
| `NameError: name 'df' is not defined` | No ejecutaste la celda que crea `df`, o reiniciaste el kernel. Ejecuta las celdas de arriba. |
| `ValueError: The truth value of a Series is ambiguous` | Usaste `and`/`or` con columnas. Usa `&` y `\|` con paréntesis. |
| El promedio sale `nan` | Hay valores faltantes. En NumPy usa `np.nanmean`; en Pandas revisa con `isnull().sum()`. |

---

## 7. Cómo leer un mensaje de error

Los errores en Python asustan al principio porque son largos, pero tienen una estructura fija.
**Lee de abajo hacia arriba:**

```
KeyError                                  Traceback (most recent call last)
Cell In[5], line 1
----> 1 df["valor_vivienda"].mean()
...
KeyError: 'valor_vivienda'
```

1. **La última línea** dice qué pasó: `KeyError: 'valor_vivienda'` → no existe una columna con
   ese nombre.
2. **La flecha `---->`** señala la línea de **tu** código donde ocurrió.
3. Todo lo que está en medio suele ser código interno de la librería; casi nunca necesitas leerlo.

Los errores no significan que lo estés haciendo mal: incluso las personas con años de experiencia
ven errores todo el tiempo. La diferencia es que saben leerlos.

---

## 8. Cómo pedir ayuda (y encontrarla por tu cuenta)

**Dentro de Jupyter:**

- `Shift + Tab` con el cursor dentro de una función → muestra sus parámetros.
- `df.groupby?` → abre la documentación de la función.
- `Tab` después de `df.` → muestra todos los métodos disponibles.

**Buscando en internet:** copia la **última línea del error** (sin tus nombres de variables) y
agrega la librería: *"pandas KeyError column"*. Lo más probable es que alguien ya haya tenido el
mismo problema en Stack Overflow.

**Preguntando a otra persona (o a una IA):** incluye siempre:

1. Qué intentabas hacer.
2. El código **mínimo** que reproduce el problema.
3. El mensaje de error **completo**.
4. Lo que ya intentaste.

> Sobre las IA (ChatGPT, Claude, Copilot...): son muy útiles para explicar errores y sugerir código,
> pero **asegúrate de entender** lo que te proponen antes de usarlo. Si no puedes explicar una línea,
> pregunta qué hace. Aprender a programar es justamente eso.

---

## 9. Atajos de Jupyter que ahorran tiempo

| Atajo | Acción |
|-------|--------|
| `Shift + Enter` | Ejecutar la celda y pasar a la siguiente |
| `Ctrl + Enter` | Ejecutar la celda y quedarse en ella |
| `Esc` y luego `A` / `B` | Insertar celda arriba / abajo |
| `Esc` y luego `D D` | Borrar la celda |
| `Esc` y luego `M` / `Y` | Convertir la celda en Markdown / código |
| `Esc` y luego `Z` | Deshacer el borrado de una celda |
| `Ctrl + /` | Comentar o descomentar las líneas seleccionadas |

(En Google Colab algunos atajos cambian: usa `Ctrl + M` en lugar de `Esc`, por ejemplo
`Ctrl + M B` para insertar una celda abajo).

---

## 10. Lista de verificación para explorar un dataset nuevo

Cada vez que recibas un dataset, recorre esta lista antes de sacar conclusiones:

- [ ] ¿Cuántas filas y columnas tiene? (`df.shape`)
- [ ] ¿Qué representa **cada fila**? (en el taller: un distrito, **no** una casa)
- [ ] ¿Qué significa cada columna y en qué **unidades** está? (el ingreso estaba en decenas de miles de USD)
- [ ] ¿Los tipos de datos son los esperados? (`df.info()`)
- [ ] ¿Hay valores faltantes? ¿Cuántos y dónde? (`df.isnull().sum()`)
- [ ] ¿Hay filas duplicadas? (`df.duplicated().sum()`)
- [ ] ¿Los mínimos y máximos tienen sentido? (`df.describe()`)
- [ ] ¿Hay topes artificiales o valores imposibles? (como el 500.001 del taller)
- [ ] ¿Cuántos datos hay en cada categoría? (`value_counts()`; recuerda las 5 islas)
- [ ] ¿De dónde vienen los datos y de qué **año** son? (este dataset es de 1990: no sirve para
      hablar de precios actuales)

---

## 11. Comunicar los resultados

Un análisis que nadie entiende no sirve. Al presentar tus hallazgos:

- **Empieza por la conclusión**, no por el proceso: *"El ingreso es el factor que más se relaciona
  con el valor"*, y después muestras cómo llegaste a eso.
- **Una gráfica, un mensaje.** Si una gráfica intenta decir cinco cosas, no dice ninguna.
- **Menciona las limitaciones:** datos de 1990, tope en 500.001, distritos y no casas, correlación
  y no causalidad. Hacerlo te da credibilidad, no te la quita.
- **Adapta el nivel técnico** a tu audiencia: a un gerente le interesa el hallazgo, no el `groupby`.

### Una nota sobre ética

Los datos suelen describir **personas**. Antes de analizar o compartir un dataset, pregúntate:
¿contiene información personal? ¿Tengo permiso para usarla? ¿Mis conclusiones podrían usarse para
discriminar a algún grupo? En Colombia, el tratamiento de datos personales está regulado por la
**Ley 1581 de 2012** (Habeas Data).

---

## 12. ¿Y después de este taller?

Un posible camino de aprendizaje:

1. **Afianza lo básico** → resuelve los ejercicios de la carpeta `ejercicios/`.
2. **Limpieza de datos** → aprende a tratar nulos, duplicados y tipos de datos.
3. **Estadística descriptiva e inferencial** → media, mediana, desviación, correlación, pruebas.
4. **Visualización avanzada** → Plotly (interactivo), mapas geográficos.
5. **Control de versiones** → Git y GitHub, para guardar el historial de tus proyectos y colaborar.
6. **SQL** → el lenguaje para consultar bases de datos; casi todos los datos empresariales viven ahí.
7. **Machine learning** → scikit-learn (¡el dataset de este taller es perfecto para empezar!).

De hecho, este mismo dataset se usa clásicamente para un primer proyecto de *machine learning*:
predecir el valor de la vivienda a partir de las demás variables. En
[`05_proyecto_guiado.md`](05_proyecto_guiado.md) hay un primer acercamiento.

---

## 13. Recursos para practicar por tu cuenta

- **Kaggle** (https://www.kaggle.com/): datasets gratuitos, competencias y notebooks de otras personas.
- **Datos abiertos de Colombia** (https://www.datos.gov.co/): practica con datos reales de tu país.
- **UCI Machine Learning Repository** (https://archive.ics.uci.edu/): datasets clásicos.

> El mejor consejo: **elige un tema que te apasione**, busca un dataset sobre él y hazle preguntas.
> Se aprende muchísimo más analizando algo que te importa que copiando tutoriales.
