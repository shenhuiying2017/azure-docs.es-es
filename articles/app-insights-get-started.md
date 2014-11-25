<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Application Insights - Introducción

*Application Insights se encuentra actualmente en su versión de vista previa.*

Application Insights le permite supervisar la disponibilidad, rendimiento y uso de su aplicación activa. (No tiene que ser una aplicación de Microsoft Azure). La configuración es muy sencilla, y verá los resultados en cuestión de minutos.

-   **Disponibilidad**: asegúrese de que la aplicación web está disponible y que responde. Probaremos las URL cada pocos minutos desde ubicaciones en todo el mundo y le haremos saber si hay un problema.
-   **Rendimiento**: diagnostique cualquier problema o excepción del rendimiento en el servicio web. Vea cómo los tiempos de respuesta varían con el recuento de solicitudes, averigüe si la CPU u otros recursos se están ampliando, obtenga seguimientos de la pila a partir de excepciones y buque fácilmente en los seguimientos de registros.
-   **Uso**: averigüe lo que están haciendo los usuarios con la aplicación para que pueda concentrar el trabajo de desarrollo donde es más útil. Actualmente, puede supervisar aplicaciones web y aplicaciones de Windows Store y de Windows Phone.

## Introducción

Existen dos formas de comenzar:

-   [Incorporación de Application Insights al proyecto de Visual Studio][Incorporación de Application Insights al proyecto de Visual Studio]

    Agregue Application Insights a los proyectos para realizar el seguimiento del uso, rendimiento y disponibilidad, y para analizar los registros de diagnóstico. Puede ver datos en cuestión de minutos en el modo de depuración y después implementar el proyecto para obtener datos activos.

    Utilice esta opción si está actualizando o creando un proyecto.

    [Introducción a la incorporación de Application Insights al proyecto.][Incorporación de Application Insights al proyecto de Visual Studio]

-   [Problemas de diagnóstico inmediato en un servicio web activo][Problemas de diagnóstico inmediato en un servicio web activo]

    Instale el agente Application Insights en el servidor de IIS y consulte los datos de rendimiento en cuestión de minutos. Inspeccione el recuento de solicitudes, los tiempos de respuesta, la carga de recursos y obtenga el seguimiento de las excepciones.

    Utilice esta opción si necesita comprender qué está ocurriendo ahora en su servidor web. No implica la reimplementación del código. Sin embargo, no necesita acceso administrativo para el servidor ni una cuenta de Microsoft Azure.

    Puede agregar supervisión de la disponibilidad en cualquier momento.

    Posteriormente, puede utilizar la otra opción para agregar Application Insights al proyecto para analizar los registros de diagnóstico y realizar el seguimiento del uso.

    [Introducción a la instalación de Application Insights en el servidor web.][Problemas de diagnóstico inmediato en un servicio web activo]

> [WACOM.NOTE] Hay una [versión anterior de Application Insights][versión anterior de Application Insights] en Visual Studio Online. La estamos volviendo a crear desde el principio como parte de Microsoft Azure y es la nueva versión sobre la que está leyendo aquí.

![Supervisión de la aplicación de ejemplo en Application Insights][Supervisión de la aplicación de ejemplo en Application Insights]

## Más información

-   [Application Insights][Application Insights]
-   [Incorporación de Application Insights al proyecto][Incorporación de Application Insights al proyecto de Visual Studio]
-   [Supervisión inmediata de un servidor web activo][Problemas de diagnóstico inmediato en un servicio web activo]
-   [Exploración de métricas en Application Insights][Exploración de métricas en Application Insights]
-   [Búsqueda del registro de diagnóstico][Búsqueda del registro de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][Seguimiento de disponibilidad con pruebas web]
-   [Seguimiento de uso con eventos y métricas][Seguimiento de uso con eventos y métricas]
-   [Preguntas y repuestas y solución de problemas][Preguntas y repuestas y solución de problemas]

<!--Link references-->

  [Incorporación de Application Insights al proyecto de Visual Studio]: ../app-insights-monitor-application-health-usage/
  [Problemas de diagnóstico inmediato en un servicio web activo]: ../app-insights-monitor-performance-live-website-now/
  [versión anterior de Application Insights]: http://msdn.microsoft.com/es-es/library/dn481095.aspx
  [Supervisión de la aplicación de ejemplo en Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights]: ../app-insights-get-started/
  [Exploración de métricas en Application Insights]: ../app-insights-explore-metrics/
  [Búsqueda del registro de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Seguimiento de disponibilidad con pruebas web]: ../app-insights-monitor-web-app-availability/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-web-track-usage-custom-events-metrics/
  [Preguntas y repuestas y solución de problemas]: ../app-insights-troubleshoot-faq/
