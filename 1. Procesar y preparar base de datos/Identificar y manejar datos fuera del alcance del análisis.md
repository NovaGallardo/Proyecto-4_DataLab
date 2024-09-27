# **Identificación y manejo de datos fuera del alcance del análisis**

En este proceso se identificaron y manejaron varios aspectos clave en los datos que están fuera del alcance del análisis o que son redundantes. Aquí están los pasos explicados:

---

### 1. Variables reiterativas: `fl_year`, `fl_month`, y `fl_day`

- **Descripción**: Las variables `fl_year`, `fl_month`, y `fl_day` son redundantes ya que la variable `fl_date` ya contiene toda la información sobre la fecha del vuelo (año, mes, y día).
- **Solución**: Estas tres variables se pueden excluir del análisis para evitar duplicación de información.

---

### 2. Uso de la tabla `airline_code_dictionary` en lugar de `dot_code_dictionary`

- **Descripción**: La tabla `dot_code_dictionary` contiene un 36.6% de datos nulos (1,000 nulos de 2,733 filas), mientras que la tabla `airline_code_dictionary` no tiene nulos.
- **Solución**: Dado que la tabla `airline_code_dictionary` es más completa y sin valores nulos, se opta por utilizar esta tabla en los **JOIN** para mejorar la calidad del análisis y evitar la pérdida de información debido a los valores nulos en `dot_code_dictionary`.

---

### 3. Medidas estadísticas de las variables numéricas

Se calcularon varias estadísticas para las variables numéricas en el dataset de vuelos. Aquí se muestran las medidas estadísticas clave:

| **Variable** | **Mínimo** | **Máximo** | **Promedio (Avg)** | **Mediana (Med)** | **Desviación Estándar (Std)** |
| --- | --- | --- | --- | --- | --- |
| `air_time` | 0 | 695 | 113.31 | 97 | 73.00 |
| `arr_delay` | -80 | 3063 | 7.61 | -5 | 56.78 |
| `delay_due_carrier` | 0 | 3024 | 5.31 | 0 | 35.77 |
| `delay_due_late_aircraft` | 0 | 2027 | 8.58 | 0 | 28.79 |
| `delay_due_nas` | 0 | 1343 | 3.17 | 0 | 16.30 |
| `delay_due_security` | 0 | 234 | 0.03 | 0 | 1.35 |
| `delay_due_weather` | 0 | 1653 | 0.95 | 0 | 17.81 |
| `dep_delay` | -52 | 3024 | 12.70 | -2 | 54.95 |
| `distance` | 31 | 5095 | 830.11 | 679 | 600.13 |
| `taxi_in` | 0 | 173 | 7.88 | 6 | 6.45 |
| `taxi_out` | 0 | 222 | 17.99 | 15 | 10.82 |

---

### 4. Creación de Box Plots para variables numéricas

Los **box plots** permiten visualizar la dispersión de los datos, identificando posibles **outliers** y comprendiendo la distribución de las variables numéricas. Aquí se muestra cómo se generan los **box plots** para diferentes variables:

### **Box Plot para la variable `arr_delay` (Retraso en la llegada)**

```python
plt.figure(figsize=(10, 6))  # Ajustar el tamaño del gráfico
sns.boxplot(x=df['arr_delay'])

plt.title('Box Plot de arr_delay (Retraso en la llegada)')
plt.xlabel('Retraso en la llegada (minutos)')
plt.show()

```

### **Box Plot para la variable `air_time` (Tiempo de vuelo)**

```python
plt.figure(figsize=(10, 6))
sns.boxplot(x=df['air_time'])

plt.title('Box Plot de air_time (Tiempo de vuelo)')
plt.xlabel('Tiempo de vuelo (minutos)')
plt.show()

```

### **Box Plot para la variable `delay_due_carrier` (Retraso del operador)**

```python
plt.figure(figsize=(10, 6))
sns.boxplot(x=df['delay_due_carrier'])

plt.title('Box Plot de delay_due_carrier (Retraso del operador)')
plt.xlabel('Retraso del operador (minutos)')
plt.show()

```

### **Box Plot para la variable `delay_due_weather` (Retraso por condiciones climáticas)**

```python
plt.figure(figsize=(10, 6))
sns.boxplot(x=df['delay_due_weather'])

plt.title('Box Plot de delay_due_weather (Retraso por condiciones climatológicas)')
plt.xlabel('Retraso por condiciones climatológicas (minutos)')
plt.show()

```

---

### 5. Creación de histogramas para las variables numéricas

Los **histogramas** son útiles para visualizar la frecuencia de ocurrencia de diferentes rangos de valores dentro de una variable numérica.

### **Histograma de la variable `dep_delay` (Retraso de salida)**

```python
plt.hist(df['dep_delay'], bins=20, edgecolor='black')
plt.title('Histograma de dep_delay')
plt.xlabel('Retraso de salida (minutos)')
plt.ylabel('Frecuencia')
plt.show()

```

### **Histograma de la variable `taxi_out` (Tiempo de taxi en la salida)**

```python
plt.hist(df['taxi_out'], bins=20, edgecolor='black')
plt.title('Histograma de taxi_out')
plt.xlabel('Tiempo de taxi en la salida (minutos)')
plt.ylabel('Frecuencia')
plt.show()

```

---

### **Conclusión**

Este proceso permite:

- Optimizar las variables numéricas clave en el dataset de vuelos.
- Visualizar las distribuciones mediante box plots e histogramas para identificar la dispersión de los datos y posibles **outliers**.
- Excluir las variables que son redundantes (`fl_year`, `fl_month`, `fl_day`) y elegir correctamente la tabla para realizar los **JOIN** en función de la calidad de los datos.

Estos pasos aseguran un análisis más preciso y manejable en términos de calidad de datos.
