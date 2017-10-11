---
title: "Creación de un front-end web para la aplicación de Azure Service Fabric mediante ASP.NET Core| Microsoft Docs"
description: "Exponga la aplicación de Service Fabric a la Web usando un proyecto de ASP.NET Core y la comunicación entre servicios a través de Service Remoting."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Creación de un front-end de servicio web para una aplicación mediante ASP.NET Core
De forma predeterminada, los servicios de Azure Service Fabric no proporcionan una interfaz pública para la Web. Para exponer la funcionalidad de su aplicación a los clientes HTTP, tendrá que crear un proyecto web para que actúe como punto de entrada y, a continuación, comunicarse desde allí con los servicios individuales.

En este tutorial, continuaremos a partir del punto en el que terminamos en el tutorial [Creación de la primera aplicación en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) y agregaremos un servicio web de ASP.NET Core delante el servicio de contador con estado. Si aún no lo ha hecho, debe revisar primero ese tutorial.

## <a name="set-up-your-environment-for-aspnet-core"></a>Configuración del entorno para ASP.NET Core

Necesita Visual Studio 2017 para seguir este tutorial. Cualquier edición servirá. 

 - [Instalación de Visual Studio 2017](https://www.visualstudio.com/)

Para desarrollar aplicaciones de ASP.NET Core de Service Fabric, debe tener las cargas de trabajo siguientes instaladas:
 - **Desarrollo de Azure** (en *Web y nube*)
 - **ASP.NET y desarrollo web** (en *Web y nube*)
 - **Desarrollo multiplataforma de .NET Core** (en *Otros conjuntos de herramientas*)

>[!Note] 
>Las herramientas de .NET Core para Visual Studio 2015 ya no se actualizan, sin embargo, si todavía usa Visual Studio 2015, debe tener instalado [.NET Core VS 2015 Tooling Preview 2](https://www.microsoft.com/net/download/core).

## <a name="add-an-aspnet-core-service-to-your-application"></a>Incorporación de un servicio ASP.NET Core a una aplicación
ASP.NET Core es un marco de desarrollo web ligero multiplataforma, que puede usar para crear modernas interfaces de usuario web y API web. 

Vamos a agregar un proyecto API web de ASP.NET a la aplicación existente.

1. En el Explorador de soluciones, haga clic con el botón derecho en **Servicios**, en el proyecto de aplicación y seleccione **Agregar > Nuevo servicio de Service Fabric**.
   
    ![Incorporación de un nuevo servicio a una aplicación existente][vs-add-new-service]
2. En la página **Create a Service** (Crear un servicio), elija **ASP.NET Core** y asígnele un nombre.
   
    ![Selección del servicio web ASP.NET en el cuadro de diálogo de nuevo servicio][vs-new-service-dialog]

3. En la página siguiente se proporciona un conjunto de plantillas de proyecto ASP.NET Core. Tenga en cuenta que son las mismas opciones que vería si crea un proyecto de ASP.NET Core fuera de una aplicación de Service Fabric, con una pequeña cantidad de código adicional para registrar el servicio con el runtime de Service Fabric. Para este tutorial, elija **API Web**. Sin embargo, puede aplicar los mismos conceptos a la creación de una aplicación web completa.
   
    ![Elección del tipo de proyecto ASP.NET][vs-new-aspnet-project-dialog]
   
    Una vez creado el proyecto API web, tendrá dos servicios en la aplicación. Mientras continúa la creación de la aplicación, puede agregar más servicios exactamente de la misma forma. Cada uno puede tener versiones y actualizaciones independientes.

## <a name="run-the-application"></a>Ejecución de la aplicación
Para tener una idea de lo que hemos hecho, vamos a implementar la nueva aplicación y a observar el comportamiento predeterminado que proporciona la plantilla API web de ASP.NET Core.

1. Presione F5 en Visual Studio para depurar la aplicación.
2. Cuando se complete la implementación, Visual Studio iniciará el explorador en la raíz del servicio API Web de ASP.NET. La plantilla API Web de ASP.NET Core no proporciona comportamiento predeterminado para la raíz, por lo que el explorador mostrará un error 404.
3. Agregue `/api/values` a la ubicación en el explorador. Esta acción invoca el método `Get` de la clase ValuesController de la plantilla de API Web. Devuelve la respuesta predeterminada proporcionada por la plantilla, una matriz JSON que contiene dos cadenas:
   
    ![Valores predeterminados devueltos desde la plantilla API web de ASP.NET Core][browser-aspnet-template-values]
   
    Al final del tutorial, la página mostrará el valor más reciente del contador de nuestro servicio con estado en lugar de las cadenas predeterminadas.

## <a name="connect-the-services"></a>Conexión de los servicios
Service Fabric proporciona una flexibilidad completa en el modo de comunicación con los servicios confiables. En una sola aplicación, podría tener servicios que son accesibles a través de TCP, servicios que son accesibles a través de una API de REST de HTTP y servicios que son accesibles a través de sockets web. Para más información sobre las opciones disponibles y sus inconvenientes, consulte [Conexión y comunicación con servicios en Service Fabric](service-fabric-connect-and-communicate-with-services.md). En este tutorial, usamos [Comunicación remota de servicios de Service Fabric](service-fabric-reliable-services-communication-remoting.md), proporcionado en el SDK.

En el enfoque de Comunicación remota de servicios (basado en llamadas a procedimientos remotos o RPC), se define una interfaz que actúa como el contrato público del servicio. Luego, se usa esa interfaz para generar una clase de proxy para interactuar con el servicio.

### <a name="create-the-remoting-interface"></a>Creación de la interfaz remota
Comenzaremos por la creación de la interfaz para que actúe como contrato entre el servicio con estado y otros servicios, en este caso, el proyecto web de ASP.NET Core. Esta interfaz debe estar compartida por todos los servicios que la usan para realizar llamadas RPC, por lo que la vamos a crear en su propio proyecto de biblioteca de clases.

1. En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Agregue** > **Nuevo proyecto**.

2. Elija la entrada **Visual C#** en el panel de navegación izquierdo y, después, seleccione la plantilla **Biblioteca de clases**. Asegúrese de que el valor de la versión de .NET Framework es **4.5.2**.
   
    ![Creación de un proyecto de interfaz para el servicio con estado][vs-add-class-library-project]

3. Instalar el paquete de NuGet **Microsoft.ServiceFabric.Services.Remoting**. Busque **Microsoft.ServiceFabric.Services.Remoting** en el administrador de paquetes de NuGet e instale el mismo en todos los proyectos de la solución que usan Comunicación remota de servicios, incluidos:
   - El proyecto de biblioteca de clases que contiene la interfaz de servicio
   - El proyecto del servicio con estado
   - El proyecto del servicio web de ASP.NET Core
   
    ![Incorporación del paquete de NuGet de servicios][vs-services-nuget-package]

4. En la biblioteca de clases, cree una interfaz con un único método, `GetCountAsync` y extienda la interfaz de `Microsoft.ServiceFabric.Services.Remoting.IService`. La interfaz de comunicación remota debe derivar de esta interfaz para indicar que es una interfaz de comunicación remota de servicio.
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Implementación de la interfaz en el servicio con estado
Ahora que hemos definido la interfaz, tenemos que implementarla en el servicio con estado.

1. En el servicio con estado, agregue una referencia al proyecto de biblioteca de clases que contiene la interfaz.
   
    ![Incorporación de una referencia al proyecto de biblioteca de clases en el servicio con estado][vs-add-class-library-reference]
2. Busque la clase que se hereda de `StatefulService`, como `MyStatefulService`, y amplíela para implementar la interfaz `ICounter`.
   
    ```c#
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Ahora, implemente el método único que se define en la interfaz `ICounter`, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Exposición del servicio con estado mediante un agente de escucha remoto
Con la interfaz `ICounter` implementada, el paso final consiste en abrir el canal de comunicación de Comunicación remota de servicio. Para los servicios con estado, Service Fabric proporciona un método reemplazable denominado " `CreateServiceReplicaListeners`". Con este método, puede especificar uno o varios agentes de escucha de comunicación, según el tipo de comunicación que desee habilitar para el servicio.

> [!NOTE]
> El método equivalente para abrir un canal de comunicación a los servicios sin estado se llama `CreateServiceInstanceListeners`.

En este caso, reemplazamos el método `CreateServiceReplicaListeners` actual e incluimos una instancia de `ServiceRemotingListener`, que crea un punto de conexión RPC al que los clientes pueden llamar usando `ServiceProxy`.  

El método de extensión `CreateServiceRemotingListener` de la interfaz `IService` le permite crear fácilmente un `ServiceRemotingListener` con todos los valores predeterminados. Para utilizar este método de extensión, asegúrese de que ha importado el espacio de nombres `Microsoft.ServiceFabric.Services.Remoting.Runtime`. 

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Uso de la clase ServiceProxy para interactuar con el servicio
Nuestro servicio con estado está ahora preparado para recibir tráfico de otros servicios a través de RPC. Así que todo lo que queda es agregar el código para comunicarse con él desde el servicio web ASP.NET.

1. En el proyecto ASP.NET, agregue una referencia a la biblioteca de clases que contiene la interfaz `ICounter` .

2. Antes ha agregado el paquete de NuGet **Microsoft.ServiceFabric.Services.Remoting** al proyecto ASP.NET. Este paquete ofrece la clase `ServiceProxy` que se utiliza para realizar llamadas RPC al servicio con estado. Asegúrese de que este paquete de NuGet está instalado en el proyecto del servicio web de ASP.NET Core.

4. En la carpeta **Controladores**, abra la clase `ValuesController`. Tenga en cuenta que, en estos momentos, el método `Get` solo devuelve una matriz de cadenas codificadas de forma rígida de value1 y value2, que coincide con lo que vimos anteriormente en el explorador. Reemplace esta implementación con el siguiente código:
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    La primera línea de código es la clave. Para crear el proxy ICounter para el servicio con estado, tiene que proporcionar dos fragmentos de información: un identificador de partición y el nombre del servicio.
   
    Puede usar particiones para escalar servicios con estado dividiendo su estado en depósitos distintos en función de una clave que usted define, como puede ser un identificador de cliente o un código postal. En nuestra sencilla aplicación, el servicio con estado tiene solo una partición, por lo que no importa la clave. Cualquier clave que proporcione llevará a la misma partición. Consulte [Partición de Service Fabric Reliable Services](service-fabric-concepts-partitioning.md)para obtener más información sobre los servicios de partición.
   
    El nombre del servicio es un identificador URI con el formato fabric:/&lt;nombre_de_aplicación&gt;/&lt;nombre_de_servicio&gt;.
   
    Con estas dos piezas de información, Service Fabric puede identificar de forma exclusiva el equipo al que se deben enviar las solicitudes. La clase `ServiceProxy` también controlará sin ningún problema los casos en los que se produce un error en la máquina que hospeda la partición de servicio con estado y hay que promover otra máquina para que ocupe su lugar. Esta abstracción simplifica considerablemente la escritura del código de cliente para tratar con otros servicios.
   
    Una vez que se tiene el proxy, simplemente se invoca el método `GetCountAsync` y se devuelve su resultado.

5. Presione F5 de nuevo para ejecutar la aplicación modificada. Como antes, Visual Studio inicia automáticamente el explorador en la raíz del proyecto web. Agregue la ruta de acceso "api/values", debería ver el valor actual del contador devuelto.
   
    ![El valor del contador con estado aparece en el explorador][browser-aspnet-counter-value]
   
    Actualice el explorador periódicamente para ver el valor actualizado del contador.

## <a name="kestrel-and-weblistener"></a>Kestrel y WebListener

Es el servidor web predeterminado de ASP.NET Core, que se conoce como Kestrel, [no se admite actualmente para controlar el tráfico directo de Internet](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel). Como resultado, la plantilla de servicio sin estado de ASP.NET Core para Service Fabric usa [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) de forma predeterminada. 

Para más información sobre Kestrel y WebListener en los servicios de Service Fabric, vea [ASP.NET Core en Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="connecting-to-a-reliable-actor-service"></a>Conexión a un servicio de Reliable Actor
Este tutorial se centra en la incorporación de un front-end web que se comunica con un servicio con estado. Sin embargo, puede seguir un modelo muy parecido a hablar con los actores. Cuando se crea un proyecto de Reliable Actor, Visual Studio genera automáticamente un proyecto de interfaz. Puede usar esta interfaz para generar un proxy de actor en el proyecto web para comunicarse con el actor. El canal de comunicación se proporciona automáticamente. Por tanto, no hay que realizar ninguna acción equivalente a establecer una instancia de `ServiceRemotingListener` como se hizo para el servicio con estado en este tutorial.

## <a name="how-web-services-work-on-your-local-cluster"></a>Cómo funcionan los servicios web en el clúster local
En general, puede implementar exactamente la misma aplicación de Service Fabric en un clúster de varias máquinas que en el clúster local y tener la completa seguridad de que funcionará como se espera. El motivo es que el clúster local es simplemente una configuración de cinco nodos que se consolida en una sola máquina.

En cuanto a los servicios web, de todas formas, existe un matiz clave. Cuando el clúster se encuentra detrás de un equilibrador de carga, como sucede en Azure, tiene que asegurarse de que los servicios web se implementan en todas las máquinas, ya que el equilibrador de carga simplemente realiza un enrutamiento del tráfico por turnos entre las máquinas. Para ello, establezca `InstanceCount` para el servicio en el valor especial de -1.

Por el contrario, cuando se ejecuta un servicio web localmente, debe asegurarse de que solo una instancia del servicio se esté ejecutando. En caso contrario, se encontrará con problemas debido a que hay varios procesos que escuchan en la misma ruta de acceso y puerto. Como resultado, el recuento de instancias de servicio web debe establecerse en "1" para implementaciones locales.

Para obtener información sobre cómo configurar valores diferentes para diferentes entorno, consulte [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un front-end web configurado para la aplicación con ASP.NET Core, obtenga más información sobre [ASP.NET Core en Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) para una descripción detallada de cómo funciona ASP.NET Core con Service Fabric.

Después, [obtenga más información sobre la comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md) en general para obtener una imagen completa de cómo funciona la comunicación con el servicio en Service Fabric.

Una vez que haya entendido bien el funcionamiento de la comunicación del servicio, [cree un clúster en Azure e implemente la aplicación en la nube](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
