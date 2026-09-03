<div align="center">

# Introducción a la manipulación y visualización de datos con Python

### NumPy · Pandas · Matplotlib · Seaborn

**Grupos de Estudio Autónomo (GEA)**
Facultad de Ingeniería · Universidad Nacional de Colombia - Sede Bogotá

</div>

---

Este repositorio contiene todo el material de un **taller introductorio** (≈ 95 minutos) para
aprender a **cargar, explorar, manipular y visualizar datos estructurados** en Python usando las
cuatro librerías más populares del ecosistema de análisis de datos.

Está pensado para **estudiantes de cualquier semestre** de la Facultad de Ingeniería que no tengan
experiencia previa con estas herramientas. Todo el material es autocontenido: puedes seguirlo en el
taller y también repasarlo por tu cuenta después.

---

## ¿Qué vas a aprender?

Al terminar el taller serás capaz de:

- Explicar con tus propias palabras para qué sirve **NumPy, Pandas, Matplotlib y Seaborn**.
- Crear y manipular **arreglos de NumPy** con operaciones básicas.
- Cargar, explorar y filtrar **datos tabulares con Pandas** (`read_csv`, `head`, `info`, `describe`, `groupby`...).
- Construir e **interpretar gráficas** básicas (histogramas, dispersión, boxplots) con Matplotlib y Seaborn.
- Seguir el **flujo completo del análisis de datos**: `Cargar → Explorar → Manipular → Visualizar → Interpretar`.

Todo se hace resolviendo una pregunta real:
> **"¿De qué depende el valor de una vivienda en California?"**

---

## Estructura del repositorio

```
.
├── README.md                     ← estás aquí
├── requirements.txt              ← librerías necesarias
├── LICENSE                       ← licencia del material (CC BY 4.0)
│
├── data/                         ← el conjunto de datos del taller
│   ├── housing.csv               ← dataset de viviendas de California
│   └── README.md                 ← descripción y diccionario de datos
│
├── notebooks/                    ← material principal del taller (una actividad por notebook)
│   ├── 01_introduccion.ipynb
│   ├── 02_numpy.ipynb
│   ├── 03_pandas_introduccion.ipynb
│   ├── 04_pandas_carga_exploracion.ipynb
│   ├── 05_pandas_manipulacion.ipynb
│   ├── 06_matplotlib.ipynb
│   ├── 07_seaborn.ipynb
│   └── 08_cierre.ipynb
│
├── ejercicios/                   ← práctica para afianzar (con retos extra)
│   ├── 01_ejercicios_numpy.ipynb
│   ├── 02_ejercicios_pandas.ipynb
│   ├── 03_ejercicios_visualizacion.ipynb
│   └── soluciones/               ← soluciones de referencia
│       ├── 01_soluciones_numpy.ipynb
│       ├── 02_soluciones_pandas.ipynb
│       └── 03_soluciones_visualizacion.ipynb
│
├── material_complementario/      ← temas que van MÁS ALLÁ del taller
│   ├── 01_numpy_mas_alla.md
│   ├── 02_pandas_mas_alla.md
│   ├── 03_visualizacion_mas_alla.md
│   └── 04_buenas_practicas.md
│
├── docs/                         ← guías de apoyo
│   ├── configuracion_entorno.md  ← cómo preparar tu entorno (Colab o local)
│   └── guia_facilitador.md       ← guía para quien dicta el taller
│
└── referencias/
    └── bibliografia.md           ← documentación oficial, libros y cursos
```

---

## Cómo empezar

Tienes **dos opciones**. Si nunca has instalado Python, usa la primera.

### Opción A — Google Colab (recomendada, no requiere instalar nada)

1. Entra a [Google Colab](https://colab.research.google.com/).
2. Sube el notebook que quieras trabajar (`Archivo → Subir cuaderno`).
3. Sube también el archivo `data/housing.csv` (o cárgalo desde una URL).

Guía detallada en [`docs/configuracion_entorno.md`](docs/configuracion_entorno.md).

### Opción B — En tu computador (entorno local)

```bash
# 1. Clona el repositorio
git clone https://github.com/DeiberD/Introduccion-manipulacion-y-visualizacion-de-datos.git
cd Introduccion-manipulacion-y-visualizacion-de-datos

# 2. (Opcional pero recomendado) crea un entorno virtual
python -m venv .venv
source .venv/bin/activate        # En Windows: .venv\Scripts\activate

# 3. Instala las librerías
pip install -r requirements.txt

# 4. Abre Jupyter
jupyter notebook
```

---

## Recorrido sugerido

| Orden | Notebook | Actividad del taller | Duración |
|-------|----------|----------------------|----------|
| 1 | `notebooks/01_introduccion.ipynb` | Introducción y pregunta guía | 10 min |
| 2 | `notebooks/02_numpy.ipynb` | Introducción a NumPy | 10 min |
| 3 | `notebooks/03_pandas_introduccion.ipynb` | Series y DataFrames | 10 min |
| 4 | `notebooks/04_pandas_carga_exploracion.ipynb` | Cargar y explorar datos | 10 min |
| 5 | `notebooks/05_pandas_manipulacion.ipynb` | Filtrar, ordenar y agrupar | 15 min |
| 6 | `notebooks/06_matplotlib.ipynb` | Gráficas con Matplotlib | 15 min |
| 7 | `notebooks/07_seaborn.ipynb` | Gráficas con Seaborn | 15 min |
| 8 | `notebooks/08_cierre.ipynb` | Cierre y repaso | 10 min |

Después del taller: practica con la carpeta **`ejercicios/`** y profundiza con **`material_complementario/`**.

---

## El conjunto de datos

Usamos el **California Housing Prices** (censo de 1990): 20.640 distritos residenciales de California
descritos por 10 variables (ingreso medio, antigüedad, población, valor de la vivienda, cercanía al
océano, etc.). Es ideal para aprender porque tiene variables numéricas, una variable categórica y
valores faltantes reales.

Detalles y diccionario de datos en [`data/README.md`](data/README.md).

---

## Créditos y licencia

- **Taller propuesto por:** Deiber Gongora.
- **Contexto:** Grupos de Estudio Autónomo (GEA), Facultad de Ingeniería, Universidad Nacional de Colombia, Sede Bogotá.
- **Licencia:** el material educativo se publica bajo [CC BY 4.0](LICENSE). Puedes usarlo, adaptarlo y
  compartirlo dando el crédito correspondiente.

¿Encontraste un error o tienes una sugerencia? ¡Abre un *issue* o un *pull request*!
