""" Cargamos las librerias más útiles que posiblemente utilizaremos en el desarrollo de la prueba técnica que en nuestro caso
son: pandas y numpy."""

import pandas as pd   # Pandas es una biblioteca para el lenguaje de programación Python proporciona herramientas que permiten:
# leer y escribir datos en diferentes formatos: CSV, Microsoft Excel, bases SQL etc. Seleccionar y filtrar de manera sencilla 
# tablas de datos en función de posición, valor o etiquetas, fusionar y unir datos etc
import numpy as np   # NumPy es una biblioteca para el lenguaje de programación Python que da soporte para crear vectores
# y matrices grandes multidimensionales, junto con una gran colección de funciones matemáticas de alto nivel para operar 
# con ellas.


""" Leo el fichero con pandas y los almaceno en un dataframe llamado data, los datos selecionados son: "Yellow Taxi" de NYC 
en las fecha de abril de 2019."""

data = pd.read_csv('yellow_tripdata_2019-04.csv')
data.head()


""" Leo el fichero con pandas y los almaceno en un dataframe llamado ("data_1"), los datos selecionados son "taxi+_zone_lookup".
Este CSV, aporta los datos de: ("Borough", "Zone" y "sevice_zone"), dependiendo de ("PULocationID" y "DOLocationID")
del dataframe original ("data")"""

data_1 = pd.read_csv('taxi+_zone_lookup.csv')
data_1.head()


""" Comprobamos el número de columnas y filas que contiene nuestro dataframe ("data"). 
Observamos que consta de 7433139 registros o filas y 18 columnas"""

data.shape


""" Comprobamos el número de columnas y filas que contiene nuestro dataframe ("data_1"). 
Observamos que consta de 265 registros o filas y 4 columnas"""

data_1.shape


""" Mergeamos los dos Dataframe ("data", "data_1"), con la función left. Así podemos determinar los diferentes destinos 
("Borough", "Zone"), dentro del dataframe principal ("data")."""

data_merged = pd.merge(left=data,right=data_1, how='left', left_on='DOLocationID', right_on='LocationID')
data_merged.head()


"""Comprobamos las columnas que tiene nuestro nuevo dataframe ("data_merged") para posteriormente eliminar las que no 
sean necesarias para realizar la prueba. Por tanto, dejamos las columnas:("trip_distance", "Borough", "Zone") y creamos
un nuevo dataframe llamado: ("df")."""

data_merged.columns

df = data_merged.drop(['VendorID', 'tpep_pickup_datetime', 'tpep_dropoff_datetime',
       'passenger_count', 'RatecodeID', 'store_and_fwd_flag',
       'PULocationID', 'DOLocationID', 'payment_type', 'fare_amount', 'extra',
       'mta_tax', 'tip_amount', 'tolls_amount', 'improvement_surcharge',
       'total_amount', 'congestion_surcharge', 'LocationID','service_zone'], axis=1)
df.head()


""" Calculamos los viajes que superan el percentil 0.95 en distancia de viaje ("trip_distance"). Para ello, primero calculamos
el valor de dicho percentil (p95), para posteriormente eliminar todos los registros ó filas que estén por debajo de la cifra
calculada."""

p95 = df["trip_distance"].quantile(0.95)
p95

df = df.drop(df[df['trip_distance']<11.2].index)
df.head()


""" Finalmente, mediante el groupby agrupamos y sumamos el numero de destinos finales iguales por Zona y creamos una nueva
columna para contabilizarlos llamada: ("Trips"), les ordenamos de mayor a menor y sacamos por pantalla los diez primeros como
marca el enunciado."""


df_final = df.groupby(["Zone","Borough"]).size().reset_index(name = "Trips").sort_values(by="Trips",ascending=False)
df_final.head(10)

	Zone				Borough		Trips
121	JFK Airport			Queens		55295
131	LaGuardia Airport		Queens		20562
167	Newark Airport			EWR		14026
224	Times Sq/Theatre District	Manhattan	11479
45	Clinton East			Manhattan	7390
157	Midtown South			Manhattan	7048
154	Midtown Center			Manhattan	6793
163	Murray Hill			Manhattan	6676
155	Midtown East			Manhattan	5953
156	Midtown North			Manhattan	5291
