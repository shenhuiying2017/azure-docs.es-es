---
title: "Muestreo de telemetría en Azure Application Insights | Microsoft Docs"
description: "Cómo mantener el volumen de telemetría bajo control."
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: 1f6c58b219a5fb040048d0075644102f5f0c5323
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="sampling-in-application-insights"></a>Muestreo en Application Insights.


El muestreo es una característica de [Azure Application Insights](app-insights-overview.md). Se trata de la manera recomendada de reducir el almacenamiento y el tráfico de telemetría conservando, al mismo tiempo, un análisis estadísticamente correcto de datos de las aplicaciones. El filtro selecciona los elementos relacionado para que pueda desplazarse entre los elementos de diagnóstico cuando esté realizando investigaciones de diagnóstico.
Cuando los recuentos de métrica se presentan al usuario en el portal, se renormalizan para tener en cuenta el muestreo y minimizar cualquier efecto en las estadísticas.

El muestreo reduce los costos de tráfico y datos y le ayuda a evitar la limitación.

## <a name="in-brief"></a>En resumen:
* El muestreo conserva 1 en *n* registros y descarta el resto. Por ejemplo, podría retener los eventos de 1 a 5, una velocidad de muestreo del 20 %. 
* El muestreo se produce automáticamente si la aplicación envía una gran cantidad de telemetría en las aplicaciones de servidor web ASP.NET.
* También puede establecer el muestreo manualmente, bien en el portal en la página de precios, bien en el SDK de ASP.NET en el archivo .config, también para reducir el tráfico de red.
* Si registra eventos personalizados y desea asegurarse de que un conjunto de eventos se retienen o se descartan juntos, asegúrese de que tengan el mismo valor para OperationId.
* El divisor de muestreo *n* se notifica en cada registro de la propiedad `itemCount`, que en la búsqueda aparece bajo el nombre descriptivo "recuento de solicitudes" o "recuento de eventos". Cuando el muestreo no está en funcionamiento, `itemCount==1`.
* Si escribe consultas de Analytics, debería [tener en cuenta el muestreo](app-insights-analytics-tour.md#counting-sampled-data). En concreto, en lugar de simplemente contar registros, debería usar `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Tipos de muestreo
Existen tres métodos de muestreo alternativos:

* **muestreo adaptable** ajusta automáticamente el volumen de telemetría enviado desde el SDK en la aplicación de ASP.NET. A partir del SDK v 2.0.0-beta3, este es el método de muestreo predeterminado. Actualmente, el muestreo solo está disponible para la telemetría del lado servidor de ASP.NET. 
* **muestreo de frecuencia fija** reduce el volumen de telemetría enviado desde el servidor ASP.NET y desde los exploradores de los usuarios. El usuario establece la frecuencia. El cliente y el servidor sincronizarán su muestreo por lo que, en Búsqueda, puede desplazarse entre las solicitudes y las vistas de página relacionadas.
* El **muestreo de ingesta** funciona en el portal de Azure. Lo que hace es descartar algunos de los datos de telemetría que llegan desde su aplicación según la frecuencia de muestreo establecida. Aunque no reduce el tráfico de telemetría enviado desde su aplicación, le ayuda a mantenerse dentro de su cuota mensual. La ventaja principal del muestreo de ingesta es que puede establecer la frecuencia de muestreo sin volver a implementar la aplicación, y funciona de manera uniforme en todos los clientes y servidores. 

Si el muestreo de velocidad adaptable o fija está funcionando, el muestreo de ingesta se deshabilita.

## <a name="ingestion-sampling"></a>muestreo de ingesta
Esta forma de muestreo funciona en el punto donde la telemetría de su servidor web, de sus exploradores y de sus dispositivos alcanza el punto de conexión del servicio de Application Insights. Aunque no reduce el tráfico de telemetría enviado desde su aplicación, sí reduce la cantidad procesada y retenida (y cobrada) por Application Insights.

Utilice este tipo de muestreo si con frecuencia su aplicación sobrepasa su cuota mensual y no tiene la opción de usar ninguno de los tipos de muestreo basados en el SDK. 

Establezca la frecuencia de muestreo en la hoja Cuotas y precios:

![En la hoja Información general de la aplicación, haga clic en Configuración, Cuota, Ejemplos y, luego, seleccione una frecuencia de muestreo y haga clic en Actualizar.](./media/app-insights-sampling/04.png)

Al igual que otros tipos de muestreo, el algoritmo conserva elementos de telemetría relacionados. Por ejemplo, cuando se inspeccione la telemetría en Búsqueda, podrá buscar la solicitud relacionada con una excepción determinada. Los recuentos de métrica, como la tasa de solicitudes y la tasa de excepciones se mantienen correctamente.

Los puntos de datos que se descartan por muestreo no están disponibles en ninguna característica de Application Insights como [exportación continua](app-insights-export-telemetry.md).

El muestreo de ingesta no funciona mientras el muestreo adaptativo o de frecuencia fija basado en el SDK está en funcionamiento. Tenga en cuenta que el muestreo adaptable está habilitado de forma predeterminada cuando el SDK de ASP.NET está habilitado en Visual Studio o con el Monitor de estado, y el muestreo de recopilación está deshabilitado. Si la velocidad de muestreo en el SDK es inferior al 100 %, se omite la velocidad de muestreo de ingesta que haya establecido.

> [!WARNING]
> El valor que se muestra en el icono indica el valor que ha establecido para el muestreo de ingesta. Si el muestreo del SDK está en funcionamiento, no representa la frecuencia real de muestreo.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Muestreo adaptable en el servidor web
El muestreo adaptable está disponible para el SDK de Application Insights para ASP.NET v 2.0.0-beta3 y versiones posteriores y está habilitado de forma predeterminada. 

Influye en el volumen de telemetría enviado desde su aplicación de servidor web hasta el punto de conexión del servicio de Application Insights. El volumen se ajusta automáticamente para mantenerlo dentro de una tasa de tráfico máxima especificada.

No funciona con volúmenes bajos de telemetría, así que una aplicación en proceso de depuración o un sitio web con poca utilización no se verán afectados.

Para lograr el volumen objetivo, se descartan algunos de los datos de telemetría generados. Pero, al igual que otros tipos de muestreo, el algoritmo conserva los elementos de telemetría relacionados. Por ejemplo, cuando se inspeccione la telemetría en Búsqueda, podrá buscar la solicitud relacionada con una excepción determinada. 

Los recuentos de métrica, como la tasa de solicitudes y la tasa de excepciones, se ajustan para compensar la frecuencia de muestreo, de modo que exhiban valores aproximadamente correctos en el Explorador de métricas.

### <a name="update-nuget-packages"></a>Administración de paquetes NuGet ###

Actualice los paquetes de NuGet del proyecto a la *versión preliminar* más reciente de Application Insights. En Visual Studio, haga clic con el botón derecho en el Explorador de soluciones, elija Administrar paquetes de NuGet, active la opción **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Configuración del muestreo adaptable ###

En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), puede ajustar varios parámetros en el nodo `AdaptiveSamplingTelemetryProcessor`. Las cifras que se muestran son los valores predeterminados:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Velocidad objetivo que el algoritmo de adaptación intenta lograr **en cada host de servidor**. Si la aplicación web se ejecuta en varios hosts, reduzca este valor para que se mantenga dentro de la velocidad objetivo de tráfico en el portal de Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervalo en el que se vuelve a evaluar la velocidad actual de telemetría. La evaluación se realiza como una media móvil. Se recomienda acortar este intervalo si la telemetría experimenta ráfagas repentinas.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Cuando se cambia el valor de porcentaje de muestreo, tiempo mínimo que se tarda en permitir de nuevo que se reduzca el porcentaje de muestreo para capturar menos datos.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Cuando se cambia el valor de porcentaje de muestreo, tiempo mínimo que se tarda en permitir de nuevo que se aumente el porcentaje de muestreo para capturar más datos.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Como el porcentaje de muestreo varía, valor mínimo que se permite establecer.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Como el porcentaje de muestreo varía, valor máximo que se permite establecer.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    En el cálculo de la media móvil, peso asignado al valor más reciente. Use un valor igual o menor que 1. Los valores menores hacen que el algoritmo reaccione con menor agilidad a los cambios repentinos.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Valor asignado cuando se acaba de iniciar la aplicación. No lo reduzca durante la depuración. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Una lista delimitada por puntos y coma de tipos que no desea que se muestreen. Los tipos reconocidos son Dependency, Event, Exception, PageView, Request, Trace. Todas las instancias de los tipos especificados se transmiten; los tipos no especificados se muestrean.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Una lista delimitada por puntos y coma de tipos que desea que se muestreen. Los tipos reconocidos son Dependency, Event, Exception, PageView, Request, Trace. Los tipos especificados se muestrean, todas las instancias del resto de tipos siempre se transmitirán.


