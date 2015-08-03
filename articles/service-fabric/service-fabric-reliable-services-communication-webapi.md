<properties
   pageTitle="Servicios de API web de Service Fabric con autohospedaje OWIN | Microsoft Azure"
   description="En este artículo de Service Fabric se explica cómo implementar la comunicación del servicio mediante la API web de ASP.NET con autohospedaje OWIN en servicios fiables."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="05/18/2015"
   ms.author="vturecek"/>

# Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN

Service Fabric pone el poder en sus manos a la hora de decidir cómo desea que se comuniquen sus servicios con los usuarios y entre sí. En este tutorial se centra en la implementación de la comunicación del servicio con la API web de ASP.NET con autohospedaje OWIN en la API de *servicios fiables* de Service Fabric. Revisaremos en profundidad la API de comunicación conectable de *servicios fiables* y mostraremos paso a paso cómo configurar un agente de escucha de comunicación personalizado para su servicio con la API web que se utiliza como ejemplo. Para ver un ejemplo completo de un agente de escucha de comunicación de API web, consulte el [ejemplo de WebApplication de Service Fabric en GitHub](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication).


## Introducción a la API web en Service Fabric

La API web de ASP.NET es un marco popular y potente que permite la creación de API HTTP sobre .NET Framework. Diríjase a la página [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para obtener más información acerca de la API web si todavía no está familiarizado con ella.

La API web de Service Fabric es la misma API web de ASP.NET que conoce y ama. La diferencia radica en cómo *hospeda* una aplicación de API web (sugerencia: no tendrá que usar IIS). Para entender mejor la diferencia, dividámosla en dos partes:

 1. La aplicación de API web (los controladores, modelos, etc.)
 2. El host (el servidor web, normalmente IIS)

La propia aplicación API web no cambia aquí: no difiere de las aplicaciones API web que haya realizado en el pasado y deberá poder mover simplemente la mayor parte del código de su aplicación. Es posible que hospedar la aplicación resulte un poco diferente de lo que está acostumbrado a si está acostumbrado a realizar el hospedaje en IIS. Pero antes de entrar en la parte de hospedaje, comencemos con la parte más familiar: la aplicación API web.


## Configurar una aplicación API web

Empiece por crear un nuevo servicio sin estado en Visual Studio 2015:

![](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Esto nos proporciona un servicio sin estado vacío en el que se va a hospedar la aplicación API web. Vamos a configurar la aplicación desde el principio para ver cómo se ensambla.

El primer paso es extraer algunos paquetes de NuGet para la API web. El paquete que queremos utilizar es **Microsoft.AspNet.WebApi.OwinSelfHost**. Este paquete incluye todos los paquetes de la API web necesarios y los paquetes *host*. Esto será importante más adelante.

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Con los paquetes instalados, podremos empezar a crear la estructura de proyecto de API web básica. Si ha utilizado la API Web, la estructura del proyecto le resultará muy familiar. Comience por crear los directorios de la API web básicos:

 + App_Start
 + Controladores
 + Modelos

Agregue las clases básicas de configuración de API web en el directorio App_Start:

 + FormatterConfig.cs

```csharp

namespace WebApi
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

 + RouteConfig.cs

```csharp

namespace WebApi
{
    using System.Web.Http;

    public static class RouteConfig
    {
        public static void RegisterRoutes(HttpRouteCollection routes)
        {
            routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { controller = "Default", id = RouteParameter.Optional }
                );
        }
    }
}

```

Agregue un controlador predeterminado en el directorio de controladores:

 + DefaultController.cs

```csharp

namespace WebApi.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    public class DefaultController : ApiController
    {
        // GET api/values
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        public void Delete(int id)
        {
        }
    }
}

```

Finalmente, agregue una clase de inicio en la raíz del proyecto para registrar el enrutamiento, los formateadores y cualquier otro programa de instalación de configuración. Aquí también es donde la API web se conecta al *host*, que se revisará de nuevo más tarde. Al configurar la clase de inicio, cree una interfaz denominada *IOwinAppBuilder* para la clase de inicio que defina el método de configuración. Aunque técnicamente no es necesario para que la API web funcione, le permitirá un uso más flexible de la clase de inicio más adelante.

 + Startup.cs

```csharp

