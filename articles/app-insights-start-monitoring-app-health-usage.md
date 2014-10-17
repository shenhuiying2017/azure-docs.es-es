<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights: comience a supervisar el estado y el uso de la aplicación

*Application Insights se encuentra en su versión de vista previa.*

Application Insights permite supervisar una aplicación activa en cuanto a:

-   **Disponibilidad**: las URL se prueban cada pocos minutos desde distintos lugares del mundo.
-   **Rendimiento**: detecte y diagnostique excepciones y problemas de rendimiento.
-   **Uso**: descubra cómo interactúan los usuarios con la aplicación para mejorarla.

La configuración es muy sencilla y obtendrá resultados en cuestión de minutos. Actualmente, se ofrece compatibilidad con las aplicaciones web de ASP.NET (en servidores particulares o Azure).

Se precisa una cuenta en [Microsoft Azure][Microsoft Azure] (existe un período de prueba gratuita).

Puede comenzar a usar Application Insights de dos formas:

-   Opción recomendada: [agregar Application Insights a un proyecto en Visual Studio][agregar Application Insights a un proyecto en Visual Studio] para supervisar el uso y el rendimiento de la aplicación.
-   [Instalar un agente en el servidor sin volver a implementar][Instalar un agente en el servidor sin volver a implementar]: supervise un sitio web activo sin necesidad de volver a implementarlo ni modificar el código fuente. Esta opción permite supervisar las excepciones y el rendimiento. Puede agregar la supervisión del uso más adelante.

> [WACOM.NOTE] Existe una [versión anterior de Application Insights][versión anterior de Application Insights] en Visual Studio Online compatible con un número mayor de tipos de aplicación. La estamos compilando de nuevo desde cero para integrarla en Microsoft Azure. Esta es la nueva versión que se describe en este artículo.

## <a name="add"></a>Agregar Application Insights a un proyecto

Se precisa [Visual Studio 2013 Update 3][Visual Studio 2013 Update 3] (o una versión posterior).

### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio 2013, asegúrese de seleccionar Application Insights.

![Crear un proyecto ASP.NET][Crear un proyecto ASP.NET]

La primera vez deberá iniciar sesión en la vista previa de Microsoft Azure (al margen de la cuenta de Visual Studio Online).

Use **Parámetros de configuración** si desea asignar al recurso de Azure un nombre diferente al del proyecto, o para que el recurso aparezca en el mismo grupo como un proyecto distinto.

*¿No encuentra la opción Application Insights? Compruebe que está usando Visual Studio 2013 Update 3, que las Herramientas de Application Insights se encuentran habilitadas en Extensiones y actualizaciones, y que está creando un proyecto web, de la Tienda Windows o Windows Phone.*

### ... Si se trata de un proyecto existente:

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Elija Agregar Application Insights][Elija Agregar Application Insights]

*Queda un paso pendiente para configurar el análisis de uso de web, pero veamos otros datos primero.*

### <a name="run"></a>2. Ejecutar el proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![][]

### <a name="monitor"></a>3. Ver los datos de supervisión

Abra Application Insights desde un proyecto.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure][Haga clic con el botón secundario en el proyecto y abra el Portal de Azure]

Busque los datos en los mosaicos de **Estado de la aplicación**. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos][Haga clic en las distintas opciones para obtener más datos]

Haga clic en un mosaico para ver su contenido con mayor detalle. Si lo desea, puede cambiar el contenido de los informes. [Obtenga más información sobre cómo configurar los informes de estado de la aplicación.][Obtenga más información sobre cómo configurar los informes de estado de la aplicación.]

### <a name="webclient"></a>4. Configurar el análisis de uso de web

Si agregó Application Insights a un proyecto web *existente*, aún no se mostrará nada en los mosaicos de análisis de uso. Queda un paso pendiente.

En Application Insights, elija Inicio rápido, Instrumentar el sitio web.

![En el proyecto, vaya a Application Insights, haga clic en Inicio rápido, en Instrumentar el sitio web y copie el código][En el proyecto, vaya a Application Insights, haga clic en Inicio rápido, en Instrumentar el sitio web y copie el código]

