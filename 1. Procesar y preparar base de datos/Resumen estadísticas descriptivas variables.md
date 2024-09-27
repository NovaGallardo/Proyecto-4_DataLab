# Resumen estadísticas descriptivas variables

```python
# Instalar librería para conectar con BigQuery
!pip install --upgrade google-cloud-bigquery

# Autenticar cuenta
from google.colab import auth
auth.authenticate_user()
# Importar las librerías necesarias
from google.cloud import bigquery
import pandas as pd
import numpy as np

# Definir tu ID de proyecto (cámbialo por tu propio ID de proyecto)
project_id = 'proyecto-4-435418'

# Crear el cliente de BigQuery con el ID de proyecto
client = bigquery.Client(project=project_id)

# Escribir la consulta para obtener todas las columnas de la tabla
query = """
SELECT *
FROM `proyecto-4-435418.vuelos.flights`
"""

# Ejecutar la consulta y cargar los datos en un DataFrame
df = client.query(query).to_dataframe()

# Filtrar solo las columnas numéricas
df_numeric = df.select_dtypes(include=[np.number])

# Calcular la moda para cada columna
def mode(series):
   return series.mode()[0] if not series.mode().empty else np.nan

# Calcular las medidas estadísticas para las columnas numéricas
stats = pd.DataFrame({
   'Mínimo': df_numeric.min(),
   'Máximo': df_numeric.max(),
   'Promedio': df_numeric.mean(),
   'Mediana': df_numeric.median(),
   'Desviación Estándar': df_numeric.std(),
   'Moda': df_numeric.apply(mode)
})

# Visualizar las estadísticas
print(stats)
```

| **Variable** | **Desviación Estándar** | **Interpretación** |
| --- | --- | --- |
| fl_number | 1547.20 | Alta variabilidad en el número de vuelo, indicando que los números de vuelo son muy diversos. |
| origin_latitude | 6.05 | Moderada variabilidad en la latitud de origen, con cambios significativos en la ubicación. |
| origin_longitude | 18.94 | Alta variabilidad en la longitud de origen, sugiriendo ubicaciones muy dispersas. |
| number_of_runways_origin | 1.60 | Baja variabilidad en el número de pistas en el origen, indicando poca diferencia entre aeropuertos. |
| dest_latitude | 6.05 | Similar a la latitud de origen, muestra variabilidad moderada en la ubicación del destino. |
| dest_longitude | 18.94 | Similar a la longitud de origen, con alta variabilidad en la ubicación del destino. |
| number_of_runways_dest | 1.60 | Similar al número de pistas en el origen, baja variabilidad en el destino. |
| dep_delay | 54.95 | Alta desviación en los retrasos de salida, indicando grandes variaciones en el tiempo de partida. |
| taxi_out | 10.82 | Moderada variabilidad en el tiempo de rodaje hacia la pista, con diferencias en los tiempos de despegue. |
| taxi_in | 6.45 | Moderada variabilidad en el tiempo de rodaje después del aterrizaje, indicando diferencias en el tiempo de llegada. |
| arr_delay | 56.78 | Alta variabilidad en los retrasos de llegada, mostrando que los tiempos de llegada son muy variables. |
| crs_elapsed_time | 73.88 | Alta desviación en el tiempo total de vuelo, con grandes diferencias en la duración planificada. |
| elapsed_time | 75.95 | Similar a crs_elapsed_time, con alta variabilidad en el tiempo total de vuelo real. |
| air_time | 73.00 | Alta variabilidad en el tiempo de vuelo en aire, indicando grandes diferencias en el tiempo real de vuelo. |
| cancelled | 0.14 | Muy baja variabilidad en los vuelos cancelados, con la mayoría de los vuelos no siendo cancelados. |
| diverted | 0.05 | Muy baja variabilidad en vuelos desviados, la mayoría no se desvía. |
| distance | 600.13 | Alta desviación en la distancia de vuelo, mostrando vuelos que varían significativamente en distancia. |
| delay_due_carrier | 35.77 | Alta variabilidad en retrasos causados por la aerolínea, indicando grandes diferencias en el servicio. |
| delay_due_weather | 17.81 | Moderada variabilidad en retrasos por clima, mostrando una cantidad considerable de retrasos debido al clima. |
| delay_due_nas | 16.30 | Moderada desviación en retrasos causados por el sistema nacional de tráfico aéreo. |
| delay_due_security | 1.35 | Baja variabilidad en retrasos por seguridad, la mayoría de los retrasos no son significativos. |
| delay_due_late_aircraft | 28.79 | Alta desviación en retrasos por aeronaves retrasadas, mostrando grandes diferencias en la puntualidad de las aeronaves. |
| distance_km | 964.11 | Alta variabilidad en la distancia en kilómetros, indicando una amplia gama de distancias de vuelo. |

```python
# Filtrar solo las columnas de tipo STRING
df_string = df.select_dtypes(include=[object])

# Calcular la moda para cada columna STRING
def mode(series):
   return series.mode()[0] if not series.mode().empty else np.nan

# Calcular la moda para las columnas STRING
modes = df_string.apply(mode)

# Crear un DataFrame para visualizar la moda
modes_df = pd.DataFrame({
   'Variable': df_string.columns,
   'Moda': modes
})

# Visualizar las modas
print(modes_df)
```