namespace WebApi
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            FormatterConfig.ConfigureFormatters(config.Formatters);
            RouteConfig.RegisterRoutes(config.Routes);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApi
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

Eso es todo en lo relacionado con la parte de la aplicación. En este momento, hemos establecido el diseño básico de proyecto de la API web. En comparación con los proyectos de la API web que haya realizado en el pasado o con la plantilla básica de la API, no debería tener un aspecto muy diferente hasta ahora. La lógica empresarial va en los controladores y los modelos como de costumbre.

¿Ahora qué hacemos acerca del hospedaje para poder ejecutarlo en realidad?


## Hospedaje de servicio

En Service Fabric, el servicio se ejecuta en un *proceso de host de servicio* (una aplicación ejecutable que ejecuta el código del servicio). Al escribir un servicio mediante la API de servicios fiables y, de hecho, en la mayoría de los casos al escribir un servicio en Service Fabric en .NET, su proyecto de servicio simplemente se compila en un .EXE que registra el tipo de servicio y ejecuta el código. De hecho, si abre **Program.cs** en el proyecto de servicio sin estado, verá:

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType(Service.ServiceTypeName, typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

Si es sospechosamente similar al punto de entrada a una aplicación de consola, eso se debe a que:

![](media/service-fabric-reliable-services-communication-webapi/webapi-projectproperties.png)

La información sobre el proceso de host de servicio y el registro del servicio está fuera del ámbito de este artículo, pero es importante saber por el momento que **el código del servicio se está ejecutando en su propio proceso**.

## API web de autohospedaje con un host OWIN

Dado que el código de aplicación de la API web se aloja en su propio proceso, ¿cómo se conecta a un servidor web? Escriba [OWIN](http://owin.org/). OWIN es simplemente un contrato entre las aplicaciones web .NET y servidores web. Tradicionalmente con ASP.NET, hasta MVC 5, la aplicación web se acoplaba estrechamente con IIS a través de System.Web. Sin embargo, la API web implementa OWIN, que le permite escribir una aplicación web que se separa del servidor web que la hospeda. Esto le permite usar un servidor web OWIN de *autohospedaje* que puede iniciar en su propio proceso, que encaja perfectamente en el modelo de hospedaje de Service Fabric que acabamos de describir.

En este artículo, usaremos Katana como el host OWIN para la aplicación API web. Katana es una implementación de host OWIN de código abierto.

> [AZURE.NOTE]Para obtener más información acerca de Katana, diríjase al [sitio de Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) y, para obtener una introducción rápida acerca de cómo usar Katana para autohospedar la API web, consulte este artículo acerca de cómo [usar OWIN para autohospedar la API web ASP.NET 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Configurar el servidor web

La API de servicios fiables proporciona dos puntos de entrada para la lógica de negocios:

 + Un método de punto de entrada abierto donde puede comenzar la ejecución de cualquier carga de trabajo que desee, dirigida principalmente a las cargas de trabajo de proceso de larga ejecución:

```csharp

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 + Un punto de entrada de comunicación en el que puede conectar su pila de comunicación preferida:

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

El servidor web (y las demás pilas de comunicación que utilice en el futuro, como WebSockets) debe utilizar la interfaz ICommunicationListener para integrarse correctamente en el sistema. Las razones para ello serán más evidentes en los pasos siguientes.

En primer lugar, cree una clase denominada OwinCommunicationListener que implemente ICommunicationListener:

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

La interfaz de ICommunicationListener proporciona 4 métodos para administrar un agente de escucha de comunicación para el servicio:

 + **Inicializar**: normalmente es donde se configura la dirección que el servicio va a escuchar. Para el servidor web, aquí es donde se configura la dirección URL.
 + **OpenAsync**: empezar a escuchar las solicitudes.
 + **CloseAsync**: dejar de escuchar las solicitudes, finalizar las solicitudes en curso y cerrar correctamente.
 + **Anular**: cancelar todo y detener inmediatamente.

Para empezar, agregue los miembros de clase privada para un prefijo de ruta de acceso de dirección URL y la clase de **Inicio** creada anteriormente. Estos se inicializarán a través del constructor y se usarán más adelante cuando configure la dirección URL de escucha. Asimismo, agregue miembros de clase privada para guardar la dirección de escucha y el identificador de servidor que se crean durante la inicialización y posteriormente, cuando se inicia el servidor, respectivamente.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
    {
        this.startup = startup;
        this.appRoot = appRoot;
    }

    ...

```

### Inicializar

La dirección URL del servidor web se configurará aquí. Para ello, necesita dos elementos de información:

 + **Prefijo de ruta de acceso de dirección URL**. Aunque es opcional, es aconsejable configurar esto ahora para poder alojar de forma segura varios servicios web en la aplicación.
 + **Un puerto**.

Antes de tomar un puerto para el servidor web, es importante comprender que Service Fabric proporciona una capa de aplicación que actúa como un búfer entre la aplicación y el sistema operativo subyacente en el que se ejecuta. Como tal, Service Fabric proporciona una manera de configurar *extremos* para los servicios. Service Fabric se encarga de asegurarse de que el extremo está disponible para ser utilizado por su servicio para que no tenga que configurarlo usted mismo con el entorno de sistema operativo subyacente. Esto permite hospedar fácilmente la aplicación de Service Fabric en diferentes entornos sin tener que realizar ningún cambio en la aplicación (por ejemplo, puede hospedar la misma aplicación en Azure o en su propio centro de datos).

Configurar un extremo HTTP en PackageRoot\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Este paso es importante porque el proceso de host de servicio se ejecuta con credenciales restringidas (servicio de red en Windows), lo que significa que el servicio no tiene acceso para configurar un extremo HTTP por sí mismo. Mediante la configuración de extremo, Service Fabric sabe cómo configurar la ACL adecuada para la dirección URL que el servicio escuchará mientras proporciona un lugar estándar para configurar extremos.

De nuevo en OwinCommunicationListener.cs, obtenga la información del extremo en el método Initialize para obtener el puerto. Cree la dirección URL que escuchará el servicio y guárdela en la variable de miembro de clase creada anteriormente. Esto se utilizará en OpenAsync para iniciar el servidor web.

```csharp

public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
}

```

Tenga en cuenta que "http://+" se utiliza aquí. Esto le permite asegurarse de que el servidor web está escuchando todas las direcciones disponibles, incluyendo localhost, FQDN y la dirección IP del equipo.

### OpenAsync

La plataforma llama a OpenAsync después de la inicialización. Aquí es donde usa la dirección que se creó en Initialize para abrir el servidor web.

La implementación de OpenAsync es una de las razones más importantes por las que el servidor web (o cualquier pila de comunicación) se implementa como un ICommunicationListener en lugar de simplemente abrirlo directamente desde RunAsync() en el servicio. El valor devuelto de OpenAsync es la dirección que está escuchando el servidor web. Cuando se devuelve esta dirección al sistema, registra la dirección con el servicio. Service Fabric proporciona una API que permite a los clientes u otros servicios pedir esta dirección por nombre de servicio. Esto es importante porque la dirección de servicio no es estática ya que los servicios se mueven en el clúster para fines de disponibilidad y equilibrio de recursos. Este es el mecanismo que permite a los clientes resolver la dirección de escucha de un servicio.

Con eso en mente, OpenAsync inicia el servidor web y devuelve la dirección que está escuchando. Tenga en cuenta que realiza escuchas en "http://+", pero antes de devolver la dirección, el "+" se sustituye por la dirección IP o FQDN del nodo en el que está actualmente. La razón de esto es que esta dirección que está devolviendo el método es la que se registra con el sistema, y es lo que los clientes y otro servicio verán cuando soliciten la dirección del servicio. Para que los clientes se conecten correctamente a ella, necesitan un IP o FQDN real en la dirección.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

    string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

    return Task.FromResult(resultAddress);
}

```

Tenga en cuenta que esto hace referencia a la clase **Inicio** que se pasó a OwinCommunicationListener en el constructor. Esta instancia de inicio es usada por el servidor web para iniciar la aplicación de la API web.

La línea ServiceEventSource.Current.Message() aparecerá en la ventana de eventos de diagnóstico más adelante al ejecutar la aplicación para que sepa que el servidor web se ha iniciado correctamente.

### CloseAsync y Abort

Por último, implemente CloseAsync y Abort para detener el servidor web. Es posible detener el servidor web eliminando el identificador de servidor que se creó durante la OpenAsync.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

En este ejemplo de implementación, CloseAsync y Abort simplemente detendrían el servidor web. Puede optar por realizar un apagado más correctamente coordinado del servidor web en CloseAsync; por ejemplo, esperando a que las solicitudes en curso se completen antes de volver.

## Iniciar el servidor web

Ahora está listo para crear y devolver una instancia de OwinCommunicationListener para iniciar el servidor web. En la clase de servicio (Service.cs), anule el método **CreateCommunicationListener()**:

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
	return new OwinCommunicationListener("api", new Startup());
}

```

Aquí es donde se encuentran finalmente la *aplicación* de la API web y el *host* OWIN: al *host* (**OwinCommunicationListener**) se le facilita una instancia de la *aplicación* (API web a través de **Inicio**) y Service Fabric administra su ciclo de vida. Normalmente este mismo patrón puede ser seguido de cualquier pila de comunicación.

## Resumen

En este ejemplo, no necesita hacer nada en el método RunAsync(), para que se pueda quitar la invalidación fácilmente.

La implementación del servicio final debe ser muy simple, debido a que solo necesita crear el agente de escucha de comunicación:

```csharp

namespace WebApi
{
    using Microsoft.ServiceFabric.Services;

    public class Service : StatelessService
    {
        public const string ServiceTypeName = "WebApiType";

        protected override ICommunicationListener CreateCommunicationListener()
        {
            return new OwinCommunicationListener("api", new Startup());
        }
    }
}

```

Y la clase OwinCommunicationListener completa:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
        {
            this.startup = startup;
            this.appRoot = appRoot;
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

            string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

            return Task.FromResult(resultAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Con todas las piezas en su lugar, el proyecto debe ser ahora como una aplicación típica de API web con los puntos de entrada de la API de Servicios fiables y un host OWIN:


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Ejecutar y conectarse a través de un explorador web

Si no lo ha hecho, [configure el entorno de desarrollo](service-fabric-get-started.md).


Ahora puede compilar e implementar su servicio. Presione **F5** en Visual Studio para compilar e implementar la aplicación. En la ventana de eventos de diagnósticos, verá un mensaje que le indicará que el servidor web se abrió en **http://localhost:80/api**


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Si el puerto ya es abierto por parte de otro proceso del equipo, es posible que se vea un error aquí que indique que no se pudo abrir el agente de escucha. Si ese es el caso, intente utilizar un puerto diferente en la configuración del extremo en ServiceManifest.xml.


Una vez que el servicio se esté ejecutando, abra un explorador y navegue hasta [http://localhost/api](http://localhost/api) para probarlo.

## Escala horizontal

Escalar aplicaciones web sin estado normalmente significa agregar más máquinas y sincronizar la aplicación web en ellos. El motor de orquestaciones de Service Fabric puede hacer esto automáticamente cada vez que se agregan nuevos nodos a un clúster. Al crear instancias de un servicio sin estado, puede especificar el número de instancias que desea crear. Service Fabric colocará ese número de instancias en los nodos del clúster según corresponda, asegurándose de no crear más de una instancia en cada nodo. También puede indicar a Service Fabric que cree siempre una instancia en cada nodo mediante la especificación de "-1" en el número de instancias. Esto garantiza que cada vez que agregue nodos para escalar horizontalmente el clúster, se creará una instancia del servicio sin estado en los nodos nuevos. Este valor es una propiedad de la instancia de servicio, por lo que se establece cuando se crea una instancia de servicio ya sea a través de PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

O bien, al definir un servicio predeterminado en un proyecto de servicio sin estado de Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para obtener más información sobre la creación de aplicaciones e instancias de servicio, consulte [cómo implementar y quitar aplicaciones](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

En ASP.NET 5, el concepto y modelo de programación de separar la *aplicación* del *host* en las aplicaciones web sigue siendo el mismo. También puede aplicarse a otras formas de comunicación. Además, aunque el *host* puede diferir en ASP.NET 5, la capa de la *aplicación* de la API web sigue siendo la misma, que es donde reside realmente la mayor parte de la lógica de la aplicación.

## Pasos siguientes

[Depuración de la aplicación de Service Fabric en Visual Studio](service-fabric-debugging-your-application.md)
 

<!---HONumber=July15_HO4-->