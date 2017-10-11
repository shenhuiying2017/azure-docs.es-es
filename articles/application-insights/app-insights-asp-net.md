---
title: "Configuración del análisis de aplicaciones web para ASP.NET con Azure Application Insights | Microsoft Docs"
description: "Configure el análisis del rendimiento, la disponibilidad y el uso de un sitio web de ASP.NET, hospedado localmente o en Azure."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: bwren
ms.openlocfilehash: cb247ee68da88265f7c51258644064463d44f8b5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configuración de Application Insights para un sitio web de ASP.NET

Este procedimiento configura una aplicación web de ASP.NET para que envíe datos de telemetría al servicio [Azure Application Insights](app-insights-overview.md). Funciona en las aplicaciones ASP.NET que se hospedan en su propio servidor IIS o en la nube. Obtenga gráficos y un eficaz lenguaje de consulta que le ayudarán a conocer el rendimiento de la aplicación y cómo la usan las personas, más alertas automáticas cuando aparezcan errores o haya problemas de rendimiento. Muchos desarrolladores consideran que estas características son excelentes tal y como están, pero si necesita ampliar y personalizar la telemetría, puede hacerlo.

Su instalación se realiza desde Visual Studio con unos pocos clics. Tiene la opción para evitar cargos. Para ello, solo debe limitar el volumen de la telemetría. Esto le permite probar y depurar un sitio con no muchos usuarios, o incluso supervisarlo. Si decide que desea supervisar su sitio de producción, no le costará trabajo aumentar el límite más adelante.

## <a name="before-you-start"></a>Antes de comenzar
Necesita:

