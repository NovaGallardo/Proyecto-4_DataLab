# **Identificar *outliers***

<aside>
💡 No se encontraron outliers. El criterio fue que sean valores que se encuentren alejados y aislados de la tendencia principal. Todos los valores inferiores y superiores encontrados que se alejan de la tendencia central son valores posibles. Por ejemplo, retrasos de 22 horas o más, ya que los vuelos de aerolíneas comerciales se pueden retrasar incluso días.

- arr_delay: 3 valores sobre 1300 (sobre aprox 22 horas)
- delay_due_carrier: 2 valores sobre 1500 (sobre aprox 25 horas)
- delay_due_aircraft: 1 valor sobre 1400 (sobre aprox 23 horas)
- dep_delay: 2 valores sobre 1250 (sobre aprox 21 horas)
- taxi_in: 6 valores aislados sobre 80. Sin embargo, ese tiempo de taxi, si bien excesivo, puede ser factible. En taxi_out hay muchos casos sobre ese valor.
- distance: 3 valores sobre 3500.
</aside>
