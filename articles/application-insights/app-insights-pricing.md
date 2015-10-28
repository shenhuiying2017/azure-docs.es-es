<properties 
	pageTitle="Administración de precios y cuotas para Application Insights" 
	description="Elija el plan de precios que necesite." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="awills"/>

# Administración de precios y cuotas para Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Los [precios][pricing] de [Application Insights para Visual Studio][start] se basan en el volumen de datos por aplicación. Hay un nivel Gratis sustancial en el que se pueden disfrutar la mayoría de las características con algunas limitaciones.

Cada recurso de Application Insights se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure.

[Consulte el esquema de precios][pricing].

## Revisión del plan de cuotas y precios para el recurso de Application Insights

Puede abrir la hoja Cuota + Precios de la configuración del recurso de la aplicación.

![Elija Configuración, Cuota + Precios.](./media/app-insights-pricing/01-pricing.png)

Su elección del esquema de precios afecta a:

* [Cuota mensual](#monthly-quota): la cantidad de telemetría que puede analizar cada mes.
* [Velocidad de datos](#data-rate): la velocidad máxima a la que se pueden procesar los datos de su aplicación.
* [Retención](#data-retention): cómo se mantienen los datos Long en el portal de Application Insights para que los vea.
* [Exportación continua](#continuous-export): si se pueden exportar datos a otras herramientas y servicios.

Estos límites se establecen por separado para cada recurso de Application Insights.

### Evaluación gratuita de Premium

Al crear un nuevo recurso de Application Insights, este se inicia en el nivel Gratis.

En cualquier momento, puede cambiar a la evaluación gratuita de Premium de 30 días. Esto le ofrecerá las ventajas del nivel Premium. Después de 30 días, la suscripción cambiará automáticamente al nivel en que se encontrase previamente, a menos que se elija explícitamente otro nivel. Puede seleccionar el nivel que desee en cualquier momento durante el período de evaluación, pero podrá seguir optando a la evaluación gratuita hasta el final del período de 30 días.


## Cuota mensual

* En cada mes natural, la aplicación puede enviar hasta una cantidad especificada de telemetría a Application Insights. Consulte el [esquema de precios][pricing] para ver los números reales. 
* La cuota depende del nivel de precios que haya elegido.
* La cuota se cuenta a partir de la medianoche UTC del primer día de cada mes.
* El gráfico de puntos de datos muestra el uso que se ha hecho de la cuota en este mes.
* La cuota se mide en *puntos de datos.* Un único punto de datos es una llamada a uno de los métodos de seguimiento, independientemente de si se llama explícitamente en el código o por uno de los módulos de telemetría estándar. Los puntos de datos incluyen:
 * Cada fila que se ve en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md). 
 * Cada medida sin procesar de una [métrica](app-insights-metrics-explorer.md), como un contador de rendimiento. (Los puntos que se ven en los gráficos normalmente son agregados de varios puntos de datos sin procesar).
 * Cada punto de los gráficos de [prueba web (disponibilidad)](app-insights-monitor-web-app-availability.md). 
* Los *datos de la sesión* no se cuentan en la cuota. Esto incluye los recuentos de datos de usuarios, sesiones, entorno y dispositivo.


### Superávit

Si una aplicación envía más de la cuota mensual, puede:

* Pagar por datos adicionales. Consulte el [esquema de precios][pricing] para obtener más información. Puede elegir esta opción de antemano. Esta opción no está disponible en el nivel de precios Gratis.
* Actualización del nivel de precios
* No haga nada. Los datos de la sesión seguirán registrándose, pero otros datos no aparecerán en la búsqueda de diagnóstico o en el explorador de métricas.


### ¿Cuántos datos estoy enviando?

El gráfico de la parte inferior de la hoja de precios muestra el volumen del punto de datos de la aplicación con grupos en función del tipo de punto de datos. (También puede crear este gráfico en el Explorador de métricas).

![En la parte inferior de la hoja de precios.](./media/app-insights-pricing/03-allocation.png)

Haga clic en el gráfico para obtener más detalles, o arrastre el puntero por él y haga clic en (+) para ver el detalle de un intervalo de tiempo.


## Velocidad de datos

Además de la cuota mensual, estos valores limitan la velocidad de los datos. Para el [nivel de precios][pricing] gratuito, el límite es de 200 puntos de datos por segundo de promedio durante 5 minutos, y para los niveles de pago es de 500/s de promedio durante 1 minuto.

Hay tres depósitos que se cuentan por separado:

* [Llamadas a TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) y [registros capturados](app-insights-asp-net-trace-logs.md)
* [Excepciones](app-insights-api-custom-events-metrics.md#track-exception), limitado a 50 puntos/s.
* Toda la demás telemetría (vistas de páginas, sesiones, solicitudes, dependencias, métricas, eventos personalizados, resultados de prueba web).

Si su aplicación envía más que el límite, se quitan algunos de los datos. Verá una notificación de advertencia que indica que esto ha sucedido.

### Sugerencias para reducir la velocidad de datos

Si alcanza los valores de limitación, puede hacer alguna de estas cosas:

* Desactivar los módulos de recopilación que no necesite; para ello, [edite ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por ejemplo, podría decidir que los contadores de rendimiento o datos de dependencia no son esenciales.
* Métricas agregadas previamente. Si ha colocado llamadas a TrackMetric en su aplicación, puede reducir el tráfico mediante la sobrecarga que acepta el cálculo de la media y la desviación estándar de un lote de medidas. O bien, puede usar un [paquete de agregación previa](https://www.myget.org/gallery/applicationinsights-sdk-labs). 


### Límites de los nombres

1.	Máximo de 200 nombres de métrica únicos y 200 nombres de propiedad únicos para la aplicación. Las métricas incluyen el envío de datos a través de TrackMetric, así como mediciones de otros tipos de datos como eventos. Los [nombres de métricas y propiedades][api] son globales por clave de instrumentación, no limitados al tipo de datos.
2.	Las [propiedades][apiproperties] se pueden usar para filtrar y agrupar por estas solo cuando tienen menos de 100 valores únicos para cada propiedad. Después de que los valores únicos superen los 100, la propiedad todavía se puede usar para búsqueda y filtrado, pero no para filtros.
3.	Las propiedades estándar como el nombre de la solicitud y la URL de página se limitan a 1000 valores únicos por semana. Después de 1000 valores únicos, los valores adicionales se marcan como "Otros valores". El valor original puede seguir usándose para la búsqueda de texto completo y el filtrado.

## Retención de datos

El nivel de precios determina cuánto tiempo se mantienen los datos en el portal y, por tanto, cuánto tiempo hacia atrás puede establecer los intervalos de tiempo.


* Puntos de datos sin procesar (es decir, instancias que puede inspeccionar en Búsqueda de diagnóstico): entre 7 y 30 días.
* Los datos agregados (es decir, recuentos, promedios y otros datos estadísticos que se ven en el Explorador de métricas) se retienen en un minuto para 30 días, y en una hora o un día (en función del tipo) para al menos 13 meses.




## Revisión de la factura de su suscripción a Azure

Los cargos de Application Insights se agregarán a la factura de Azure. Puede ver los detalles de su factura de Azure en la sección de facturación del portal de Azure o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions).

![En el menú lateral, elija Facturación.](./media/app-insights-pricing/02-billing.png)

## Resumen de límites

[AZURE.INCLUDE [límites-de-application-insights](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=Oct15_HO3-->