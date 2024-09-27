# RR de default flag según age group

```python
# Execute the query and load the data into a pandas DataFrame
flights_data = client.query(query).to_dataframe()

# Select only numerical columns for correlation
numerical_columns = flights_data.select_dtypes(include=[float, int])

# Compute the correlation matrix
correlation_matrix = numerical_columns.corr()

# Plotting the heatmap for the correlation matrix
plt.figure(figsize=(12, 8))
plt.title("Correlation Heatmap of Flight Data", fontsize=16)
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".2f", linewidths=.5)
plt.show()
```

| **variables** | **correlacion_pearson** | **interpretacion** |
| --- | --- | --- |
| elapsed_time y air_time | 0.99 | positiva alta |
| csr_elapsed_time y distance_km | 0.98 | positiva alta |
| dep_delay y arr_delay | 0.96 | positiva alta |
| air_time y csr_elapsed_time | 0.96 | positiva alta |
| air_time y distance_km | 0.95 | positiva alta |
| elapsed_time y csr_elapsed_time | 0.94 | positiva alta |
| elapsed_time y distance_km | 0.92 | positiva alta |
| delay_due_carrier y dep_delay | 0.69 | positiva moderada |
| delay_due_carrier y arr_delay | 0.68 | positiva moderada |
| delay_due_late_aircraft y dep_delay | 0.59 | positiva moderada |
| delay_due_late_aircraft y arr_delay | 0.58 | positiva moderada |
| delay_due_nas y arr_delay | 0.35 | positiva baja |
| delay_due_weather y arr_delay | 0.33 | positiva baja |
| delay_due_weather y dep_delay | 0.32 | positiva baja |
| delay_due_nas y taxi_out | 0.29 | positiva baja |
| number_runways_dest y taxi_in | 0.27 | positiva baja |
| delay_due_nas y dep_delay | 0.27 | positiva baja |
| elapsed_time y taxi_out | 0.23 | positiva baja |
| arr_delay y taxi_out | 0.21 | positiva baja |
| elapsed_time y taxi_in | 0.21 | positiva baja |
| number_runways_origin y taxi_out | 0.13 | sin correlacion significativa |
| air_time y taxi_in | 0.12 | sin correlacion significativa |
| crs_elapsed_time y taxi_in | 0.11 | sin correlacion significativa |
| arr_delay y taxi_in | 0.1 | sin correlacion significativa |
| crs_elapsed_time y taxi_out | 0.07 | sin correlacion significativa |
| delay_due_security y arr_delay | 0.03 | sin correlacion significativa |
| delay_due_security y dep_delay | 0.03 | sin correlacion significativa |
| number_runways_origin y arr_delay | 0.02 | sin correlacion significativa |
| number_runways_origin y dep_delay | 0.01 | sin correlacion significativa |
| number_runways_dest y arr_delay | 0.01 | sin correlacion significativa |

Todas las **correlaciones positivas altas** son esperables, ya que relacionan duración de vuelo con distancia del viaje, o bien retraso de partida con retraso en llegada. No arrojan nueva información importante sobre causas de retraso, cancelación o desviación. La única deducción interesante podría ser que si un avión se retrasa al salir, siempre llegará retrasado a destino. Es decir, no es algo que se pueda revertir o subsanar. A su vez, ambos retrasos tienen el mismo impacto, por lo cual se puede elegir solo 1 para efectos del análisis. En este caso, el arr_delay.

Las **correlaciones positivas moderadas** son las que más luz arrojan respecto a las causas de retraso. Delay_du_carrier es la causa más significativa en relación a retrasos de partida (0.69) y de llegada (0.68). Le sigue delay_due_aicraft (0.59 y 0.58). Ambos tipos de retraso, del operador y de aeronaves tardías, están estrechamente relacionados.

Las **correlaciones positivas bajas** nos arrojan información sobre las demás causas de retraso que tienen incidencia en arr_delay y dep_delay, que son delay_due_nas (0.35 y 0.27) y delay_due_weather (0.33 y 0.32). Delay_due_nas tiene correlación también con taxi_out, lo cual tiene sentido, pues se trata de un retraso del Sistema Aéreo Nacional. Algo interesante es que taxi_in tiene relación con la cantidad de pistas del aeropuerto de destino (0.27). Elapsed_time (tiempo total de vuelo) tiene correlación baja con taxi_out (0.23) y taxi_in (0.21). La correlación entre retraso de llegada y taxi_in, aunque baja (0.21), es esperable.

En conclusión, a partir de la correlación de Pearson, podemos resumir lo siguiente:

- Las causas de retrasos más significativas son delay_du_carrier (retraso del operador) y delay_due_aircraft (retraso de aeronaves tardías), ambas lógicamente relacionadas. Sin embargo, no se trata de correlación alta, si no que moderada.
- Delay_due_nas (retraso del Sistema Aéreo Nacional) y delay_due_weather (retraso meteorológico) son las causas de retraso que siguen en significancia, la cual es baja.
- Delay_due_security (retraso de seguridad) no posee correlación significativa con los retrasos.
- La cantidad de pistas en el aeropuerto de destino tiene correlación positiva baja con el taxi_in, es decir, el tiempo que transcurre desde que el avión aterriza hasta que llega a la puerta de embarque.
- La cantidad de pistas en el aeropuerto de origen no posee correlación significativa con el taxi_out, así como tampoco con el arr_delay.
- No se pueden establecer correlaciones ni para la cancelación ni para la desviación de vuelos, ya que son variables booleanas y categóricas.