**Para desactivar** el muestreo adaptable, quite el nodo AdaptiveSamplingTelemetryProcessor de applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: configure el muestreo adaptivo en el código
En lugar de establecer el parámetro de muestreo en el archivo .config, puede establecer estos valores mediante programación. Esto le permite especificar una función de devolución de llamada que se invoca cuando se vuelve a evaluar la frecuencia de muestreo. Puede utilizarlo, por ejemplo, para averiguar la velocidad de muestreo que se está usando.

Quite el nodo `AdaptiveSamplingTelemetryProcessor` del archivo .config.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Más información sobre los procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering)).

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Muestreo para páginas web con JavaScript
Puede configurar páginas web para el muestreo de frecuencia fija desde cualquier servidor. 

Cuando [configure las páginas web para Application Insights](app-insights-javascript.md), modifique el fragmento de JavaScript que obtiene del portal de Application Insights. (En las aplicaciones ASP.NET, el fragmento normalmente está en _Layout.cshtml).  Inserte una línea como `samplingPercentage: 10,` antes de la clave de instrumentación:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Para el porcentaje de muestreo, elija un porcentaje que esté cerca de 100/N, donde N es un número entero.  Actualmente el muestreo no es compatible con otros valores.

Si también habilita el muestreo de frecuencia fija en el servidor, los clientes y el servidor se sincronizarán de modo que, en Búsqueda, puede desplazarse entre las solicitudes y las vistas de página relacionadas.

