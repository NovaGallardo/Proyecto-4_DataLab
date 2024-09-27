# Cálculo de riesgo relativo

# **Cancelación vuelos**

Las aerolíneas con mayor riesgo de cancelación de vuelos son:

| SkyWest Airlines Inc. | 1.88 |
| --- | --- |
| Frontier Airlines Inc. | 1.76 |
| Southwest Airlines Co. | 1.74 |
| Envoy Air | 1.36 |
| Spirit Air Lines | 1.22 |
| American Airlines Inc. | 0.99 |
| PSA Airlines Inc. | 0.84 |
| Republic Airline | 0.82 |
| Endeavor Air Inc. | 0.76 |
| Alaska Airlines Inc. | 0.73 |

Las rutas con mayor riesgo de cancelación de vuelos son:

| BWI,ABQ | 52.34 |
| --- | --- |
| CNY,SLC | 52.34 |
| DEN,GRB | 52.34 |
| FSD,SNA | 52.34 |
| GRB,DEN | 52.34 |
| SNA,FSD | 52.34 |
| COD,DEN | 17.45 |
| DEN,COD | 17.45 |
| ASE,PHX | 13.52 |
| ADK,ANC | 13.09 |

Los aeropuertos de origen con mayor riesgo de cancelación de vuelos son:

| COD | 17.45 |
| --- | --- |
| ADK | 13.09 |
| VEL | 10.27 |
| DVL | 9.35 |
| ASE | 8.91 |
| LBF | 8.84 |
| XWA | 8.33 |
| CNY | 8.18 |
| CMX | 7.73 |
| CYS | 6.87 |

Los aeropuertos de destino con mayor riesgo de cancelación de vuelos son:

| COD | 17.45 |
| --- | --- |
| ADK | 13.09 |
| VEL | 11.30 |
| DVL | 7.48 |
| JMS | 7.48 |
| XWA | 7.35 |
| LAR | 6.92 |
| LBF | 6.31 |
| PVU | 6.24 |
| GUC | 6.13 |

### **RR aerolíneas**

```sql
-- Calcular cantidad de vuelos cancelados y no cancelados por airline_description
WITH airline_counts AS (
SELECT
  airline_description,
  COUNTIF(cancelled = 1) AS total_cancelled,
  COUNTIF(cancelled = 0) AS total_not_cancelled
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY airline_description
),

-- Calcular los totales combinados de todas las aerolíneas excepto la actual
combined_airlines AS (
SELECT
 airline_description,
 total_cancelled,
 total_not_cancelled,
 -- Calcular totales para el resto de aerolíneas
 (SELECT SUM(total_cancelled) FROM airline_counts) - total_cancelled AS other_airlines_cancelled,
 (SELECT SUM(total_not_cancelled) FROM airline_counts) - total_not_cancelled AS other_airlines_not_cancelled
FROM airline_counts
)

-- Calcular el riesgo relativo comparando aerolíneas
SELECT
 ac.airline_description,
 ac.total_cancelled AS airline_cancelled,
 ac.total_not_cancelled AS airline_not_cancelled,
 ac.other_airlines_cancelled,
 ac.other_airlines_not_cancelled,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(ac.total_cancelled, (ac.total_cancelled + ac.total_not_cancelled)) /
 SAFE_DIVIDE(ac.other_airlines_cancelled, (ac.other_airlines_cancelled + ac.other_airlines_not_cancelled)) AS relative_risk
FROM combined_airlines ac
```

