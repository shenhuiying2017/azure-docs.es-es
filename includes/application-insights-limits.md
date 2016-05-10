Hay algunos límites en el número de métricas y eventos por aplicación (es decir, por clave de instrumentación).

Los límites dependen del [nivel de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

**Recurso** | **Límite predeterminado** | **Límite máximo**
-------- | ------------- | -------------
Puntos de datos de sesión<sup>1</sup> por mes | sin límite | 
Otros puntos de datos de sesión por mes | 5 millones | 50 millones<sup>2</sup>
Velocidad de datos del [seguimiento o registro](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Velocidad de datos de [excepción](../articles/application-insights/app-insights-asp-net-exceptions.md) | 50 dp/s | 50 dp/s
Velocidad de datos de otra telemetría | 200 dp/s | 500 dp/s
Retención de [datos sin procesar](../articles/application-insights/app-insights-diagnostic-search.md) | 7 días
Retención de [datos agregados](../articles/application-insights/app-insights-metrics-explorer.md) | 90 días
Cantidad de nombres de [propiedad](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longitud del nombre de la propiedad | 100 | 
Longitud del valor de la propiedad | 1000 | 
Longitud del mensaje de seguimiento y excepción | 10000 |
Cantidad de nombres de [métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longitud del nombre de métrica | 100 | 
[Pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> Un punto de datos es un evento o valor métrico individual, con propiedades adjuntas y medidas.

<sup>2</sup> Puede comprar capacidad adicional más allá de los 50 millones.
 
[Información acerca de precios y cuotas para Application Insights](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0427_2016-->