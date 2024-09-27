# **Query para reemplazar la tabla `flights`**

La siguiente query reemplaza la tabla `flights` con una nueva versión, realizando los siguientes cambios clave:

- **Separación de la variable `origin_city` (ciudad y estado)** en dos variables: `origin_city` y `origin_state`.
- **Separación de la variable `dest_city` (ciudad y estado)** en dos variables: `dest_city` y `dest_state`.
- **Conversión de las variables de tiempo (INT64) a formato `TIME`** para mejorar la legibilidad y manipulación.
- **Imputación de valores nulos** con `0` en varias columnas relacionadas con retrasos y tiempos de vuelo.

---

### Query Completa

```sql
CREATE OR REPLACE VIEW `proyecto-4-435418.vuelos.flights` AS

SELECT
  fl_date,
  airline_code,
  dot_code,
  fl_number,
  origin,

  -- Separar ciudad y estado de origen
  SPLIT(origin_city, ', ')[SAFE_OFFSET(0)] AS origin_city,
  SPLIT(origin_city, ', ')[SAFE_OFFSET(1)] AS origin_state,

  dest,

  -- Separar ciudad y estado de destino
  SPLIT(dest_city, ', ')[SAFE_OFFSET(0)] AS dest_city,
  SPLIT(dest_city, ', ')[SAFE_OFFSET(1)] AS dest_state,

  -- Convertir variables que contienen tiempos en INT64 a formato hora TIME
  PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', crs_dep_time) = '2400' THEN '0000' ELSE FORMAT('%04d', crs_dep_time) END) AS crs_dep_time,
  PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', dep_time) = '2400' THEN '0000' ELSE FORMAT('%04d', dep_time) END) AS dep_time,
  PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', wheels_off) = '2400' THEN '0000' ELSE FORMAT('%04d', wheels_off) END) AS wheels_off,
  PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', wheels_on) = '2400' THEN '0000' ELSE FORMAT('%04d', wheels_on) END) AS wheels_on,
  PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', crs_arr_time) = '2400' THEN '0000' ELSE FORMAT('%04d', crs_arr_time) END) AS crs_arr_time,
  PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', arr_time) = '2400' THEN '0000' ELSE FORMAT('%04d', arr_time) END) AS arr_time,

  -- Imputar nulos por 0
  IFNULL(dep_delay, 0) AS dep_delay,
  IFNULL(taxi_out, 0) AS taxi_out,
  IFNULL(taxi_in, 0) AS taxi_in,
  IFNULL(arr_delay, 0) AS arr_delay,
  IFNULL(crs_elapsed_time, 0) AS crs_elapsed_time,
  IFNULL(elapsed_time, 0) AS elapsed_time,
  IFNULL(air_time, 0) AS air_time,

  cancelled,
  cancellation_code,
  diverted,
  distance,

  -- Imputar nulos por 0 en retrasos
  IFNULL(delay_due_carrier, 0) AS delay_due_carrier,
  IFNULL(delay_due_weather, 0) AS delay_due_weather,
  IFNULL(delay_due_nas, 0) AS delay_due_nas,
  IFNULL(delay_due_security, 0) AS delay_due_security,
  IFNULL(delay_due_late_aircraft, 0) AS delay_due_late_aircraft,

  fl_year,
  fl_month,
  fl_day
FROM `proyecto-4-435418.vuelos.flights_202301`

```

---

### Explicación paso a paso

### **1. Separación de las variables `origin_city` y `dest_city`**

- En el dataset original, las columnas `origin_city` y `dest_city` contienen tanto la ciudad como el estado, separados por una coma. Para facilitar el análisis, estas columnas se separan en dos nuevas variables: `origin_city` y `origin_state` para el origen, y `dest_city` y `dest_state` para el destino.

```sql
-- Separar ciudad y estado de origen
SPLIT(origin_city, ', ')[SAFE_OFFSET(0)] AS origin_city,
SPLIT(origin_city, ', ')[SAFE_OFFSET(1)] AS origin_state,

-- Separar ciudad y estado de destino
SPLIT(dest_city, ', ')[SAFE_OFFSET(0)] AS dest_city,
SPLIT(dest_city, ', ')[SAFE_OFFSET(1)] AS dest_state,

```

