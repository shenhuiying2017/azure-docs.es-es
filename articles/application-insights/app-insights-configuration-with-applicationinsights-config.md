<properties 
	pageTitle="Configuración del SDK de Application Insights con ApplicationInsights.config o .xml" 
	description="Habilitación o deshabilitación de los módulos de recopilación de datos e incorporación de contadores de rendimiento y otros parámetros" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# Configuración del SDK de Application Insights con ApplicationInsights.config o .xml

El SDK de Application Insights consta de varios módulos. El módulo central proporciona la API básica que envía la telemetría al portal de Application Insights. Los módulos adicionales recopilan datos de la aplicación y su contexto. Ajustando el archivo de configuración, puede habilitar o deshabilitar los módulos y establecer los parámetros para algunos de ellos.

El archivo de configuración se denomina `ApplicationInsights.config` o `ApplicationInsights.xml`, dependiendo del tipo de la aplicación. Se agrega automáticamente al proyecto cuando se [instala el SDK][start]. También se agrega a una aplicación web mediante el [Monitor de estado en un servidor IIS][redfield], o cuando se [selecciona la extensión de Application Insights para un sitio web o una máquina virtual de Azure][azure].

No hay un archivo equivalente para controlar el [SDK en una página web][client].


## Módulos de telemetría

Hay un nodo en el archivo de configuración para cada módulo. Para deshabilitar un módulo, elimine el nodo o conviértalo en comentario.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Informa de errores en el SDK. Por ejemplo, si el SDK no puede tener acceso a los contadores de rendimiento o un TelemetryInitializer personalizado produce una excepción.

Los datos aparecen en la [Búsqueda de diagnóstico][diagnostic].

#### RuntimeTelemetry.RemoteDependencyModule

Recopila los datos de la capacidad de respuesta de los componentes externos utilizados por la aplicación. Para permitir que este módulo funcione en un servidor IIS, deberá [instalar el Monitor de estado][redfield]. Para utilizarlo en aplicaciones web o máquinas virtuales de Azure, [seleccione la extensión Application Insights][azure].

#### Web.WebApplicationLifecycleModule

Intenta vaciar todos los búferes de memoria de datos de telemetría para que no se pierdan en el proceso de cierre.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Cuenta las solicitudes que llegan a su aplicación web y mide los tiempos de respuesta.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Cuentas las excepciones no controladas de su aplicación web. Consulte [Errores y excepciones][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Módulo del recopilador de rendimiento

#### PerfCollector.PerformanceCollectorModule

De forma predeterminada, este módulo reúne una variedad de contadores de rendimiento de Windows. Puede ver estos contadores al abrir la hoja Filtros en el Explorador de métrica.

Puede supervisar los contadores de rendimiento adicionales, tanto los contadores de Windows estándar como cualquier otro que haya agregado.
      
Utilice la siguiente sintaxis para recopilar los contadores de rendimiento adicionales:
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
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

    <ApplicationInsights>
      ...
      <Channel>
      <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
      </ApplicationInsights>

#### FlushIntervalInSeconds 

Determina la frecuencia con que los datos almacenados en el almacenamiento en memoria deben vaciarse (enviarse a Application Insights).

-	Mín: 1
-	Máx: 300
-	Valor predeterminado: 5

    <ApplicationInsights>
      ...
      <Channel>
      <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
      </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Determina el tamaño máximo en MB que se asigna al almacenamiento persistente en el disco local. Este almacenamiento se utiliza para conservar los elementos de telemetría que no pudieron transmitirse al extremo de Application Insights. Cuando se ha alcanzado el tamaño de almacenamiento, se descartarán los nuevos elementos de telemetría.

-	Mín: 1
-	Máx: 100
-	Valor predeterminado: 10

    <ApplicationInsights>
      ... <Channel>
      <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
      </ApplicationInsights>


## Inicializadores de contexto

Estos componentes recopilan datos de la plataforma. Los datos se recopilan en el [objeto TelemetryContext][api].

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Inicializadores de telemetría

Estos componentes agregan datos a cada evento de telemetría enviado a Application Insights.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Este componente identifica las solicitudes HTTP que parecen proceder de robots, como motores de búsqueda y pruebas web. Establece TelemetryClient.Context.Operation.SyntheticSource.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Agrega un nombre de operación para cada elemento de telemetría. Para las aplicaciones web, "operación" significa una solicitud HTTP. Establece TelemetryClient.Context.Operation.Name.

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Esto habilita la característica "buscar eventos en la misma solicitud" en la [Búsqueda de diagnóstico][diagnostic]. Establece TelemetryClient.Context.Operation.Id

Agrega un identificador de operación a cada elemento de datos que se envía a Application Insights. Para las aplicaciones web, una "operación" es una solicitud HTTP. Así, por ejemplo, la solicitud y los eventos y seguimientos personalizados que forman parte del procesamiento de la solicitud llevan todos el mismo identificador de operación.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Agrega un identificador de usuario anónimo a cada elemento de telemetría. Esto le permite filtrar únicamente los eventos relacionados con las actividades de un usuario en la búsqueda de diagnóstico. Por ejemplo, si se informa de una excepción, puede hacer un seguimiento lo que hacía el usuario.

Establece telemetryClient.Context.User.

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Agrega un identificador de sesión a cada evento. Establece telemetryClient.Context.Session.

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

 
<!--HONumber=62-->