# Proceso de Identificación y Manejo de Valores Duplicados

### **Tabla `airline_code_dictionary`**

En esta tabla se verificaron posibles duplicados tanto en la variable `CODE` (código de operador único) como en la variable `DESCRIPTION` (descripción de la agencia operadora). Las consultas SQL utilizadas fueron:

```sql
-- Identificar duplicados en la variable CODE
SELECT
 CODE,
 COUNT(*) AS veces_repetido
FROM `proyecto-4-435418.vuelos.airline_code_dictionary`
GROUP BY
 CODE
HAVING
 COUNT(*) > 1

```

```sql
-- Identificar duplicados en la variable DESCRIPTION
SELECT
 DESCRIPTION,
 COUNT(*) AS veces_repetido
FROM `proyecto-4-435418.vuelos.airline_code_dictionary`
GROUP BY
 DESCRIPTION
HAVING
 COUNT(*) > 1

```

### Resultado:

- **No se encontraron duplicados** en las variables `CODE` ni `DESCRIPTION`. Esto indica que no hay registros repetidos en esta tabla, y no es necesario tomar ninguna acción adicional en esta etapa.

---

### **Tabla `dot_code_dictionary`**

Para la tabla `dot_code_dictionary`, se realizó un proceso similar, buscando duplicados en las variables `CODE` (identificación asignada por el DOT) y `DESCRIPTION` (descripción de la aerolínea). Las consultas fueron las siguientes:

```sql
-- Identificar duplicados en la variable CODE
SELECT
 CODE,
 COUNT(*) AS veces_repetido
FROM `proyecto-4-435418.vuelos.dot_code_dictionary`
WHERE
 CODE IS NOT NULL
GROUP BY
 CODE
HAVING
 COUNT(*) > 1

```

```sql
-- Identificar duplicados en la variable DESCRIPTION
SELECT
 DESCRIPTION,
 COUNT(*) AS veces_repetido
FROM `proyecto-4-435418.vuelos.dot_code_dictionary`
WHERE
 DESCRIPTION IS NOT NULL
GROUP BY
 DESCRIPTION
HAVING
 COUNT(*) > 1

```

### Resultado:

- **8 duplicados** se encontraron en la variable `CODE`. Los códigos duplicados son: **22114, 22115, 22116, 22117, 22120, 22121, 22122 y 22123**, y cada uno de ellos se repite dos veces.
    - Estos duplicados serán manejados automáticamente en los pasos de **JOIN**, asegurando que no haya duplicaciones innecesarias en el análisis final.
- **No se encontraron duplicados** en la variable `DESCRIPTION`. Esto indica que no hay problemas de repetición en este campo.

---

### **Tabla `flights_202301`**

Dado el gran volumen de datos en la tabla de vuelos, se buscó identificar duplicados a nivel de filas, no por variable, debido a que es esperable que ciertas variables tengan datos repetidos (por ejemplo, múltiples vuelos desde o hacia el mismo aeropuerto). La consulta SQL fue:

```sql
-- Identificar filas duplicadas en la tabla flights
SELECT
 FL_DATE,
 AIRLINE_CODE,
 DOT_CODE,
 FL_NUMBER,
 ORIGIN,
 ORIGIN_CITY,
 DEST,
 DEST_CITY,
 CRS_DEP_TIME,
 DEP_TIME,
 DEP_DELAY,
 TAXI_OUT,
 WHEELS_OFF,
 WHEELS_ON,
 TAXI_IN,
 CRS_ARR_TIME,
 ARR_TIME,
 ARR_DELAY,
 CANCELLED,
 CANCELLATION_CODE,
 DIVERTED,
 CRS_ELAPSED_TIME,
 ELAPSED_TIME,
 AIR_TIME,
 DISTANCE,
 DELAY_DUE_CARRIER,
 DELAY_DUE_WEATHER,
 DELAY_DUE_NAS,
 DELAY_DUE_SECURITY,
 DELAY_DUE_LATE_AIRCRAFT,
 FL_YEAR,
 FL_DAY,
 COUNT(*) AS veces_repetido
FROM `proyecto-4-435418.vuelos.flights_202301`
GROUP BY
 FL_DATE,
 AIRLINE_CODE,
 DOT_CODE,
 FL_NUMBER,
 ORIGIN,
 ORIGIN_CITY,
 DEST,
 DEST_CITY,
 CRS_DEP_TIME,
 DEP_TIME,
 DEP_DELAY,
 TAXI_OUT,
 WHEELS_OFF,
 WHEELS_ON,
 TAXI_IN,
 CRS_ARR_TIME,
 ARR_TIME,
 ARR_DELAY,
 CANCELLED,
 CANCELLATION_CODE,
 DIVERTED,
 CRS_ELAPSED_TIME,
 ELAPSED_TIME,
 AIR_TIME,
 DISTANCE,
 DELAY_DUE_CARRIER,
 DELAY_DUE_WEATHER,
 DELAY_DUE_NAS,
 DELAY_DUE_SECURITY,
 DELAY_DUE_LATE_AIRCRAFT,
 FL_YEAR,
 FL_DAY
HAVING
 COUNT(*) > 1

```

### Resultado:

- **No se encontraron filas duplicadas**. Esto significa que todos los registros en la tabla de vuelos son únicos en cuanto a la combinación de las variables claves, y no es necesario realizar ninguna corrección o eliminación de duplicados.

### Nota:

- No se buscaron duplicados por variable individual debido a la naturaleza de los datos. Es completamente normal que haya múltiples vuelos en un mismo origen o destino, o con la misma aerolínea. Por eso, se evaluaron los duplicados a nivel de filas completas, asegurando que cada combinación de datos sea única.

---

### Conclusión:

- En la tabla `airline_code_dictionary`, no se encontraron duplicados, lo cual indica que los códigos de las aerolíneas y sus descripciones son únicos.
- En la tabla `dot_code_dictionary`, se identificaron duplicados en la variable `CODE`, que serán manejados automáticamente durante los pasos de **JOIN**. No se encontraron duplicados en la descripción.
- En la tabla `flights_202301`, no se encontraron filas duplicadas, lo que asegura que los registros de vuelos son únicos y no es necesario realizar ninguna acción adicional.

Este proceso asegura la integridad de los datos antes de continuar con el análisis.
