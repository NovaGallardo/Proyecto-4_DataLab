# Agrupar datos según variables categóricas

- Vuelos retrasados:
    - Distancia: corta
    - Franja horaria origen: tarde (por poca diferencia)
    - Tipo de aeropuerto origen: internacional
    - Cantidad pistas aeropuerto origen: 4
    - Flujo aeropuerto origen: grande
    - Aerolínea: American Airlines Inc.
    - Nr. de vuelo: 1473, 1680, 5477, 5551, 568, 5531, 5041, 1536 (2 casos c/u)
    - Ciudad origen: Denver y Dallas/Fort Worth
    - Ciudad destino: New York
    - Estado origen: CO Colorado y TX Texas
    - Estado destino: NY New York y CA California
    - Costa origen: East Cast y Central (por poca diferencia)
    - Costa destino: East Cast y Central (por poca diferencia)
    - Día de la semana vuelo: miércoles y lunes
    - Día del mes vuelo: 11
    - Puntualidad: short y luego long (por poca diferencia)
    - Taxi out: moderado
    - Taxi in: moderado
- Vuelos cancelados:
    - Motivo: clima
    - Distancia: corta
    - Franja horaria origen: noche
    - Tipo de aeropuerto origen: internacional
    - Cantidad pistas aeropuerto origen: 2 y 4
    - Flujo aeropuerto origen: grande
    - Aerolínea: Southwest Airlines Co.
    - Nr. de vuelo: 1155 (por poca diferencia)
    - Ciudad origen: Denver y Chicago
    - Ciudad destino: Chicago y Atlanta
    - Estado origen: CA California y FL Florida
    - Estado destino: CA California y FL Florida
    - Costa origen: Central (por harta diferencia)
    - Costa destino: Central (por harta diferencia)
    - Día de la semana vuelo: martes y miércoles (por poca diferencia)
    - Día del mes vuelo: 31
- Vuelos desviados:
    - Distancia: corta
    - Franja horaria origen: mañana
    - Tipo de aeropuerto origen: internacional
    - Cantidad pistas aeropuerto origen: 2 y 4
    - Flujo aeropuerto origen: grande
    - Aerolínea: SkyWest Airlines Inc.
    - Nr. de vuelo: 5077 (casi no hay diferencia)
    - Ciudad origen: San Diego
    - Ciudad destino: Chicago
    - Estado origen: CA California
    - Estado destino: CA California y CO Colorado
    - Costa origen: Central
    - Costa destino: Central e East Coast
    - Día de la semana vuelo: lunes y martes
    - Día del mes vuelo: 2 y 3
    - Puntualidad: early
    - Taxi out: moderado
    - Taxi in: moderado

Variables más significativas:

- Distancia
- Causa retraso
- Flujo aeropuerto
- Cantidad de pistas
- Estado / Ciudad
- Aeropuerto

```sql
SELECT
 origin_city,
 COUNT(DISTINCT origin) AS airport_count
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_city`
GROUP BY origin_city
HAVING COUNT(DISTINCT origin) > 1
```

<aside>
💡

Resultado: 2 de las ciudades con más incidentes registrados, Chicago y New York, poseen 2 aeropuertos. Hay que indagar cuál de los 2 aeropuertos es el que presenta los incidentes.

</aside>

## **Scatter plots**

```python
import matplotlib.pyplot as plt

# Execute the query and load the data into a pandas DataFrame
flights_data = client.query(query).to_dataframe()

# Select only numerical columns for correlation
numerical_columns = flights_data.select_dtypes(include=[float, int])

# Assuming the flights_data DataFrame is already loaded and contains 'dep_delay' and 'distance_km'

plt.figure(figsize=(10, 6))
plt.scatter(flights_data['dep_delay'], flights_data['distance_km'], alpha=0.5, c='blue')
plt.title('Scatter Plot: Departure Delay vs Distance (km)')
plt.xlabel('Departure Delay (minutes)')
plt.ylabel('Distance (km)')
plt.grid(True)
plt.show()
```

## **Histogramas**

```python
# Crear el histograma para la variable origin_latitude
plt.hist(df['origin_latitude'], bins=20, edgecolor='black')

# Añadir el título
plt.title('Histograma de origin_latitude')

# Añadir etiquetas a los ejes
plt.xlabel('origin_latitude')
plt.ylabel('Frecuencia')

# Mostrar el gráfico
plt.show()
```

```python
# Crear el histograma para la variable origin_latitude
plt.hist(df['origin_latitude'], bins=20, edgecolor='black')

# Añadir el título
plt.title('Histograma de origin_latitude')

# Añadir etiquetas a los ejes
plt.xlabel('origin_latitude')
plt.ylabel('Frecuencia')

# Mostrar el gráfico
plt.show()
```

```python
# Crear el histograma para la variable dest_latitude

plt.hist(df['dest_latitude'], bins=20, edgecolor='black')

# Añadir el título
plt.title('dest_latitude')

# Añadir etiquetas a los ejes
plt.xlabel('dest_latitude')
plt.ylabel('Frecuencia')

# Mostrar el gráfico
plt.show()
```

```python
# Crear el histograma para la variable dest_longitude

plt.hist(df['dest_longitude'], bins=20, edgecolor='black')

# Añadir el título
plt.title('dest_longitude')

# Añadir etiquetas a los ejes
plt.xlabel('dest_longitude')
plt.ylabel('Frecuencia')

# Mostrar el gráfico
plt.show()
```

```python
# Crear el histograma para la variable distance_km

plt.hist(df['distance_km'], bins=20, edgecolor='black')

# Añadir el título
plt.title('distance_km')

# Añadir etiquetas a los ejes
plt.xlabel('distance_km')
plt.ylabel('Frecuencia')

# Mostrar el gráfico
plt.show()
```
