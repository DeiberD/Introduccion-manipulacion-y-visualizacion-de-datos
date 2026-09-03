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

---

## 7. ¿Y después de este taller?

Un posible camino de aprendizaje:

1. **Afianza lo básico** → resuelve los ejercicios de la carpeta `ejercicios/`.
2. **Limpieza de datos** → aprende a tratar nulos, duplicados y tipos de datos.
3. **Estadística descriptiva e inferencial** → media, mediana, desviación, correlación, pruebas.
4. **Visualización avanzada** → Plotly (interactivo), mapas geográficos.
5. **Machine learning** → scikit-learn (¡el dataset de este taller es perfecto para empezar!).

De hecho, este mismo dataset se usa clásicamente para un primer proyecto de *machine learning*:
predecir el valor de la vivienda a partir de las demás variables.

---

## 8. Recursos para practicar por tu cuenta

- **Kaggle** (https://www.kaggle.com/): datasets gratuitos, competencias y notebooks de otras personas.
- **Datos abiertos de Colombia** (https://www.datos.gov.co/): practica con datos reales de tu país.
- **UCI Machine Learning Repository** (https://archive.ics.uci.edu/): datasets clásicos.

> El mejor consejo: **elige un tema que te apasione**, busca un dataset sobre él y hazle preguntas.
> Se aprende muchísimo más analizando algo que te importa que copiando tutoriales.
