Hay algunos límites en el número de métricas y eventos por aplicación (es decir, por clave de instrumentación). Los límites dependen del [plan de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

| **Recurso** | **Límite predeterminado** | **Nota:**
| --- | --- | --- |
| Total de datos por día | 100 GB | Se pueden reducir los datos si se establece un límite. Si necesita más, puede aumentar el límite hasta 1000 GB desde el portal. Para capacidades mayores de 1000 GB, envíe un correo electrónico a AIDataCap@microsoft.com.
| Datos gratis por mes<br/> (plan de precios Básico) | 1 GB | Los datos adicionales se cobran por gigabyte.
| Limitaciones | 32 000 eventos por segundo | El límite se mide por minuto.
| Retención de datos | 90 días | Este recurso es para [Search](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) y el [Explorador de métricas](../articles/application-insights/app-insights-metrics-explorer.md).
| Retención de resultados detallados para la [prueba de disponibilidad de varios pasos](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 días | Este recurso proporciona resultados detallados de cada paso.
| Tamaño máximo del evento | 64 K | 
| Longitud de nombres de propiedades y métricas | 150 | Consulte [esquemas de tipos](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Longitud de cadena del valor de propiedad | 8192 | Consulte [esquemas de tipos](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Longitud del mensaje de seguimiento y excepción | 10 000 | Consulte [esquemas de tipos](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Número de [pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) por aplicación  | 100 |
| Retención de datos del [generador de perfiles](../articles/application-insights/app-insights-profiler.md) | 5 días |
| Datos enviados por día del [generador de perfiles](../articles/application-insights/app-insights-profiler.md) | 10 GB |

Para más información, consulte [Administración de precios y cuotas para Application Insights](../articles/application-insights/app-insights-pricing.md).