## <a name="fixed-rate-sampling-for-aspnet-web-sites"></a>Muestreo de frecuencia fija para sitios web ASP.NET
El muestreo de frecuencia fija reduce el tráfico enviado desde el servidor web y los exploradores web. A diferencia del muestreo adaptable, reduce la telemetría a una tasa fija que usted decide. También sincroniza el muestreo del cliente y del servidor para que los elementos relacionados se conserven; por ejemplo, cuando mira una vista de página en Búsqueda, puede encontrar su solicitud relacionada.

El algoritmo de muestreo conserva los elementos relacionados. Para cada evento de solicitud HTTP, esta y sus eventos relacionados se descartan o transmiten conjuntamente. 

En el Explorador de métricas, las tasas, como los recuentos de solicitudes y de excepciones, se multiplican por un factor para compensar la frecuencia de muestreo, de forma que sean aproximadamente correctas.

### <a name="configuring-fixed-rate-sampling"></a>Configuración del muestreo de frecuencia fija ###

1. **Actualice los paquetes de NuGet del proyecto** a la *versión preliminar* más reciente de Application Insights. En Visual Studio, haga clic con el botón derecho en el Explorador de soluciones, elija Administrar paquetes de NuGet, active la opción **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web. 
2. **Deshabilite el muestreo adaptable**: en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), quite o convierta en comentario el nodo `AdaptiveSamplingTelemetryProcessor`.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

