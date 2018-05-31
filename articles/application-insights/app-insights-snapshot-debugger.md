---
title: Depurador de instantáneas de Azure Application Insights para aplicaciones de .NET | Microsoft Docs
description: Depuración de las instantáneas que se recopilan automáticamente cuando se producen excepciones en aplicaciones de producción de .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mbullwin; pharring
ms.openlocfilehash: 66339e5f5d2cc7447df0f8faf70d2d9fd45db738
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159142"
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
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
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

    Agregue las siguientes instrucciones using a `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Agregue la siguiente clase `SnapshotCollectorTelemetryProcessorFactory` a la clase `Startup`.

   ```csharp
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
       ...
    ```
    Agregue los servicios `SnapshotCollectorConfiguration` y `SnapshotCollectorTelemetryProcessorFactory` a la canalización de inicio:

    ```csharp
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
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
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

1. Vaya al recurso de Application Insights en Azure Portal.
1. Haga clic en **Access Control (IAM)**.
1. Haga clic en el botón **+Agregar**.
1. Seleccione **Application Insights Snapshot Debugger** en la lista desplegable **Roles**.
1. Busque el usuario que quiere agregar y escriba un nombre.
1. Haga clic en el botón **Guardar** para agregar el usuario al rol.


> [!IMPORTANT]
> Las instantáneas pueden contener información personal y otra información confidencial en valores de variables y parámetros.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Depuración de instantáneas en el portal de Application Insights

Si una instantánea está disponible para una excepción o un identificador de problema determinados, se mostrará un botón **Abrir instantánea de depuración** en la [excepción](app-insights-asp-net-exceptions.md) del portal de Application Insights.

