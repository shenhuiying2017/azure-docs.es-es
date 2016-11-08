Hay algunos límites en el número de métricas y eventos por aplicación (es decir, por clave de instrumentación).

Los límites dependen del [nivel de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

| **Recurso** | **Límite predeterminado** | **Límite máximo** |
| --- | --- | --- |
| Puntos de datos de sesión<sup>1, 2</sup> por mes |sin límite | |
| Puntos de datos totales por mes de solicitud, evento, dependencia, seguimiento, excepción y vista de página |5 millones |50 millones<sup>3</sup> |
| Velocidad de datos de [seguimiento y registro](../articles/application-insights/app-insights-search-diagnostic-logs.md) |200 dp/s |500 dp/s |
| Velocidad de datos de [excepción](../articles/application-insights/app-insights-asp-net-exceptions.md) |50 dp/s |50 dp/s |
| Velocidad total de datos de solicitud, evento, dependencia y telemetría de vista de página |200 dp/s |500 dp/s |
| Retención de datos sin procesar para [Búsqueda](../articles/application-insights/app-insights-diagnostic-search.md) y [Análisis](../articles/application-insights/app-insights-analytics.md) |7 días | |
| Retención de datos agregada para [Explorador de métricas](../articles/application-insights/app-insights-metrics-explorer.md) |90 días | |
| Cantidad de nombres de [propiedad](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) |100 | |
| Longitud del nombre de la propiedad |150 | |
| Longitud del valor de la propiedad |8192 | |
| Longitud del mensaje de seguimiento y excepción |10000 | |
| Cantidad de nombres de [métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) |100 | |
| Longitud del nombre de métrica |150 | |
| [Pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) |10 | |

<sup>1</sup> Un punto de datos es un evento o valor métrico individual, con propiedades adjuntas y medidas.

<sup>2</sup> Un punto de datos de sesión registra el inicio o el final de una sesión y registra la identidad del usuario.

<sup>3</sup> Puede comprar capacidad adicional por encima de 50 millones.

[Información acerca de precios y cuotas para Application Insights](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0803_2016-->