| **airline_description** | **airline_cancelled** | **airline_not_cancelled** | **other_airlines_cancelled** | **other_airlines_not_cancelled** | **relative_risk** |
| --- | --- | --- | --- | --- | --- |
| SkyWest Airlines Inc. | 1670 | 48677 | 8625 | 479865 | 1.878622188 |
| Frontier Airlines Inc. | 438 | 12847 | 9857 | 515695 | 1.75785678 |
| Southwest Airlines Co. | 3234 | 109196 | 7061 | 419346 | 1.737064397 |
| Envoy Air | 482 | 18367 | 9813 | 510175 | 1.355034168 |
| Spirit Air Lines | 507 | 21369 | 9788 | 507173 | 1.224063265 |
| American Airlines Inc. | 1417 | 73582 | 8878 | 454960 | 0.9871100241 |
| PSA Airlines Inc. | 250 | 15206 | 10045 | 513336 | 0.8427735774 |
| Republic Airline | 386 | 24090 | 9909 | 504452 | 0.8186251136 |
| Endeavor Air Inc. | 249 | 16677 | 10046 | 511865 | 0.7642725949 |
| Alaska Airlines Inc. | 280 | 19521 | 10015 | 509021 | 0.7328539538 |
| Allegiant Air | 115 | 8500 | 10180 | 520042 | 0.6952684528 |
| Hawaiian Airlines Inc. | 72 | 6625 | 10223 | 521917 | 0.5596283949 |
| JetBlue Airways | 194 | 23055 | 10101 | 505487 | 0.4259276971 |
| Delta Air Lines Inc. | 586 | 74588 | 9709 | 453954 | 0.3722698773 |
| United Air Lines Inc. | 415 | 56242 | 9880 | 472300 | 0.3574759014 |

### **RR aeropuerto origen**

```sql
-- Calcular cantidad de vuelos cancelados y no cancelados por origen
WITH origin_counts AS (
SELECT
  origin,
  COUNTIF(cancelled = 1) AS total_cancelled,
  COUNTIF(cancelled = 0) AS total_not_cancelled
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY origin
),

-- Calcular los totales combinados de todos los orígenes excepto el actual
combined_origins AS (
SELECT
 origin,
 total_cancelled,
 total_not_cancelled,
 -- Calcular totales para el resto de los orígenes
 (SELECT SUM(total_cancelled) FROM origin_counts) - total_cancelled AS other_origins_cancelled,
 (SELECT SUM(total_not_cancelled) FROM origin_counts) - total_not_cancelled AS other_origins_not_cancelled
FROM origin_counts
)

-- Calcular el riesgo relativo comparando orígenes y unir con clasificación de aeropuertos
SELECT
 co.origin,
 ac.coast,
 co.total_cancelled AS origin_cancelled,
 co.total_not_cancelled AS origin_not_cancelled,
 co.other_origins_cancelled,
 co.other_origins_not_cancelled,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(co.total_cancelled, (co.total_cancelled + co.total_not_cancelled)) /
 SAFE_DIVIDE(co.other_origins_cancelled, (co.other_origins_cancelled + co.other_origins_not_cancelled)) AS relative_risk
FROM combined_origins co
LEFT JOIN `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_classification` AS ac
ON co.origin = ac.airport
```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=873492588#gid=873492588)

### **RR aeropuerto destino**

```sql
-- Calcular cantidad de vuelos cancelados y no cancelados por destino
WITH dest_counts AS (
SELECT
  dest,
  COUNTIF(cancelled = 1) AS total_cancelled,
  COUNTIF(cancelled = 0) AS total_not_cancelled
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY dest
),

-- Calcular los totales combinados de todos los destinos excepto el actual
combined_dest AS (
SELECT
 dest,
 total_cancelled,
 total_not_cancelled,
 -- Calcular totales para el resto de los destinos
 (SELECT SUM(total_cancelled) FROM dest_counts) - total_cancelled AS other_dest_cancelled,
 (SELECT SUM(total_not_cancelled) FROM dest_counts) - total_not_cancelled AS other_dest_not_cancelled
FROM dest_counts
)

-- Calcular el riesgo relativo comparando destinos y unir con clasificación de aeropuertos
SELECT
 cd.dest,
 ac.coast,
 cd.total_cancelled AS dest_cancelled,
 cd.total_not_cancelled AS dest_not_cancelled,
 cd.other_dest_cancelled,
 cd.other_dest_not_cancelled,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(cd.total_cancelled, (cd.total_cancelled + cd.total_not_cancelled)) /
 SAFE_DIVIDE(cd.other_dest_cancelled, (cd.other_dest_cancelled + cd.other_dest_not_cancelled)) AS relative_risk
FROM combined_dest cd
LEFT JOIN `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_classification` AS ac
ON cd.dest = ac.airport
```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=581418435#gid=581418435)