* Visual Studio 2013, actualización 3 o superior. Es mejor que sea superior.
* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarla a ella con su [cuenta Microsoft](http://live.com).

Si está interesado, puede examinar otros temas:

* [Instrumentación de aplicaciones web en tiempo de ejecución con Application Insights](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a>Paso 1: Agregue el SDK de Application Insights

Haga clic con el botón derecho en su proyecto de aplicación web en el Explorador de soluciones y elija **Agregar** > **Telemetría de Application Insights...**  o **Configurar Application Insights**.

![Captura de pantalla del Explorador de soluciones, con Agregar telemetría de Application Insights resaltado](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(En Visual Studio 2015, también hay una opción para agregar Application Insights en el cuadro de diálogo Nuevo proyecto.)

Vaya a la página de configuración de Application Insights:

![Captura de pantalla de la página Registre la aplicación en Application Insights](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** Seleccione la cuenta y la suscripción que usa para acceder a Azure.

**b.** Seleccione el recurso de Azure en el que desea ver los datos de la aplicación. Por lo general:

* Use un [único recurso para diferentes componentes](app-insights-monitor-multi-role-apps.md) de una sola aplicación. 
* Cree recursos independientes para las aplicaciones no relacionadas.
 
Si desea establecer el grupo de recursos o la ubicación en que se almacenan los datos, haga clic en **Parámetros de configuración**. Los grupos de recursos se utilizan para controlar el acceso a los datos. Por ejemplo, si tiene varias aplicaciones que forman parte del mismo sistema, puede poner sus datos de Application Insights en el mismo grupo de recursos.

**c.** Para evitar cargos, ponga un tope en el límite de volumen de datos gratuito. Application Insights es gratuito hasta un volumen de telemetría determinado. Una vez que se crea el recurso, puede cambiar la opción seleccionadas en el portal. Para ello, es preciso abrir **Características y precios** >  **Administración de datos** > **Límite de volumen diario**.

**d.** Haga clic en **Registrar** para avanzar y configurar Application Insights para su aplicación web. La telemetría se enviará a [Azure Portal](https://portal.azure.com), durante la depuración y después de que se haya publicado la aplicación.

**e.** Si no desea enviar datos de telemetría al portal mientras se lleva a cabo una depuración, agregue el SDK de Application Insights a la aplicación, pero no configure ningún recurso en el portal. Durante la depuración podrá ver la telemetría en Visual Studio. Posteriormente, puede volver a esta página de configuración, o bien puede esperar hasta que haya implementado la aplicación y [activar la telemetría en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md).


## <a name="run"></a>Paso 2: Ejecute la aplicación
Ejecute la aplicación con F5. Abra distintas páginas para generar telemetría.

En Visual Studio, aparece un recuento de los eventos que se han registrado.

![Captura de pantalla de Visual Studio. El botón Application Insights se muestra durante la depuración.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>Paso 3: Vea su telemetría
La telemetría se puede ver en Visual Studio o en el portal web de Application Insights. Busque telemetría en Visual Studio, ya que le ayudará a depurar la aplicación. Supervise el rendimiento y uso en el portal web cuando el sistema esté activo. 

### <a name="see-your-telemetry-in-visual-studio"></a>Visualización de datos de telemetría en Visual Studio

En Visual Studio, abra la ventana de Application Insights. Haga clic en el botón **Application Insights** o haga clic con el botón derecho en el Explorador de soluciones, seleccione **Application Insights** y, después, haga clic en el proyecto en **Buscar telemetría activa**.

En la ventana de búsqueda de Application Insights de Visual Studio, en la vista de **datos de la sesión de depuración** encontrará la telemetría generada en el lado del servidor de la aplicación. Experimente con los filtros y haga clic en cualquier evento para ver más información al respecto.

![Captura de pantalla de la vista de datos de la sesión de depuración en la ventana de Application Insights.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Si no ve datos, asegúrese de que el intervalo de tiempo es correcto y haga clic en el icono de búsqueda.

[Más información acerca de las herramientas de Application Insights en Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Visualización de datos de telemetría en el portal web

La telemetría también se puede ver en el portal web de Application Insights (salvo que se elija instalar solo el SDK). El portal tiene más gráficos, herramientas de análisis y vistas de componentes que Visual Studio. El portal también ofrece alertas.

Abra el recurso de Application Insights. Inicie sesión en [Azure Portal](https://portal.azure.com/) y búsquelo allí, o haga clic en el proyecto en Visual Studio y deje que lo lleve allí.

![Captura de pantalla de Visual Studio, en la que se muestra cómo abrir el portal de Application Insights](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Si recibe un error de acceso, es posible que tenga más de un conjunto de credenciales de Microsoft y que haya iniciado sesión con el erróneo. En el portal, cierre la sesión y vuelva a iniciarla.

El portal se abrirá en una vista de los datos de telemetría de su aplicación.

![Captura de pantalla de la página de información general de Application Insights](./media/app-insights-asp-net/66.png)

En el portal, haga clic en cualquier icono para ver su contenido con mayor detalle.

[Más información acerca del uso de Application Insights en Azure Portal](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Paso 4: Publique la aplicación
Publique su aplicación en el servidor IIS o en Azure. Consulte [Secuencia de métricas en directo](app-insights-metrics-explorer.md#live-metrics-stream) para asegurarse de que todo está ejecutándose sin problemas.

La telemetría se crea en el portal de Application Insights, donde puede supervisar las métricas, buscar en la telemetría y configurar los [paneles](app-insights-dashboards.md). También puede usar el eficaz [lenguaje de consulta de Log Analytics](https://docs.loganalytics.io/) para analizar el uso y el rendimiento o para buscar eventos concretos.

También puede seguir analizando la telemetría en [Visual Studio](app-insights-visual-studio.md) con herramientas como búsqueda de diagnóstico y las [tendencias](app-insights-visual-studio-trends.md).

> [!NOTE]
> Si la aplicación envía suficiente telemetría que se acerque a las [limitaciones de peticiones](app-insights-pricing.md#limits-summary), se activará el [muestreo](app-insights-sampling.md) automático. El muestreo reduce la cantidad de datos de telemetría enviados desde su aplicación, a la vez que conserva los datos correlacionados para fines de diagnóstico.
>
>

## <a name="land"></a>Ya está listo

¡Enhorabuena! Ha instalado el paquete de Application Insights en la aplicación y lo ha configurado para que envíe telemetría al servicio Application Insights en Azure.

![Diagrama del movimiento de la telemetría](./media/app-insights-asp-net/01-scheme.png)

El recurso de Azure que recibe la telemetría de la aplicación se identifica mediante una *clave de instrumentación*, que se encuentra en el archivo ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Actualización a futuras versiones del SDK
Para actualizar a una [nueva versión del SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), vuelva a abrir el **Administrador de paquetes NuGet** y filtre por los paquetes instalados. Seleccione **Microsoft.ApplicationInsights.Web** y elija **Actualizar**.

Si ha hecho alguna personalización en ApplicationInsights.config, guarde una copia del mismo antes de realizar la actualización. Luego, combine los cambios en la nueva versión.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Pasos siguientes

### <a name="more-telemetry"></a>Más telemetría

* **[Datos de carga de página y explorador](app-insights-javascript.md)**: inserte un fragmento de código en las páginas web.
* **[Obtenga una supervisión más detallada de las dependencias y excepciones](app-insights-monitor-performance-live-website-now.md)**: instale el Monitor de estado en el servidor.
* **[Codifique eventos personalizados](app-insights-api-custom-events-metrics.md)** para contar, cronometrar y medir las acciones de los usuarios.
* **[Obtenga datos del registro](app-insights-asp-net-trace-logs.md)**: ponga en correlación los datos del registro con la telemetría.

### <a name="analysis"></a>Análisis

* **[Trabajo con Application Insights en Visual Studio](app-insights-visual-studio.md)**<br/>Incluye información acerca de la depuración con telemetría, la búsqueda de diagnóstico y la profundización en el código.
* **[Trabajo con el portal de Application Insights](app-insights-dashboards.md)**<br/> Incluye información acerca de los paneles, eficaces herramientas de diagnóstico y análisis, alertas, un mapa activo de dependencias de la aplicación y exportación de la telemetría.
* **[Analytics](app-insights-analytics-tour.md)**: eficaz lenguaje de consulta.

### <a name="alerts"></a>Alertas

* [Pruebas de disponibilidad](app-insights-monitor-web-app-availability.md): cree estas pruebas para asegurarse de que el sitio se ve en la web.
* [Diagnósticos inteligentes](app-insights-proactive-diagnostics.md): estas pruebas se realizan automáticamente, por lo que no es preciso hacer nada para configurarlas. Le indican si la aplicación tiene una tasa de solicitudes con error inusual.
* [Alertas de métricas](app-insights-alerts.md): establézcalas para que le adviertan si una métrica supera un umbral. Puede establecerlas en las métricas personalizadas que codifique en la aplicación.

### <a name="automation"></a>Automation

* [Creación de recursos de Application Insights mediante PowerShell](app-insights-powershell.md)
