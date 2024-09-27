# Proyecto 4 - Análisis de Cancelaciones y Retrasos de Vuelos ✈️

**Descripción del Proyecto**

Este proyecto se enfoca en el análisis de cancelaciones y retrasos de vuelos en enero de 2023, utilizando datos del Departamento de Transporte de EE.UU. El objetivo es identificar patrones y proponer recomendaciones para mejorar la eficiencia operativa de aerolíneas y aeropuertos.

## Enlaces Importantes 🔗

- [Bitácora del Proyecto](https://docs.google.com/document/d/1J828SucRgOCuxv2QmOCihrxq2Bi-esE6-VxK_k5gfVY/edit#heading=h.t1b03udrlm)
- [Google Colab](https://colab.research.google.com/drive/1N2gXTLj2PnFTdm85rO8QCL-6vE2uKd1L?usp=sharing)
- [Google Sheets con datos](https://docs.google.com/spreadsheets/d/1yO1KKRCYZarm0-nQO-KPxt-DyzyhTuElycjvfub4cMs/edit?usp=sharing)
- [Presentación del Proyecto](https://docs.google.com/presentation/d/1wjjoqR6o_OEJ7mPBCmd3tWOW0XTWDgtsKg6fBfyJ6qQ/edit?usp=sharing)
- [Looker Studio](https://lookerstudio.google.com/reporting/578f0e04-4650-4502-a05a-a340924f28ed)

## Objetivo 🎯

El análisis se centra en:

- Identificar las rutas y aerolíneas con mayor cantidad de incidentes (retrasos, cancelaciones, desvíos).
- Proponer recomendaciones estratégicas para reducir incidentes y mejorar la eficiencia operativa.

## Herramientas Utilizadas 🛠️

- **Google BigQuery**: Manipulación y consulta de grandes volúmenes de datos.
- **Python**: Análisis de datos y visualizaciones.
- **Google Colab**: Desarrollo colaborativo y generación de gráficos.
- **Google Sheets**: Creación de tablas auxiliares y limpieza de datos.
- **Looker Studio**: Visualización interactiva de los resultados mediante dashboards.

## Composición del Dataset 📊

El análisis se basa en tres tablas principales con datos de vuelos realizados en enero de 2023:

### Tabla 1: `DOT_CODE_DICTIONARY`

| Columna | Descripción |
| --- | --- |
| `CODE` | Identificador numérico del U.S. Department of Transportation (DOT) para aerolíneas. |
| `DESCRIPTION` | Descripción de la aerolínea. |

### Tabla 2: `AIRLINE_CODE_DICTIONARY`

| Columna | Descripción |
| --- | --- |
| `CODE` | Código de operador único para agencias operadoras de aeronaves. |
| `DESCRIPTION` | Descripción de la agencia operadora de aeronaves. |

### Tabla 3: `FLIGHTS_202301`

| Columna | Descripción |
| --- | --- |
| `FL_DATE` | Fecha de vuelo (yyyymmdd). |
| `AIRLINE_CODE` | Código de operador único. |
| `DOT_CODE` | Número de identificación asignado por el DOT de EE.UU. para identificar una aerolínea única. |
| `FL_NUMBER` | Número de vuelo. |
| `ORIGIN` | Aeropuerto de origen. |
| `ORIGIN_CITY` | Aeropuerto de origen, nombre de la ciudad. |
| `DEST` | Aeropuerto de destino. |
| `DEST_CITY` | Aeropuerto de destino, nombre de la ciudad. |
| `CRS_DEP_TIME` | Hora local de salida registrada en CRS (Sistema de control de reservas). |
| `DEP_TIME` | Hora local de salida real. |
| `DEP_DELAY` | Diferencia en minutos entre la hora de salida prevista y la real. |
| `TAXI_OUT` | Tiempo de taxi en la salida. |
| `WHEELS_OFF` | Hora exacta de despegue. |
| `WHEELS_ON` | Hora exacta de aterrizaje. |
| `TAXI_IN` | Tiempo de taxi en la llegada. |
| `ARR_TIME` | Hora de llegada real. |
| `ARR_DELAY` | Diferencia en minutos entre la hora de llegada prevista y la real. |
| `CANCELLED` | Indicador de vuelo cancelado. |
| `CANCELLATION_CODE` | Motivo de la cancelación. |
| `DIVERTED` | Indicador de vuelo desviado. |
| `AIR_TIME` | Tiempo en el aire. |
| `DISTANCE` | Distancia entre aeropuertos (millas). |
| `DELAY_DUE_CARRIER` | Retraso por aerolínea en minutos. |
| `DELAY_DUE_WEATHER` | Retraso meteorológico en minutos. |
| `DELAY_DUE_NAS` | Retraso del Sistema Aéreo Nacional en minutos. |
| `DELAY_DUE_SECURITY` | Retraso de seguridad en minutos. |
| `DELAY_DUE_LATE_AIRCRAFT` | Retraso de aeronaves tardías en minutos. |
| `FL_YEAR` | Año del vuelo. |
| `FL_MONTH` | Mes del vuelo. |
| `FL_DAY` | Día del vuelo. |

### Nueva Tabla: `airport_classification`

Esta tabla fue creada en Google Sheets para clasificar los 339 aeropuertos del dataset original.

| Columna | Descripción |
| --- | --- |
| `airport` | Código IATA de tres letras que identifica de manera única a cada aeropuerto. |
| `type_airport` | Large, Medium y Small, según la clasificación de la FAA. |
| `coordinates` | Coordenadas geográficas del aeropuerto (latitud, longitud). |
| `national_or_international` | Clasificación según si maneja principalmente vuelos nacionales o internacionales. |
| `usa_or_international` | Clasificación según si el aeropuerto está en EE.UU. o en un territorio de EE.UU. |
| `number_of_runways` | Número de pistas operativas. |
| `coast` | Clasificación de aeropuertos según su ubicación geográfica (East Coast, West Coast, Central). |

## Proceso

### 1. Preparación de Datos

- **Conectar e importar datos**: Cargamos los archivos .csv de vuelos, aerolíneas y clasificación de aeropuertos en BigQuery.
- **Inspección y limpieza**: Estandarizamos nombres de variables, corregimos tipos de datos y manejamos valores nulos mediante la imputación de datos faltantes.
- **Creación de nuevas variables**: Se generaron nuevas variables para la segmentación de vuelos y aeropuertos, como la categorización de duración de vuelo, número de pistas y tipo de aeropuerto.

### 2. Análisis Exploratorio

- **Agrupación de datos**: Agrupamos los vuelos según las variables de interés como la distancia del vuelo, el horario de partida y las causas de retraso.
- **Visualización de datos**: Se utilizaron herramientas como Google Colab para crear boxplots, histogramas y scatter plots, destacando las correlaciones entre variables numéricas, como la distancia y los retrasos.
- **Correlación de Pearson**: Analizamos las correlaciones entre diferentes variables para identificar las causas más importantes de los retrasos.

### 3. Validación de Hipótesis

Se plantearon las siguientes hipótesis y se validaron utilizando riesgo relativo.

1. **Rutas con más retrasos**: Algunas rutas presentan más retrasos, pero la diferencia no es significativa.
2. **Tiempo promedio de retraso por ruta**: El tiempo promedio de retraso es de 7.61 minutos considerando todos los vuelos, pero aumenta a 69.44 minutos en vuelos con más de 14 minutos de retraso.
3. **Motivo principal de retraso**: Los retrasos más comunes son causados por aeronaves tardías y problemas internos de las aerolíneas.
4. **Orígenes y destinos con más retrasos**: Pago Pago (PPG) fue identificado como el origen y destino con más retrasos asociados.

## Hallazgos Principales ✈️

### Generales

- La mayoría de los incidentes (retrasos, cancelaciones y desvíos) se concentran en aeropuertos grandes, en rutas cortas (hasta 1,500 km / 930 millas) y en vuelos que salen de lunes a miércoles.
- Las aerolíneas con mayor acumulación de incidentes son **Southwest Airlines Co.**, **American Airlines Inc.**, **SkyWest Airlines Inc.**, **Delta Air Lines Inc.**, y **JetBlue Airways**. Southwest Airlines concentra el 27,42% del total de incidentes, pero este porcentaje es proporcional al alto volumen de vuelos que realiza.
- Las aerolíneas con el mayor riesgo relativo de incidentes (retrasos, cancelaciones y desvíos) son **SkyWest Airlines Inc.**, **Frontier Airlines Inc.**, **Southwest Airlines Co.**, **Spirit Air Lines**, y **Allegiant Air**.

### Retrasos

- Los retrasos son el incidente más común, representando el 21.7% de los vuelos. Aunque hay más vuelos que salen antes de tiempo que retrasados, los retrasos son más relevantes.
- **Frontier Airlines Inc.** tiene el mayor riesgo relativo de retrasos (1.73), seguido de **Spirit Air Lines** (1.37), **JetBlue Airways** (1.26), **Allegiant Air** (1.24), **United Air Lines Inc.** (1.07) y **Southwest Airlines Co.** (1.07).
- Las principales causas de los retrasos son **motivos logísticos e internos**. El retraso del operador (`delay_due_carrier`) tiene la mayor correlación con retrasos de salida (0.69) y llegada (0.68). Los retrasos por **aeronaves tardías** (`delay_due_late_aircraft`) también tienen una correlación significativa (0.59 y 0.58).
- La mayoría de los retrasos son cortos (15-30 minutos), con un retraso promedio de **69 minutos**. Sin embargo, acumulativamente, hay más vuelos que salen antes de tiempo que retrasados.
- Los retrasos se concentran en la mañana (06:00 a 12:00) y en la tarde (16:00 a 20:00), con la madrugada siendo el horario con menos incidentes (00:00 a 06:00).
- Los miércoles y lunes destacan como los días con más retrasos, particularmente en la **costa este**.
- No hay una correlación significativa entre el número de pistas de los aeropuertos y los retrasos. Sin embargo, los aeropuertos grandes con un número intermedio de pistas (4) presentan más retrasos.

### Cancelaciones

- Las rutas tienen mayor relevancia en las cancelaciones. Algunas rutas específicas como **BWI-ABQ**, **CNY-SLC**, y **DEN-GRB** tienen un riesgo relativo de cancelación alto (52.35).
- **Condiciones meteorológicas** son el motivo principal de cancelación de vuelos, representando un 64.2% de los casos. Los retrasos por seguridad son los menos comunes (1.8%).
- La **costa central** registra la mayor concentración de cancelaciones, especialmente durante la noche (20:00 a 00:00).
- Los aeropuertos de origen con mayor riesgo de cancelación son **COD / Cody** (17.45), **ADK / Adak Island** (13.09), y **VEL / Vernal** (10.27).
- Las aerolíneas con mayor riesgo de cancelación son **SkyWest Airlines Inc.** (1.88), **Frontier Airlines Inc.** (1.76), **Southwest Airlines Co.** (1.74), y **Envoy Air** (1.36).

### Desvíos

- Las **rutas** son el factor clave en los desvíos, seguido del aeropuerto de destino. Rutas como **SAN-DSM** (400.92) y **SAN-KOA** (11.70) muestran los mayores riesgos.
- Los destinos con mayor riesgo de desvíos son **PSG / Petersburg** (33.51), **ASE / Aspen** (28.85), y **DVL / Devils Lake** (21.51).
- Las aerolíneas con mayor riesgo de desvíos son **SkyWest Airlines Inc.** (2.77), **Alaska Airlines Inc.** (2.11), y **PSA Airlines Inc.** (1.06).
- La mayoría de los vuelos desviados ocurren en la **costa central**, generalmente los lunes y martes.


## Recomendaciones Estratégicas 💡

1. **Enfocar esfuerzos en retrasos**: Dado que los retrasos están principalmente asociados a factores internos controlables por la aerolínea y el aeropuerto, es crucial mejorar los procesos logísticos y operacionales.
2. **Investigar causas de retrasos internos**: Se recomienda analizar en detalle las causas específicas de retrasos por parte de las aerolíneas (`delay_due_carrier`) y aeronaves tardías (`delay_due_late_aircraft`).
3. **Analizar las causas de desvíos**: Dado que las rutas juegan un papel clave en los desvíos, es importante investigar qué factores contribuyen a estos incidentes.
4. **Examinar aerolíneas problemáticas**: SkyWest Airlines Inc., Frontier Airlines Inc., Southwest Airlines Co., Spirit Air Lines, y Allegiant Air presentan los mayores riesgos en cuanto a incidentes, lo que justifica una revisión de sus operaciones.
5. **Aumentar la cantidad de pistas en aeropuertos grandes**: Aeropuertos grandes con menos de 4 pistas son más propensos a incidentes, por lo que se sugiere evaluar la expansión de pistas.
6. **Ampliar el análisis de datos**: Dado que el dataset solo cubre enero de 2023, se recomienda ampliar el análisis con datos de meses adicionales para capturar patrones estacionales y obtener conclusiones más robustas.
