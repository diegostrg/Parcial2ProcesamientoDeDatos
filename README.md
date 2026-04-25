Este repo contiene el notebook del 2do parcial (PAVD) donde armamos un pipeline con PySpark para limpiar/preparar datos de calidad del agua en India y, al final, entrenamos un modelo con Keras para predecir el índice WQI (Water Quality Index).

La idea fue trabajar sobre lo que ya estaba hecho: dejarlo más reproducible (que corra en local sin depender del cluster), y mejorar la documentación dentro del notebook (texto más claro, títulos y comentarios consistentes), sin cambiar el “fondo” del flujo.

Archivos del repo
- Clean_ML_Water.ipynb: notebook principal.
- waterquality.csv: dataset (fuente de datos para Spark).
- Indian_States.shp/.shx/.dbf/.prj: shapefile para graficar el mapa de India.
- Indian_States.zip: backup del shapefile.

Requisitos (local)
- Python 3.
- Java (requerido por Spark).
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

Qué se hace en el notebook (parte por parte)

1) Importación de bibliotecas
Se importan librerías de análisis (numpy/pandas), visualización (matplotlib/seaborn) y Spark (findspark + pyspark). La intención es dejar listo el entorno para ejecutar todo el flujo desde un solo notebook.

2) Levantamiento de sesión Spark
Se crea la SparkSession (sparkMunoz) con un nombre de aplicación. Desde ahí se trabaja todo el dataset usando DataFrames de Spark.

3) Carga de datos
Se carga el CSV en un DataFrame de Spark (df00) y se hace una inspección rápida con show() y columns.

4) Análisis y preparación de datos
Se revisan columnas y consistencia. Se hace un chequeo de valores nulos/imposibles y se filtran registros con campos críticos en null.

5) Tratamiento de datos
Se ajustan tipos (cast a FloatType en columnas numéricas) y se eliminan columnas que no se van a usar (por ejemplo TOTAL_COLIFORM). Esto ayuda a que los cálculos posteriores sean consistentes.

6) Construcción de rangos de calidad (qr*)
Para cada parámetro (pH, DO, Conductivity, BOD, Nitrates y Fecal Coliform) se crean columnas tipo “qr...” con una escala discreta (por ejemplo 100/80/60/40/0) según rangos. Estas columnas representan rangos de “calidad” por variable y son la base para el WQI.

7) Cálculo del índice WQI
Con las columnas de rangos (qr*) se calcula el WQI. Luego se crean etiquetas/categorías de calidad del agua a partir del WQI (por ejemplo Excelente/Buena/etc.).

8) Visualización por estado + mapa
Se prepara el shapefile de India usando GeoPandas y se cruza con los datos (por la columna STATE) para poder pintar el mapa.
- En esta versión del repo, la ruta del shapefile se deja como Indian_States.shp (archivo local del repo).
- También se ajustan algunos nombres de estados para que coincidan entre el shapefile y el DataFrame principal.

9) Histograma de WQI por estado
Se grafica un histograma (o visualización equivalente) para revisar cómo se distribuye el WQI (y/o etiquetas) a través de estados.

10) Creación de modelo (Keras)
Se arma un modelo Sequential de Keras para regresión:
- Entradas: qrPH, qrDO, qrCOND, qrBOD, qrNN, qrFecal.
- Salida: WQI.
Se hace split de entrenamiento/prueba con scikit-learn, se compila con Adam y MSE, y se entrena. También se deja un gráfico de la pérdida (MSE) durante el entrenamiento y una comparación simple “real vs predicho” sobre el set de entrenamiento.

Qué se mejoró (sin cambiar el flujo)
- Se dejó la lectura HDFS como alternativa comentada y se habilitó la carga local desde waterquality.csv.
- Se limpiaron textos y títulos que estaban con formato extraño (por ejemplo headings dentro de viñetas).
- Se ajustó la carga del shapefile para que apunte a Indian_States.shp del repo.
- Se pulió la sección de “Creación de Modelo” para que explique el proceso de forma más natural.

Siguiente paso (cuando me digas)
- Agregar la parte final de métricas/comparativa del modelo (MAE/RMSE/R², baselines) y luego conclusiones + referencias, pero solo cuando lo autorices (para mantener el control de cambios).