### **RR rutas**

```sql
-- Calcular cantidad de vuelos cancelados y no cancelados por ruta
WITH route_counts AS (
SELECT
 route,
 COUNTIF(cancelled = 1) AS total_cancelled,
 COUNTIF(cancelled = 0) AS total_not_cancelled
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY route
),

-- Calcular los totales combinados de todas las rutas excepto la actual
combined_routes AS (
SELECT
 route,
 total_cancelled,
 total_not_cancelled,
 -- Calcular totales para el resto de las rutas
 (SELECT SUM(total_cancelled) FROM route_counts) - total_cancelled AS other_routes_cancelled,
 (SELECT SUM(total_not_cancelled) FROM route_counts) - total_not_cancelled AS other_routes_not_cancelled
FROM route_counts
)

-- Calcular el riesgo relativo comparando rutas
SELECT
 cr.route,
 cr.total_cancelled AS route_cancelled,
 cr.total_not_cancelled AS route_not_cancelled,
 cr.other_routes_cancelled,
 cr.other_routes_not_cancelled,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(cr.total_cancelled, (cr.total_cancelled + cr.total_not_cancelled)) /
 SAFE_DIVIDE(cr.other_routes_cancelled, (cr.other_routes_cancelled + cr.other_routes_not_cancelled)) AS relative_risk
FROM combined_routes cr

```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=1159257907#gid=1159257907)

# **Retraso vuelos**

Las aerolíneas con mayor riesgo de retraso de vuelos son:

| Spirit Air Lines | 1.37 |
| --- | --- |
| Allegiant Air | 1.24 |
| United Air Lines Inc. | 1.07 |
| Southwest Airlines Co. | 1.07 |
| Delta Air Lines Inc. | 0.96 |
| American Airlines Inc. | 0.95 |
| Alaska Airlines Inc. | 0.89 |
| SkyWest Airlines Inc. | 0.89 |
| Endeavor Air Inc. | 0.87 |
| Envoy Air | 0.84 |

Las rutas con mayor riesgo de retraso de vuelos son:

| EWR,OAK | 4.82 |
| --- | --- |
| IAH,BTR | 4.82 |
| DFW,EUG | 4.82 |
| FLL,ORF | 4.82 |
| JAX,LAX | 4.82 |
| LAX,BUF | 4.82 |
| LAX,JAX | 4.82 |
| ORF,FLL | 4.82 |
| ALB,DEN | 4.82 |
| AUS,SDF | 4.82 |

Los aeropuertos de origen con mayor riesgo de retraso de vuelos son:

| PPG | 2.63 |
| --- | --- |
| OWB | 2.14 |
| JAC | 2.11 |
| LNK | 2.07 |
| PSM | 2.03 |
| CKB | 1.93 |
| BQN | 1.91 |
| PBG | 1.88 |
| OTH | 1.88 |
| SWF | 1.81 |

Los aeropuertos de destino con mayor riesgo de retraso de vuelos son:

| PPG | 2.63 |
| --- | --- |
| FNT | 2.11 |
| LBE | 2.02 |
| PVU | 1.95 |
| OTH | 1.88 |
| TTN | 1.72 |
| BQN | 1.72 |
| PSE | 1.71 |
| PGD | 1.61 |
| BIH | 1.61 |

### **RR aerolíneas**

