<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Supervisión del estado y del uso de la aplicación

*Application Insights se encuentra actualmente en su versión de vista previa.*

Asegúrese de que la aplicación se está ejecutando correctamente y que satisface a los usuarios. Application Insights le alertará de cualquier posible problema de rendimiento y le ayudará a encontrar y diagnosticar las causas raíces. Le permitirá realizar el seguimiento de las actividades del usuario para que pueda optimizar la aplicación en función de las necesidades de los clientes.

Application Insights puede supervisar aplicaciones de Windows Phone, de Windows Store y aplicaciones web hospedadas en local o en máquinas virtuales, así como en sitios web de Windows Azure.

Necesitará [Visual Studio Update 3][] y una cuenta de [Microsoft Azure][] (hay un período de prueba gratuito).

1.  [Incorporación de Application Insights][]

-   [Ejecución del proyecto][]
-   [Consulta de los datos del monitor][]
-   [Implementación de la aplicación][]
-   [Pasos siguientes][]

*Además, si desea supervisar un servicio web existente sin volver a implementarlo o utilizando Visual Studio, puede [instalar un agente en el servidor][].*

## <a name="add"></a>1. Incorporación de Application Insights

### Si es un nuevo proyecto...

Cuando crea un nuevo proyecto en Visual Studio 2013, asegúrese de que Application Insights está seleccionado.

![Create an ASP.NET project][]

Si es la primera vez, se le solicitará que proporcione su nombre de usuario para Microsoft Azure en vista previa, o que se registre. (Es diferente de la cuenta de Visual Studio Online).

> ¿No ve la opción de Application Insights? Compruebe que está utilizando Visual Studio 2013 Update 3 y de que está creando un proyecto web, de Windows Store o de Windows Phone.

### ... o si es un proyecto existente

Para agregar Application Insights a un nuevo proyecto, haga clic con el botón secundario en el proyecto en Explorador de soluciones y elija Add Application Insights.

![Choose Add Application Insights][]

## <a name="run"></a>2. Ejecución del proyecto

Ejecute la aplicación con F5 y pruébela. Abra diferentes páginas.

En Visual Studio, verá un recuento de los eventos que se han recibido.

![][]

## <a name="monitor"></a>3. Consulta de los datos del monitor

Abra Application Insights desde su proyecto.

![Right-click your project and open the Azure portal][]

Busque datos en los mosaicos de análisis de uso y del estado de la aplicación. Por ejemplo:

![Click through to more data][]

Haga clic en cualquier mosaico para ver más detalles. [Más información sobre los mosaicos e informes.][]

> [WACOM.NOTE] Muchos de los mosaicos muestran detalles limitados en esta versión en vista previa.

## <a name="deploy"></a>4. Implementación de la aplicación

Implemente la aplicación y observe los datos acumulados.

## <a name="next"></a>Pasos siguientes

[Más información sobre los mosaicos e informes][Más información sobre los mosaicos e informes.]

[Pruebas web][]

[Captura y búsqueda de registros de diagnóstico][]

[Solución de problemas][]

## Más información

-   [Application Insights][]
-   [Incorporación de Application Insights al proyecto][]
-   [Supervisión inmediata de un servidor web activo][instalar un agente en el servidor]
-   [Exploración de métricas en Application Insights][Más información sobre los mosaicos e informes.]
-   [Búsqueda del registro de diagnóstico][Captura y búsqueda de registros de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][Pruebas web]
-   [Seguimiento de uso con eventos y métricas][]
-   [Preguntas y repuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [Incorporación de Application Insights]: #add
  [Ejecución del proyecto]: #run
  [Consulta de los datos del monitor]: #monitor
  [Implementación de la aplicación]: #deploy
  [Pasos siguientes]: #next
  [instalar un agente en el servidor]: ../app-insights-monitor-performance-live-website-now/
  [Create an ASP.NET project]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Choose Add Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Right-click your project and open the Azure portal]: ./media/appinsights/appinsights-04-openPortal.png
  [Click through to more data]: ./media/appinsights/appinsights-05-usageTiles.png
  [Más información sobre los mosaicos e informes.]: ../app-insights-explore-metrics/
  [Pruebas web]: ../app-insights-monitor-web-app-availability/
  [Captura y búsqueda de registros de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Incorporación de Application Insights al proyecto]: ../app-insights-monitor-application-health-usage/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-track-usage-custom-events-metrics/
