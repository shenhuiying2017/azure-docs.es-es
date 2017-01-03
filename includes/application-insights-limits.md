Hay algunos límites en el número de métricas y eventos por aplicación (es decir, por clave de instrumentación). 

Los límites dependen del [plan de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

| **Recurso** | **Límite predeterminado** | **Nota:**
| --- | --- | --- |
| Total de datos por día | 100 GB* | Se puede reducir si se establece un límite. Si necesita más información, envíe un correo electrónico a AIDataCap@microsoft.com. 
| Datos gratis por mes<br/> (plan de precios Básico) | 1 GB | Datos adicionales cobrados por GB
| Retención de datos | 90 días | para [Search](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) y el [Explorador de métricas](../articles/application-insights/app-insights-metrics-explorer.md)
| Retención de resultados detallados para la [prueba de disponibilidad de varios pasos](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 días | Resultados detallados de cada paso
| Número de nombres de [propiedades](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) y [métricas](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> | 200 | 
| Longitud de nombres de propiedades y métricas | 150 |
| Longitud de cadena del valor de propiedad | 8192 |
| Valores distintos para propiedades<sup>3,4</sup> | 100 | >100 => no puede usar propiedad como filtro en el Explorador de métricas
| Longitud del mensaje de seguimiento y excepción | 10000 |
| Número de [pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) por aplicación  | 10 |

1. Todos estos números corresponden a cada clave de instrumentación.
2. Los nombres de métrica se definen tanto en TrackMetric como en el parámetro measurement de otras llamadas Track*(). Los nombres de métricas son globales para cada clave de instrumentación.
3. Las propiedades se pueden usar para filtrar y agrupar por solo cuando tienen menos de 100 valores únicos para cada propiedad. Cuando el número de valores únicos sea superior a 100, podrá seguir buscando la propiedad, pero no podrá utilizarlos en los filtros ni agruparlos.
4. Las propiedades estándar como el nombre de la solicitud y la URL de página se limitan a 1000 valores únicos por semana. Después de 1000 valores únicos, los valores adicionales se marcan como "Otros valores". Los valores originales pueden seguir usándose para buscar texto completo y filtrar.


[Información acerca de precios y cuotas para Application Insights](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Jan17_HO1-->


