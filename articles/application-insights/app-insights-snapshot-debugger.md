---
title: "Depurador de instantáneas de Azure Application Insights para aplicaciones de .NET | Microsoft Docs"
description: "Depuración de las instantáneas que se recopilan automáticamente cuando se produzcan excepciones en aplicaciones de producción de .NET"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depuración de las instantáneas cuando se produzcan excepciones en aplicaciones de .NET

*Esta característica se encuentra en versión preliminar.*

Recopile automáticamente una instantánea de depuración desde la aplicación web en vivo cuando se produce una excepción. La instantánea muestra el estado del código fuente y las variables el momento en que se produjo la excepción. El Depurador de instantáneas de [Application Insights](app-insights-overview.md) supervisa la telemetría de excepción de su aplicación web. Se encarga de obtener instantáneas de las excepciones más importantes con el fin de que tenga la información necesaria para diagnosticar problemas en producción. Incluya el [paquete de NuGet del recopilador de instantáneas](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación y, opcionalmente, configure los parámetros de recopilación en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Las instantáneas aparecen en [excepciones](app-insights-asp-net-exceptions.md) en el portal de Application Insights.

Puede ver las instantáneas de depuración en el portal para examinar la pila de llamadas e inspeccionar las variables en cada marco de pila de llamadas. Para obtener una experiencia de depuración más eficaz con el código fuente, abra Instantáneas con Visual Studio Enterprise de 2017 [descargando la extensión del Depurador de instantáneas para Visual Studio](https://aka.ms/snapshotdebugger).

La recopilación de instantáneas está disponible para las aplicaciones web de ASP.NET que se ejecuta en .NET Framework 4.6 y versiones posteriores, y está hospedado en IIS en Azure Compute o en Azure App Service.

## <a name="configure-snapshot-collection"></a>Configuración de la recopilación de instantáneas

1. Si aún no lo ha hecho, [habilite Application Insights en su aplicación web](app-insights-asp-net.md).

2. Incluya el paquete de NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación. 

3. Revise las opciones predeterminadas que el paquete ha agregado a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
      <IsEnabled>true</IsEnabled>
      <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
      <!-- DeveloperMode is a property on the active TelemetryChannel. -->
      <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
      <!-- How many times we need to see an exception before we ask for snapshots. -->
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

Las instantáneas solo se recopilan en las excepciones que son visibles en el SDK de Application Insights. En algunos casos, puede que necesite [configurar la recopilación de excepciones](app-insights-asp-net-exceptions.md#exceptions) para ver las excepciones con las instantáneas mostrándose en el portal.

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Depuración de instantáneas en el portal de Application Insights

Si una instantánea está disponible para una excepción o un identificador de problema determinados, se mostrará un vínculo *Abrir instantánea de depuración* en la [excepción](app-insights-asp-net-exceptions.md) del portal de Application Insights.

![Botón Abrir instantánea de depuración de la excepción](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

En la vista de depuración instantánea, verá una pila de llamadas y un panel de variables. Al seleccionar los marcos de la pila de llamadas en el panel de la pila de llamadas, podrá ver las variables locales y los parámetros para que se llame a esa función en el panel de variables.

![Visualización de la instantánea de depuración en el portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Las instantáneas pueden contener información confidencial y, de forma predeterminada, no están visibles. Para verlas, debe tener el rol `Application Insights Snapshot Debugger` asignado en el portal para la suscripción o el recurso. Actualmente este rol solo puede asignarlos los propietarios de suscripciones por usuario. En estos momentos, no se puede asignar el rol grupos de Azure Active Directory.

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Depuración de instantáneas con Visual Studio Enterprise 2017
Puede hacer clic en el botón *Descargar instantánea* para descargar un archivo `.diagsession`, que puede abrirse en Visual Studio Enterprise 2017. Para abrir el archivo `.diagsession`, actualmente se requiere que primero [descargar e instalar la extensión del Depurador de instantáneas para Visual Studio](https://aka.ms/snapshotdebugger).

Después de abrir el archivo de instantáneas, se le redirigirá a la página de depuración de minivolcado, donde puede empezar a depurar la instantánea haciendo clic en *Depurar código administrado*. Pasará a la línea de código donde se produjo la excepción y puede depurar el estado actual del proceso.

![Visualización de la instantánea de depuración en Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

La instantánea descargada contiene los archivos de símbolos que se encontraron en el servidor de aplicaciones web. Estos archivos de símbolos son necesarios para asociar los datos de instantáneas con el código fuente. Para las aplicaciones de Azure App Service, asegúrese de habilitar la implementación de símbolos al publicar las aplicaciones web.

## <a name="how-snapshots-work"></a>Funcionamiento de las instantáneas

Cuando se inicia la aplicación, se crea un proceso independiente de carga de instantáneas que supervisa las solicitudes de instantáneas de su aplicación. Cuando se solicita una instantánea, se realiza una instantánea de los procesos en ejecución en aproximadamente 10-20 ms. El proceso de instantáneas, a continuación, analiza y crea una instantánea mientras el proceso principal continúa ejecutándose y sirviendo tráfico a los usuarios. Después, la instantánea se carga en Application Insights junto con los archivos pertinentes símbolos (.pdb) necesarios para ver la instantánea.

## <a name="current-limitations"></a>Limitaciones actuales

### <a name="publishing-symbols"></a>Símbolos de publicación
El Depurador de instantáneas requiere que los archivos de símbolos estén presentes en el servidor de producción para descodificar variables y proporcionar una experiencia de depuración en Visual Studio. La versión 15.2 de Visual Studio 2017 publica símbolos de compilaciones Release de forma predeterminada al publicar en Azure App Service. En las versiones anteriores, debe agregar la siguiente línea al archivo `.pubxml` de su perfil de publicación para que los símbolos se publican en modo de lanzamiento.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para Azure Compute y otros tipos, asegúrese de que los archivos de símbolos están en la misma carpeta del archivo .dll de la aplicación principal (normalmente, `wwwroot/bin`), o que están disponibles en la ruta de acceso actual.

### <a name="optimized-builds"></a>Compilaciones optimizadas
En algunos casos, las variables locales no están visibles en las compilaciones Release debido a las optimizaciones que se aplican durante el proceso de compilación. Esta limitación se corregirá en una versión futura del paquete de NuGet.

## <a name="next-steps"></a>Pasos siguientes

* En el artículo sobre cómo [diagnosticar excepciones en aplicaciones web](app-insights-asp-net-exceptions.md) se explica cómo hacer más visibles las excepciones en Application Insights. 
* [Detección inteligente](app-insights-proactive-diagnostics.md) detecta automáticamente las anomalías de rendimiento.

