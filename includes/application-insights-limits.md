Hay algunos límites en el número de métricas y eventos por aplicación (es decir, por clave de instrumentación). 

Los límites dependen del [plan de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

| **Recurso** | **Límite predeterminado** | **Nota:**
| --- | --- | --- |
| Total de datos por día | 500 GB | Se puede reducir si se establece un límite. Si necesita más información, envíe un correo electrónico a AIDataCap@microsoft.com. 
| Datos gratis por mes<br/> (plan de precios Básico) | 1 GB | Datos adicionales cobrados por GB
| Limitaciones | 16&000; eventos por segundo | Se mide por minuto. 
| Retención de datos | 90 días | para [Search](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) y el [Explorador de métricas](../articles/application-insights/app-insights-metrics-explorer.md)
| Retención de resultados detallados para la [prueba de disponibilidad de varios pasos](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 días | Resultados detallados de cada paso
| Longitud de nombres de propiedades y métricas | 150 |
| Longitud de cadena del valor de propiedad | 8192 |
| Longitud del mensaje de seguimiento y excepción | 10000 |
| Número de [pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) por aplicación  | 10 |

1. Todos estos números corresponden a cada clave de instrumentación.

[Información acerca de precios y cuotas para Application Insights](../articles/application-insights/app-insights-pricing.md)


<!--HONumber=Jan17_HO4-->