```sql
-- Calcular cantidad de vuelos retrasados y no retrasados por airline_description
WITH airline_counts AS (
SELECT
 airline_description,
 COUNTIF(delay = 1) AS total_delayed,
 COUNTIF(delay = 0) AS total_not_delayed
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY airline_description
),

-- Calcular los totales combinados de todas las aerolíneas excepto la actual
combined_airlines AS (
SELECT
 airline_description,
 total_delayed,
 total_not_delayed,
 -- Calcular totales para el resto de las aerolíneas
 (SELECT SUM(total_delayed) FROM airline_counts) - total_delayed AS other_airlines_delayed,
 (SELECT SUM(total_not_delayed) FROM airline_counts) - total_not_delayed AS other_airlines_not_delayed
FROM airline_counts
)

-- Calcular el riesgo relativo comparando aerolíneas
SELECT
 ac.airline_description,
 ac.total_delayed AS airline_delayed,
 ac.total_not_delayed AS airline_not_delayed,
 ac.other_airlines_delayed,
 ac.other_airlines_not_delayed,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(ac.total_delayed, (ac.total_delayed + ac.total_not_delayed)) /
 SAFE_DIVIDE(ac.other_airlines_delayed, (ac.other_airlines_delayed + ac.other_airlines_not_delayed)) AS relative_risk
FROM combined_airlines ac
```

| airline_description | airline_delayed | airline_not_delayed | other_airlines_delayed | other_airlines_not_delayed | relative_risk |
| --- | --- | --- | --- | --- | --- |
| Spirit Air Lines | 6103 | 15773 | 105591 | 411370 | 1.365860461 |
| Allegiant Air | 2202 | 6413 | 109492 | 420730 | 1.23776269 |
| United Air Lines Inc. | 12507 | 44150 | 99187 | 382993 | 1.073134143 |
| Southwest Airlines Co. | 24575 | 87855 | 87119 | 339288 | 1.069849677 |
| Delta Air Lines Inc. | 15013 | 60161 | 96681 | 366982 | 0.9577697848 |
| American Airlines Inc. | 14927 | 60072 | 96767 | 367071 | 0.9540169883 |
| Alaska Airlines Inc. | 3681 | 16120 | 108013 | 411023 | 0.8933057849 |
| SkyWest Airlines Inc. | 9407 | 40940 | 102287 | 386203 | 0.8923038827 |
| Endeavor Air Inc. | 3071 | 13855 | 108623 | 413288 | 0.8717664217 |
| Envoy Air | 3289 | 15560 | 108405 | 411583 | 0.8369886458 |
| PSA Airlines Inc. | 1904 | 13552 | 109790 | 413591 | 0.5872526734 |

### **RR aeropuerto origen**

```sql
-- Calcular cantidad de vuelos retrasados y no retrasados por origen
WITH origin_counts AS (
SELECT
 origin,
 COUNTIF(delay = 1) AS total_delayed,
 COUNTIF(delay = 0) AS total_not_delayed
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY origin
),

-- Calcular los totales combinados de todos los orígenes excepto el actual
combined_origins AS (
SELECT
 origin,
 total_delayed,
 total_not_delayed,
 -- Calcular totales para el resto de los orígenes
 (SELECT SUM(total_delayed) FROM origin_counts) - total_delayed AS other_origins_delayed,
 (SELECT SUM(total_not_delayed) FROM origin_counts) - total_not_delayed AS other_origins_not_delayed
FROM origin_counts
)

-- Calcular el riesgo relativo comparando orígenes y unir con clasificación de aeropuertos
SELECT
co.origin,
ac.coast,
co.total_delayed AS origin_delayed,
co.total_not_delayed AS origin_not_delayed,
co.other_origins_delayed,
co.other_origins_not_delayed,
-- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
SAFE_DIVIDE(co.total_delayed, (co.total_delayed + co.total_not_delayed)) /
SAFE_DIVIDE(co.other_origins_delayed, (co.other_origins_delayed + co.other_origins_not_delayed)) AS relative_risk
FROM combined_origins co
LEFT JOIN `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_classification` AS ac
ON co.origin = ac.airport

```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=438640295#gid=438640295)

### **RR aeropuerto destino**