1. **Habilite el módulo de muestreo de frecuencia fija.** Agregue este fragmento a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

> [!NOTE]
> Para el porcentaje de muestreo, elija un porcentaje que esté cerca de 100/N, donde N es un número entero.  Actualmente el muestreo no es compatible con otros valores.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: habilitación del muestreo de frecuencia fija en el código de servidor
En lugar de establecer el parámetro de muestreo en el archivo .config, puede establecer estos valores mediante programación. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Más información sobre los procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering)).

## <a name="when-to-use-sampling"></a>¿Cuándo usar un muestreo?
El muestreo adaptable está habilitado automáticamente si usa el SDK de ASP.NET versión 2.0.0-beta3 o posterior. Independientemente de la versión del SDK que use, puede habilitar el muestreo de ingesta para permitir que Application Insights tome muestras de los datos recopilados.

En general, para la mayoría de las aplicaciones pequeñas y medianas no es necesario realizar muestreos. La información de diagnóstico más útil y las estadísticas más precisas se obtienen mediante la recopilación de datos en todas las actividades del usuario. 

Las principales ventajas del muestreo son:

* El servicio de Application Insights descarta ("limita") puntos de datos cuando la aplicación envía un número muy elevado de telemetría en un intervalo de tiempo corto. 
* Para mantenerse dentro de la [cuota](app-insights-pricing.md) de puntos de datos de su plan de tarifa. 
* Para reducir el tráfico de red de la recopilación de telemetría. 

### <a name="which-type-of-sampling-should-i-use"></a>¿Qué tipo de muestreo debo utilizar?
**Use el muestreo de ingesta si:**

* Con frecuencia sobrepasa su cuota mensual de telemetría.
* Usa una versión del SDK que no admite muestreo; por ejemplo, el SDK de Java o las versiones de ASP.NET anteriores a la 2.
* Recibe mucha telemetría de los exploradores web de sus usuarios.

**Use el muestreo de frecuencia fija si:**

* Usa el SDK de Application Insights para servicios web de ASP.NET versión 2.0.0 o posterior.
* Quiere un muestreo sincronizado entre cliente y servidor para que, cuando investigue eventos en [Búsqueda](app-insights-diagnostic-search.md), pueda desplazarse entre los eventos relacionados en el cliente y el servidor, como vistas de página y solicitudes HTTP.
* Está seguro del porcentaje de muestreo adecuado para la aplicación. Debe ser lo bastante alto como para obtener métricas precisas, pero inferior a la frecuencia que supera la cuota de precios y los valores de limitación. 

**Use el muestreo adaptable si:**

