<properties title="Track custom usage events and metrics in your web app with Application Insights" pageTitle="Seguir eventos y métricas de uso en su aplicación web con Application Insights" description="Inserte algunas líneas de código para averiguar qué están haciendo los usuarios con su sitio web." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani"  />
 
<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-01" ms.author="awills" />

# Seguir los eventos y las métricas de uso personalizados en su aplicación web

*Application Insights se encuentra en su versión de vista previa.*

Inserte unas pocas líneas de código en la aplicación web para averiguar qué hacen los usuarios con él. Puede realizar un seguimiento de eventos, métricas y vistas de página. Verá los gráficos y tablas de los datos, agregados para todos los usuarios. 

> [AZURE.NOTE] Actualmente, la experiencia de usuario completa no tiene lugar. Puede enviar métricas y eventos personalizados a Application Insights y buscar la telemetría sin procesar en [Búsqueda de diagnósticos][diagnostic]. Pero aún no puede ver los gráficos estadísticos implícitos (pronto estarán disponibles).

<!-- Sample pic -->

* [Seguimiento de cliente y servidor](#clientServer)
* [Antes de comenzar](#prep)
* [Seguir las métricas](#metrics)
* [Seguir los eventos](#events)
* [Seguir las vistas de página](#pageViews)
* [Filtrar, buscar y segmentar los datos con propiedades](#properties)
* [Combinar las métricas y los eventos](#measurements)
* [Establecer valores de propiedad predeterminados](#defaults)
* [Definir varios contextos](#contexts)
* [Activar y desactivar la telemetría](#disable)
* [Pasos siguientes](#next)



## <a name="clientServer"></a> Seguimiento de cliente y servidor

Puede enviar telemetría desde el cliente (página web) o desde el servidor de la aplicación, o desde ambos.

Las API de cliente y servidor son muy similares. Puede enviar los mismos tipos de telemetría tanto desde los exploradores web de los usuarios como desde el servidor web. La diferencia reside en el ámbito de los datos que se puede enviar.

* El seguimiento en el cliente web es especialmente útil si tiene páginas web altamente activas con gran cantidad de JavaScript. Por ejemplo, podría supervisar la frecuencia con la que los usuarios hacen clic en un botón determinado o la frecuencia con la que encuentran errores de validación.
* El seguimiento en el servidor web es más útil para supervisar métricas y eventos empresariales, como el valor del carro de la compra de un cliente o el número de pedidos abandonados.

En una aplicación web ASP.NET típica, tiene la llamada de JavaScript predeterminada a trackPageView() en la página maestra web y agregará algunas llamadas para seguir eventos y métricas en el código del servidor. Si el código de cliente es bastante completo, también puede agregar algunas llamadas para seguir eventos y métricas en el cliente.


## <a name="prep"></a>Antes de comenzar

Si no ha hecho esto aún:

* Para obtener la telemetría de una aplicación web ASP.NET:
    [Incorporación de Application Insights al proyecto][greenbrown]
    En el código del servidor web, incluya:
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [Configure el análisis de uso web][usage]. El código de inicialización de JavaScript debe incluirse en cada página web en la que desea escribir código de supervisión o en una página maestra. 
    Si funciona, debería ver datos en la hoja Información general bajo Análisis de uso.

Al ejecutar la aplicación en la máquina de desarrollo en modo de depuración, los resultados aparecerán en Application Insights en unos segundos. Al implementar la aplicación, los datos tardan más tiempo en desplazarse por la canalización desde el servidor y los clientes.

<!--
## <a name="metrics"></a> Seguir las métricas

No tiene que hacer nada más para obtener datos de uso básicos, como por ejemplo vistas de página. Pero puede escribir unas pocas líneas de código para obtener más información acerca de lo que hacen los usuarios con su aplicación.

Por ejemplo, si la aplicación es un juego, podría querer saber el promedio de puntuación que logran usuarios y ver si les resulta más fácil o más difícil después de publicar una nueva versión.

Para seguir una métrica (es decir, un valor numérico como una puntuación) inserte una línea de script como esta en un lugar adecuado en su aplicación:

JavaScript en el cliente

    appInsights.trackMetric("Alerts", notifications.Count);

C# en el servidor

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

VB en el servidor

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

Pruebe la aplicación y úsela para ejecutar la llamada trackMetric().


A continuación, vaya a la aplicación en Application Insights y haga clic en el título [Métricas][metrics]. Seleccione la métrica para ver los primeros resultados.


El gráfico muestra el promedio reciente sobre los valores registrados de todos los usuarios. 


(Por cierto: las métricas no están optimizadas para diagnosticar problemas. Si es lo que necesita, consulte [Registro de diagnóstico][diagnostic]). -->


## <a name="events"></a>Seguir los eventos

Los eventos le indican la frecuencia de una aparición, promediada entre los usuarios. Por ejemplo, supongamos que desea saber con qué frecuencia los usuarios completan su juego. En el código que finaliza el juego, inserte una línea como esta:

JavaScript en el cliente

    appInsights.trackEvent("EndOfGame");

C# en el servidor
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

VB en el servidor


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

Si envía la telemetría desde el cliente y el servidor, asegúrese de asignar nombres diferentes a los eventos.


## <a name="pageViews"></a>Vistas de página (solo cliente)

De forma predeterminada, el script de inicialización en el encabezado de la página web registra una vista de página, asignando al evento el nombre de la dirección URL relativa de la página. Estas llamadas proporcionan las estadísticas de uso de la página básicas. 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### Datos de página personalizados

Si lo desea, puede modificar la llamada para cambiar el nombre, o bien puede insertar llamadas adicionales. Por ejemplo, si la aplicación de una página web muestra varias fichas, podría estar interesado en grabar una vista de página cuando el usuario cambie a otra ficha. Por ejemplo:

JavaScript en el cliente:

    appInsights.trackPageView("tab1");

Si tiene varias fichas dentro de páginas HTML diferentes, puede especificar también la dirección URL:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>Filtrar, buscar y segmentar los datos con propiedades

Puede asociar propiedades y medidas a los eventos, a las vistas de página y a otros datos de telemetría. 

Las **propiedades** son valores de cadena que se pueden utilizar para filtrar la telemetría en los informes de uso. Por ejemplo si la aplicación proporciona varios juegos, querrá asociar el nombre del juego a cada evento, de forma que pueda ver qué juegos son los más populares.

Las **mediciones** son valores numéricos de los que puede obtener las estadísticas en los informes de uso.


JavaScript en el cliente

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# en el servidor

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


VB en el servidor

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


Asocie propiedades a vistas de página de la misma manera:

JavaScript en el cliente

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] Tenga cuidado de no registrar información de identificación personal en las propiedades.


## Vistas de página y eventos programados

Puede asociar datos de tiempo a eventos y vistas de página. En lugar de una llamada a trackEvent o trackPageView, use estas llamadas:

JavaScript en el cliente

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Use la misma cadena como primer parámetro en las llamadas inicial y final.

## <a name="defaults"></a>Establecer valores predeterminados de propiedad (no en el cliente web)

Puede establecer valores predeterminados en TelemetryContext. Están asociados a cada métrica y evento enviados desde el contexto. 
    

C# en el servidor

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
VB en el servidor

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
La telemetría individual puede invalidar los valores predeterminados.

Si desea cambiar entre grupos de valores predeterminados de propiedad, configure varios contextos.



## <a name="next"></a>Pasos siguientes


[Buscar eventos y registros][diagnostic]

[Solución de problemas][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




