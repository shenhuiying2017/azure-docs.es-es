<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Supervisión de la disponibilidad y capacidad de respuesta de la aplicación web

Después de haber implementado la aplicación web, puede configurar pruebas web para supervisar su disponibilidad y capacidad de respuesta. Application Insights enviará solicitudes web a intervalos regulares desde puntos de todo el mundo y puede alertarle si la aplicación responde lentamente o no responde en absoluto.

Puede configurar las pruebas web para cualquier extremo de HTTP que sea accesible desde la red pública de Internet.

1.  [Creación de una nueva aplicación][Creación de una nueva aplicación]
+ [Configuración de una prueba web][Configuración de una prueba web]
+ [Consulta de informes de supervisión][Consulta de informes de supervisión]
+ [Modificación o deshabilitación de una prueba][Modificación o deshabilitación de una prueba]
+ [Pasos siguientes][Pasos siguientes]

## <a name="create"></a>1. Creación de una nueva aplicación

Omita este paso si ya está [supervisando el uso y el estado][supervisando el uso y el estado] de esta aplicación web y desea consultar los datos de disponibilidad en el mismo lugar.

Pero si desea mantener estos resultados separados, inicie sesión en la vista previa de Microsoft Azure y cree una nueva aplicación en Application Insights.

![New \> Application Insights][New \> Application Insights]

## <a name="setup"></a>2. Configuración de una prueba web

En el cuadro doméstico para la aplicación de Application Insights, haga clic en cualquier mosaico vacío de las pruebas web.

![Click the empty availability test][Click the empty availability test]

> *¿Ya tiene pruebas web? Haga clic en el mosaico de las pruebas web y, a continuación, elija Agregar pruebas web.*

Indique un nombre para la prueba y la URL que desea probar. La URL tiene que estar visible desde la red pública de Internet.

![Fill at least the URL of your website][Fill at least the URL of your website]

-   Las ubicaciones de prueba son los lugares desde donde nuestros servidores envían solicitudes web a la URL. Elija dos o tres de tal forma que pueda distinguir los problemas del sitio web de los problemas de red. No puede seleccionar más de tres.

-   Criterio correcto - Especificar los códigos de retorno HTTP aceptables - 200 es lo habitual.

    También puede especificar una cadena que debe encontrarse en cada respuesta correcta. Debe ser una cadena sin formato, sin caracteres comodín. No se olvide de que si el contenido cambia, es posible que tenga que actualizarlo.

-   Alertas - De manera predeterminada, las alertas se envían si hay fallos repetidos cada 15 minutos. Pero puede cambiarlo para ser más sensible y también puede cambiar las direcciones de correo electrónico notificadas.

### Prueba de más URL

Puede agregar más pruebas para tantas URL como desee. Por ejemplo, así como probar la página principal, podría asegurarse de que la base de datos se está ejecutando al probar la URL con una búsqueda.

![On the web tests blade, choose Add][On the web tests blade, choose Add]

## <a name="monitor"></a>3. Consulta de informes de supervisión

Después de uno o dos minutos, cierre y vuelva a abrir el cuadro de la aplicación. (En esta versión, no se actualiza automáticamente).

![Summary results on the home blade][Summary results on the home blade]

Este es un resumen. Si ha definido varias pruebas web para esta aplicación, todas ellas se combinan aquí.

Haga clic por el cuadro de pruebas web para ver una lista de las pruebas web.

Abra una prueba web específica.

![Click a specific webtest][Click a specific webtest]

En el cuadro de pruebas web específico, desplácese hacia abajo a **Pruebas con errores** y seleccione un resultado.

![Click a specific webtest][1]

El resultado muestra el motivo del error.

![Webtest run result][Webtest run result]

Para obtener más información, descargue el archivo de resultados e inspecciónelo en Visual Studio.

## <a name="edit"></a>4. Modificación o deshabilitación de una prueba

Abra una prueba individual para editarla o deshabilitarla.

![Edit or disable a web test][Edit or disable a web test]

Es posible que desee deshabilitar las pruebas web mientras está realizando un mantenimiento en el servicio.

## <a name="next"></a>Pasos siguientes

[Búsqueda de registros de diagnóstico][Búsqueda de registros de diagnóstico]

[Solución de problemas][Solución de problemas]

## <a name="next"></a>Más información

-   [Application Insights][Application Insights]
-   [Incorporación de Application Insights al proyecto][supervisando el uso y el estado]
-   [Supervisión inmediata de un servidor web activo][Supervisión inmediata de un servidor web activo]
-   [Exploración de métricas en Application Insights][Exploración de métricas en Application Insights]
-   [Búsqueda del registro de diagnóstico][Búsqueda de registros de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][Seguimiento de disponibilidad con pruebas web]
-   [Seguimiento de uso con eventos y métricas][Seguimiento de uso con eventos y métricas]
-   [Preguntas y repuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [Creación de una nueva aplicación]: #create
  [Configuración de una prueba web]: #setup
  [Consulta de informes de supervisión]: #monitor
  [Modificación o deshabilitación de una prueba]: #edit
  [Pasos siguientes]: #next
  [supervisando el uso y el estado]: ../app-insights-monitor-application-health-usage/
  [New \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Click the empty availability test]: ./media/appinsights/appinsights-12avail.png
  [Fill at least the URL of your website]: ./media/appinsights/appinsights-13availChoices.png
  [On the web tests blade, choose Add]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Summary results on the home blade]: ./media/appinsights/appinsights-14availSummary.png
  [Click a specific webtest]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Webtest run result]: ./media/appinsights/appinsights-18-availDetails.png
  [Edit or disable a web test]: ./media/appinsights/appinsights-19-availEdit.png
  [Búsqueda de registros de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Supervisión inmediata de un servidor web activo]: ../app-insights-monitor-performance-live-website-now/
  [Exploración de métricas en Application Insights]: ../app-insights-explore-metrics/
  [Seguimiento de disponibilidad con pruebas web]: ../app-insights-monitor-web-app-availability/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-track-usage-custom-events-metrics/
