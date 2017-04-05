---
title: "Comunicación de servicio con la API web de ASP.NET | Microsoft Docs"
description: "Aprenda a implementar la comunicación del servicio paso a paso mediante la API web de ASP.NET con autohospedaje OWIN en la API de Reliable Services."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 02/10/2017
ms.author: vturecek
redirect_url: /azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 73b7e1c0cb93ae7c54780a3aab837b0e5bcdb0a0
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN
Azure Service Fabric delega en usted la responsabilidad de decidir cómo desea que se comuniquen sus servicios con los usuarios y entre sí. Este tutorial se centra en la implementación de la comunicación del servicio mediante la API web de ASP.NET con autohospedaje OWIN (Open Web Interface para .NET) en la API de Reliable Services de Service Fabric. Profundizaremos más en la API de comunicación acoplable de Reliable Services. También vamos a usar la API web en un ejemplo paso a paso para mostrar cómo configurar un agente de escucha de comunicación personalizado.

## <a name="introduction-to-web-api-in-service-fabric"></a>Introducción a las API web de Service Fabric
La API web de ASP.NET es un marco popular y potente que permite la creación de API HTTP sobre .NET Framework. Si no está familiarizado con el marco de trabajo, consulte [Introducción a ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para obtener más información.

La API web de Service Fabric es la misma API web de ASP.NET que conoce y ama. La diferencia radica en cómo se *hospeda* la aplicación de la API web. No va a utilizar Microsoft Internet Information Services (IIS). Para entender mejor la diferencia, dividámosla en dos partes:

1. La aplicación de la API web (incluidos los controladores y modelos)
2. El host (el servidor web, normalmente IIS)

Una aplicación de la API web no cambia. No difiere de las aplicaciones API web que haya realizado en el pasado y deberá poder mover simplemente la mayor parte del código de la aplicación. Pero si ha utilizado IIS para hospedarla, donde hospeda la aplicación puede diferir ligeramente del hospedaje que suele utilizar. Antes de profundizar en el hospedaje, vamos a hablar sobre algo más familiar, que es la aplicación de la API web.

## <a name="create-the-application"></a>Creación de la aplicación
Empiece por crear una nueva aplicación Service Fabric, con un servicio único sin estado en Visual Studio 2015.

Si usa API web, encontrará una plantilla de Visual Studio para un servicio sin estado. En este tutorial, crearemos desde cero un proyecto de API web que tenga como resultado lo que se obtendría si seleccionara esta plantilla.

Seleccione un proyecto de servicio sin estado en blanco para aprender a crear un proyecto de API web desde cero; también puede empezar con la plantilla de API web de servicio sin estado y seguir los pasos.  

El primer paso es extraer algunos paquetes de NuGet para la API web. El paquete que queremos utilizar es Microsoft.AspNet.WebApi.OwinSelfHost. Este paquete incluye todos los paquetes de la API web necesarios y los paquetes *host* . Esto será importante más adelante.

Después de haber instalado los paquetes, podremos empezar a crear la estructura de proyecto de API web básica. Si ha utilizado la API Web, la estructura del proyecto le resultará muy familiar. Empiece agregando un directorio `Controllers` y un controlador de valores sencillos:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;

namespace WebService.Controllers
{
    public class ValuesController : ApiController
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

Finalmente, agregue una clase de inicio en la raíz del proyecto para registrar el enrutamiento, los formateadores y cualquier otro programa de configuración. Aquí también es donde la API web se conecta al *host*, que se revisará de nuevo más tarde. 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Eso es todo en lo relacionado con la parte de la aplicación. En este momento, hemos establecido el diseño básico de proyecto de la API web. Hasta ahora, el aspecto no debe diferir mucho de los proyectos de API web que ha creado en el pasado o de la plantilla básica de API web. La lógica empresarial va en los controladores y los modelos como de costumbre.

¿Ahora qué hacemos con el hospedaje para poder ejecutarlo en realidad?

## <a name="service-hosting"></a>Hospedaje de servicio
En Service Fabric, el servicio se ejecuta en un *proceso de host de servicio*(un archivo ejecutable que ejecuta el código del servicio). Al escribir un servicio con la API de Reliable Services, el proyecto de servicio simplemente se compila en un archivo ejecutable que registra el tipo de servicio y ejecuta el código. Esto ocurre en la mayoría de los casos al escribir un servicio de Service Fabric en .NET. Si abre Program.cs en el proyecto de servicio sin estado, verá:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si es sospechosamente similar al punto de entrada a una aplicación de consola, eso es porque lo es.

Los detalles adicionales sobre el proceso de host de servicio y el registro del servicio están fuera del ámbito de este artículo. Sin embargo, es importante saber por ahora que *el código del servicio se ejecuta en su propio proceso*.

## <a name="self-host-web-api-with-an-owin-host"></a>Autohospedaje de la API web con un host OWIN
Dado que el código de aplicación de la API web se hospeda en su propio proceso, ¿cómo se conecta a un servidor web? Escriba [OWIN](http://owin.org/). OWIN es simplemente un contrato entre las aplicaciones web .NET y servidores web. Tradicionalmente, cuando se usa ASP.NET (hasta MVC 5), la aplicación web se acoplaba estrechamente con IIS a través de System.Web. Sin embargo, la API web implementa OWIN, lo que le permite escribir una aplicación web que se separa del servidor web que la hospeda. Por este motivo, puede usar un servidor web OWIN de *autohospedaje* que puede iniciar en su propio proceso. Encaja perfectamente con el modelo de hospedaje de Service Fabric que acabamos de describir.

En este artículo, usaremos Katana como el host OWIN para la aplicación API web. Katana es una implementación del host OWIN de código abierto basada en [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) y la [API de servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) de Windows.

> [!NOTE]
> Para más información sobre Katana, vaya al [sitio de Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Para una introducción rápida sobre cómo usar Katana para el autohospedaje de la API web, consulte [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)(Uso de OWIN para autohospedaje de la API web de ASP.NET 2).
> 
> 

## <a name="set-up-the-web-server"></a>Configurar el servidor web
La API de Reliable Services ofrece un punto de entrada de comunicación en el que puede conectar pilas de comunicación para permitir a los usuarios y a los clientes conectarse al servicio:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

El servidor web (y las demás pilas de comunicación que utilice en el futuro, como WebSockets) debe utilizar la interfaz ICommunicationListener para integrarse correctamente en el sistema. Las razones para ello serán más evidentes en los pasos siguientes.

En primer lugar, cree una clase denominada OwinCommunicationListener que implemente ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

La interfaz de ICommunicationListener ofrece tres métodos para administrar un agente de escucha de comunicación para el servicio:

* *OpenAsync*. Empezar a escuchar las solicitudes.
* *CloseAsync*. Dejar de escuchar las solicitudes, finalizar las solicitudes en curso y cerrar correctamente.
* *Abort*. Cancelar todo y detener inmediatamente.

Para empezar, agregue los miembros de clase privada de elementos que el agente de escucha necesite para funcionar. Estos se inicializarán a través del constructor y se usarán más adelante cuando configure la dirección URL de escucha.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }


    ...

```

## <a name="implement-openasync"></a>Implementación de OpenAsync
Para configurar el servidor web, necesitamos un par de datos:

* *Prefijo de ruta de acceso de dirección URL*. Aunque es opcional, es aconsejable configurar esto ahora para poder hospedar de forma segura varios servicios web en la aplicación.
* *Un puerto*.

Antes de obtener un puerto para el servidor web, es importante comprender que Service Fabric proporciona una capa de aplicación que actúa como un búfer entre la aplicación y el sistema operativo subyacente en el que se ejecuta. Como tal, Service Fabric proporciona una manera de configurar *extremos* para los servicios. Service Fabric garantiza que los puntos de conexión están disponibles para que el servicio los utilice. De este modo, no tiene que configurarlos por su cuenta en el entorno de sistema operativo subyacente. Puede hospedar fácilmente su aplicación de Service Fabric en diferentes entornos sin tener que realizar cambios en la aplicación. (Por ejemplo, puede hospedar la misma aplicación en Azure o en su propio centro de datos).

Configurar un extremo HTTP en PackageRoot\ServiceManifest.xml:

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Este paso es importante porque el proceso de host de servicio se ejecuta con credenciales restringidas (servicio de red en Windows). Esto significa que el servicio no tendrá acceso para configurar un punto de conexión HTTP por sí mismo. Mediante la configuración del punto de conexión, Service Fabric sabe configurar la lista de control de acceso (ACL) adecuada para la dirección URL que el servicio escuchará. Service Fabric también proporciona un lugar estándar para configurar puntos de conexión.

De nuevo en OwinCommunicationListener.cs, puede iniciar la implementación de OpenAsync. Aquí es donde inicia el servidor web. En primer lugar, obtenga la información de punto de conexión y cree la dirección URL en la que escucha el servicio. La dirección URL será diferente dependiendo de si se utiliza el agente de escucha en un servicio sin estado o uno con estado. Para los servicios con estado, el agente de escucha debe crear una dirección única para cada réplica de servicio con estado en la que el agente realiza la escucha. Para los servicios sin estado, la dirección puede ser mucho más sencilla. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }

    ...

```

Tenga en cuenta que "http://+" se utiliza aquí. Esto le permite asegurarse de que el servidor web está escuchando todas las direcciones disponibles, incluyendo localhost, FQDN y la dirección IP del equipo.

La implementación de OpenAsync es una de las razones más importantes por las que el servidor web (o cualquier pila de comunicación) se implementa como una interfaz ICommunicationListener en lugar de simplemente abrirse directamente desde `RunAsync()` en el servicio. El valor devuelto de OpenAsync es la dirección que está escuchando el servidor web. Cuando se devuelve esta dirección al sistema, registra la dirección con el servicio. Service Fabric proporciona una API que permite a los clientes y otros servicios pedir esta dirección por nombre de servicio. Esto es importante porque la dirección del servicio no es estática. Los servicios se mueven en el clúster para fines de disponibilidad y equilibrio de recursos. Este es el mecanismo que permite a los clientes resolver la dirección de escucha de un servicio.

Con eso en mente, OpenAsync inicia el servidor web y devuelve la dirección en que está escuchando. Tenga en cuenta que realiza escuchas en "http://+", pero antes de que OpenAsync devuelva la dirección, el "+" se sustituye por la dirección IP o FQDN del nodo en el que está actualmente. La dirección que este método devuelve es la que se registra con el sistema. También es lo que los clientes y otros servicios ven cuando solicitan la dirección de un servicio. Para que los clientes se conecten correctamente a ella, necesitan un IP o FQDN real en la dirección.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Tenga en cuenta que esto hace referencia a la clase Inicio que se pasó a OwinCommunicationListener en el constructor. El servidor web utiliza esta instancia de inicio para arrancar la aplicación de la API web.

La línea `ServiceEventSource.Current.Message()` aparecerá en la ventana Eventos de diagnóstico más adelante al ejecutar la aplicación para confirmar que el servidor web se ha iniciado correctamente.

## <a name="implement-closeasync-and-abort"></a>Implementación de CloseAsync y Abort
Por último, implemente CloseAsync y Abort para detener el servidor web. Es posible detener el servidor web eliminando el identificador de servidor que se creó durante la OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

En este ejemplo de implementación, CloseAsync y Abort simplemente detendrían el servidor web. Puede optar por realizar un apagado coordinado del servidor web de manera más correcta en CloseAsync. Por ejemplo, el apagado podría esperar a que finalicen las solicitudes en proceso antes de la devolución.

## <a name="start-the-web-server"></a>Iniciar el servidor web
Ahora está listo para crear y devolver una instancia de OwinCommunicationListener para iniciar el servidor web. De nuevo en la clase de servicio (WebService.cs), reemplace el método `CreateServiceInstanceListeners()`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Aquí es donde la *aplicación* de la API web y el *host* OWIN se encuentran finalmente. Al host (OwinCommunicationListener) se le asigna una instancia de la *aplicación* (la API web) a través del inicio. Service Fabric administra su ciclo de vida. Normalmente este mismo patrón puede ser seguido de cualquier pila de comunicación.

## <a name="put-it-all-together"></a>Colocación de todo junto
En este ejemplo, no necesita hacer nada en el método `RunAsync()` , de modo que simplemente se puede quitar la invalidación.

La implementación del servicio final debe ser muy sencilla. Solo es necesario crear el agente de escucha de comunicación:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

La clase `OwinCommunicationListener` completa:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
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

Ahora que ya lo tiene todo listo, el proyecto debe presentar el aspecto de una aplicación típica de API web con los puntos de entrada de la API de Reliable Services y un host OWIN.

## <a name="run-and-connect-through-a-web-browser"></a>Ejecutar y conectarse a través de un explorador web
Si no lo ha hecho, [configure el entorno de desarrollo](service-fabric-get-started.md).

Ahora puede compilar e implementar su servicio. Presione **F5** en Visual Studio para compilar e implementar la aplicación. En la ventana Eventos de diagnóstico, debe aparecer un mensaje que indica que el servidor web se ha abierto en http://localhost:8281/.

> [!NOTE]
> Si el puerto ya lo ha abierto otro proceso en el equipo, puede aparecer un error aquí. Esto indica que no se ha podido abrir el agente de escucha. Si ese es el caso, intente utilizar un puerto diferente para configurar el punto de conexión en ServiceManifest.xml.
> 
> 

Una vez que el servicio se esté ejecutando, abra un explorador y vaya a [http://localhost:8281/api/values](http://localhost:8281/api/values) para probarlo.

## <a name="scale-it-out"></a>Escala horizontal
Escalar aplicaciones web sin estado normalmente supone agregar más equipos y sincronizar aplicaciones web en ellos. El motor de orquestaciones de Service Fabric puede hacer esto automáticamente cada vez que se agregan nuevos nodos a un clúster. Al crear instancias de un servicio sin estado, puede especificar el número de instancias que desea crear. Service Fabric coloca ese número de instancias en los nodos del clúster. Y se asegura de no crear más de una instancia en cada nodo. También puede indicar a Service Fabric que cree siempre una instancia en cada nodo mediante la especificación de **-1** en el número de instancias. Esto garantiza que cada vez que agregue nodos para escalar horizontalmente el clúster, se creará una instancia del servicio sin estado en los nodos nuevos. Este valor es una propiedad de la instancia de servicio, por lo que se establece cuando se crea una instancia de servicio. Puede hacerlo a través de PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

También puede hacerlo al definir un servicio predeterminado en un proyecto de servicio sin estado de Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para más información sobre cómo crear aplicaciones e instancias de servicio, consulte [Implementar una aplicación](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Pasos siguientes
[Depurar la aplicación de Service Fabric con Visual Studio](service-fabric-debugging-your-application.md)