```sql
-- Calcular cantidad de vuelos retrasados y no retrasados por destino
WITH dest_counts AS (
SELECT
 dest,
 COUNTIF(delay = 1) AS total_delayed,
 COUNTIF(delay = 0) AS total_not_delayed
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY dest
),

-- Calcular los totales combinados de todos los destinos excepto el actual
combined_dest AS (
SELECT
dest,
total_delayed,
total_not_delayed,
-- Calcular totales para el resto de los destinos
(SELECT SUM(total_delayed) FROM dest_counts) - total_delayed AS other_dest_delayed,
(SELECT SUM(total_not_delayed) FROM dest_counts) - total_not_delayed AS other_dest_not_delayed
FROM dest_counts
)

-- Calcular el riesgo relativo comparando destinos y unir con clasificación de aeropuertos
SELECT
cd.dest,
ac.coast,
cd.total_delayed AS dest_delayed,
cd.total_not_delayed AS dest_not_delayed,
cd.other_dest_delayed,
cd.other_dest_not_delayed,
-- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
SAFE_DIVIDE(cd.total_delayed, (cd.total_delayed + cd.total_not_delayed)) /
SAFE_DIVIDE(cd.other_dest_delayed, (cd.other_dest_delayed + cd.other_dest_not_delayed)) AS relative_risk
FROM combined_dest cd
LEFT JOIN `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_classification` AS ac
ON cd.dest = ac.airport
```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=1921587738#gid=1921587738)

### **RR route**

```sql
-- Calcular cantidad de vuelos retrasados y no retrasados por ruta
WITH route_counts AS (
SELECT
 route,
 COUNTIF(delay = 1) AS total_delayed,
 COUNTIF(delay = 0) AS total_not_delayed
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY route
),

-- Calcular los totales combinados de todas las rutas excepto la actual
combined_routes AS (
SELECT
 route,
 total_delayed,
 total_not_delayed,
 -- Calcular totales para el resto de las rutas
 (SELECT SUM(total_delayed) FROM route_counts) - total_delayed AS other_routes_delayed,
 (SELECT SUM(total_not_delayed) FROM route_counts) - total_not_delayed AS other_routes_not_delayed
FROM route_counts
)

-- Calcular el riesgo relativo comparando rutas y unir con clasificación de aeropuertos
SELECT
 cr.route,
 cr.total_delayed AS route_delayed,
 cr.total_not_delayed AS route_not_delayed,
 cr.other_routes_delayed,
 cr.other_routes_not_delayed,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(cr.total_delayed, (cr.total_delayed + cr.total_not_delayed)) /
 SAFE_DIVIDE(cr.other_routes_delayed, (cr.other_routes_delayed + cr.other_routes_not_delayed)) AS relative_risk
FROM combined_routes cr
```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=530565987#gid=530565987)

# **Desviación vuelos**

Las aerolíneas con mayor riesgo de desviación de vuelos son:

| SkyWest Airlines Inc. | 2.77 |
| --- | --- |
| Alaska Airlines Inc. | 2.11 |
| PSA Airlines Inc. | 1.06 |
| Allegiant Air | 1.02 |
| United Air Lines Inc. | 0.99 |
| Endeavor Air Inc. | 0.95 |
| Envoy Air | 0.89 |
| Delta Air Lines Inc. | 0.89 |
| Southwest Airlines Co. | 0.65 |
| American Airlines Inc. | 0.65 |

Las rutas con mayor riesgo de desviación de vuelos son:

| SAN,DSM | 400.92 |
| --- | --- |
| SAN,KOA | 111.70 |
| JAC,ATL | 100.60 |
| AMA,IAH | 100.23 |
| GRR,IAH | 100.23 |
| SNA,MDW | 100.23 |
| AUS,DSM | 89.16 |
| EYW,CVG | 89.16 |
| SAN,LIH | 76.53 |
| AZA,FWA | 66.82 |

Los aeropuertos de origen con mayor riesgo de desviación de vuelos son:

