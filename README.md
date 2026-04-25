## **Parcial 2: Procesamiento de Datos a Gran Escala**
### Juan Diego Muñoz Angulo
### Jhon Jairo Corredor Franco

This repository contains the notebook for the second project, where we built a PySpark pipeline to clean/prepare water quality data in India and, at the end, trained a Keras model to predict the WQI (Water Quality Index).

The idea was to work on what was already done: make it more reproducible (so it runs locally without depending on the cluster), and improve the documentation inside the notebook (clearer text, consistent titles, and comments), without changing the core workflow.

Repository files
- Clean_ML_Water.ipynb: main notebook.
- waterquality.csv: dataset (data source for Spark).
- Indian_States.shp/.shx/.dbf/.prj: shapefile used to plot the India map.
- Indian_States.zip: shapefile backup.

Requirements
- Python 3
- Java
- Python packages used throughout the notebook:
  - findspark, pyspark
  - numpy, pandas
  - matplotlib, seaborn
  - scikit-learn
  - tensorflow/keras
  - geopandas
  - adjustText

Note: on Linux, geopandas may require system dependencies (GDAL/GEOS). If installation fails, the most practical approach is to install geopandas in an environment with available wheels (or use conda). For this project, the key point is that the map works in your environment.

How to run it
1) Open Clean_ML_Water.ipynb.
2) Select a Python kernel with the required dependencies.
3) Run cells in order (Run All).

Dataset loading (local vs cluster)
There are two options in the notebook:
- Option A (cluster/HDFS): read from HDFS (left commented).
- Option B (local): read from the waterquality.csv file in the same notebook directory (active).

What is done in the notebook.

1) Library imports
Analysis libraries (numpy/pandas), visualization libraries (matplotlib/seaborn), and Spark libraries (findspark + pyspark) are imported. The goal is to have the environment ready to execute the complete workflow from a single notebook.

1) Spark session setup
The SparkSession (sparkMunoz) is created with an application name. From that point, the whole dataset is processed using Spark DataFrames.

1) Data loading
The CSV is loaded into a Spark DataFrame (df00), followed by a quick inspection using show() and columns.

1) Data analysis and preparation
Columns and consistency are reviewed. A check for null/impossible values is performed, and rows with nulls in critical fields are filtered out.

1) Data treatment
Types are adjusted (cast to FloatType on numeric columns) and columns that are not used are removed (for example, TOTAL_COLIFORM). This helps keep later calculations consistent.

1) Quality range construction (qr*)
For each parameter (pH, DO, Conductivity, BOD, Nitrates, and Fecal Coliform), “qr...” columns are created with a discrete quality scale (for example 100/80/60/40/0) according to ranges. These columns represent quality ranges by variable and are the basis for WQI.

1) WQI index calculation
WQI is computed using the quality range columns (qr*). Then, water quality labels/categories are created from WQI (for example, Excellent/Good/etc.).

1) State-level visualization + map
The India shapefile is prepared with GeoPandas and merged with the data (using the STATE column) to plot the map.
- In this repository version, the shapefile path is set as Indian_States.shp (local repository file).
- Some state names are also adjusted to match between the shapefile and the main DataFrame.

1) WQI histogram by state
A histogram (or equivalent visualization) is plotted to inspect how WQI (and/or labels) is distributed across states.

1)  Model creation (Keras)
A Keras Sequential model for regression is built:
- Inputs: qrPH, qrDO, qrCOND, qrBOD, qrNN, qrFecal.
- Output: WQI.
A train/test split is done with scikit-learn, the model is compiled with Adam and MSE, and then trained. A training loss (MSE) plot and a simple “real vs predicted” comparison on the training set are also included.

Key theoretical aspects of the work
- WQI was approached as a synthetic quality index, useful for summarizing multiple physicochemical and microbiological variables into a single interpretable measure.
- Transforming original variables into discrete scales (qr*) followed a range-based normalization logic, allowing heterogeneous parameters to be compared on the same scale.
- The use of Spark in the preparation stage was justified by its distributed and reproducible approach for cleaning, typing, and transforming tabular data.
- Geospatial analysis was based on the idea that water quality varies not only in magnitude, but also in territorial pattern; therefore, the shapefile merge was key to interpreting spatial structure.
- Supervised modeling was framed as a regression problem over WQI, where the objective was to capture the functional relationship between input indicators and the aggregated index.

