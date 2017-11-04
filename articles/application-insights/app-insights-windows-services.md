---
title: Azure Application Insights para Windows Server y roles de trabajo | Microsoft Docs
description: "Agregue manualmente el SDK de Application Insights a su aplicación de ASP.NET para analizar el uso, la disponibilidad y el rendimiento."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: bf7921926c69fff214e94a2d0edc22b28fb8eac0
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Configuración manual de Application Insights para aplicaciones .NET

Puede configurar [Application Insights](app-insights-overview.md) para que supervise una amplia variedad de aplicaciones o roles de aplicación, componentes o microservicios. Para servicios y aplicaciones web, Visual Studio ofrece una [configuración de un solo paso](app-insights-asp-net.md). Para otros tipos de aplicación. NET, como roles de servidor back-end o aplicaciones de escritorio, puede configurar Application Insights manualmente.

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Antes de comenzar

Necesita:

* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com).
* Visual Studio 2013 o posterior.

## <a name="add"></a>1. Selección de un recurso en Application Insights

El "recurso" es donde los datos se recopilan y se muestran en Azure Portal. Debe decidir si desea crear uno o compartir uno ya existente.

### <a name="part-of-a-larger-app-use-existing-resource"></a>Parte de una aplicación de mayor tamaño: usar un recurso existente

Si la aplicación web tiene varios componentes (por ejemplo, una aplicación web front-end y uno o varios servicios back-end), debería enviar telemetría desde todos los componentes al mismo recurso. Esto permitirá que se muestren en una sola asignación de aplicaciones y hace posible realizar un seguimiento de una solicitud de un componente a otro.

Por lo tanto, si ya está supervisando otros componentes de esta aplicación, basta con que use el mismo recurso.

Abra el recurso en [Azure Portal](https://portal.azure.com/). 

### <a name="self-contained-app-create-a-new-resource"></a>Aplicación independiente: crear un recurso

Si la nueva aplicación no guarda relación con otras aplicaciones, debería tener su propio recurso.

Inicie sesión en el [Portal de Azure](https://portal.azure.com/)y cree un nuevo recurso de Application Insights. Elija ASP.NET como el tipo de aplicación.

![Haga clic en Nuevo, Application Insights.](./media/app-insights-windows-services/01-new-asp.png)

La elección del tipo de aplicación establece el contenido predeterminado de las hojas de recurso.

## <a name="2-copy-the-instrumentation-key"></a>2. Copia de la clave de instrumentación
La clave identifica al recurso. La va a instalar pronto en el SDK, con el fin de dirigir los datos hacia el recurso.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Instalación del paquete de Application Insights en su aplicación
La instalación y configuración del paquete de Application Insights varía en función de la plataforma en la que trabaje. 

1. En Visual Studio, haga clic con el botón derecho en el proyecto y elija **Administrar paquetes NuGet**.
   
    ![Haga clic con el botón secundario en el proyecto y seleccione Administrar paquetes de Nuget.](./media/app-insights-windows-services/03-nuget.png)
2. Instale el paquete de Application Insights para aplicaciones de Windows Server, "Microsoft.ApplicationInsights.WindowsServer".
   
    ![Busque "Application Insights"](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *¿Qué versión?*

    Active **incluir versión preliminar** si desea probar las características más recientes. Los documentos o blogs pertinentes indican si se necesita una versión preliminar.
    
    *¿Puedo usar otros paquetes?*
   
    Sí. Elija "Microsoft.ApplicationInsights" si solo desea usar la API para enviar su propia telemetría. El paquete de Windows Server incluye la API más otra serie de paquetes, como la colección de contadores de rendimiento y la supervisión de dependencias. 

### <a name="to-upgrade-to-future-package-versions"></a>Para actualizar a futuras versiones del paquete
De vez en cuando, lanzamos una versión nueva del SDK.

Para actualizar a una [nueva versión del paquete](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), vuelva a abrir el Administrador de paquetes NuGet y filtre los paquetes instalados. Seleccione **Microsoft.ApplicationInsights.WindowsServer** y elija **Actualizar**.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.

## <a name="4-send-telemetry"></a>4. Envío de datos de telemetría
**Si ha instalado solo el paquete de la API:**

* Establezca la clave de instrumentación en el código, por ejemplo en `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "`*su clave*`";` 
* [Escriba su propia telemetría mediante la API](app-insights-api-custom-events-metrics.md#ikey).

**Si ha instalado otros paquetes de Application Insights,** puede, si lo prefiere, usar el archivo .config para establecer la clave de instrumentación:

* Edite ApplicationInsights.config (que la instalación de NuGet agregó). Inserte esto justo antes de la etiqueta de cierre:
  
    `<InstrumentationKey>`*la clave de instrumentación que copió*`</InstrumentationKey>`
* Asegúrese de que las propiedades de ApplicationInsights.config en el Explorador de soluciones se establecen en **Build Action = Content, Copy to Output Directory = Copy**.

Es útil establecer la clave de instrumentación en el código si desea [cambiar la clave para diferentes configuraciones de compilación](app-insights-separate-resources.md). Si establece la clave en el código, no tiene que establecerla en el archivo `.config`.

## <a name="run"></a> Ejecución del proyecto
Use **F5**para ejecutar la aplicación y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![Recuento de eventos en Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Visualización de los datos de telemetría
Vuelva al [Portal de Azure](https://portal.azure.com/) y busque el recurso de Application Insights.

Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-windows-services/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas](app-insights-web-monitor-performance.md)

### <a name="no-data"></a>¿No hay datos?
* Use la aplicación y abra varias páginas para generar telemetría.
* Abra el icono [Buscar](app-insights-diagnostic-search.md) para ver los eventos individuales. A veces, los eventos tardan un poco en llegar a través de la canalización de métricas.
* Espere unos segundos y haga clic en **Actualizar**. Los gráficos se actualizan automáticamente de forma periódica, pero puede actualizarlos manualmente si espera que aparezcan algunos datos.
* Vea [Solución de problemas](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicación de la aplicación
Implemente ahora la aplicación en su servidor o en Azure y observe cómo se acumulan los datos.

![Uso de Visual Studio para publicar la aplicación](./media/app-insights-windows-services/15-publish.png)

Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Al implementar la aplicación en la configuración de lanzamiento, los datos se acumulan más lentamente.

### <a name="no-data-after-you-publish-to-your-server"></a>¿No hay datos después de publicar en el servidor?
Abra los puertos para el tráfico de salida en el firewall del servidor. Consulte [esta página](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) para obtener la lista de direcciones necesarias 

### <a name="trouble-on-your-build-server"></a>¿Tiene problemas el servidor de compilación?
Consulte [este apartado de la solución de problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Si la aplicación genera mucha telemetría, el módulo de muestreo adaptable reducirá automáticamente el volumen que se envía al portal mediante el envío de únicamente una fracción representativa de eventos. Sin embargo, los eventos relacionados con la misma solicitud se seleccionarán o se anulará su selección como grupo, por lo que puede navegar entre ellos. 
> [Más información sobre el muestreo](app-insights-sampling.md).
> 
> 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Pasos siguientes
* [Agregue más telemetría](app-insights-asp-net-more.md) para obtener la visión completa de 360 grados de la aplicación.