Si no se cumplen las condiciones para usar las otras formas de muestreo, le recomendamos el muestreo adaptable. Esta opción está habilitada de forma predeterminada en el SDK de servidor de ASP.NET versión 2.0.0-beta3 o posterior. No reducirá el tráfico hasta que se alcance un determinado índice mínimo. Por lo tanto, los sitios con poco uso no se verán afectados.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>¿Cómo se puede saber si el muestreo está en funcionamiento?
Para conocer la frecuencia de muestreo real independientemente de dónde se ha aplicado, use una [consulta de Analytics](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

En cada registro retenido, `itemCount` indica el número de registros originales que representa, equivale a 1 + el número de registros descartados anteriores. 

## <a name="how-does-sampling-work"></a>¿Cómo funciona el muestreo?
El muestreo adaptable y de frecuencia fija es una característica del SDK de ASP.NET 2.0.0 y versiones posteriores. El muestreo de ingesta es una característica del servicio de Application Insights y puede funcionar si el SDK no realiza muestreo. 

El algoritmo de muestreo decide qué elementos de telemetría quitar y cuáles conservar (ya sea en el SDK o en el servicio de Application Insights). La decisión del muestreo se basa en varias reglas que tienen como objetivo conservar intactos todos los puntos de datos interrelacionados, manteniendo una experiencia de diagnóstico en Application Insights que sea práctica y confiable, incluso con un conjunto reducido de datos. Por ejemplo, si para una solicitud errónea su aplicación envía elementos de telemetría adicionales (como la excepción y los seguimientos registrados para dicha solicitud), el muestreo no dividirá esta solicitud y otra telemetría. Escogerá mantenerlos o descartarlos todos juntos Como resultado, al examinar los detalles de solicitud en Application Insights, siempre puede ver la solicitud junto con sus elementos de telemetría asociados. 

Para las aplicaciones que definen "usuario" (es decir, las aplicaciones web más típicas), la decisión de muestreo se basa en el hash del identificador de usuario, lo que significa que toda la telemetría para cualquier usuario específico o se conservan o se descarta. Para los tipos de aplicaciones que no definen a los usuarios (como los servicios web) la decisión de muestreo se basa en el identificador de operación de la solicitud. Por último, para los elementos de telemetría que no tienen establecido ni identificador de usuario ni identificador de operación (por ejemplo elementos de telemetría notificados desde subprocesos asincrónicos sin contexto de http), el muestreo simplemente captura un porcentaje de elementos de telemetría de cada tipo. 

Al presentarle la telemetría, el servicio de Application Insights ajusta las métricas con el mismo porcentaje de muestreo que se usó en el momento de la recopilación, para compensar por los puntos de datos que faltan. Por lo tanto, al examinar la telemetría en Application Insights, los usuarios ven aproximaciones estadísticamente correctas que están muy próximas a los números reales.

La precisión de la aproximación depende en gran medida del porcentaje de muestreo configurado. También, la precisión aumenta en las aplicaciones que administran un gran volumen de solicitudes básicamente similares de muchos usuarios. Por otro lado, para las aplicaciones que no funcionan con una carga significativa, el muestreo no es necesario ya que estas aplicaciones normalmente pueden enviar toda su telemetría manteniéndose dentro de la cuota, sin causar pérdida de datos por causa de la limitación. 

> [!WARNING]
> Application Insights no muestrea métricas de ejemplo ni tipos de telemetría de sesiones. La reducción en la precisión puede ser altamente indeseable para estos tipos de telemetría.
> 

### <a name="adaptive-sampling"></a>muestreo adaptable
El muestreo adaptable agrega un componente que supervisa la velocidad actual de transmisión desde el SDK y ajusta el porcentaje de muestreo para mantenerse dentro de la velocidad objetivo máxima. El ajuste se actualiza a intervalos regulares y se basa en una media móvil de la velocidad de transmisión de salida.

## <a name="sampling-and-the-javascript-sdk"></a>Muestreo y el SDK de JavaScript
El SDK del lado cliente (JavaScript) participa en el muestreo de frecuencia fija junto con el SDK del lado servidor. Las páginas instrumentadas solo enviarán telemetría de cliente desde los mismos usuarios para los que el lado del servidor decidió tomar el muestreo. Esta lógica está diseñada para mantener la integridad de la sesión de usuario a través de los lados cliente y servidor. Como resultado, a partir de cualquier elemento específico de telemetría en Application Insights, puede encontrar todos los demás elementos de telemetría para ese usuario o esa sesión. 

*Las telemetrías del lado de cliente y de servidor no muestran ejemplos coordinados tal y como se describe anteriormente.*

* Compruebe que habilitó el muestreo de frecuencia fija tanto en el cliente como en el servidor.
* Asegúrese de que la versión del SDK es 2.0 o superior.
* Compruebe que establece el mismo porcentaje de muestreo en el cliente y el servidor.

## <a name="frequently-asked-questions"></a>Preguntas frecuentes
*¿Por qué realizar un muestreo no consiste simplemente en "recopilar X por ciento de cada tipo de telemetría"?*

* Aunque este método de muestreo proporcionaría un nivel de precisión muy alto en las aproximaciones métricas, destruiría la capacidad para poner en correlación datos de diagnóstico por usuario, sesión y solicitud, lo cual es crítico para el diagnóstico. Por lo tanto, el muestreo funciona mejor con una lógica "recopilar todos los elementos de telemetría para X por ciento de los usuarios de la aplicación", o "recopilar toda la telemetría para X por ciento de las solicitudes de aplicación". Para los elementos de telemetría no asociados con las solicitudes (por ejemplo, el procesamiento asincrónico en segundo plano), el recurso es "recopilar X por ciento de todos los elementos para cada tipo de telemetría". 

*¿Se puede cambiar el porcentaje de muestreo con el tiempo?*

* Sí, el muestreo adaptable cambia gradualmente el porcentaje de muestreo, en función del volumen de datos de telemetría observado en ese momento.

*Si uso el muestreo de frecuencia fija, ¿cómo sé cuál es el porcentaje de muestreo que funcionará mejor para mi aplicación?*

* Una manera de comenzar es con muestreo adaptable, averigüe qué velocidad elige (consulte la pregunta anterior) y luego cambie a muestreo de tasa fija con esa velocidad. 
  
    Si no, lo tendrá que adivinar. Analice su uso actual de telemetría en Application Insights, observe las limitaciones que se produzcan y estime el volumen de telemetría recopilado. Estas tres entradas, junto con el plan de tarifa seleccionado, le sugerirán cuánto debería reducir el volumen de telemetría recopilado. Sin embargo, un aumento del número de usuarios o algún otro incremento en el volumen de datos de telemetría podrían provocar que la estimación no fuese válida.

*¿Qué sucede si configuro un porcentaje de muestreo demasiado bajo?*

* Un porcentaje de muestreo excesivamente bajo (muestreo demasiado drástico) reduce la precisión de las aproximaciones cuando Application Insights intenta compensar la visualización de los datos por la reducción del volumen de datos. Además, la experiencia de diagnóstico puede verse afectada negativamente, ya que algunas de las solicitudes con poca frecuencia errores o lentas pueden quedar fuera del muestreo.

*¿Qué sucede si configuro un porcentaje de muestreo demasiado alto?*

* Configurar un porcentaje de muestreo demasiado alto (no suficientemente drástico) da como resultado una reducción insuficiente del volumen de telemetría recopilada. Se pueden seguir produciendo pérdidas de datos de telemetría relacionadas con la limitación, y el costo de usar Application Insights puede ser mayor de lo planeado, debido a los cargos debidos al uso por encima del límite.

*¿En qué plataformas puedo usar muestreo?*

* El muestreo de ingesta puede producirse automáticamente cuando cualquier dato de telemetría se sitúa por encima de un determinado volumen, si el SDK no realiza muestreo. Este sería el caso, por ejemplo, si su aplicación utiliza un servidor Java o si usa una versión anterior del SDK de ASP.NET.
* Si usa el SDK de ASP.NET 2.0.0 y versiones posteriores (hospedado en Azure o en su propio servidor), obtendrá de forma predeterminada muestreo adaptable, pero puede cambiar al de frecuencia fija como se ha descrito anteriormente. Con el muestreo de frecuencia fija, el SDK del explorador se sincroniza automáticamente con los eventos relacionados con el muestreo. 

*Hay ciertos eventos excepcionales que siempre quiero ver. ¿Cómo se consigue que el módulo de muestreo los reconozca?*

* Inicialice una instancia independiente de TelemetryClient con un nuevo valor de TelemetryConfiguration (no el activo de forma predeterminada). Úsela para enviar sus eventos excepcionales.

## <a name="next-steps"></a>pasos siguientes
* [filtro](app-insights-api-filtering-sampling.md) puede proporcionar un control más estricto de los SDK que envía.