| CDV | 20.07 |
| --- | --- |
| SUX | 19.74 |
| YAK | 13.37 |
| WRG | 13.37 |
| ESC | 13.15 |
| SIT | 13.09 |
| LWS | 12.94 |
| RIW | 12.93 |
| JAC | 10.30 |
| EGE | 10.28 |

Los aeropuertos de destino con mayor riesgo de desviación de vuelos son:

| PSG | 33.51 |
| --- | --- |
| ASE | 28.85 |
| DVL | 21.51 |
| YAK | 20.07 |
| LBF | 19.36 |
| ADQ | 15.73 |
| MCW | 15.43 |
| PLN | 15.43 |
| XWA | 15.02 |
| SUN | 14.45 |

### **RR aerolíneas**

```sql
-- Calcular cantidad de vuelos desviados y no desviados por airline_description
WITH airline_counts AS (
SELECT
 airline_description,
 COUNTIF(diverted = 1) AS total_diverted,
 COUNTIF(diverted = 0) AS total_not_diverted
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY airline_description
),

-- Calcular los totales combinados de todas las aerolíneas excepto la actual
combined_airlines AS (
SELECT
airline_description,
total_diverted,
total_not_diverted,
-- Calcular totales para el resto de aerolíneas
(SELECT SUM(total_diverted) FROM airline_counts) - total_diverted AS other_airlines_diverted,
(SELECT SUM(total_not_diverted) FROM airline_counts) - total_not_diverted AS other_airlines_not_diverted
FROM airline_counts
)

-- Calcular el riesgo relativo comparando aerolíneas
SELECT
ac.airline_description,
ac.total_diverted AS airline_diverted,
ac.total_not_diverted AS airline_not_diverted,
ac.other_airlines_diverted,
ac.other_airlines_not_diverted,
-- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
SAFE_DIVIDE(ac.total_diverted, (ac.total_diverted + ac.total_not_diverted)) /
SAFE_DIVIDE(ac.other_airlines_diverted, (ac.other_airlines_diverted + ac.other_airlines_not_diverted)) AS relative_risk
FROM combined_airlines ac
```

| airline_description | airline_diverted | airline_not_diverted | other_airlines_diverted | other_airlines_not_diverted | relative_risk |
| --- | --- | --- | --- | --- | --- |
| SkyWest Airlines Inc. | 299 | 50048 | 1046 | 487444 | 2.773457938 |
| Alaska Airlines Inc. | 100 | 19701 | 1245 | 517791 | 2.105430966 |
| PSA Airlines Inc. | 41 | 15415 | 1304 | 522077 | 1.064699644 |
| Allegiant Air | 22 | 8593 | 1323 | 528899 | 1.023446861 |
| United Air Lines Inc. | 140 | 56517 | 1205 | 480975 | 0.9887730177 |
| Endeavor Air Inc. | 40 | 16886 | 1305 | 520606 | 0.9451300975 |
| Envoy Air | 42 | 18807 | 1303 | 518685 | 0.8892213503 |
| Delta Air Lines Inc. | 169 | 75005 | 1176 | 462487 | 0.8863681949 |
| Southwest Airlines Co. | 197 | 112233 | 1148 | 425259 | 0.6508283358 |
| American Airlines Inc. | 128 | 74871 | 1217 | 462621 | 0.6504744522 |

### **RR aeropuerto origen**

