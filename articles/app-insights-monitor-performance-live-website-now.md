<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

<!-- Required end of Sprint 69 - AUX48 -->

# Supervisión inmediata del rendimiento en un sitio web activo

*Application Insights se encuentra actualmente en su versión de vista previa.*

¿La aplicación web se comporta de manera errónea? Diagnostique con rapidez las excepciones y los problemas de rendimiento sin volver a crearla o implementarla. Instale el agente Application Insights en el servidor y podrá encontrar cuellos de botella de rendimiento y obtener seguimientos de la pila para todas las excepciones.

#### ¿Cuándo debo utilizar este método para configurar Application Insights?

Este método se utiliza fundamentalmente para diagnosticar rápidamente los problemas de rendimiento en un sitio web activo de IIS.

Solo instale un agente en el servidor y consulte los datos de rendimiento en Application Insights.

-   Puede aplicarlo a una aplicación ASP .NET hospedada en un servidor de IIS.

-   Necesita una [cuenta de Microsoft Azure][cuenta de Microsoft Azure], para que pueda ver los datos.

-   Necesitará acceso de administrador en el servidor donde se está ejecutando la aplicación web.

-   *No* necesita el código de la aplicación web y no tiene que volver a crear o implementar la aplicación.

-   Este método instrumenta la aplicación web actual. No introduzca código de seguimiento o de registro. (Pero puede hacerlo en otro momento si lo desea).

Si desea insertar seguimientos de registro o diagnóstico, no continúe aquí. En cambio, [agregue Application Insights al proyecto][existente] y vuelva a implementarla.

## Instalación del agente Application Insights en el servidor web

1.  En el servidor web, inicie sesión con las credenciales de administrador.

2.  Asegúrese de que cuenta con la versión 5.0 o posterior del [instalador de la plataforma web][instalador de la plataforma web].
3.  Instale el agente Application Insights, utilizando el instalador de la plataforma web.

    ![][0]

4.  En el asistente de instalación, inicie sesión en Microsoft Azure.

    ![][1]

5.  Seleccione la aplicación web instalada o sitio web que desea supervisar y, a continuación, configure el nombre bajo el cual desea ver los resultados del portal Application Insights. Finalmente, haga clic en el botón 'Agregar'.

    ![][2]

    Normalmente, debe elegir crear un nuevo recurso.

    Puede usar un recurso existente si, por ejemplo, si ya ha configurado [pruebas web][pruebas web] para el sitio.

6.  Tenga en cuenta que ApplicationInsights.config se ha insertado en los sitios web que desea supervisar.

    ![][3]

   También hay algunos cambios en web.config.

### ¿Desea (volver a) configurarlo después?

Una vez completado el asistente, puede volver a configurar el agente siempre que lo desee. También puede utilizarlo si ha instalado el agente pero hay algún problema con la configuración inicial.

![Hacer clic en el icono de Application Insights en la barra de tareas][Hacer clic en el icono de Application Insights en la barra de tareas]

## Consulta de los datos

Abra su cuenta en Azure, busque Application Insights y abra los recursos que ha creado

![][4]

Verá los datos en Estado de la aplicación.

![][5]

#### ¿No hay datos?

-   Utilice su sitio para generar algunos datos.
-   Espere unos minutos para dejar que lleguen los datos.
-   Asegúrese de que el firewall del servidor permite el tráfico saliente en el puerto 443 a dc.services.visualstudio.com

## <a name="next"></a>Pasos siguientes

[Consulta de datos][Consulta de datos]

[Búsqueda de registros de diagnóstico][Búsqueda de registros de diagnóstico]

[Pruebas web][pruebas web]

[Configuración de la supervisión de uso][Configuración de la supervisión de uso]

[Solución de problemas][Solución de problemas]

## Más información

-   [Application Insights][Application Insights]
-   [Incorporación de Application Insights al proyecto][Configuración de la supervisión de uso]
-   [Supervisión inmediata de un servidor web activo][Supervisión inmediata de un servidor web activo]
-   [Exploración de métricas en Application Insights][Consulta de datos]
-   [Búsqueda del registro de diagnóstico][Búsqueda de registros de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][pruebas web]
-   [Seguimiento de uso con eventos y métricas][Seguimiento de uso con eventos y métricas]
-   [Preguntas y repuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [cuenta de Microsoft Azure]: http://azure.com
  [instalador de la plataforma web]: http://www.microsoft.com/web/downloads/platform.aspx
  [0]: ./media/appinsights/appinsights-031-wpi.png
  [1]: ./media/appinsights/appinsights-035-signin.png
  [2]: ./media/appinsights/appinsights-036-configAIC.png
  [pruebas web]: ../app-insights-monitor-web-app-availability/
  [3]: ./media/appinsights/appinsights-034-aiconfig.png
  [Hacer clic en el icono de Application Insights en la barra de tareas]: ./media/appinsights/appinsights-033-aicRunning.png
  [4]: ./media/appinsights/appinsights-08openApp.png
  [5]: ./media/appinsights/appinsights-037-results.png
  [Consulta de datos]: ../app-insights-explore-metrics/
  [Búsqueda de registros de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Configuración de la supervisión de uso]: ../app-insights-monitor-application-health-usage/
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Supervisión inmediata de un servidor web activo]: ../app-insights-monitor-performance-live-website-now/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-track-usage-custom-events-metrics/
