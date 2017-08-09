---
title: "Depurador de instantáneas de Azure Application Insights para aplicaciones de .NET | Microsoft Docs"
description: "Depuración de las instantáneas que se recopilan automáticamente cuando se producen excepciones en aplicaciones de producción de .NET"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: cfreeman
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: dcc5cc0be4c03ad661cf1539cb98a7d4fc94e778
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantáneas cuando se producen excepciones en aplicaciones de .NET

Cuando se produce una excepción, puede recopilar automáticamente una instantánea de depuración desde la aplicación web activa. La instantánea muestra el estado del código fuente y las variables en el momento en que se produjo la excepción. El depurador de instantáneas (versión preliminar) de [Azure Application Insights](app-insights-overview.md) supervisa la telemetría de excepciones de su aplicación web. Recopila instantáneas de las excepciones más importantes con el fin de que tenga la información necesaria para diagnosticar problemas en producción. Incluya el [paquete NuGet del recopilador de instantáneas](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación y, opcionalmente, configure los parámetros de recopilación en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Las instantáneas aparecen en [excepciones](app-insights-asp-net-exceptions.md) en el portal de Application Insights.

Puede ver las instantáneas de depuración en el portal para examinar la pila de llamadas e inspeccionar las variables en cada marco de pila de llamadas. Para obtener una experiencia de depuración más eficaz con el código fuente, abra las instantáneas con Visual Studio Enterprise de 2017 [descargando la extensión del depurador de instantáneas para Visual Studio](https://aka.ms/snapshotdebugger).

La recopilación de instantáneas está disponible para:
* Aplicaciones de .NET Framework y ASP.NET que ejecuten .NET Framework 4.5 o posterior.
* Aplicaciones de .NET Core 2.0 y ASP.NET Core 2.0 que se ejecuten en Windows.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar la recopilación de instantáneas para aplicaciones ASP.NET

1. [Habilite Application Insights en su aplicación web](app-insights-asp-net.md), si aún no lo ha hecho.

2. Incluya el paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación. 

3. Revise las opciones predeterminadas que el paquete ha agregado a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

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

4. Las instantáneas solo se recopilan en las excepciones de las que se informa a Application Insights. En algunos casos (por ejemplo, en versiones anteriores de la plataforma. NET), es posible que tenga que [configurar la recopilación de excepciones](app-insights-asp-net-exceptions.md#exceptions) para ver las excepciones con las instantáneas en el portal.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar la recopilación de instantáneas para aplicaciones ASP.NET Core 2.0

1. [Habilite Application Insights en su aplicación web ASP.NET Core](app-insights-asp-net-core.md), si aún no lo ha hecho.

2. Incluya el paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación.

3. Modifique el método `ConfigureServices` de la clase `Startup` de la aplicación para agregar el procesador de telemetría del recopilador de instantáneas. El código que debe agregar depende de la versión del paquete NuGet Microsoft.ApplicationInsights.ASPNETCore a la que se hace referencia.

   Para Microsoft.ApplicationInsights.AspNetCore 2.1.0, agregue:
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

   Para Microsoft.ApplicationInsights.AspNetCore 2.1.1, agregue:
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           public ITelemetryProcessor Create(ITelemetryProcessor next) =>
               new SnapshotCollectorTelemetryProcessor(next);
       }

       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
            services.AddSingleton<ITelemetryProcessorFactory>(new SnapshotCollectorTelemetryProcessorFactory());
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar la recopilación de instantáneas para otras aplicaciones .NET

1. Si la aplicación aún no tiene Application Insights, debe empezar por [habilitar Application Insights y establecer la clave de instrumentación](app-insights-windows-desktop.md).

2. Agregue el paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación.

3. Las instantáneas solo se recopilan en las excepciones de las que se informa a Application Insights. Es posible que tenga que modificar el código para informar de las excepciones. El código de control de excepciones depende de la estructura de la aplicación, pero a continuación se muestra un ejemplo:
   ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }

## Grant permissions

Owners of the Azure subscription can inspect snapshots. Other users must be granted permission by an owner.

To grant permission, assign the `Application Insights Snapshot Debugger` role to users who will inspect snapshots. This role can be assigned to individual users or groups by subscription owners for the target Application Insights resource or its resource group or subscription.

1. Open the Access Control (IAM) blade.
1. Click the +Add button.
1. Select Application Insights Snapshot Debugger from the Roles drop-down list.
1. Search for and enter a name for the user to add.
1. Click the Save button to add the user to the role.


[!IMPORTANT]
    Snapshots can potentially contain personal and other sensitive information in variable and parameter values.

## Debug snapshots in the Application Insights portal

If a snapshot is available for a given exception or a problem ID, an **Open Debug Snapshot** button appears on the [exception](app-insights-asp-net-exceptions.md) in the Application Insights portal.

![Open Debug Snapshot button on exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In the Debug Snapshot view, you see a call stack and a variables pane. When you select frames of the call stack in the call stack pane, you can view local variables and parameters for that function call in the variables pane.

![View Debug Snapshot in the portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snapshots might contain sensitive information, and by default they are not viewable. To view snapshots, you must have the `Application Insights Snapshot Debugger` role assigned to you.

## Debug snapshots with Visual Studio 2017 Enterprise
1. Click the **Download Snapshot** button to download a `.diagsession` file, which can be opened by Visual Studio 2017 Enterprise. 

2. To open the `.diagsession` file, you must first [download and install the Snapshot Debugger extension for Visual Studio](https://aka.ms/snapshotdebugger).

3. After you open the snapshot file, the Minidump Debugging page in Visual Studio appears. Click **Debug Managed Code** to start debugging the snapshot. The snapshot opens to the line of code where the exception was thrown so that you can debug the current state of the process.

    ![View debug snapshot in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

The downloaded snapshot contains any symbol files that were found on your web application server. These symbol files are required to associate snapshot data with source code. For App Service apps, make sure to enable symbol deployment when you publish your web apps.

## How snapshots work

When your application starts, a separate snapshot uploader process is created that monitors your application for snapshot requests. When a snapshot is requested, a shadow copy of the running process is made in about 10 to 20 minutes. The shadow process is then analyzed, and a snapshot is created while the main process continues to run and serve traffic to users. The snapshot is then uploaded to Application Insights along with any relevant symbol (.pdb) files that are needed to view the snapshot.

## Current limitations

### Publish symbols
The Snapshot Debugger requires symbol files on the production server to decode variables and to provide a debugging experience in Visual Studio. The 15.2 release of Visual Studio 2017 publishes symbols for release builds by default when it publishes to App Service. In prior versions, you need to add the following line to your publish profile `.pubxml` file so that symbols are published in release mode:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para Azure Compute y otros tipos, asegúrese de que los archivos de símbolos están en la misma carpeta del archivo .dll principal de la aplicación (normalmente, `wwwroot/bin`), o que están disponibles en la ruta de acceso actual.

### <a name="optimized-builds"></a>Compilaciones optimizadas
En algunos casos, las variables locales no se pueden ver en las compilaciones de versión debido a las optimizaciones que se aplican durante el proceso de compilación.

## <a name="troubleshooting"></a>Solución de problemas

Estas sugerencias le ayudarán a solucionar problemas con el depurador de instantáneas.

### <a name="verify-the-instrumentation-key"></a>Comprobar la clave de instrumentación

Asegúrese de que está usando la clave de instrumentación correcta en la aplicación publicada. Por lo general, Application Insights lee la clave de instrumentación desde el archivo ApplicationInsights.config. Compruebe que el valor es igual que la clave de instrumentación para el recurso de Application Insights que ve en el portal.

### <a name="check-the-uploader-logs"></a>Comprobar los registros de usuario de carga

Después de crear una instantánea, se crea un archivo de minivolcado (.dmp) en el disco. Un proceso de usuario de carga independiente toma ese archivo de minivolcado y lo carga, junto con los archivos PDB asociados, al almacenamiento del depurador de instantáneas de Application Insights. Después de cargar correctamente el minivolcado, se elimina del disco. Los archivos de registro para el usuario de carga del minivolcado se conservan en disco. En un entorno de App Service, puede encontrar estos registros en `D:\Home\LogFiles\Uploader_*.log`. Use el sitio de administración de Kudu para App Service para buscar estos archivos de registro.

1. Abra la aplicación App Service en Azure Portal.

2. Seleccione la hoja **Herramientas avanzadas** o busque **Kudu**.
3. Haga clic en **Ir**.
4. En el cuadro de lista desplegable **Consola de depuración**, seleccione **CMD**.
5. Haga clic en **LogFiles**.

Debería ver al menos un archivo con un nombre que comienza con `Uploader_` y una extensión `.log`. Haga clic en el icono adecuado para descargar los archivos de registro o abrirlos en un explorador.
El nombre de archivo incluye el nombre del equipo. Si la instancia de App Service se hospeda en más de un equipo, hay archivos de registro independientes para cada equipo. Cuando el usuario de carga detecta un nuevo archivo de minivolcado, se registra en el archivo de registro. Este es un ejemplo de carga correcta:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

En el ejemplo anterior, la clave de instrumentación es `c12a605e73c44346a984e00000000000`. Este valor debe coincidir con la clave de instrumentación de la aplicación.
El minivolcado está asociado a una instantánea con el identificador `139e411a23934dc0b9ea08a626db16c5`. Puede usar este identificador más adelante para buscar la telemetría de excepciones asociada en Application Insights Analytics.

El usuario de carga busca nuevos archivos PDB una vez cada 15 minutos. Este es un ejemplo:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Para las aplicaciones que _no_ están hospedadas en App Service, los registros de usuario de carga están en la misma carpeta que los minivolcados: `%TEMP%\Dumps\<ikey>` (donde `<ikey>` es la clave de instrumentación).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usar la búsqueda de Application Insights para buscar excepciones con instantáneas

Cuando se crea una instantánea, la excepción iniciada se etiqueta con un identificador de instantánea. Cuando se notifica la telemetría de excepciones a Application Insights, ese identificador de instantánea se incluye como una propiedad personalizada. Mediante la hoja de búsqueda de Application Insights, puede buscar toda la telemetría con la propiedad personalizada `ai.snapshot.id`.

1. Vaya al recurso de Application Insights en Azure Portal.
2. Haga clic en **Buscar**.
3. En el cuadro de texto Buscar, escriba `ai.snapshot.id` y presione Entrar.

![Buscar telemetría con un identificador de instantánea en el portal](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Si esta búsqueda no devuelve ningún resultado, no se ha informado de ninguna instantánea a Application Insights para la aplicación en el intervalo de tiempo seleccionado.

Para buscar un identificador de instantánea específico en los registros de usuario de carga, escriba ese identificador en el cuadro Buscar. Si no se encuentra la telemetría para una instantánea que sabe que se cargó, siga estos pasos:

1. Compruebe que está viendo el recurso de Application Insights correcto comprobando la clave de instrumentación.

2. Mediante la marca de tiempo del registro del usuario de carga, ajuste el filtro de intervalo de tiempo de la búsqueda para cubrir ese intervalo de tiempo.

Si sigue sin ver una excepción con ese identificador de instantánea, significa que la excepción de telemetría no se ha notificado a Application Insights. Esta situación puede ocurrir si se bloqueó la aplicación después de que tomó la instantánea, pero antes de notificar la telemetría de excepción. En este caso, compruebe los registros de App Service en `Diagnose and solve problems` para ver si hay reinicios inesperados o excepciones no controladas.

## <a name="next-steps"></a>Pasos siguientes

* [Establezca puntos de ajuste en el código](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/) para obtener instantáneas sin tener que esperar una excepción.
* En el artículo sobre cómo [diagnosticar excepciones en aplicaciones web](app-insights-asp-net-exceptions.md) se explica cómo hacer más visibles las excepciones en Application Insights. 
* [Detección inteligente](app-insights-proactive-diagnostics.md) detecta automáticamente las anomalías de rendimiento.

