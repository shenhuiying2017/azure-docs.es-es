<properties 
	pageTitle="Configuración del SDK de Application Insights con ApplicationInsights.config o .xml" 
	description="Habilitación o deshabilitación de los módulos de recopilación de datos e incorporación de contadores de rendimiento y otros parámetros" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# Configuración del SDK de Application Insights con ApplicationInsights.config o .xml

El SDK de Application Insights para .NET consta de varios paquetes de NuGet. El [paquete principal](http://www.nuget.org/packages/Microsoft.ApplicationInsights)proporciona la API para enviar telemetría a Application Insights. Los [paquetes adicionales](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) proporcionan _módulos_ e _inicializadores_ de telemetría para hacer un seguimiento automático de la aplicación y su contexto. Si ajusta el archivo de configuración, puede habilitar o deshabilitar los módulos e inicializadores de telemetría, y establecer los parámetros para algunos de ellos.

El archivo de configuración se denomina `ApplicationInsights.config` o `ApplicationInsights.xml`, dependiendo del tipo de la aplicación. Se agrega automáticamente al proyecto cuando se [instalan algunas versiones del SDK][start]. También se agrega a una aplicación web con el [Monitor de estado en un servidor IIS][redfield] o al seleccionar la [extensión de Application Insights para un sitio web o una máquina virtual de Azure][azure].

No hay un archivo equivalente para controlar el [SDK en una página web][client].

Hay un nodo en el archivo de configuración para cada módulo. Para deshabilitar un módulo, elimine el nodo o conviértalo en comentario.

## Paquete de NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)

El paquete de NuGet `Microsoft.ApplicationInsights` proporciona los siguientes módulos de telemetría en el archivo `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
`DiagnosticsTelemetryModule` informa de errores en el propio código de instrumentación de Application Insights. Por ejemplo, si el código no puede tener acceso a los contadores de rendimiento o si un `ITelemetryInitializer` inicia una excepción. La telemetría de seguimiento que sigue este módulo aparece en la [Búsqueda de diagnóstico][diagnostic].

## Paquete de NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

El paquete de NuGet `Microsoft.ApplicationInsights.DependencyCollector` proporciona los módulos de telemetría siguientes en el archivo `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
El `DependencyTrackingTelemetryModule`realiza un seguimiento de telemetría sobre las llamadas a las dependencias externas que realiza la aplicación, como las solicitudes HTTP y las consultas SQL. Para permitir que este módulo funcione en un servidor IIS, deberá [instalar el Monitor de estado][redfield]. Para utilizarlo en aplicaciones web o máquinas virtuales de Azure, [seleccione la extensión Application Insights][azure].

También puede escribir su propio código de seguimiento de dependencias con la [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

## Paquete de NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

El paquete de NuGet `Microsoft.ApplicationInsights.Web` proporciona los siguientes inicializadores y módulos de telemetría en el archivo `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**Inicializadores**

* `SyntheticTelemetryInitializer` actualiza las propiedades de los contextos `User`, `Session` y `Operation` de todos los elementos de telemetría de los que se realiza un seguimiento al tratar una solicitud de un origen sintético, como una prueba de disponibilidad. El portal de Application Insights no muestra la telemetría sintética de forma predeterminada.
* `ClientIpHeaderTelemetryInitializer` actualiza la propiedad `Ip` del contexto `Location` de todos los elementos de telemetría según el encabezado HTTP `X-Forwarded-For` de la solicitud.
* `UserAgentTelemetryInitializer` actualiza la propiedad `UserAgent` del contexto `User` de todos los elementos de telemetría según el encabezado HTTP `User-Agent` de la solicitud.
* `OperationNameTelemetryInitializer` actualiza la propiedad `Name` de la propiedad `RequestTelemetry` y `Name` propiedad del contexto `Operation` de todos los elementos de telemetría según el método HTTP, así como los nombres del controlador MVC de ASP.NET y la acción que se invoca para procesar la solicitud.
* `OperationNameTelemetryInitializer` actualiza 'Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id'.
* `UserTelemetryInitializer` actualiza las propiedades `Id` y `AcquisitionDate` del contexto `User` para todos los elementos de telemetria con valores extraídos de la cookie `ai_user` que genera el código de instrumentación JavaScript de Application Insights que se ejecuta en el explorador del usuario.
* `SessionTelemetryInitializer` actualiza la propiedad `Id` del contexto `Session` para todos los elementos de telemetría con valor extraído de la cookie `ai_session` que genera el código de instrumentación JavaScript de Application Insights que se ejecuta en el explorador del usuario. 
* `AzureRoleEnvironmentTelemetryInitializer` actualiza las propiedades `RoleName` y `RoleInstance` del contexto `Device` para todos los elementos de telemetría con información extraída del entorno de tiempo de ejecución de Azure.
* `DomainNameRoleInstanceTelemetryInitializer` actualiza la propiedad `RoleInstance` del contexto `Device` para todos los elementos de telemetría con el nombre de dominio del equipo donde se ejecuta la aplicación web.
* `BuildInfoConfigComponentVersionTelemetryInitializer` actualiza la propiedad `Version` del contexto `Component` para todos los elementos de telemetría con el valor extraído del archivo `BuildInfo.config` que produce la compilación de TFS.
* `DeviceTelemetryInitializer` actualiza las propiedades siguientes del contexto `Device` para todos los elementos de telemetría.
 - `Type` se establece en "PC".
 - `Id` se establece en el nombre de dominio del equipo donde se ejecuta la aplicación web.
 - `OemName` se establece en el valor extraído del campo `Win32_ComputerSystem.Manufacturer` con WMI.
 - `Model` se establece en el valor extraído del campo `Win32_ComputerSystem.Model` con WMI.
 - `NetworkType` se establece en el valor extraído de `NetworkInterface`.
 - `Language` se establece en el nombre de `CurrentCulture`.

**Módulos**

* `RequestTrackingTelemetryModule` realiza un seguimiento de las solicitudes que recibe la aplicación web y mide los tiempos de respuesta.
* `ExceptionTrackingTelemetryModule` realiza excepciones no controladas en la aplicación web. Consulte [Errores y excepciones][exceptions].
* `DeveloperModeWithDebuggerAttachedTelemetryModule` fuerza al `TelemetryChannel` de Application Insights a enviar los datos inmediatamente, elemento de telemetría a elemento, cuando se adjunta un depurador al proceso de aplicación. Esto reduce la cantidad de tiempo entre el momento en que la aplicación realiza un seguimiento de telemetría y el momento en que esta aparece en el portal de Application Insights a costa de una sobrecarga de ancho de banda de red y una CPU considerable.

## Paquete de NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)

El paquete de NuGet `Microsoft.ApplicationInsights.PerfCounterCollector` agrega los módulos de telemetría siguientes al archivo `ApplicationInsights.config` de forma predeterminada.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` realiza un seguimiento de una serie de contadores de rendimiento de Windows. Puede ver estos contadores haciendo clic en un gráfico en el Explorador de métricas para abrir la hoja de detalles.

Puede supervisar los contadores de rendimiento adicionales, tanto los contadores de Windows estándar como cualquier otro que haya agregado.
      
Utilice la siguiente sintaxis para recopilar los contadores de rendimiento adicionales:

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
`PerformanceCounter` debe ser `\CategoryName(InstanceName)\CounterName` o `\CategoryName\CounterName`
      
Si proporciona un atributo `ReportAs`, se utilizará como nombre para mostrar en Application Insights.

Para crear informes para Application Insights, los nombres de contador deben incluir solo letras, paréntesis, barras diagonales, guiones, caracteres de subrayado, espacios y puntos.

Debe usar ReportAs si el contador que desea supervisar contiene caracteres no válidos como "#" o dígitos.
      
Se admiten los siguientes marcadores de posición como `InstanceName`:

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Parámetros de canal (Java)

Estos parámetros afectan al modo en que el SDK de Java debe almacenar y vaciar los datos de telemetría que recopila.

#### MaxTelemetryBufferCapacity

El número de elementos de telemetría que pueden almacenarse en el almacenamiento del SDK en memoria. Cuando se alcanza este número, se vacía el búfer de telemetría; es decir, los elementos de telemetría se envían al servidor de Application Insights.

-	Mín: 1
-	Máx: 1000
-	Valor predeterminado: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Determina la frecuencia con que los datos almacenados en el almacenamiento en memoria deben vaciarse (enviarse a Application Insights).

-	Mín: 1
-	Máx: 300
-	Valor predeterminado: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Determina el tamaño máximo en MB que se asigna al almacenamiento persistente en el disco local. Este almacenamiento se utiliza para conservar los elementos de telemetría que no pudieron transmitirse al extremo de Application Insights. Cuando se ha alcanzado el tamaño de almacenamiento, se descartarán los nuevos elementos de telemetría.

-	Mín: 1
-	Máx: 100
-	Valor predeterminado: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## Inicializadores personalizados


Si los inicializadores estándar no son adecuados para su aplicación, puede crear los suyos propios.

Use inicializadores de contexto para establecer los valores que se utilizarán para inicializar todos los clientes de telemetría. Por ejemplo, si ha publicado más de una versión de la aplicación, podría asegurarse de que se pueden separar los datos mediante el filtrado sobre una propiedad personalizada:

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Use los inicializadores de telemetría para agregar procesamiento a cada evento. Por ejemplo, el SDK de web marca como errónea cualquier solicitud con un código de respuesta >= 400. Puede pasar por alto este comportamiento:

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Para instalar los inicializadores, agregue líneas en ApplicationInsights.config:

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


También puede escribir código para instalar el inicializador en un punto anterior de la ejecución de la aplicación. Por ejemplo:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Esto determina el recurso de Application Insights en el que aparecen los datos. Normalmente se crea un recurso independiente, con una clave independiente, para cada una de las aplicaciones.

Si desea establecer la clave de forma dinámica (por ejemplo, si desea enviar los resultados de su aplicación a distintos recursos) puede omitir la clave del archivo de configuración y establecerla en el código.

Para establecer la clave de todas las instancias de TelemetryClient, incluidos los módulos de telemetría estándar, establezca la clave en TelemetryConfiguration.Active. Hágalo en un método de inicialización, como global.aspx.cs, en un servicio de ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si solo desea enviar un conjunto específico de eventos a un recurso diferente, puede establecer la clave para un TelemetryClient específico:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Obtenga más información acerca de la API][api].

Para obtener una nueva clave, [cree un nuevo recurso en el portal de Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=July15_HO4-->