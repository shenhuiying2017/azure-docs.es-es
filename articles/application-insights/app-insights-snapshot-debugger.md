---
title: "Depurador de instantáneas de Azure Application Insights para aplicaciones de .NET | Microsoft Docs"
description: "Depuración de las instantáneas que se recopilan automáticamente cuando se producen excepciones en aplicaciones de producción de .NET"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 8d6f2347e06e58ec2b506aa9eaf716b3f71f3a77
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantáneas cuando se producen excepciones en aplicaciones de .NET

Cuando se produce una excepción, puede recopilar automáticamente una instantánea de depuración desde la aplicación web activa. La instantánea muestra el estado del código fuente y las variables en el momento en que se produjo la excepción. El depurador de instantáneas (versión preliminar) de [Azure Application Insights](app-insights-overview.md) supervisa la telemetría de excepciones de su aplicación web. Recopila instantáneas de las excepciones más importantes con el fin de que tenga la información necesaria para diagnosticar problemas en producción. Incluya el [paquete NuGet del recopilador de instantáneas](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación y, opcionalmente, configure los parámetros de recopilación en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Las instantáneas aparecen en [excepciones](app-insights-asp-net-exceptions.md) en el portal de Application Insights.

Puede ver las instantáneas de depuración en el portal para examinar la pila de llamadas e inspeccionar las variables en cada marco de pila de llamadas. Para obtener una experiencia de depuración más eficaz con el código fuente, abra las instantáneas con Visual Studio Enterprise de 2017 [descargando la extensión del depurador de instantáneas para Visual Studio](https://aka.ms/snapshotdebugger). En Visual Studio también puede [establecer puntos de acoplamiento para tomar instantáneas de forma interactiva](https://aka.ms/snappoint) sin tener que esperar una excepción.

La recopilación de instantáneas está disponible para:
* Aplicaciones de .NET Framework y ASP.NET que ejecuten .NET Framework 4.5 o posterior.
* Aplicaciones de .NET Core 2.0 y ASP.NET Core 2.0 que se ejecuten en Windows.

Se admiten los siguientes entornos:
* Azure App Service.
* Servicio en la nube de Azure con la familia del SO 4 o posterior.
* Servicios de Azure Service Fabric con Windows Server 2012 R2 o posterior.
* Azure Virtual Machines con Windows Server 2012 R2 o posterior.
* Máquinas virtuales o físicas locales con Windows Server 2012 R2 o posterior.

> [!NOTE]
> No se admiten las aplicaciones cliente (por ejemplo, WPF, Windows Forms o UWP).

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
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Las instantáneas solo se recopilan en las excepciones de las que se informa a Application Insights. En algunos casos (por ejemplo, en versiones anteriores de la plataforma. NET), es posible que tenga que [configurar la recopilación de excepciones](app-insights-asp-net-exceptions.md#exceptions) para ver las excepciones con las instantáneas en el portal.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar la recopilación de instantáneas para aplicaciones ASP.NET Core 2.0

1. [Habilite Application Insights en su aplicación web ASP.NET Core](app-insights-asp-net-core.md), si aún no lo ha hecho.

    > [!NOTE]
    > Procure que la aplicación haga referencia a la versión 2.1.1 (o una más reciente) del paquete Microsoft.ApplicationInsights.AspNetCore.

2. Incluya el paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación.

3. Modifique la clase `Startup` de la aplicación para agregar y configurar el procesador de telemetría del recopilador de instantáneas.

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Configure el recopilador de instantáneas mediante la adición de una sección de SnapshotCollectorConfiguration en appsettings.json. Por ejemplo: 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar la recopilación de instantáneas para otras aplicaciones .NET

1. Si la aplicación aún no tiene Application Insights, debe empezar por [habilitar Application Insights y establecer la clave de instrumentación](app-insights-windows-desktop.md).

2. Agregue el paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) en la aplicación.

3. Las instantáneas solo se recopilan en las excepciones de las que se informa a Application Insights. Es posible que tenga que modificar el código para informar de las excepciones. El código de control de excepciones depende de la estructura de la aplicación, pero a continuación se muestra un ejemplo:
    ```csharp
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
    ```
    
## <a name="grant-permissions"></a>Concesión de permisos

Los propietarios de la suscripción de Azure pueden inspeccionar instantáneas. Otros usuarios deben obtener permiso de un propietario.

Para conceder permiso, asigne el rol `Application Insights Snapshot Debugger` a los usuarios que inspeccionarán instantáneas. Los propietarios de suscripción pueden asignar este rol a usuarios individuales o grupos en el recurso de Application Insights de destino o en su grupo de recursos o suscripción.

1. Abra la hoja Access Control (IAM).
1. Haga clic en el botón +Agregar.
1. Seleccione Depurador de instantáneas de Application Insights en la lista desplegable Roles.
1. Busque el usuario que quiere agregar y escriba un nombre.
1. Haga clic en el botón Guardar para agregar el usuario al rol.


> [!IMPORTANT]
> Las instantáneas pueden contener información personal y otra información confidencial en valores de variables y parámetros.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Depuración de instantáneas en el portal de Application Insights

Si una instantánea está disponible para una excepción o un identificador de problema determinados, se mostrará un botón **Abrir instantánea de depuración** en la [excepción](app-insights-asp-net-exceptions.md) del portal de Application Insights.

![Botón Abrir instantánea de depuración de la excepción](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

En la vista de depuración instantánea, verá una pila de llamadas y un panel de variables. Al seleccionar marcos de la pila de llamadas en el panel de la pila de llamadas, podrá ver las variables locales y los parámetros para esa llamada de función en el panel de variables.

![Visualización de la instantánea de depuración en el portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Las instantáneas pueden contener información confidencial y, de forma predeterminada, no están visibles. Para ver las instantáneas, debe tener asignado el rol `Application Insights Snapshot Debugger`.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Depuración de instantáneas con Visual Studio Enterprise 2017
1. Haga clic en el botón **Descargar instantánea** para descargar un archivo `.diagsession`, que puede abrirse en Visual Studio Enterprise 2017. 

2. Para abrir el archivo `.diagsession`, debe primero [descargar e instalar la extensión del Depurador de instantáneas para Visual Studio](https://aka.ms/snapshotdebugger).

3. Después de abrir el archivo de instantánea, aparece la página de depuración de minivolcado de Visual Studio. Haga clic en **Depurar código administrado** para empezar a depurar la instantánea. La instantánea se abre en la línea de código donde se produjo la excepción para que pueda depurar el estado actual del proceso.

    ![Visualización de la instantánea de depuración en Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

La instantánea descargada contiene los archivos de símbolos que se encontraron en el servidor de aplicaciones web. Estos archivos de símbolos son necesarios para asociar los datos de instantáneas con el código fuente. Para las aplicaciones de App Service, asegúrese de habilitar la implementación de símbolos cuando publique las aplicaciones web.

## <a name="how-snapshots-work"></a>Funcionamiento de las instantáneas

Cuando se inicia la aplicación, se crea un proceso independiente de usuario de carga de instantáneas que supervisa las solicitudes de instantáneas de la aplicación. Cuando se solicita una instantánea, se realiza una instantánea de los procesos en ejecución en unos 10-20 milisegundos. El proceso de instantánea se analiza y se crea una instantánea mientras el proceso principal sigue ejecutándose y entregando el tráfico a los usuarios. Luego, la instantánea se carga en Application Insights junto con los correspondientes archivos de símbolos (.pdb) que sean necesarios para ver la instantánea.

## <a name="current-limitations"></a>Limitaciones actuales

### <a name="publish-symbols"></a>Publicación de símbolos
El Depurador de instantáneas requiere que los archivos de símbolos estén presentes en el servidor de producción para descodificar variables y proporcionar una experiencia de depuración en Visual Studio. La versión 15.2 de Visual Studio 2017 publica símbolos de compilaciones de versión de forma predeterminada al publicar en App Service. En las versiones anteriores, tiene que agregar la siguiente línea al archivo `.pubxml` de su perfil de publicación para que los símbolos se publiquen en modo de versión:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para Azure Compute y otros tipos, asegúrese de que los archivos de símbolos están en la misma carpeta del archivo .dll principal de la aplicación (normalmente, `wwwroot/bin`), o que están disponibles en la ruta de acceso actual.

### <a name="optimized-builds"></a>Compilaciones optimizadas
En algunos casos, las variables locales no se pueden ver en las compilaciones de versión debido a las optimizaciones que se aplican durante el proceso de compilación.

## <a name="troubleshooting"></a>solución de problemas

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

### <a name="troubleshooting-cloud-services"></a>Solución de problemas de Cloud Services
Para los roles de Cloud Services, la carpeta temporal predeterminada puede ser demasiado pequeña para contener los archivos de minivolcado, dando lugar a pérdida de instantáneas.
El espacio necesario depende del espacio de trabajo total de la aplicación y el número de instantáneas simultáneas.
El espacio de trabajo de un rol web ASP.NET de 32 bits está normalmente entre 200 y 500 MB.
Debe disponer del espacio necesario para al menos dos instantáneas simultáneas.
Por ejemplo, si la aplicación usa 1 GB de espacio de trabajo total, debe asegurarse de que hay al menos 2 GB de espacio en disco para almacenar las instantáneas.
Siga estos pasos para configurar el rol del servicio en la nube con un recurso local dedicado para las instantáneas.

1. Agregue un nuevo recurso local a su servicio en la nube editando el archivo de definición (.csdf) del servicio en la nube. En el ejemplo siguiente se define un recurso llamado `SnapshotStore` con un tamaño de 5 GB.
```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
```

2. Modifique el método `OnStart` del rol para agregar una variable de entorno que apunte al recurso local `SnapshotStore`.
```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
```

3. Actualice el archivo ApplicationInsights.config del rol para reemplazar la ubicación de la carpeta temporal utilizada por `SnapshotCollector`
```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
  </TelemetryProcessors>
```

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

## <a name="next-steps"></a>pasos siguientes

* [Establezca puntos de ajuste en el código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obtener instantáneas sin tener que esperar una excepción.
* En el artículo sobre cómo [diagnosticar excepciones en aplicaciones web](app-insights-asp-net-exceptions.md) se explica cómo hacer más visibles las excepciones en Application Insights. 
* [Detección inteligente](app-insights-proactive-diagnostics.md) detecta automáticamente las anomalías de rendimiento.
