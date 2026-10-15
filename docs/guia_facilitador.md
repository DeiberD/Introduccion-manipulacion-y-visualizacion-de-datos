# Guía para el facilitador

Documento de apoyo para quien **dicta** el taller. Resume la planeación, sugiere tiempos y añade
consejos prácticos para la sesión.

---

## Antes del taller (lista de verificación)

- [ ] Comparte con los asistentes, **con antelación**, el enlace al repositorio y la
      [guía de configuración](configuracion_entorno.md).
- [ ] Pídeles que verifiquen que pueden abrir un notebook y cargar el dataset (Colab o local).
- [ ] Ten a mano el enlace directo al CSV por si alguien no lo descargó:
      `https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv`
- [ ] Prepara las diapositivas de apoyo (agenda + flujo de datos + una diapositiva por librería).
- [ ] Prepara el formulario/QR de asistencia para el cierre.
- [ ] Ejecuta tú mism@ todos los notebooks (`Restart & Run All`) para confirmar que corren.

---

## Estructura y tiempos (≈ 95 minutos)

| Momento | Actividad | Notebook | Duración | Clave a transmitir |
|---------|-----------|----------|----------|--------------------|
| M1 | Introducción | `01_introduccion` | 10 min | El flujo de datos y la pregunta guía. |
| M2 | NumPy | `02_numpy` | 10 min | Arreglos y vectorización. No entrar en matrices. |
| M3 | Pandas: estructuras | `03_pandas_introduccion` | 10 min | Series vs. DataFrame; filas/columnas. |
| M4 | Pandas: cargar y explorar | `04_pandas_carga_exploracion` | 10 min | `read_csv`, `head`, `info`, `describe`. |
| M5 | Pandas: manipular | `05_pandas_manipulacion` | 15 min | Filtrar, ordenar, nulos, `groupby`. |
| M6 | Matplotlib | `06_matplotlib` | 15 min | Histograma y dispersión; título y ejes. |
| M7 | Seaborn | `07_seaborn` | 15 min | `histplot`, `scatterplot`, `boxplot`; comparar con MPL. |
| M8 | Cierre | `08_cierre` | 10 min | Repasar el flujo; reto final; asistencia. |

> Los tiempos son una guía. Si el grupo va lento, prioriza M4–M7 (el corazón del taller) y deja
> los ejercicios como práctica autónoma.

---

## Hilo conductor: la pregunta guía

Todo el taller gira en torno a una sola pregunta, que se responde de a poco:

> **"¿De qué depende el valor de una vivienda en California?"**

Dividida en dos sub-preguntas:
1. *¿El ingreso de la zona se relaciona con el valor?* → se responde con el **scatter** (M6/M7).
2. *¿La cercanía al océano influye?* → se responde con el **groupby** (M5) y el **boxplot** (M7).

Menciónalas explícitamente en cada módulo ("¿recuerdan la pregunta? ahora tenemos otra pieza").
Esto le da **narrativa** al taller y evita que se sienta como una lista de funciones sueltas.

---

## Consejos pedagógicos por módulo

**M1 · Introducción.** Arranca con la pregunta abierta *"¿qué entienden por analizar datos?"* y
recoge respuestas. No definas las librerías a fondo: solo di, en una frase, para qué sirve cada una.
Cierra con **"Haz tu apuesta"**: que voten a mano alzada qué factor creen que pesa más. Anota el
resultado en el tablero; lo retomarás en M7 y M8.

**M2 · NumPy.** El objetivo es la **idea** de arreglo y vectorización, no el álgebra lineal.
Resiste la tentación de mostrar operaciones matriciales. La **carrera lista vs. NumPy** es el gancho:
pide predicciones antes de ejecutar y compara resultados entre computadores.

**M3 · Pandas intro.** Usa el DataFrame de ejemplo (creado a mano) como "maqueta" del dataset real.
Enfatiza filas = registros, columnas = variables.

**M4 · Cargar y explorar.** Aquí es donde ven el dato real. Haz que **lean** la salida de `info()`
y descubran ellos mismos que a `total_bedrooms` le faltan valores. El **momento detective** de
`value_counts()` (solo 5 distritos `ISLAND`) prepara una lección clave de M5.

**M5 · Manipular.** El módulo más denso. Plantea micro-preguntas y respóndelas con código en vivo.
El `groupby` por `ocean_proximity` es el momento "ajá" de la sub-pregunta 2. Antes de mostrar
`agg(["mean", "count"])`, pregunta: *"¿confiamos en que ISLAND es la zona más cara?"*

**M6 · Matplotlib.** Insiste en que **toda** gráfica lleva título y ejes. Señala el "pico" en 500.001
del histograma como ejemplo de que graficar revela cosas. El **mapa escondido** (longitud vs. latitud)
suele ser el momento más memorable: no anuncies qué va a aparecer, deja que lo descubran.

**M7 · Seaborn.** Muestra lo poco que hay que escribir comparado con Matplotlib. El `boxplot` cierra
la sub-pregunta 2 de forma contundente. El `heatmap` de correlaciones resuelve la **apuesta** de M1:
explica la escala -1 a 1 en una frase y compara con lo que votó el grupo. Si vas corto de tiempo,
esta es la sección que puedes mostrar ya ejecutada.

**M8 · Cierre.** Deja que **ellos** propongan variables y tipo de gráfico para el reto final.
Repasa el flujo completo, las **tres ideas para llevar** y comparte el material complementario.
La solución del reto está oculta en un desplegable para quien quiera revisarla después.

---

## Gestión del ritmo y la participación

- **Programa en vivo**, no solo leas celdas. Cometer (y corregir) un error a propósito enseña mucho.
- Deja **pausas** para que ejecuten y comprueben que les sale lo mismo.
- Ten preparadas las **respuestas a los "¿por qué?"** más probables (¿por qué `[[ ]]`?, ¿qué es `alpha`?).
- Si alguien se atasca con el entorno, ten lista la alternativa de **Colab con URL** para no frenar al grupo.

---

## Después del taller

- Comparte el repositorio completo (notebooks ejecutados + `ejercicios/` + `material_complementario/`).
- Anima a resolver los ejercicios, incluidos los retos extra que van más allá de lo visto.
- Registra la asistencia.
- Recoge retroalimentación (¿qué quedó claro?, ¿qué faltó?) para mejorar la próxima edición.

---

## Mapa de archivos → actividades

```
notebooks/01_introduccion.ipynb            → Actividad 1 (M1)
notebooks/02_numpy.ipynb                    → Actividad 2 (M2)
notebooks/03_pandas_introduccion.ipynb      → Actividad 3 (M3)
notebooks/04_pandas_carga_exploracion.ipynb → Actividad 4 (M4)
notebooks/05_pandas_manipulacion.ipynb      → Actividad 5 (M5)
notebooks/06_matplotlib.ipynb               → Actividad 6 (M6)
notebooks/07_seaborn.ipynb                  → Actividad 7 (M7)
notebooks/08_cierre.ipynb                   → Actividad 8 (M8)
```
