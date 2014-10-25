<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights: comience a supervisar el estado y el uso de la aplicación

*Application Insights se encuentra en su versión de vista previa.*

Application Insights permite supervisar una aplicación activa en cuanto a:

-   **Disponibilidad**: las URL se prueban cada pocos minutos desde distintos lugares del mundo.
-   **Rendimiento**: detecte y diagnostique excepciones y problemas de rendimiento.
-   **Uso**: descubra cómo interactúan los usuarios con la aplicación para mejorarla.

La configuración es muy sencilla y obtendrá resultados en cuestión de minutos. Actualmente, se ofrece compatibilidad con las aplicaciones web de ASP.NET (en servidores particulares o Azure).

Se precisa una cuenta en [Microsoft Azure](http://azure.com) (existe un período de prueba gratuita).

Puede comenzar a usar Application Insights de dos formas:

-   Opción recomendada: [agregar Application Insights a un proyecto en Visual Studio](#add) para supervisar el uso y el rendimiento de la aplicación.
-   [Instalar un agente en el servidor sin volver a implementar][redfield]: supervise un sitio web activo sin necesidad de volver a implementarlo ni modificar el código fuente. Esta opción permite supervisar las excepciones y el rendimiento. Puede agregar la supervisión del uso más adelante.

> [WACOM.NOTE] Existe una [versión anterior de Application Insights](http://msdn.microsoft.com/es-es/library/dn481095.aspx) en Visual Studio Online compatible con un número mayor de tipos de aplicación. La estamos compilando de nuevo desde cero para integrarla en Microsoft Azure. Esta es la nueva versión que se describe en este artículo.

## <a name="add"></a>Agregar Application Insights a un proyecto

Se precisa [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (o una versión posterior).

### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio 2013, asegúrese de seleccionar Application Insights.

![Crear un proyecto ASP.NET](./media/appinsights/appinsights-01-vsnewp1.png)

La primera vez deberá iniciar sesión en la vista previa de Microsoft Azure (al margen de la cuenta de Visual Studio Online).

Use **Parámetros de configuración** si desea asignar al recurso de Azure un nombre diferente al del proyecto, o para que el recurso aparezca en el mismo grupo como un proyecto distinto.

*¿No encuentra la opción Application Insights? Compruebe que está usando Visual Studio 2013 Update 3, que las Herramientas de Application Insights se encuentran habilitadas en Extensiones y actualizaciones, y que está creando un proyecto web, de la Tienda Windows o Windows Phone.*

### ... Si se trata de un proyecto existente:

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Elija Agregar Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Queda un paso pendiente para configurar el análisis de uso de web, pero veamos otros datos primero.*

### <a name="run"></a>2. Ejecutar el proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Ver los datos de supervisión

Abra Application Insights desde un proyecto.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/appinsights/appinsights-04-openPortal.png)

Busque los datos en los mosaicos de **Estado de la aplicación**. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/appinsights/appinsights-41firstHealth.png)

Haga clic en un mosaico para ver su contenido con mayor detalle. Si lo desea, puede cambiar el contenido de los informes. [Obtenga más información sobre cómo configurar los informes de estado de la aplicación.][perf]

### <a name="webclient"></a>4. Configurar el análisis de uso de web

Si agregó Application Insights a un proyecto web *existente*, aún no se mostrará nada en los mosaicos de análisis de uso. Queda un paso pendiente.

En Application Insights, elija Inicio rápido, Instrumentar el sitio web.

![En el proyecto, vaya a Application Insights, haga clic en Inicio rápido, en Instrumentar el sitio web y copie el código](./media/appinsights/appinsights-06webcode.png)

Copie el código JavaScript en las páginas web que desea supervisar, justo delante de la etiqueta de cierre \</head\>. En los proyectos ASP.NET, un modo muy eficaz de supervisar todas las páginas es insertar el código en la página maestra, a la que se suele denominar `_SiteLayout` o `Views\Shared\_Layout`. Tenga en cuenta que el código contiene la clave de Application Insights de la aplicación.

Ejecute la aplicación de nuevo y los datos aparecerán en **Análisis de uso**.

![Haga clic en las distintas opciones para obtener más datos](./media/appinsights/appinsights-05-usageTiles.png)

[Haga clic en los gráficos para ver su contenido con mayor detalle.][perf]

### <a name="deploy"></a>5. Implementar la aplicación

Implemente la aplicación y observe cómo se acumulan los datos.

Cuando la aplicación se active, [configure las pruebas web][availability] para comprobar que efectivamente está activa.

![Ejemplo de supervisión de una aplicación en Application Insights](./media/appinsights/appinsights-00-appblade.png)

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

[Seguir el uso de una aplicación web][usage]

[Supervisar el rendimiento de aplicaciones web][perf]

[Capturar y buscar registros de diagnóstico][diagnostic]

[Solución de problemas][qna]

## Más información

-   [Application Insights: primeros pasos][start]
-   [Supervisar un servidor web activo en este momento][redfield]
-   [Supervisar el rendimiento de aplicaciones web][perf]
-   [Buscar registros de diagnóstico][diagnostic]
-   [Seguimiento de disponibilidad con pruebas web][availability]
-   [Seguir el uso][usage]
-   [Preguntas y respuestas y solución de problemas][qna]

<!--Link references-->

[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/
