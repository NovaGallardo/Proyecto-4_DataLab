# **Query final tabla flights**

```sql
CREATE OR REPLACE VIEW `proyecto-4-435418.vuelos.flights` AS

WITH flight_data AS (
SELECT
f.fl_date,
f.airline_code,
acd.description AS airline_description,
f.fl_number,
f.origin,
aco.type_airport AS type_airport_origin,

-- Agregar coast_origin después de type_airport_origin
aco.coast AS coast_origin,

-- Crear variables latitud y longitud origen
aco.coordinates AS coordinates_origin,
CAST(SPLIT(aco.coordinates, ',')[SAFE_OFFSET(0)] AS FLOAT64) AS origin_latitude,
CAST(SPLIT(aco.coordinates, ',')[SAFE_OFFSET(1)] AS FLOAT64) AS origin_longitude,

aco.national_or_international AS national_or_international_origin,
aco.usa_or_international AS usa_or_international_origin,
aco.number_of_runways AS number_of_runways_origin,

-- Separar ciudad y estado de origen
SPLIT(f.origin_city, ', ')[SAFE_OFFSET(0)] AS origin_city,
SPLIT(f.origin_city, ', ')[SAFE_OFFSET(1)] AS origin_state,

f.dest,
acd2.type_airport AS type_airport_dest,

-- Agregar coast_dest después de type_airport_dest
acd2.coast AS coast_dest,

-- Crear variables latitud y longitud destino
acd2.coordinates AS dest_coordinates,
CAST(SPLIT(acd2.coordinates, ',')[SAFE_OFFSET(0)] AS FLOAT64) AS dest_latitude,
CAST(SPLIT(acd2.coordinates, ',')[SAFE_OFFSET(1)] AS FLOAT64) AS dest_longitude,

acd2.national_or_international AS national_or_international_dest,
acd2.usa_or_international AS usa_or_international_dest,
acd2.number_of_runways AS number_of_runways_dest,

-- Separar ciudad y estado de destino
SPLIT(f.dest_city, ', ')[SAFE_OFFSET(0)] AS dest_city,
SPLIT(f.dest_city, ', ')[SAFE_OFFSET(1)] AS dest_state,

-- Crear variable ruta de vuelo
CONCAT(origin, ",",dest) AS route,

-- Convertir variables que contienen tiempos en INT64 a formato hora TIME
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', f.crs_dep_time) = '2400' THEN '0000' ELSE FORMAT('%04d', f.crs_dep_time) END) AS crs_dep_time,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', f.dep_time) = '2400' THEN '0000' ELSE FORMAT('%04d', f.dep_time) END) AS dep_time,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', f.wheels_off) = '2400' THEN '0000' ELSE FORMAT('%04d', f.wheels_off) END) AS wheels_off,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', f.wheels_on) = '2400' THEN '0000' ELSE FORMAT('%04d', f.wheels_on) END) AS wheels_on,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', f.crs_arr_time) = '2400' THEN '0000' ELSE FORMAT('%04d', f.crs_arr_time) END) AS crs_arr_time,
PARSE_TIME('%H%M', CASE WHEN FORMAT('%04d', f.arr_time) = '2400' THEN '0000' ELSE FORMAT('%04d', f.arr_time) END) AS arr_time,

-- Imputar nulos con 0
IFNULL(f.DEP_DELAY, 0) AS dep_delay,

-- Crea categorías de duración retraso
CASE
  WHEN f.dep_delay < 0 THEN 'Early'
  WHEN f.dep_delay BETWEEN 0 AND 14 THEN 'On-time'
  WHEN f.dep_delay BETWEEN 15 AND 30 THEN 'Short'
  WHEN f.dep_delay BETWEEN 31 AND 60 THEN 'Intermediate'
  WHEN f.dep_delay > 60 THEN 'Long'
  ELSE 'Unknown'
END AS delay_duration_category,

-- Crear variable booleana vuelos retrasados
CASE
 WHEN f.arr_delay BETWEEN 0 AND 14 THEN 1
 ELSE 0
END AS delayed,

-- Imputar nulos con 0
IFNULL(f.TAXI_OUT, 0) AS taxi_out,

-- Crear categorías para taxi_out
CASE
 WHEN f.taxi_out < 10 THEN 'Short'
 WHEN f.taxi_out BETWEEN 10 AND 20 THEN 'Moderate'
 WHEN f.taxi_out BETWEEN 21 AND 30 THEN 'Long'
 WHEN f.taxi_out > 30 THEN 'Very long'
 ELSE 'Unknown'
END AS taxi_out_category,

-- Imputar nulos con 0
IFNULL(f.TAXI_IN, 0) AS taxi_in,

-- Crear categorías para taxi_in
CASE
 WHEN f.taxi_in < 5 THEN 'Short'
 WHEN f.taxi_in BETWEEN 5 AND 10 THEN 'Moderate'
 WHEN f.taxi_in BETWEEN 11 AND 20 THEN 'Long'
 WHEN f.taxi_in > 20 THEN 'Very long'
 ELSE 'Unknown'
END AS taxi_in_category,

IFNULL(f.ARR_DELAY, 0) AS arr_delay,
IFNULL(f.CRS_ELAPSED_TIME, 0) AS crs_elapsed_time,
IFNULL(f.ELAPSED_TIME, 0) AS elapsed_time,

-- Crear categorías duración vuelo
CASE
  WHEN f.elapsed_time = 0 THEN 'Cancelled'
  WHEN f.elapsed_time BETWEEN 17 AND 180 THEN 'Short'
  WHEN f.elapsed_time BETWEEN 181 AND 360 THEN 'Intermediate'
  WHEN f.elapsed_time BETWEEN 361 AND 749 THEN 'Long'
  ELSE 'Unknown'
END AS flight_duration_category,

-- Imputar nulos con 0
IFNULL(f.AIR_TIME, 0) AS air_time,
f.cancelled,
f.cancellation_code,

-- Crear variable motivo de cancelación
CASE
  WHEN f.cancellation_code = 'A' THEN 'Carrier Caused'
  WHEN f.cancellation_code = 'B' THEN 'Weather'
  WHEN f.cancellation_code = 'C' THEN 'National Aviation System'
  WHEN f.cancellation_code = 'D' THEN 'Security'
  ELSE 'Other or not identified'
END AS cancellation_reason,

f.diverted,
f.distance,

-- Imputar nulos con 0 en retrasos
IFNULL(f.delay_due_carrier, 0) AS delay_due_carrier,
IFNULL(f.delay_due_weather, 0) AS delay_due_weather,
IFNULL(f.delay_due_nas, 0) AS delay_due_nas,
IFNULL(f.delay_due_security, 0) AS delay_due_security,
IFNULL(f.delay_due_late_aircraft, 0) AS delay_due_late_aircraft

FROM `proyecto-4-435418.vuelos.flights_202301` AS f
LEFT JOIN `proyecto-4-435418.vuelos.airline_code_dictionary` AS acd
ON f.airline_code = acd.code
LEFT JOIN `proyecto-4-435418.vuelos.dot_code_dictionary` AS dcd
ON f.dot_code = dcd.code
LEFT JOIN `proyecto-4-435418.vuelos.airport_classification` AS aco
ON f.origin = aco.airport
LEFT JOIN `proyecto-4-435418.vuelos.airport_classification` AS acd2
ON f.dest = acd2.airport
)

SELECT
fd.*,

-- Crear variable distancia entre aeropuertos en km
ST_DISTANCE(ST_GEOGPOINT(fd.origin_longitude, fd.origin_latitude),
          ST_GEOGPOINT(fd.dest_longitude, fd.dest_latitude)) / 1000 AS distance_km,

-- Crear variable categorías de distancia
CASE
WHEN ST_DISTANCE(ST_GEOGPOINT(fd.origin_longitude, fd.origin_latitude),
                 ST_GEOGPOINT(fd.dest_longitude, fd.dest_latitude)) / 1000 <= 1500 THEN 'Short'
WHEN ST_DISTANCE(ST_GEOGPOINT(fd.origin_longitude, fd.origin_latitude),
                 ST_GEOGPOINT(fd.dest_longitude, fd.dest_latitude)) / 1000 BETWEEN 1500 AND 4000 THEN 'Intermediate'
WHEN ST_DISTANCE(ST_GEOGPOINT(fd.origin_longitude, fd.origin_latitude),
                 ST_GEOGPOINT(fd.dest_longitude, fd.dest_latitude)) / 1000 > 4000 THEN 'Long'
END AS distance_category,

-- Crear variable tipo de vuelo
CASE
WHEN fd.usa_or_international_origin = 'National' AND fd.usa_or_international_dest = 'National' THEN 'National'
ELSE 'International'
END AS flight_type,

-- Crear variable franja horaria de partida
CASE
WHEN EXTRACT(HOUR FROM fd.dep_time) BETWEEN 0 AND 6 THEN 'Early Morning'
WHEN EXTRACT(HOUR FROM fd.dep_time) BETWEEN 6 AND 12 THEN 'Morning'
WHEN EXTRACT(HOUR FROM fd.dep_time) BETWEEN 12 AND 16 THEN 'Midday'
WHEN EXTRACT(HOUR FROM fd.dep_time) BETWEEN 16 AND 20 THEN 'Afternoon'
ELSE 'Night'
END AS departure_time_slot,

-- Crear variable franja horaria de llegada
CASE
WHEN EXTRACT(HOUR FROM fd.arr_time) BETWEEN 0 AND 6 THEN 'Early Morning'
WHEN EXTRACT(HOUR FROM fd.arr_time) BETWEEN 6 AND 12 THEN 'Morning'
WHEN EXTRACT(HOUR FROM fd.arr_time) BETWEEN 12 AND 16 THEN 'Midday'
WHEN EXTRACT(HOUR FROM fd.arr_time) BETWEEN 16 AND 20 THEN 'Afternoon'
ELSE 'Night'
END AS dest_time_slot

FROM flight_data AS fd

```

### **Identificar nuevos datos atípicos y nuevos nulos**

No se identificaron nuevos datos atípicos.

Se encontraron 10.519 nuevos nulos en las variables que contabilizan los minutos en la entrada y salida de los vuelos, tanto en vuelos cancelados como desviados.

Los 224 nulos asociados a vuelos no cancelados corresponden a viajes que fueron desviados.

Se tomó la decisión de mantener los nulos para no perder los registros de esos vuelos.
