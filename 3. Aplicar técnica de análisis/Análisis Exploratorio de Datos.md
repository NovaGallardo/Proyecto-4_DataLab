| **aerolinea** | **total_incidentes** | **%_incidentes** | **ctd_fl_number** |
| --- | --- | --- | --- |
| Southwest Airlines Co. | 91877 | 27.42% | 3313 |
| American Airlines Inc. | 60943 | 18.19% | 2310 |
| SkyWest Airlines Inc. | 41220 | 12.30% | 1965 |
| Delta Air Lines Inc. | 32491 | 9.70% | 2367 |
| JetBlue Airways | 19706 | 5.88% | 922 |
| Spirit Air Lines | 17981 | 5.37% | 836 |
| Alaska Airlines Inc. | 15846 | 4.73% | 1025 |
| Endeavor Air Inc. | 14502 | 4.33% | 783 |
| PSA Airlines Inc. | 13176 | 3.93% | 514 |
| Frontier Airlines Inc. | 11012 | 3.29% | 678 |
| Allegiant Air | 7140 | 2.13% | 1324 |
| United Air Lines Inc. | 4678 | 1.40% | 1852 |
| Hawaiian Airlines Inc. | 4522 | 1.35% | 270 |

| **aerolinea** | **total_delay_due_carrier** | **total_delay_due_late_aircraft** | **total_delay_due_nas** | **total_delay_due_weather** | **total_delay_due_security** |
| --- | --- | --- | --- | --- | --- |
| Southwest Airlines Co. | 7155 | 10619 | 4633 | 318 | 42 |
| American Airlines Inc. | 5001 | 6296 | 6139 | 618 | 83 |
| SkyWest Airlines Inc. | 6390 | 2482 | 529 | 1690 | 79 |
| Delta Air Lines Inc. | 6128 | 4796 | 5036 | 695 | 15 |
| JetBlue Airways | 2668 | 2329 | 2139 | 42 | 40 |
| Spirit Air Lines | 2145 | 1669 | 8260 | 117 | 92 |
| Alaska Airlines Inc. | 1007 | 1389 | 1779 | 119 | 20 |
| Endeavor Air Inc. | 997 | 1415 | 1520 | 181 | 4 |
| PSA Airlines Inc. | 517 | 878 | 864 | 114 | 7 |
| Frontier Airlines Inc. | 1691 | 1978 | 1616 | 119 | 0 |
| Allegiant Air | 627 | 1017 | 885 | 151 | 7 |
| United Air Lines Inc. | 4663 | 4742 | 4939 | 356 | 1 |
| Hawaiian Airlines Inc. | 801 | 538 | 74 | 36 | 1 |
- Las aerolíneas que más incidentes registran son Southwest Airlines Co., American Airlines Inc., SkyWest Airlines Inc., Delta Air Lines Inc. y JetBlue Airways. La primera concentra 27,42% del total de incidentes del dataset. Sin embargo, se trata de las aerolíneas que más vuelos realizan, por lo cual es proporcional y lógico.
- La mayoría de los retrasos son delay_due_late_aircraft (de aeronaves tardías), seguido de delay_due_carrier (de problemas internos de la aerolínea) y delay_due_nas (del Sistema Aéreo Nacional). Es decir, retrasos relacionados a logística y gestiones internas. Estos se pueden deber a los siguientes motivos:
    - **delay_due_late_aircraft (de aeronaves tardías):** Cuando la aeronave que debía realizar el vuelo llega tarde al aeropuerto de salida, lo cual puede deberse a diversas razones, como:
- **Retrasos en vuelos anteriores:** La aeronave llega tarde a su destino anterior debido a condiciones climáticas adversas, problemas técnicos o congestión en el aeropuerto.
- **Mantenimiento no programado:**Cuando se detectan problemas técnicos en la aeronave que requieren reparaciones de última hora.
- **Limpieza o reabastecimiento de combustible:** En ocasiones, la aeronave necesita más tiempo para ser limpiada o abastecida de combustible antes de poder realizar el siguiente vuelo.
- **delay_due_carrier (de operador):** Se atribuye a problemas internos de la aerolínea. Algunas de las causas más comunes son:
- **Retraso en el embarque de pasajeros o carga:** Problemas con el check-in, pérdida de equipaje, o dificultades para cargar la bodega.
- **Fallas en los sistemas informáticos:** Los sistemas de reserva, despacho de vuelos o comunicación pueden experimentar fallas técnicas.
- **Falta de tripulación:** Si la tripulación no está disponible a tiempo.
- **delay_due_nas (del Sistema Aéreo Nacional):** Se producen por factores externos a la aerolínea y a la aeronave, estando relacionados con el funcionamiento del Sistema Aéreo Nacional (NAS). Algunas de las causas más comunes son:
- **Congestión en el aeropuerto:** Un alto tráfico aéreo, condiciones climáticas adversas en el aeropuerto de destino o problemas en las pistas.
- **Control de tráfico aéreo:** Problemas en la comunicación o coordinación entre los controladores aéreos.
- **Restricciones impuestas por las autoridades:** Las autoridades aeronáuticas pueden imponer restricciones al tráfico aéreo debido a condiciones climáticas adversas, emergencias o eventos especiales.
- Los retrasos por motivos de clima y de seguridad son los menos recurrentes. Particularmente, los de seguridad son muy pocos.
- Casos atípicos:
    - Spirit Air Lines, sin ser parte de las 5 aerolíneas con más incidentes, es la que más retrasos por NAS y por seguridad tiene.
- Dado que la mayoría de los retrasos se producen por motivos logísticos e internos, las variables más interesantes a indagar pueden ser:
    - aerolíneas
    - aeropuerto nacional o internacional
    - cantidad de pistas en aeropuerto origen y destino
    - clasificación de aeropuerto (*large*, *medium* y *small*)
    - franja horaria