Relevant methodological addition
- In addition to base model training, I incorporated a more robust validation using cross-validation to evaluate performance stability.
- I included complementary metrics (MAE, RMSE, and R²) to avoid conclusions based on a single error measure.
- I added a comparison against traditional baselines to contrast whether the neural model actually provides additional predictive value.
- I added a sensitivity analysis of WQI weights to observe how changes in weighting impact the final index.

Conclusions
- The built pipeline makes it possible to move from raw data to an interpretable quality index in a consistent way, with a clear sequence of cleaning, transformation, and analysis.
- Range-based construction (qr*) facilitates the integration of variables with different units and scales, and stays aligned with the objective of representing quality in a single dimension.
- Territorial visualization adds analytical value because it reveals differences across states that are not always visible in tables or global summaries.
- The Keras regression model is useful to approximate WQI, but its interpretation must rely on out-of-sample validation and comparison with baseline models.
- Incorporating cross-validation, multiple metrics, and sensitivity analysis strengthens the methodological solidity of the work and reduces the risk of over-interpreting isolated results.

---

## **Parcial 2: Procesamiento de Datos a Gran Escala**
### Juan Diego Muñoz Angulo
### Jhon Jairo Corredor Franco

Este repo contiene el notebook del 2do parcial, donde armamos un pipeline con PySpark para limpiar/preparar datos de calidad del agua en India y, al final, entrenamos un modelo con Keras para predecir el índice WQI (Water Quality Index).

La idea fue trabajar sobre lo que ya estaba hecho: dejarlo más reproducible (que corra en local sin depender del cluster), y mejorar la documentación dentro del notebook (texto más claro, títulos y comentarios consistentes), sin cambiar el “fondo” del flujo.

Archivos del repo
- Clean_ML_Water.ipynb: notebook principal.
- waterquality.csv: dataset (fuente de datos para Spark).
- Indian_States.shp/.shx/.dbf/.prj: shapefile para graficar el mapa de India.
- Indian_States.zip: backup del shapefile.

Requisitos
- Python 3
- Java
- Paquetes de Python usados a lo largo del notebook:
  - findspark, pyspark
  - numpy, pandas
  - matplotlib, seaborn
  - scikit-learn
  - tensorflow/keras
  - geopandas
  - adjustText

Nota: en Linux, geopandas a veces requiere dependencias del sistema (GDAL/GEOS). Si tu instalación falla, lo más práctico es instalar geopandas desde un entorno con wheels disponibles (o usar conda). Para el parcial, lo importante es que el mapa funcione en tu entorno.

Cómo ejecutarlo
1) Abre Clean_ML_Water.ipynb.
2) Selecciona un kernel de Python que tenga las dependencias.
3) Ejecuta las celdas en orden (Run All).

Carga del dataset (local vs cluster)
En el notebook hay dos opciones:
- Opción A (cluster/HDFS): lectura desde HDFS (queda comentada).
- Opción B (local): lectura desde el archivo waterquality.csv en el mismo directorio del notebook (queda activa).

Qué se hace en el notebook.

1) Importación de bibliotecas
Se importan librerías de análisis (numpy/pandas), visualización (matplotlib/seaborn) y Spark (findspark + pyspark). La intención es dejar listo el entorno para ejecutar todo el flujo desde un solo notebook.

1) Levantamiento de sesión Spark
Se crea la SparkSession (sparkMunoz) con un nombre de aplicación. Desde ahí se trabaja todo el dataset usando DataFrames de Spark.

1) Carga de datos
Se carga el CSV en un DataFrame de Spark (df00) y se hace una inspección rápida con show() y columns.

1) Análisis y preparación de datos
Se revisan columnas y consistencia. Se hace un chequeo de valores nulos/imposibles y se filtran registros con campos críticos en null.

