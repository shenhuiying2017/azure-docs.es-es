<properties 
	pageTitle="Muestreo de telemetría en Application Insights." 
	description="Cómo mantener el volumen de telemetría bajo control." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="awills"/>

#  Muestreo en Application Insights.

*Application Insights se encuentra en su versión de vista previa.*


El muestreo es una opción en Application Insights que permite recopilar y almacenar un conjunto reducido de telemetría, manteniendo un análisis estadísticamente correcto de datos de la aplicación. Se usa normalmente para reducir el tráfico y evitar las [limitaciones](app-insights-pricing.md#data-rate). Los datos se filtran de manera que los elementos relacionados se permiten, para que sea posible realizar las investigaciones de diagnóstico con un conjunto reducido de datos. El lado del cliente y el del servidor se coordinan automáticamente para filtrar los elementos relacionados. Cuando los recuentos de métrica se presentan al usuario en el portal, se renormalizan para tener en cuenta el muestreo y minimizar cualquier efecto en las estadísticas.


El muestreo está actualmente en versión beta y puede cambiar en el futuro.

## Configuración de muestreo para su aplicación

El muestreo está disponible actualmente para el SDK de ASP.NET o [cualquier página web](#other-web-pages).

### Servidor ASP.NET

1. Actualice los paquetes de NuGet del proyecto a la versión *preliminar* más reciente de Application Insights. Haga clic en el proyecto en el Explorador de soluciones, elija Administrar paquetes de NuGet, consulte **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web. 

2. Agregar este fragmento de código a ApplicationInsights.config

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

> [AZURE.NOTE]Para el porcentaje de muestreo, elija un porcentaje que esté cerca de 100/N, donde N es un número entero. Actualmente el muestreo no es compatible con otros valores.

<a name="other-web-pages"></a>
### Páginas web con JavaScript

Puede configurar páginas web para el muestreo desde cualquier servidor. Para servidores ASP.NET, configure ambos lados de cliente y de servidor.

Cuando [configure las páginas web para Application Insights](app-insights-javascript.md), modifique el fragmento de código que obtiene desde el portal de Application Insights. (En ASP.NET, lo encontrará en \_Layout.cshtml.) Inserte una línea como `samplingPercentage: 10,` antes de la clave de instrumentación:

    <script>
	var appInsights= ... 
	}({ 

	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Asegúrese de proporcionar el mismo porcentaje de muestreo en JavaScript que el que proporcionó en el lado del servidor.

[Obtenga más información acerca de la API](app-insights-api-custom-events-metrics.md)


### Alternativa: establecer el muestreo en el código de servidor


En lugar de establecer el parámetro de muestreo en el archivo .config, puede usar código. Esto le permitiría activar o desactivar el muestreo.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // It's recommended to set SamplingPercentage in the .config file instead.

    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```


## ¿Cuándo usar un muestreo?

Para la mayoría de las aplicaciones pequeñas y medianas no es necesario realizar muestreos. La información de diagnóstico más útil y las estadísticas más precisas se obtienen mediante la recopilación de datos en todas las actividades del usuario.

 
Las principales razones para usar un muestreo son:


* El servicio de Application Insights descarta ("limita") puntos de datos cuando la aplicación envía un número muy elevado de telemetría en un intervalo de tiempo corto. 
* Para mantenerse dentro de la [cuota](app-insights-pricing.md) de puntos de datos para su plan de tarifa. 
* Para reducir el tráfico de red de la recopilación de telemetría. 

## ¿Cómo funciona el muestreo?

Desde el punto de vista de aplicación, el muestreo es una característica de SDK de Application Insights. Se especifica qué porcentaje de todos los puntos de datos debe enviarse al servicio Application Insights. Desde la versión 2.0.0 de SDK de Application Insights puede controlar el porcentaje de muestreo desde el código. (Las versiones futuras de SDK además permitirán configurar el porcentaje de muestreo desde el archivo ApplicationInsights.config.)

El SDK decide qué elementos de telemetría se descartarán y cuáles se van conservar. La decisión del muestreo se basa en varias reglas que tienen como objetivo conservar intactos todos los puntos de datos interrelacionados, manteniendo una experiencia de diagnóstico en Application Insights que sea práctica y confiable, incluso con un conjunto reducido de datos. Por ejemplo, si para una solicitud errónea su aplicación envía elementos de telemetría adicionales (como la excepción y los seguimientos registrados para dicha solicitud), el muestreo no dividirá esta solicitud y otra telemetría. Escogerá mantenerlos o descartarlos todos juntos Como resultado, al examinar los detalles de solicitud en Application Insights, siempre puede ver la solicitud junto con sus elementos de telemetría asociados.

Para las aplicaciones que definen "usuario" (es decir, las aplicaciones web más típicas), la decisión de muestreo se basa en el hash del identificador de usuario, lo que significa que toda la telemetría para cualquier usuario específico o se conservan o se descarta. Para los tipos de aplicaciones que no definen a los usuarios (como los servicios web) la decisión de muestreo se basa en el identificador de operación de la solicitud. Por último, para los elementos de telemetría que no tienen establecido ni identificador de usuario ni identificador de operación (por ejemplo elementos de telemetría notificados desde subprocesos asincrónicos sin contexto de http), el muestreo simplemente captura un porcentaje de elementos de telemetría de cada tipo.

Al presentarle la telemetría, el servicio de Application Insights ajusta las métricas con el mismo porcentaje de muestreo que se usó en el momento de la recopilación, para compensar por los puntos de datos que faltan. Por lo tanto, al examinar la telemetría en Application Insights, los usuarios ven aproximaciones estadísticamente correctas que están muy próximas a los números reales.

La precisión de la aproximación depende en gran medida del porcentaje de muestreo configurado. También, la precisión aumenta en las aplicaciones que administran un gran volumen de solicitudes básicamente similares de muchos usuarios. Por otro lado, para las aplicaciones que no funcionan con una carga significativa, el muestreo no es necesario ya que estas aplicaciones normalmente pueden enviar toda su telemetría normalmente manteniéndose dentro de la cuota, sin causar pérdida de datos por causa de la limitación.

Tenga en cuenta que Application Insights no muestrea tipos de telemetría de Métricas y Sesiones, ya que para estos tipos la reducción en la precisión puede ser no deseable en absoluto.

## Muestreo y el SDK de JavaScript

El SDK del lado del cliente (JavaScript) participa en el muestreo junto con el SDK del lado del servidor. Las páginas instrumentadas solo enviarán telemetría de cliente desde los mismos usuarios para los que el lado del servidor decidió tomar el muestreo. Esta lógica está diseñada para mantener la integridad de la sesión de usuario a través de los lados cliente y servidor. Como resultado, a partir de cualquier elemento específico de telemetría en Application Insights, puede encontrar todos los demás elementos de telemetría para ese usuario o esa sesión.

*Las telemetrías del lado de cliente y de servidor no muestran ejemplos coordinados tal y como se describe anteriormente.*

* Compruebe que habilitó el muestreo tanto en el cliente como en el servidor.
* Asegúrese de que la versión del SDK es 2.0 o superior.
* Compruebe que establece el mismo porcentaje de muestreo en el cliente y el servidor.


## Preguntas más frecuentes 

*¿Por qué realizar un muestreo no consiste simplemente en "recopilar X por ciento de cada tipo de telemetría"?*

 *  Aunque este método de muestreo proporcionaría un nivel de precisión muy alto en las aproximaciones métricas, destruiría la capacidad para poner en correlación datos de diagnóstico por usuario, sesión y solicitud, lo cual es crítico para el diagnóstico. Por lo tanto, el muestreo funciona mejor con una lógica "recopilar todos los elementos de telemetría para X por ciento de los usuarios de la aplicación", o "recopilar toda la telemetría para X por ciento de las solicitudes de aplicación". Para los elementos de telemetría no asociados con las solicitudes (por ejemplo, el procesamiento asincrónico en segundo plano), el recurso es "recopilar X por ciento de todos los elementos para cada tipo de telemetría". 

*¿Se puede cambiar el porcentaje de muestreo con el tiempo?*

 * En la implementación de hoy en día normalmente no se cambia el porcentaje de muestreo después de configurarlo en el inicio de la aplicación. Incluso aunque se tenga control sobre el tiempo de ejecución del porcentaje de muestreo, no hay ninguna manera de determinar qué porcentaje de muestreo sería óptimo y recopilaría "la cantidad perfecta de volumen de datos", antes de que la lógica limitación se inicie o se alcance la cuota de volumen de datos mensuales. Futuras versiones de SDK de Application Insights incluirán muestreo adaptativo que, sobre la marcha, ajustará el porcentaje de muestreo hacia arriba y abajo, en función del volumen de telemetría observado en ese momento y otros factores. 

*¿Cómo sé cuál es el porcentaje de muestreo que funcionará mejor para mi aplicación?*

* En este momento tiene que "calcularlo a ojo". Analice su uso actual de telemetría en AI, observe los descartes de datos relacionados con la limitación y calcule el volumen de telemetría recopilado. Estas tres entradas, junto con el plan de tarifa seleccionado, le sugerirán en cuánto debería reducir el volumen de telemetría recopilado. Sin embargo, un cambio en el patrón del volumen de telemetría puede invalidar un porcentaje de muestreo configurado de manera óptima (por ejemplo, un aumento en el número de los usuarios). Cuando se implemente, el muestreo adaptativo controlará automáticamente el porcentaje de muestreo para mantenerlo en un nivel óptimo, en función del volumen de telemetría observado.

*¿Qué sucede si configuro un porcentaje de muestreo demasiado bajo?*

* Un porcentaje de muestreo excesivamente bajo (muestreo demasiado drástico) reducirá la precisión de las aproximaciones cuando Application Insights intente compensar la visualización de los datos por la reducción del volumen de datos. Además, la experiencia de diagnóstico puede verse afectada negativamente, ya que algunas de las solicitudes con poca frecuencia errores o lentas pueden quedar fuera del muestreo.

*¿Qué sucede si configuro un porcentaje de muestreo demasiado alto?*

* Configurar un porcentaje de muestreo demasiado alto (no suficientemente drástico) dará como resultado una reducción insuficiente del volumen de telemetría recopilada. Se pueden seguir produciendo pérdidas de datos de telemetría relacionadas con la limitación, y el costo de usar Application Insights puede ser mayor de lo planeado, debido a los cargos debidos al uso por encima del límite.

*¿En qué plataformas puedo usar muestreo?*

* Actualmente el muestreo está disponible para cualquier página web y para las aplicaciones web .NET tanto del lado del cliente como del lado del servidor.

*¿Puedo usar muestreo con aplicaciones de dispositivos (Windows Phone, iOS, Android o aplicaciones de escritorio)?*

* No, en este momento el muestreo no es compatible con las aplicaciones de dispositivo. 

<!---HONumber=Nov15_HO1-->