<properties 
	pageTitle="Application Insights para aplicaciones de Windows Phone y de la Tienda  Windows" 
	description="Analice el uso y el rendimiento de la aplicación de su dispositivo Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>

# Application Insights para aplicaciones de Windows Phone y de la Tienda  Windows

*Application Insights se encuentra en su versión de vista previa.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Application Insights de Visual Studio permite supervisar una aplicación activa en cuanto a:

* [**Uso**][windowsUsage] \: sepa cuántos usuarios tiene y qué hacen con su aplicación.
* [**Bloqueos**][windowsCrash] \: obtenga informes de diagnóstico de bloqueos y comprenda el impacto de estos sobre los usuarios.

![](./media/appinsights/appinsights-d018-oview.png)

En muchos tipos de aplicaciones, [Visual Studio puede agregar Application Insights a la aplicación en cuestión](#ide) casi sin que usted se dé cuenta. Sin embargo, puesto que está leyendo esto para entender mejor el proceso, le guiaremos a través de los pasos manualmente.

Necesitará:

* Una suscripción a [Microsoft Azure][azure].
* Visual Studio 2013 o posterior.

## 1\. Creación de recursos en Application Insights 

En el [portal de Azure][portal], cree un nuevo recurso de Application Insights.

![Seleccione Nuevo, Servicios para desarrolladores, Application Insights](./media/app-insights-windows-get-started/01-new.png)

Un [recurso][roles] de Azure es una instancia de un servicio. Este recurso es donde se le presentará telemetría de su aplicación analizada.

#### Copia de la clave de instrumentación

La clave identifica el recurso. La necesitará pronto para configurar el SDK para enviar los datos al recurso.

![Abra el espacio de la lista desplegable de Essentials y seleccione la clave de instrumentación](./media/app-insights-windows-get-started/02-props.png)


## 2\. Incorporación del SDK de Application Insights a las aplicaciones

En Visual Studio, agregue el SDK adecuado a su proyecto.

Si es una aplicación universal de Windows, repita los pasos para el proyecto de Windows Phone y el proyecto de Windows.

1. Haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione **Administrar paquetes de NuGet**.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. Seleccione **En línea**, **Incluir versión preliminar** y busque "Application Insights".

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. Seleccione la versión más reciente del paquete apropiado, una de las siguientes:
   * Application Insights para aplicaciones de Windows: *para aplicaciones de la Tienda Windows*
   * Application Insights para aplicaciones de Windows Phone
   * Application Insights para aplicaciones web: *úselo para una aplicación de escritorio* 
4. Edite ApplicationInsights.config \(que la instalación de NuGet ha agregado\). Inserte esto justo antes de la etiqueta de cierre:

    `<InstrumentationKey>`\* la clave que copió \*`</InstrumentationKey>`

**Aplicaciones universales de Windows**: repita los pasos para el proyecto de Windows Phone y de la Tienda Windows.

## <a name="network"></a>3. Habilitación del acceso de red para la aplicación

Si la aplicación no [solicita acceso de red saliente](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx) aún, tendrá que agregar esta acción a su manifiesto como una [funcionalidad obligatoria](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Ejecución del proyecto

[Ejecute la aplicación con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) y úsela para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![](./media/appinsights/appinsights-09eventcount.png)

En modo de depuración, la telemetría se envía tan pronto como se genera. En modo de lanzamiento, la telemetría se almacena en el dispositivo y solo se envía cuando se reanuda la aplicación.

## <a name="monitor"></a>5. Consulta de los datos del monitor

Abra Application Insights desde un proyecto.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/appinsights/appinsights-04-openPortal.png)


Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/appinsights/appinsights-26-devices-01.png)

Si espera más datos, haga clic en Actualizar después de unos segundos.

Haga clic en un gráfico para ver su contenido con mayor detalle.


## <a name="deploy"></a>5. Publicación de la aplicación en Azure

[Publique la aplicación](http://dev.windows.com/publish) y vea cómo se acumulan los datos a medida que los usuarios la descargan y la usan.

## <a name="ide"></a>La manera automatizada

Si prefiere usar Visual Studio para completar los pasos de instalación, puede hacerlo con Windows Phone, la Tienda Windows y muchos otros tipos de aplicaciones.

###<a name="new"></a> Si está creando un nuevo proyecto de aplicación de Windows...

En el cuadro de diálogo Nuevo proyecto, seleccione Application Insights.

Si se le pide que inicie sesión, use las credenciales para la cuenta de Azure \(que es independiente de su cuenta de Visual Studio Online\).

![](./media/appinsights/appinsights-d21-new.png)


###<a name="existing"></a> O si se trata de un proyecto existente...

Agregue Application Insights desde el Explorador de soluciones.


![](./media/appinsights/appinsights-d22-add.png)


## <a name="usage"></a>Pasos siguientes

[Seguimiento del uso de una aplicación][windowsUsage]

[Detección y diagnóstico de bloqueos en la aplicación][windowsCrash]

[Captura y búsqueda de registros de diagnóstico][diagnostic]

[Uso de la API para enviar telemetría personalizada][api]

[Solución de problemas][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md


<!--HONumber=54-->