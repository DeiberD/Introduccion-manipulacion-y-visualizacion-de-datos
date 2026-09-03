# Guía de configuración del entorno

Para hacer el taller necesitas poder ejecutar **Jupyter Notebooks** con Python. Tienes dos caminos.
Si no quieres instalar nada, usa **Google Colab**.

---

## Opción A - Google Colab (sin instalar nada)

[Google Colab](https://colab.research.google.com/) ejecuta notebooks en la nube, gratis, desde el
navegador. Ya trae NumPy, Pandas, Matplotlib y Seaborn instalados.

### Pasos

1. Entra a **https://colab.research.google.com/** e inicia sesión con tu cuenta de Google.
2. `Archivo → Subir cuaderno` y selecciona el notebook `.ipynb` que quieras trabajar.
3. Ahora necesitas que Colab pueda leer el dataset. Tienes dos formas:

   **Forma 1 (la más fácil): cargar el CSV desde la URL.**
   Cambia la línea de carga del notebook por:
   ```python
   url = "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
   df = pd.read_csv(url)
   ```

   **Forma 2: subir el archivo `housing.csv` a la sesión.**
   ```python
   from google.colab import files
   files.upload()          # se abre un botón para subir housing.csv

   import pandas as pd
   df = pd.read_csv("housing.csv")   # ¡ojo! sin '../data/', queda en la raíz
   ```

> Recuerda: en Colab las rutas cambian. Si el notebook dice `../data/housing.csv` y subiste el
> archivo con `files.upload()`, la ruta correcta es simplemente `housing.csv`.

---

## Opción B — Instalación local

### Paso 1: Instalar Python

Descarga Python (versión 3.9 o superior) desde https://www.python.org/downloads/.

> En Windows, durante la instalación marca la casilla **"Add Python to PATH"**.

Verifica en una terminal:
```bash
python --version
```

### Paso 2: Descargar este repositorio

Con Git:
```bash
git clone https://github.com/DeiberD/Introduccion-manipulacion-y-visualizacion-de-datos.git
cd Introduccion-manipulacion-y-visualizacion-de-datos
```

O sin Git: en GitHub, botón verde **`Code → Download ZIP`**, y descomprime.

### Paso 3: Crear un entorno virtual (recomendado)

Un entorno virtual mantiene las librerías del taller separadas del resto de tu sistema.

```bash
python -m venv .venv
```

Actívalo:
```bash
# En Windows (PowerShell):
.venv\Scripts\activate

# En macOS / Linux:
source .venv/bin/activate
```

Sabrás que está activo porque tu terminal mostrará `(.venv)` al principio.

### Paso 4: Instalar las librerías

```bash
pip install -r requirements.txt
```

### Paso 5: Abrir Jupyter

```bash
jupyter notebook
```

Se abrirá en tu navegador. Entra a la carpeta `notebooks/` y abre `01_introduccion.ipynb`.

---

## Verificar que todo funciona

Abre un notebook y ejecuta esta celda (con `Shift + Enter`):

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

print("NumPy     :", np.__version__)
print("Pandas    :", pd.__version__)
print("Matplotlib:", plt.matplotlib.__version__)
print("Seaborn   :", sns.__version__)
print("\n¡Todo listo para el taller! 🎉")
```

Si no aparece ningún error, estás list@.

---

## Problemas comunes

| Problema | Solución |
|----------|----------|
| `ModuleNotFoundError: No module named 'pandas'` | No instalaste las librerías o no activaste el entorno. Corre `pip install -r requirements.txt`. |
| `FileNotFoundError` al leer el CSV | Estás ejecutando desde otra carpeta. Verifica la ruta (`../data/housing.csv` desde `notebooks/`). |
| Las gráficas no aparecen | En notebooks modernos aparecen solas; si no, agrega `%matplotlib inline` al inicio. |
| `jupyter: command not found` | Instala Jupyter con `pip install jupyter` (o usa Google Colab). |

---

## Atajos útiles de Jupyter

| Atajo | Acción |
|-------|--------|
| `Shift + Enter` | Ejecutar la celda y pasar a la siguiente |
| `Ctrl + Enter` | Ejecutar la celda y quedarse en ella |
| `A` / `B` | Insertar celda arriba / abajo (en modo comando) |
| `M` / `Y` | Convertir celda a Markdown / código |
| `D D` | Borrar la celda seleccionada |
| `Kernel → Restart & Run All` | Reiniciar y ejecutar todo desde cero |