- **`SPLIT()`**: Divide la cadena de texto de las columnas `origin_city` y `dest_city` en dos partes, separadas por una coma y un espacio `,` .
    - **`SAFE_OFFSET(0)`**: Obtiene la ciudad (la primera parte de la cadena).
    - **`SAFE_OFFSET(1)`**: Obtiene el estado (la segunda parte de la cadena).

### **2. Conversión de las variables de tiempo (INT64) al formato `TIME`**

- Las columnas relacionadas con los tiempos (`crs_dep_time`, `dep_time`, `wheels_off`, `wheels_on`, `crs_arr_time`, `arr_time`) están en formato `INT64` y se transforman al formato `TIME` para facilitar la lectura y el análisis.

```sql
-- Convertir variables que contienen tiempos en INT64 a formato hora TIME
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', crs_dep_time) = '2400' THEN '0000' ELSE FORMAT('%04d', crs_dep_time) END) AS crs_dep_time,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', dep_time) = '2400' THEN '0000' ELSE FORMAT('%04d', dep_time) END) AS dep_time,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', wheels_off) = '2400' THEN '0000' ELSE FORMAT('%04d', wheels_off) END) AS wheels_off,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', wheels_on) = '2400' THEN '0000' ELSE FORMAT('%04d', wheels_on) END) AS wheels_on,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', crs_arr_time) = '2400' THEN '0000' ELSE FORMAT('%04d', crs_arr_time) END) AS crs_arr_time,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', arr_time) = '2400' THEN '0000' ELSE FORMAT('%04d', arr_time) END) AS arr_time,

```

- **`PARSE_TIME('%H%M', ...)`**: Convierte un número `INT64` (por ejemplo, 1830) en una hora legible (`18:30`).
- **`FORMAT('%04d', ...)`**: Asegura que los valores de tiempo tengan cuatro dígitos. Por ejemplo, si el valor es `700`, se convierte en `0700`.
- **`CASE WHEN ... THEN '0000'`**: Se encarga de que los valores de `2400` (medianoche) se conviertan en `0000`, que es un valor válido en formato de hora.

### **3. Imputación de valores nulos con `0`**

- Para garantizar que los análisis no se vean afectados por valores nulos, se imputan `0` en las columnas relacionadas con los retrasos y tiempos de vuelo.

```sql
-- Imputar nulos por 0
IFNULL(dep_delay, 0) AS dep_delay,
IFNULL(taxi_out, 0) AS taxi_out,
IFNULL(taxi_in, 0) AS taxi_in,
IFNULL(arr_delay, 0) AS arr_delay,
IFNULL(crs_elapsed_time, 0) AS crs_elapsed_time,
IFNULL(elapsed_time, 0) AS elapsed_time,
IFNULL(air_time, 0) AS air_time,

```

```sql
-- Imputar nulos por 0 en retrasos
IFNULL(delay_due_carrier, 0) AS delay_due_carrier,
IFNULL(delay_due_weather, 0) AS delay_due_weather,
IFNULL(delay_due_nas, 0) AS delay_due_nas,
IFNULL(delay_due_security, 0) AS delay_due_security,
IFNULL(delay_due_late_aircraft, 0) AS delay_due_late_aircraft,

```

- **`IFNULL()`**: Reemplaza los valores nulos por `0` en cada columna. Esto es importante para columnas donde un valor nulo significa que no hubo retraso o un valor relacionado, lo que se interpreta mejor con un `0` en lugar de un valor vacío.

---

### **Conclusión**

Esta query transforma y limpia la tabla `flights` al:

- Separar correctamente las ciudades y estados para mejorar el análisis de rutas.
- Convertir las variables de tiempo a un formato más legible (`TIME`).
- Imputar valores nulos con `0`, lo que garantiza la integridad de los datos para análisis posteriores.

Este enfoque hace que la tabla sea mucho más útil y robusta