Copie el código JavaScript en las páginas web que desea supervisar, justo delante de la etiqueta de cierre \</head\>. En los proyectos ASP.NET, un modo muy eficaz de supervisar todas las páginas es insertar el código en la página maestra, a la que se suele denominar `_SiteLayout` o `Views\Shared\_Layout`. Tenga en cuenta que el código contiene la clave de Application Insights de la aplicación.

Ejecute la aplicación de nuevo y los datos aparecerán en **Análisis de uso**.

![Haga clic en las distintas opciones para obtener más datos][1]

[Haga clic en los gráficos para ver su contenido con mayor detalle.][Obtenga más información sobre cómo configurar los informes de estado de la aplicación.]

### <a name="deploy"></a>5. Implementar la aplicación

Implemente la aplicación y observe cómo se acumulan los datos.

Cuando la aplicación se active, [configure las pruebas web][configure las pruebas web] para comprobar que efectivamente está activa.

![Ejemplo de supervisión de una aplicación en Application Insights][Ejemplo de supervisión de una aplicación en Application Insights]

### ¿Desea cambiar el nombre de la aplicación en el portal?

Si lo desea, puede cambiar el recurso al que el proyecto envía la telemetría.

El "recurso" es la hoja con nombre de Application Insights donde aparecen los resultados. Puede usar varios recursos en un grupo (por ejemplo, si cuenta con varios proyectos que forman parte de una aplicación empresarial).

En el Explorador de soluciones, haga clic con el botón secundario en ApplicationInsights.config y elija **Actualizar Application Insights**. Se abrirá un asistente donde puede elegir otro recurso existente o crear uno nuevo.

A continuación, vuelva al portal y elimine el recurso anterior.

## <a name="video"></a>Vídeos

### Introducción

> [AZURE.VIDEO application-insights-introduction]

### Primeros pasos

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Pasos siguientes

[Seguir el uso de una aplicación web][Seguir el uso de una aplicación web]

[Supervisar el rendimiento de aplicaciones web][Obtenga más información sobre cómo configurar los informes de estado de la aplicación.]

[Capturar y buscar registros de diagnóstico][Capturar y buscar registros de diagnóstico]

[Solución de problemas][Solución de problemas]

## Más información

-   [Application Insights: primeros pasos][Application Insights: primeros pasos]
-   [Supervisar un servidor web activo en este momento][Instalar un agente en el servidor sin volver a implementar]
-   [Supervisar el rendimiento de aplicaciones web][Obtenga más información sobre cómo configurar los informes de estado de la aplicación.]
-   [Buscar registros de diagnóstico][Capturar y buscar registros de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][configure las pruebas web]
-   [Seguir el uso][Seguir el uso de una aplicación web]
-   [Preguntas y respuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [Microsoft Azure]: http://azure.com
  [agregar Application Insights a un proyecto en Visual Studio]: #add
  [Instalar un agente en el servidor sin volver a implementar]: ../app-insights-monitor-performance-live-website-now/
  [versión anterior de Application Insights]: http://msdn.microsoft.com/en-us/library/dn481095.aspx
  [Visual Studio 2013 Update 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Crear un proyecto ASP.NET]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Elija Agregar Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Haga clic con el botón secundario en el proyecto y abra el Portal de Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Haga clic en las distintas opciones para obtener más datos]: ./media/appinsights/appinsights-41firstHealth.png
  [Obtenga más información sobre cómo configurar los informes de estado de la aplicación.]: ../app-insights-web-monitor-performance/
  [En el proyecto, vaya a Application Insights, haga clic en Inicio rápido, en Instrumentar el sitio web y copie el código]: ./media/appinsights/appinsights-06webcode.png
  [1]: ./media/appinsights/appinsights-05-usageTiles.png
  [configure las pruebas web]: ../app-insights-monitor-web-app-availability/
  [Ejemplo de supervisión de una aplicación en Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Seguir el uso de una aplicación web]: ../app-insights-web-track-usage/
  [Capturar y buscar registros de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights: primeros pasos]: ../app-insights-start-monitoring-app-health-usage/