1) Tratamiento de datos
Se ajustan tipos (cast a FloatType en columnas numéricas) y se eliminan columnas que no se van a usar (por ejemplo TOTAL_COLIFORM). Esto ayuda a que los cálculos posteriores sean consistentes.

1) Construcción de rangos de calidad (qr*)
Para cada parámetro (pH, DO, Conductivity, BOD, Nitrates y Fecal Coliform) se crean columnas tipo “qr...” con una escala discreta (por ejemplo 100/80/60/40/0) según rangos. Estas columnas representan rangos de “calidad” por variable y son la base para el WQI.

1) Cálculo del índice WQI
Con las columnas de rangos (qr*) se calcula el WQI. Luego se crean etiquetas/categorías de calidad del agua a partir del WQI (por ejemplo Excelente/Buena/etc.).

1) Visualización por estado + mapa
Se prepara el shapefile de India usando GeoPandas y se cruza con los datos (por la columna STATE) para poder pintar el mapa.
- En esta versión del repo, la ruta del shapefile se deja como Indian_States.shp (archivo local del repo).
- También se ajustan algunos nombres de estados para que coincidan entre el shapefile y el DataFrame principal.

1) Histograma de WQI por estado
Se grafica un histograma (o visualización equivalente) para revisar cómo se distribuye el WQI (y/o etiquetas) a través de estados.

1)  Creación de modelo (Keras)
Se arma un modelo Sequential de Keras para regresión:
- Entradas: qrPH, qrDO, qrCOND, qrBOD, qrNN, qrFecal.
- Salida: WQI.
Se hace split de entrenamiento/prueba con scikit-learn, se compila con Adam y MSE, y se entrena. También se deja un gráfico de la pérdida (MSE) durante el entrenamiento y una comparación simple “real vs predicho” sobre el set de entrenamiento.

Aspectos teóricos clave del trabajo
- El WQI se abordó como un índice sintético de calidad, útil para resumir múltiples variables fisicoquímicas y microbiológicas en una sola medida interpretable.
- La transformación de variables originales a escalas discretas (qr*) respondió a una lógica de normalización por rangos de calidad, permitiendo comparar parámetros heterogéneos en una misma escala.
- El uso de Spark en la etapa de preparación se justificó por su enfoque distribuido y reproducible para limpieza, tipado y transformación de datos tabulares.
- El análisis geoespacial se sostuvo en la idea de que la calidad del agua no solo varía en magnitud, sino también en patrón territorial; por eso el cruce con shapefile fue clave para interpretar estructura espacial.
- El modelado supervisado se planteó como un problema de regresión sobre WQI, donde el objetivo fue capturar la relación funcional entre indicadores de entrada y el índice agregado.

Agregado metodológico relevante
- Además del entrenamiento base del modelo, incorporé una validación más robusta con validación cruzada para evaluar estabilidad de desempeño.
- Incluí métricas complementarias (MAE, RMSE y R²) para evitar conclusiones apoyadas en una sola medida de error.
- Sumé una comparación contra baselines tradicionales para contrastar si el modelo neuronal realmente aporta capacidad predictiva adicional.
- Añadí un análisis de sensibilidad de los pesos del WQI para observar cómo cambios en ponderaciones impactan el índice final.

Conclusiones
- El pipeline construido permite pasar de datos crudos a un índice de calidad interpretable de forma consistente, con una secuencia clara de limpieza, transformación y análisis.
- La construcción por rangos (qr*) facilita la integración de variables con unidades y escalas distintas, y mantiene coherencia con el objetivo de representar calidad en una sola dimensión.
- La visualización territorial aporta valor analítico porque muestra diferencias por estado que no siempre se perciben en tablas o resúmenes globales.
- El modelo de regresión con Keras resulta útil para aproximar el WQI, pero su interpretación debe apoyarse en validación fuera de muestra y comparación con modelos base.
- La incorporación de validación cruzada, métricas múltiples y análisis de sensibilidad fortalece la solidez metodológica del trabajo y reduce el riesgo de sobreinterpretar resultados puntuales.