```sql
-- Calcular cantidad de vuelos desviados y no desviados por origen
WITH origin_counts AS (
SELECT
origin,
COUNTIF(diverted = 1) AS total_diverted,
COUNTIF(diverted = 0) AS total_not_diverted
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY origin
),

-- Calcular los totales combinados de todos los orígenes excepto el actual
combined_origins AS (
SELECT
origin,
total_diverted,
total_not_diverted,
-- Calcular totales para el resto de los orígenes
(SELECT SUM(total_diverted) FROM origin_counts) - total_diverted AS other_origins_diverted,
(SELECT SUM(total_not_diverted) FROM origin_counts) - total_not_diverted AS other_origins_not_diverted
FROM origin_counts
)

-- Calcular el riesgo relativo comparando orígenes y unir con clasificación de aeropuertos
SELECT
co.origin,
ac.coast,
co.total_diverted AS origin_diverted,
co.total_not_diverted AS origin_not_diverted,
co.other_origins_diverted,
co.other_origins_not_diverted,
-- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
SAFE_DIVIDE(co.total_diverted, (co.total_diverted + co.total_not_diverted)) /
SAFE_DIVIDE(co.other_origins_diverted, (co.other_origins_diverted + co.other_origins_not_diverted)) AS relative_risk
FROM combined_origins co
LEFT JOIN `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_classification` AS ac
ON co.origin = ac.airport
```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=664071498#gid=664071498)

### **RR aeropuerto destino**

```sql
-- Calcular cantidad de vuelos desviados y no desviados por destino
WITH dest_counts AS (
SELECT
dest,
COUNTIF(diverted = 1) AS total_diverted,
COUNTIF(diverted = 0) AS total_not_diverted
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY dest
),

-- Calcular los totales combinados de todos los destinos excepto el actual
combined_dest AS (
SELECT
dest,
total_diverted,
total_not_diverted,
-- Calcular totales para el resto de los destinos
(SELECT SUM(total_diverted) FROM dest_counts) - total_diverted AS other_dest_diverted,
(SELECT SUM(total_not_diverted) FROM dest_counts) - total_not_diverted AS other_dest_not_diverted
FROM dest_counts
)

-- Calcular el riesgo relativo comparando destinos y unir con clasificación de aeropuertos
SELECT
cd.dest,
ac.coast,
cd.total_diverted AS dest_diverted,
cd.total_not_diverted AS dest_not_diverted,
cd.other_dest_diverted,
cd.other_dest_not_diverted,
-- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
SAFE_DIVIDE(cd.total_diverted, (cd.total_diverted + cd.total_not_diverted)) /
SAFE_DIVIDE(cd.other_dest_diverted, (cd.other_dest_diverted + cd.other_dest_not_diverted)) AS relative_risk
FROM combined_dest cd
LEFT JOIN `erudite-scholar-426802-h9.proyecto4_flightdelay.airport_classification` AS ac
ON cd.dest = ac.airport
```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=1937177031#gid=1937177031)

### **RR route**

```sql
-- Calcular cantidad de vuelos desviados y no desviados por ruta
WITH route_counts AS (
SELECT
 route,
 COUNTIF(diverted = 1) AS total_diverted,
 COUNTIF(diverted = 0) AS total_not_diverted
FROM `erudite-scholar-426802-h9.proyecto4_flightdelay.flights_data`
GROUP BY route
),

-- Calcular los totales combinados de todas las rutas excepto la actual
combined_routes AS (
SELECT
 route,
 total_diverted,
 total_not_diverted,
 -- Calcular totales para el resto de las rutas
 (SELECT SUM(total_diverted) FROM route_counts) - total_diverted AS other_routes_diverted,
 (SELECT SUM(total_not_diverted) FROM route_counts) - total_not_diverted AS other_routes_not_diverted
FROM route_counts
)

-- Calcular el riesgo relativo comparando rutas y unir con clasificación de aeropuertos
SELECT
 cr.route,
 cr.total_diverted AS route_diverted,
 cr.total_not_diverted AS route_not_diverted,
 cr.other_routes_diverted,
 cr.other_routes_not_diverted,
 -- Cálculo del riesgo relativo, usando SAFE_DIVIDE para evitar divisiones por cero
 SAFE_DIVIDE(cr.total_diverted, (cr.total_diverted + cr.total_not_diverted)) /
 SAFE_DIVIDE(cr.other_routes_diverted, (cr.other_routes_diverted + cr.other_routes_not_diverted)) AS relative_risk
FROM combined_routes cr

```

[Tabla riesgo relativo](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?gid=825637827#gid=825637827)
