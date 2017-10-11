---
title: "Comunicación de servicio con ASP.NET Core | Microsoft Docs"
description: Aprenda a usar ASP.NET Core en Reliable Services con y sin estado.
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
ms.date: 05/02/2017
ms.author: vturecek
ms.openlocfilehash: 8ac4d409f7363e8b4ae98be659a627ac8db8d787
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core en Reliable Services de Service Fabric

ASP.NET Core es un nuevo marco de código abierto multiplataforma para crear aplicaciones conectadas a Internet modernas y basadas en la nube, como aplicaciones web, aplicaciones de IoT y back-ends móviles. 

Este artículo es una guía en profundidad sobre cómo hospedar servicios de ASP.NET Core en Reliable Services de Service Fabric mediante el conjunto de paquetes NuGet **Microsoft.ServiceFabric.AspNetCore.***.

Para ver un tutorial introductorio sobre ASP.NET Core en Service Fabric e instrucciones para configurar el entorno de desarrollo, consulte [Creación de un front-end de servicio web para una aplicación mediante ASP.NET Core](service-fabric-add-a-web-frontend.md).

En el resto de este artículo se da por supuesto que ya conoce ASP.NET Core. Si no es así, se recomienda leer los [fundamentos de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core en el entorno de Service Fabric

Aunque las aplicaciones de ASP.NET Core se pueden ejecutar en .NET Core o en la versión completa de .NET Framework, actualmente los servicios de Service Fabric solo se pueden ejecutar en esta última. Esto significa que, cuando se crea un servicio de Service Fabric de ASP.NET Core, todavía debe tener como destino la versión completa de .NET Framework.

ASP.NET Core se puede utilizar de dos maneras diferentes en Service Fabric:
 - **Implementado como archivo ejecutable invitado**. Se usa principalmente para ejecutar aplicaciones ASP.NET Core existentes en Service Fabric sin realizar ningún cambio en el código.
 - **Ejecutado dentro de una instancia de Reliable Services**. Permite una mejor integración con el entorno de ejecución de Service Fabric y permite servicios ASP.NET Core con estado.

En el resto de este artículo se explica cómo usar ASP.NET Core dentro de una instancia de Reliable Services mediante los componentes de integración de ASP.NET Core que se incluyen con el SDK de Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Hospedaje de servicios de Service Fabric

En Service Fabric, una o varias instancias o réplicas del servicio se ejecutan en un *proceso de host de servicio* (un archivo ejecutable que ejecuta el código del servicio). Aunque, como autor del servicio, usted es el propietario del proceso de host de servicio, Service Fabric lo activa y supervisa automáticamente.

Tradicionalmente, ASP.NET (hasta MVC 5) ha estado estrechamente unido a IIS a través de System.Web.dll. ASP.NET Core proporciona una separación entre el servidor web y la aplicación web. Esto permite que las aplicaciones web sean portátiles entre diferentes servidores web y también que los servidores web se *hospeden a sí mismos*, lo que significa que puede iniciar un servidor web en su propio proceso, al contrario que un proceso que pertenece a software de servidor web dedicado, como IIS. 

Para combinar un servicio de Service Fabric y ASP.NET, ya sea como ejecutable invitado o en una instancia de Reliable Services, debe ser capaz de iniciar ASP.NET dentro de su proceso de host de servicio. El autohospedaje de ASP.NET Core lo permite.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedaje de ASP.NET Core en una instancia de Reliable Services
Por lo general, las aplicaciones ASP.NET Core autohospedadas crean un elemento WebHost en el punto de entrada de una aplicación, como el método `static void Main()` en `Program.cs`. En este caso, el ciclo de vida de WebHost está ligado al del proceso.

![Hospedaje de ASP.NET Core en un proceso][0]

Sin embargo, el punto de entrada de la aplicación no es el lugar adecuado para crear un elemento WebHost en una instancia de Reliable Services, porque el punto de entrada de la aplicación solo se usa para registrar un tipo de servicio con el sistema en tiempo de ejecución de Service Fabric, para que pueda crear instancias de ese tipo de servicio. El elemento WebHost debe crearse en una instancia de Reliable Services. Dentro del proceso de host de servicio, las instancias o réplicas de servicio pueden pasar por varios ciclos de vida. 

Una instancia de Reliable Services se representa mediante la clase de servicio derivada de `StatelessService` o `StatefulService`. La pila de comunicación de un servicio se encuentra en una implementación `ICommunicationListener` en la clase de servicio. Los paquetes NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` contienen implementaciones de `ICommunicationListener` que inician y administran el elemento WebHost de ASP.NET Core para Kestrel o WebListener en una instancia de Reliable Services.

![Hospedaje de ASP.NET Core en una instancia de Reliable Services][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners en ASP.NET Core
Las implementaciones de `ICommunicationListener` para Kestrel y WebListener en los paquetes NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` tienen patrones de uso parecidos pero realizan acciones ligeramente diferentes, específicas de cada servidor web. 

Ambos agentes de escucha de comunicación proporcionan un constructor que acepta los argumentos siguientes:
 - **`ServiceContext serviceContext`**: el objeto `ServiceContext` que contiene información sobre el servicio en ejecución.
 - **`string endpointName`**: el nombre de una configuración `Endpoint` en ServiceManifest.xml. Es en este aspecto en lo que se diferencian principalmente ambos agentes de escucha de comunicación: WebListener **requiere** una configuración `Endpoint`, pero Kestrel no.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: expresión lambda que se implementa para crear y devolver `IWebHost`. Esto permite configurar `IWebHost` tal como lo haría normalmente en una aplicación de ASP.NET Core. La lambda proporciona una dirección URL que se genera automáticamente en función de las opciones de integración de Service Fabric que use y la configuración `Endpoint` que proporcione. Esa dirección URL se puede modificar o utilizar tal cual para iniciar el servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integración de Service Fabric
El paquete NuGet `Microsoft.ServiceFabric.Services.AspNetCore` incluye el método de extensión `UseServiceFabricIntegration` en `IWebHostBuilder` que agrega middleware compatible con Service Fabric. Este middleware configura la implementación `ICommunicationListener` de Kestrel o WebListener para que registre una dirección URL de servicio única con el servicio de nomenclatura de Service Fabric y después valida las solicitudes de cliente para asegurarse de que los clientes se conecten al servicio adecuado. Esto es necesario en un entorno de host compartido como Service Fabric, donde varias aplicaciones web se pueden ejecutar en la misma máquina física o virtual pero no utilizan nombres de host únicos, para evitar que los clientes se conecten por error al servicio incorrecto. Este escenario se describe con más detalle en la sección siguiente.

### <a name="a-case-of-mistaken-identity"></a>Un caso de identidad equivocada
Las réplicas de servicio, con independencia del protocolo, escuchan en una combinación IP:puerto única. Una vez que una réplica de servicio ha comenzado a escuchar en un punto de conexión IP:puerto, notifica esa dirección de punto de conexión al servicio de nomenclatura de Service Fabric, donde los clientes u otros servicios pueden detectarla. Si los servicios utilizan puertos de aplicación asignados de forma dinámica, una réplica de servicio podría casualmente utilizar el mismo punto de conexión IP:puerto que otro servicio que se encontraba antes en la misma máquina física o virtual. Esto puede hacer que un cliente se conecte por error al servicio incorrecto. Esto puede ocurrir si se produce la siguiente secuencia de eventos:

 1. Un servicio A escucha en 10.0.0.1:30000 a través de HTTP. 
 2. El cliente resuelve el servicio A y obtiene la dirección 10.0.0.1:30000.
 3. El servicio A se mueve a otro nodo.
 4. El servicio B se coloca en 10.0.0.1 y casualmente usa el mismo puerto 30000.
 5. El cliente intenta conectarse al servicio A con la dirección 10.0.0.1:30000 en caché.
 6. Ahora el cliente está conectado al servicio B sin darse cuenta de que es el servicio incorrecto.

Esto puede causar errores aleatoriamente que son difíciles de diagnosticar. 

### <a name="using-unique-service-urls"></a>Uso de direcciones URL de servicio únicas
Para evitar esto, los servicios pueden exponer un punto de conexión ante el servicio de nomenclatura con un identificador único y después validar este último durante las solicitudes de cliente. Se trata de una acción cooperativa entre servicios en un entorno de confianza sin inquilinos hostiles. No proporciona autenticación de servicio segura en un entorno con inquilinos hostiles.

En un entorno de confianza, el middleware que se agrega mediante el método `UseServiceFabricIntegration` anexa automáticamente un identificador único a la dirección que se expone ante el servicio de nomenclatura y valida ese identificador con cada solicitud. Si el identificador no coincide, el middleware devuelve inmediatamente una respuesta HTTP 410 Ya no existe.

Los servicios que utilizan un puerto asignado de forma dinámica deberían usar este middleware.

Los servicios que utilizan un puerto fijo único no tienen este problema en un entorno cooperativo. Un puerto fijo único se utiliza normalmente para servicios orientados hacia el exterior que necesitan usar un puerto conocido al que las aplicaciones cliente puedan conectarse. Por ejemplo, la mayoría de las aplicaciones web accesibles desde Internet usarán el puerto 80 o 443 para las conexiones del explorador web. En este caso, el identificador único no se debe habilitar.

En el siguiente diagrama se muestra el flujo de la solicitud con el middleware habilitado:

![Integración de ASP.NET Core de Service Fabric][2]

Tanto las implementaciones `ICommunicationListener` de Kestrel como las de WebListener utilizan este mecanismo exactamente de la misma manera. Aunque WebListener puede diferenciar internamente las solicitudes basadas en rutas de acceso de dirección URL únicas mediante la característica subyacente para compartir puertos *http.sys*, esa funcionalidad *no* se usa en la implementación `ICommunicationListener` de WebListener, ya que se producirían códigos de estado de error HTTP 503 y HTTP 404 en el escenario descrito antes. A su vez, esto hace muy difícil que los clientes determinen la intención del error, ya que HTTP 503 y HTTP 404 ya se usan habitualmente para indicar otros errores. Por lo tanto, las implementaciones `ICommunicationListener` de Kestrel y WebListener se normalizan en el middleware proporcionado por el método de extensión `UseServiceFabricIntegration` para que los clientes solo necesiten repetir la acción de resolución del punto de conexión de servicio con las respuestas HTTP 410.

## <a name="weblistener-in-reliable-services"></a>WebListener en Reliable Services
WebListener puede utilizarse en una instancia de Reliable Services si se importa el paquete NuGet **Microsoft.ServiceFabric.AspNetCore.WebListener**. Este paquete contiene `WebListenerCommunicationListener`, una implementación de `ICommunicationListener`, que le permite crear un elemento WebHost de ASP.NET Core dentro de una instancia de Reliable Services con WebListener como servidor web.

WebListener se basa en la [API de servidor HTTP de Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Utiliza el controlador de kernel *http.sys* que IIS usa para procesar solicitudes HTTP y enrutarlas a procesos que ejecutan aplicaciones web. Esto permite que varios procesos en la misma máquina física o virtual hospeden aplicaciones web en el mismo puerto, eliminando la ambigüedad con un nombre de host o una ruta de acceso de dirección URL únicos. Estas características son útiles en Service Fabric para hospedar varios sitios web en el mismo clúster.

En el siguiente diagrama se muestra cómo WebListener usa el controlador de kernel *http.sys* en Windows para compartir puertos:

![http.sys][3]

### <a name="weblistener-in-a-stateless-service"></a>WebListener en un servicio sin estado
Para usar `WebListener` en un servicio sin estado, invalide el método `CreateServiceInstanceListeners` y devuelva una instancia de `WebListenerCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseWebListener()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="weblistener-in-a-stateful-service"></a>WebListener en un servicio con estado

Actualmente, `WebListenerCommunicationListener` no está diseñado para usarse en servicios con estado a causa de las complicaciones con la característica subyacente para compartir puertos *http.sys*. Para más información, consulte la sección siguiente sobre la asignación dinámica de puertos con WebListener. Para los servicios con estado, Kestrel es el servidor web recomendado.

### <a name="endpoint-configuration"></a>Configuración del punto de conexión

Se necesita una configuración `Endpoint` para los servidores web que usen la API de servidor HTTP de Windows, lo que incluye WebListener. Los servidores web que usan la API de servidor HTTP de Windows deben reservar en primer lugar su dirección URL con *http.sys* (normalmente con la herramienta [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)). Esta acción requiere privilegios elevados de los que sus servicios carecen de forma predeterminada. Las opciones "http" o "https" para la propiedad `Protocol` de la configuración `Endpoint` en *ServiceManifest.xml* se utilizan específicamente para indicar al entorno de ejecución de Service Fabric que registre una dirección URL con *http.sys* en su nombre mediante un prefijo de URL con [*comodín seguro*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx).

Por ejemplo, si se quiere reservar `http://+:80` para un servicio, debe usarse la siguiente configuración en ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Y se debe pasar el nombre del punto de conexión al constructor `WebListenerCommunicationListener`:

```csharp
 new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseWebListener()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-weblistener-with-a-static-port"></a>Uso de WebListener con un puerto estático
Para usar un puerto estático con WebListener, proporcione el número de puerto en la configuración `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-weblistener-with-a-dynamic-port"></a>Uso de WebListener con un puerto dinámico
Para usar un puerto asignado de forma dinámica con WebListener, omita la propiedad `Port` en la configuración `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Tenga en cuenta que un puerto dinámico asignado por una configuración `Endpoint` proporciona solo un puerto *por cada proceso de host*. El modelo de hospedaje actual de Service Fabric permite que varias instancias o réplicas de servicio se hospeden en el mismo proceso, lo que significa que cada una compartirá el mismo puerto cuando se asigne por medio de la configuración `Endpoint`. Varias instancias de WebListener pueden compartir un puerto mediante la característica subyacente para compartir puertos *http.sys*, pero eso no es compatible con `WebListenerCommunicationListener` debido a las complicaciones que presenta para las solicitudes de cliente. Para el uso de puertos dinámicos, Kestrel es el servidor web recomendado.

## <a name="kestrel-in-reliable-services"></a>Kestrel en Reliable Services
Kestrel puede utilizarse en una instancia de Reliable Services si se importa el paquete NuGet **Microsoft.ServiceFabric.AspNetCore.Kestrel**. Este paquete contiene `KestrelCommunicationListener`, una implementación de `ICommunicationListener`, que le permite crear un elemento WebHost de ASP.NET Core dentro de una instancia de Reliable Services con Kestrel como servidor web.

Kestrel es un servidor web multiplataforma para ASP.NET Core basado en libuv, una biblioteca de E/S asincrónica multiplataforma. A diferencia de WebListener, Kestrel no utiliza un administrador centralizado de puntos de conexión como *http.sys*. Y, a diferencia de WebListener, Kestrel no es compatible con el uso compartido de puertos entre varios procesos. Cada instancia de Kestrel debe usar un puerto único.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel en un servicio sin estado
Para usar `Kestrel` en un servicio sin estado, invalide el método `CreateServiceInstanceListeners` y devuelva una instancia de `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel en un servicio con estado
Para usar `Kestrel` en un servicio con estado, invalide el método `CreateServiceReplicaListeners` y devuelva una instancia de `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

En este ejemplo, se proporciona una instancia singleton de `IReliableStateManager` en el contenedor de inyección de dependencias de WebHost. Esto no es estrictamente necesario, pero sí permite usar `IReliableStateManager` y Reliable Collections en los métodos de acción de controlador MVC.

Tenga en cuenta que **no** se proporciona un nombre de configuración `Endpoint` a `KestrelCommunicationListener` en un servicio con estado. Esto se explica con más detalle en la sección siguiente.

### <a name="endpoint-configuration"></a>Configuración del punto de conexión
No se requiere una configuración `Endpoint` para usar Kestrel. 

Kestrel es un servidor web independiente simple; a diferencia de WebListener (o HttpListener), no necesita una configuración `Endpoint` en *ServiceManifest.xml* porque no requiere el registro de direcciones URL antes de comenzar. 

#### <a name="use-kestrel-with-a-static-port"></a>Uso de Kestrel con un puerto estático
Se puede configurar un puerto estático en la configuración `Endpoint` de ServiceManifest.xml para usarlo con Kestrel. Aunque esto no es estrictamente necesario, ofrece dos ventajas potenciales:
 1. Si el puerto no se encuentra en el intervalo de puertos de la aplicación, Service Fabric lo abre a través del firewall del sistema operativo.
 2. La dirección URL proporcionada a través de `KestrelCommunicationListener` usará este puerto.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Si se configura una instancia de `Endpoint`, se debe pasar su nombre al constructor `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Si no se usa una configuración `Endpoint`, se omite el nombre en el constructor `KestrelCommunicationListener`. En este caso, se utilizará un puerto dinámico. Consulte la siguiente sección para más información.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Uso de Kestrel con un puerto dinámico
Kestrel no puede usar la asignación automática de puertos de la configuración `Endpoint` en ServiceManifest.xml, porque la asignación automática de puertos de una configuración `Endpoint` asigna un puerto único por *cada proceso de host*, y un único proceso de host puede contener varias instancias de Kestrel. Puesto que Kestrel no admite el uso compartido de puertos, esto no funciona porque cada instancia de Kestrel debe abrirse en un puerto único.

Para usar la asignación dinámica de puertos con Kestrel, basta con que omita la configuración `Endpoint` en ServiceManifest.xml por completo y no pase un nombre de punto de conexión al constructor `KestrelCommunicationListener`:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

En esta configuración, `KestrelCommunicationListener` seleccionará automáticamente un puerto no utilizado en el intervalo de puertos de la aplicación.

## <a name="scenarios-and-configurations"></a>Escenarios y configuraciones
En esta sección se describen los siguientes escenarios y se proporciona la combinación recomendada de servidor web, configuración de puerto, opciones de integración de Service Fabric y diversas configuraciones para lograr un servicio que funcione correctamente:
 - Servicio sin estado de ASP.NET Core expuesto al exterior
 - Servicio sin estado de ASP.NET Core solo interno
 - Servicio con estado de ASP.NET Core solo interno

Un servicio **expuesto al exterior** es aquel que expone un punto de conexión accesible desde fuera del clúster, por lo general mediante un equilibrador de carga.

Un servicio **solo interno** es aquel cuyo punto de conexión solo es accesible desde dentro del clúster.

> [!NOTE]
> Los puntos de conexión de servicio con estado generalmente no se deberían exponer a Internet. Los clústeres que se encuentran tras equilibradores de carga que desconocen la resolución del servicio de Service Fabric, como Azure Load Balancer, no podrán exponer servicios con estado porque el equilibrador de carga no podrá localizar ni enrutar el tráfico hacia la réplica de servicio con estado adecuada. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Servicios sin estado de ASP.NET Core expuestos al exterior
WebListener es el servidor web recomendado para los servicios de front-end que exponen puntos de conexión HTTP externos orientados a Internet en Windows. Proporciona mejor protección frente a ataques y admite características incompatibles con Kestrel, como la autenticación de Windows y el uso compartido de puertos. 

Kestrel no se admite como servidor perimetral (orientado a Internet) en este momento. Se debe usar un servidor proxy inverso, como IIS o Nginx, para administrar el tráfico procedente de la Internet pública.
 
Cuando se expone a Internet, un servicio sin estado debe usar un punto de conexión conocido y estable que sea accesible a través de un equilibrador de carga. Se trata de la dirección URL que va a proporcionar a los usuarios de la aplicación. Se recomienda la siguiente configuración:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor web | WebListener | Si el servicio solo se expone a una red de confianza, como una intranet, se puede usar Kestrel. De lo contrario, WebListener es la opción preferida. |
| Configuración de puerto | estática | Se debe configurar un puerto estático conocido en la configuración `Endpoints` de ServiceManifest.xml, como 80 para HTTP o 443 para HTTPS. |
| ServiceFabricIntegrationOptions | None | La opción `ServiceFabricIntegrationOptions.None` se debe usar al configurar el middleware de integración de Service Fabric para que el servicio no intente validar solicitudes entrantes para un identificador único. Los usuarios externos de la aplicación no conocerán la información de identificación única utilizada por el middleware. |
| Recuento de instancias | -1 | En casos de uso habituales, la configuración de recuento de instancias debe establecerse en "-1" para que una instancia esté disponible en todos los nodos que reciban tráfico de un equilibrador de carga. |

Si varios servicios expuestos al exterior comparten el mismo conjunto de nodos, se debe usar una ruta de acceso de dirección URL única pero estable. Para lograr esto, se puede modificar la dirección URL proporcionada al configurar IWebHost. Tenga en cuenta que esto solo se aplica a WebListener.

 ```csharp
 new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseWebListener()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Servicio sin estado de ASP.NET Core solo interno
Los servicios sin estado a los que solo se llama desde dentro del clúster deben utilizar direcciones URL únicas y puertos asignados de forma dinámica para garantizar la cooperación entre varios servicios. Se recomienda la siguiente configuración:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor web | Kestrel | Aunque WebListener puede utilizarse para servicios sin estado internos, Kestrel es el servidor recomendado para permitir que varias instancias de servicio compartan un host.  |
| Configuración de puerto | asignado de forma dinámica | Varias réplicas de un servicio con estado pueden compartir un proceso de host o un sistema operativo host y, por tanto, necesitarán puertos únicos. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con la asignación dinámica de puertos, esta configuración evita el problema de identidad equivocada descrito antes. |
| InstanceCount | cualquiera | La configuración de recuento de instancias se puede establecer en cualquier valor necesario para hacer funcionar el servicio. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Servicio con estado de ASP.NET Core solo interno
Los servicios con estado a los que solo se llama desde dentro del clúster deben utilizar puertos asignados de forma dinámica para garantizar la cooperación entre varios servicios. Se recomienda la siguiente configuración:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor web | Kestrel | `WebListenerCommunicationListener` no está diseñado para usarse en servicios con estado en los que las réplicas comparten un proceso de host. |
| Configuración de puerto | asignado de forma dinámica | Varias réplicas de un servicio con estado pueden compartir un proceso de host o un sistema operativo host y, por tanto, necesitarán puertos únicos. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Con la asignación dinámica de puertos, esta configuración evita el problema de identidad equivocada descrito antes. |

## <a name="next-steps"></a>Pasos siguientes
[Depurar la aplicación de Service Fabric con Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