![Botón Abrir instantánea de depuración de la excepción](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

En la vista de depuración instantánea, verá una pila de llamadas y un panel de variables. Al seleccionar marcos de la pila de llamadas en el panel de la pila de llamadas, podrá ver las variables locales y los parámetros para esa llamada de función en el panel de variables.

![Visualización de la instantánea de depuración en el portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Las instantáneas pueden incluir información confidencial y, de manera predeterminada, no están visibles. Para ver las instantáneas, debe tener asignado el rol `Application Insights Snapshot Debugger`.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Depuración de instantáneas con Visual Studio Enterprise 2017
1. Haga clic en el botón **Descargar instantánea** para descargar un archivo `.diagsession`, que puede abrirse en Visual Studio Enterprise 2017.

2. Para abrir el archivo `.diagsession`, debe primero [descargar e instalar la extensión del Depurador de instantáneas para Visual Studio](https://aka.ms/snapshotdebugger).

3. Después de abrir el archivo de instantánea, aparece la página de depuración de minivolcado de Visual Studio. Haga clic en **Depurar código administrado** para empezar a depurar la instantánea. La instantánea se abre en la línea de código donde se produjo la excepción para que pueda depurar el estado actual del proceso.

    ![Visualización de la instantánea de depuración en Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

La instantánea descargada incluye los archivos de símbolos que se encontraron en el servidor de aplicaciones web. Estos archivos de símbolos son necesarios para asociar los datos de instantáneas con el código fuente. Para las aplicaciones de App Service, asegúrese de habilitar la implementación de símbolos cuando publique las aplicaciones web.

## <a name="how-snapshots-work"></a>Funcionamiento de las instantáneas

Snapshot Collector se implementa como un [procesador de telemetría de Application Insights](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Al ejecutar la aplicación, el procesador de telemetría de Snapshot Collector se agrega a la canalización de telemetría de la aplicación.
Cada vez que la aplicación llama a [TrackException](app-insights-asp-net-exceptions.md#exceptions), Snapshot Collector calcula un identificador del problema del tipo de excepción producida y el método de lanzamiento.
Cada vez que la aplicación llama a TrackException, se incrementa un contador para el identificador del problema adecuado. Cuando el contador alcanza el valor `ThresholdForSnapshotting`, el identificador del problema se agrega a un plan de recolección.

Snapshot Collector también supervisa las excepciones a medida que se producen si se suscribe al evento [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). Cuando ese evento se desencadena, el identificador del problema de la excepción se calcula y se compara con los identificadores del problema del plan de recolección.
Si se encuentra una coincidencia, se crea una instantánea del proceso en ejecución. Se asigna un identificador único a la instantánea y la excepción se marca con ese identificador. Tras la devolución del controlador de FirstChanceException, la excepción producida se procesa con normalidad. Finalmente, la excepción alcanza el método TrackException de nuevo que, junto con el identificador de instantáneas, se notifica a Application Insights.

El proceso principal sigue ejecutándose y ofrece tráfico a los usuarios con poca interrupción. Mientras tanto, la instantánea se entrega al proceso del cargador de instantáneas. El cargador de instantáneas crea un minivolcado y lo carga en Application Insights junto con los archivos de símbolos (.pdb) pertinentes.

> [!TIP]
> - Una instantánea de proceso es un clon suspendido del proceso en ejecución.
> - La creación de la instantánea tarda aproximadamente de 10 a 20 milisegundos.
> - El valor predeterminado de `ThresholdForSnapshotting` es 1. También es el valor mínimo. Por lo tanto, la aplicación debe desencadenar la misma excepción **dos veces** antes de crear una instantánea.
> - Establezca `IsEnabledInDeveloperMode` en true si quiere generar instantáneas durante la depuración en Visual Studio.
> - La velocidad de creación de instantáneas está limitada por la configuración de `SnapshotsPerTenMinutesLimit`. De manera predeterminada, el límite es una instantánea cada diez minutos.
> - No se pueden cargar más de 50 instantáneas al día.

## <a name="current-limitations"></a>Limitaciones actuales

### <a name="publish-symbols"></a>Publicación de símbolos
El Depurador de instantáneas requiere que los archivos de símbolos estén presentes en el servidor de producción para descodificar variables y proporcionar una experiencia de depuración en Visual Studio. La versión 15.2 de Visual Studio 2017 publica símbolos de compilaciones de versión de forma predeterminada al publicar en App Service. En las versiones anteriores, tiene que agregar la siguiente línea al archivo `.pubxml` de su perfil de publicación para que los símbolos se publiquen en modo de versión:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para Azure Compute y otros tipos, asegúrese de que los archivos de símbolos están en la misma carpeta del archivo .dll principal de la aplicación (normalmente, `wwwroot/bin`), o que están disponibles en la ruta de acceso actual.

### <a name="optimized-builds"></a>Compilaciones optimizadas
En algunos casos, las variables locales no se pueden ver en las compilaciones de versión debido a las optimizaciones que aplica el compilador JIT.
Sin embargo, en Azure App Services, Snapshot Collector puede invalidar la optimización de los métodos de lanzamiento que forman parte del plan de recolección.

> [!TIP]
> Instale la extensión de sitio de Application Insights en su App Service para obtener soporte técnico para la invalidación de optimizaciones.

## <a name="troubleshooting"></a>solución de problemas

Estas sugerencias le ayudarán a solucionar problemas con el depurador de instantáneas.

### <a name="use-the-snapshot-health-check"></a>Uso de la comprobación de estado de instantáneas
Algunos problemas comunes provocan que no se muestre la opción Abrir instantánea de depuración. Por ejemplo, el uso de una instancia de Snapshot Collector no actualizada, el hecho de alcanzar el límite diario de carga o, quizás, la tardanza de la instantánea en cargarse. Use la comprobación de estado de instantáneas para solucionar problemas comunes.

Hay un vínculo en el panel de excepción de la vista de seguimiento de un extremo a otro que le lleva a la comprobación de estado de instantáneas.

![Acceso a la comprobación de estado de instantáneas](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

La interfaz interactiva, similar a un chat, busca problemas comunes y le guía para corregirlos.

![Comprobación de estado](./media/app-insights-snapshot-debugger/healthcheck.png)

Si no se soluciona el problema, consulte los siguientes pasos de solución de problemas de forma manual.

### <a name="verify-the-instrumentation-key"></a>Comprobar la clave de instrumentación

Asegúrese de que está usando la clave de instrumentación correcta en la aplicación publicada. Por lo general, la clave de instrumentación se lee desde el archivo ApplicationInsights.config. Compruebe que el valor es igual que la clave de instrumentación para el recurso de Application Insights que ve en el portal.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Actualización a la versión más reciente del paquete NuGet

Use el administrador de paquetes de NuGet de Visual Studio para asegurarse de que usa la versión más reciente de Microsoft.ApplicationInsights.SnapshotCollector. Puede encontrar notas de la versión en https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Comprobar los registros de usuario de carga

Después de crear una instantánea, se crea un archivo de minivolcado (.dmp) en el disco. Un proceso de usuario de carga independiente crea ese archivo de minivolcado y lo carga, junto con los archivos PDB asociados, en el almacenamiento de Snapshot Debugger de Application Insights. Después de cargar correctamente el minivolcado, se elimina del disco. Los archivos de registro del proceso de usuario de carga se conservan en el disco. En un entorno de App Service, puede encontrar estos registros en `D:\Home\LogFiles`. Use el sitio de administración de Kudu para App Service para buscar estos archivos de registro.

1. Abra la aplicación App Service en Azure Portal.
2. Haga clic en **Herramientas avanzadas** o busque **Kudu**.
3. Haga clic en **Ir**.
4. En el cuadro de lista desplegable **Consola de depuración**, seleccione **CMD**.
5. Haga clic en **LogFiles**.

Debería ver al menos un archivo con un nombre que comienza con `Uploader_` o `SnapshotUploader_` y una extensión `.log`. Haga clic en el icono adecuado para descargar los archivos de registro o abrirlos en un explorador.
El nombre de archivo incluye un sufijo único que identifica la instancia de App Service. Si la instancia de App Service se hospeda en más de un equipo, hay archivos de registro independientes para cada equipo. Cuando el usuario de carga detecta un nuevo archivo de minivolcado, se registra en el archivo de registro. Este es un ejemplo de una instantánea y una carga correctas:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> El ejemplo anterior es de la versión 1.2.0 del paquete NuGet Microsoft.ApplicationInsights.SnapshotCollector. En versiones anteriores, el proceso de usuario de carga se llama `MinidumpUploader.exe` y el registro está menos detallado.

En el ejemplo anterior, la clave de instrumentación es `c12a605e73c44346a984e00000000000`. Este valor debe coincidir con la clave de instrumentación de la aplicación.
El minivolcado está asociado a una instantánea con el identificador `139e411a23934dc0b9ea08a626db16c5`. Puede usar este identificador más adelante para buscar la telemetría de excepciones asociada en Application Insights Analytics.

El usuario de carga busca nuevos archivos PDB una vez cada 15 minutos. Este es un ejemplo:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Para las aplicaciones que _no_ están hospedadas en App Service, los registros de usuario de carga están en la misma carpeta que los minivolcados: `%TEMP%\Dumps\<ikey>` (donde `<ikey>` es la clave de instrumentación).

### <a name="troubleshooting-cloud-services"></a>Solución de problemas de Cloud Services
Para los roles de Cloud Services, la carpeta temporal predeterminada puede ser demasiado pequeña para contener los archivos de minivolcado, dando lugar a pérdida de instantáneas.
El espacio necesario depende del espacio de trabajo total de la aplicación y el número de instantáneas simultáneas.
El espacio de trabajo de un rol web ASP.NET de 32 bits está normalmente entre 200 y 500 MB.
Debe disponer del espacio necesario para al menos dos instantáneas simultáneas.
Por ejemplo, si la aplicación usa 1 GB de espacio de trabajo total, debe asegurarse de que hay al menos 2 GB de espacio en disco para almacenar las instantáneas.
Siga estos pasos para configurar el rol del servicio en la nube con un recurso local dedicado para las instantáneas.

1. Agregue un nuevo recurso local a su servicio en la nube editando el archivo de definición (.csdef) del servicio en la nube. En el ejemplo siguiente se define un recurso llamado `SnapshotStore` con un tamaño de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modifique el código de inicio del rol para agregar una variable de entorno que apunte al recurso local `SnapshotStore`. En roles de trabajo, el código se debe agregar al método `OnStart` del rol:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   En roles web (ASP.NET), el código se debe agregar al método `Application_Start` de la aplicación web:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
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

Cuando se crea una instantánea, la excepción iniciada se etiqueta con un identificador de instantánea. Ese identificador de instantánea se incluye como una propiedad personalizada cuando se notifica la telemetría de excepciones a Application Insights. Mediante la **búsqueda** de Application Insights, puede buscar toda la telemetría con la propiedad personalizada `ai.snapshot.id`.

1. Vaya al recurso de Application Insights en Azure Portal.
2. Haga clic en **Buscar**.
3. En el cuadro de texto Buscar, escriba `ai.snapshot.id` y presione Entrar.

![Buscar telemetría con un identificador de instantánea en el portal](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Si esta búsqueda no devuelve ningún resultado, no se ha informado de ninguna instantánea a Application Insights para la aplicación en el intervalo de tiempo seleccionado.

Para buscar un identificador de instantánea específico en los registros de usuario de carga, escriba ese identificador en el cuadro Buscar. Si no se encuentra la telemetría para una instantánea que sabe que se cargó, siga estos pasos:

1. Compruebe que está viendo el recurso de Application Insights correcto comprobando la clave de instrumentación.

2. Mediante la marca de tiempo del registro del usuario de carga, ajuste el filtro de intervalo de tiempo de la búsqueda para cubrir ese intervalo de tiempo.

Si sigue sin ver una excepción con ese identificador de instantánea, significa que la excepción de telemetría no se ha notificado a Application Insights. Esta situación puede ocurrir si se bloqueó la aplicación después de que tomó la instantánea, pero antes de notificar la telemetría de excepción. En este caso, compruebe los registros de App Service en `Diagnose and solve problems` para ver si hay reinicios inesperados o excepciones no controladas.

### <a name="edit-network-proxy-or-firewall-rules"></a>Edición de reglas de firewall o proxy de red

Si la aplicación se conecta a Internet a través de un proxy o un firewall, es posible que tenga que editar las reglas para permitir que la aplicación se comunique con el servicio de Snapshot Debugger. A continuación, se incluye [una lista de puertos y direcciones IP que usa Snapshot Debugger](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Pasos siguientes

* [Establezca puntos de ajuste en el código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obtener instantáneas sin tener que esperar una excepción.
* En el artículo sobre cómo [diagnosticar excepciones en aplicaciones web](app-insights-asp-net-exceptions.md) se explica cómo hacer más visibles las excepciones en Application Insights.
* [Detección inteligente](app-insights-proactive-diagnostics.md) detecta automáticamente las anomalías de rendimiento.
