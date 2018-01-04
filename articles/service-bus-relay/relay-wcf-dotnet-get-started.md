---
title: "Introducción a las retransmisiones WCF de Azure Relay en .NET | Microsoft Docs"
description: "Descubra cómo utilizar las retransmisiones WCF de Azure Relay para conectar dos aplicaciones hospedadas en ubicaciones distintas."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: face684190456fbf4b78a84ac3afe7a4ead8995a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Uso de las retransmisiones WCF de Azure Relay con .NET
En este artículo se describe cómo usar el servicio Azure Relay. Los ejemplos se escriben en C# y usan la API de Windows Communication Foundation (WCF) con extensiones contenidas en el conjunto de Service Bus. Para obtener más información sobre Azure Relay, consulte la sección [Introducción a Azure Relay](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>¿Qué es Retransmisión de WCF?

El servicio [*Retransmisión de WCF*](relay-what-is-it.md) de Azure permite crear aplicaciones híbridas que se pueden ejecutar en los centros de datos de Azure y en un entorno empresarial local. El servicio de retransmisión facilita esta posibilidad, ya que le permite exponer de forma segura los servicios Windows Communication Foundation (WCF) que se encuentran en una red corporativa en la nube pública sin tener que abrir una conexión de firewall ni realizar cambios intrusivos en una infraestructura de red corporativa.

![Conceptos del relé WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Azure Relay permite hospedar servicios WCF en su entorno empresarial existente. A continuación, puede delegar la escucha de las sesiones entrantes y las solicitudes a esos servicios WCF al servicio de retransmisión que se ejecuta en Azure. Esto le permite exponer esos servicios en el código de aplicación que se ejecuta en Azure o en entornos móviles de trabajo o asociados de extranet. Relay permite controlar de forma segura quién puede obtener acceso a estos servicios en un nivel específico. Proporciona una forma segura y eficaz de exponer los datos y la funcionalidad de aplicaciones de las soluciones empresariales existentes y aprovecharlos desde la nube.

En este artículo se muestra cómo usar Azure Relay para crear un servicio web de WCF, expuesto mediante el enlace de canales TCP, que implementa una conversación segura entre dos partes.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtenga el paquete NuGet de Service Bus
El [paquete NuGet de Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la forma más sencilla de obtener la API de Service Bus y configurar su aplicación con todas las dependencias de Service Bus. Realice los pasos siguientes para instalar el paquete NuGet en su proyecto:

1. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** y, a continuación, en **Administrar paquetes NuGet**.
2. Busque "Service Bus" y seleccione el elemento **Microsoft Azure Service Bus** . Haga clic en **Instalar** para completar la instalación y, a continuación, cierre el siguiente cuadro de diálogo:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Exponga y consuma un servicio web SOAP con TCP
Para exponer un servicio web WCF (SOAP) para consumo externo, debe realizar cambios en las direcciones y enlaces de los servicios. Esto puede requerir cambios en el archivo de configuración o en el código, según la forma en la que se hayan establecido y configurado los servicios WCF. Tenga en cuenta que WCF le permite disponer de varios puntos de conexión de red en el mismo servicio, por lo que puede retener los puntos de conexión internos existentes mientras agrega puntos de conexión de retransmisión para el acceso externo al mismo tiempo.

En esta tarea, crea un servicio WCF simple y le agrega un agente de escucha de retransmisión. En este ejercicio se presupone que está familiarizado con Visual Studio y, por lo tanto, no se proporcionarán todos los detalles relacionados con la creación de un proyecto. El ejercicio se centra en el código.

Antes comenzar a realizar estos pasos, complete el procedimiento que aparece a continuación para configurar su entorno:

1. En Visual Studio, cree una aplicación de consola que contenga dos proyectos en la solución: "Client" y "Service".
2. Agregue el paquete NuGet de Service Bus a ambos proyectos. Este paquete agrega a los proyectos todas las referencias al conjunto necesarias.

### <a name="how-to-create-the-service"></a>Creación del servicio
Primero cree el servicio. Los servicios WCF cuentan con al menos tres partes distintas:

* Definición de un contrato que describe qué mensajes se intercambian y qué operaciones se van a invocar.
* Implementación de dicho contrato.
* Host que hospeda el servicio WCF y expone varios puntos de conexión.

Los ejemplos de código de esta sección están relacionados con cada uno de estos componentes.

El contrato define una única operación, `AddNumbers`, que agrega dos números y devuelve el resultado. La interfaz de `IProblemSolverChannel` permite al cliente administrar más fácilmente la duración del proxy. La creación de esta interfaz se considera una práctica recomendada. Es buena idea poner la definición del contrato en un archivo independiente para poder hacer referencia a ese archivo desde los proyectos "Client" y "Service". También puede copiar el código en los dos proyectos:

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Con el contrato en su lugar, la implementación se realiza de la manera siguiente:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configuración de un host de servicio mediante programación
Con el contrato y la implementación en su lugar, puede hospedar el servicio. El hospedaje se produce dentro de un objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), que se encarga de administrar las instancias del servicio y hospeda los extremos que escuchan mensajes. El código siguiente configura el servicio con un punto de conexión local normal y un punto de conexión de retransmisión para mostrar la apariencia, en paralelo, de los puntos de conexión internos y externos. Reemplace la cadena *namespace* por el nombre del espacio de nombres y *yourKey* por la clave SAS obtenida en el paso anterior de la configuración.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

En el ejemplo, se crean dos extremos que se encuentran en la misma implementación del contrato. Uno es local y el otro se proyecta a través de Azure Relay. Las principales diferencias entre ellos son los enlaces; [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) para el local y [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) para el punto de conexión de retransmisión y las direcciones. El extremo local dispone de una dirección de red local con un puerto distinto. El punto de conexión de retransmisión cuenta con una dirección del punto de conexión compuesta por la cadena `sb`, el nombre del espacio de nombres y la ruta "solver". Esto genera el URI `sb://[serviceNamespace].servicebus.windows.net/solver`, que identifica el punto de conexión de servicio como punto de conexión TCP de Service Bus (retransmisión) con un nombre de DNS externo completo. Si coloca el código que reemplaza a los marcadores de posición en la función `Main` de la aplicación **Service**, tendrá un servicio funcional. Si quiere que el servicio escuche exclusivamente en la retransmisión, quite la declaración del punto de conexión local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configuración de un host de servicio en el archivo App.config
También puede configurar el host con el archivo App.config. El servicio que aloja el código en este caso aparece en el ejemplo siguiente.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Las definiciones del extremo se trasladan al archivo App.config. El paquete NuGet ya agregó un rango de definiciones al archivo App.config, que son las extensiones de configuración requeridas para Azure Relay. El siguiente ejemplo, que es el equivalente exacto del ejemplo anterior, debe aparecer directamente debajo del elemento **system.serviceModel**. Este ejemplo de código presupone que el espacio de nombres C# del proyecto tiene el nombre de **Service**.
Reemplace los marcadores de posición por el nombre del espacio de nombres de retransmisión y la clave SAS.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Una vez que realice estos cambios, el servicio se iniciará como antes, pero con dos extremos activos: uno local y otro que escucha en la nube.

### <a name="create-the-client"></a>Creación del cliente
#### <a name="configure-a-client-programmatically"></a>Configuración de un cliente mediante programación
Para consumir el servicio, puede construir un cliente WCF mediante un objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Service Bus usa un modelo basado en tokens de seguridad implementado mediante SAS. La clase [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) representa un proveedor de tokens de seguridad con patrones de diseño Factory Method integrados que devuelven algunos proveedores de tokens conocidos. El ejemplo siguiente usa el método [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) para controlar la adquisición del token SAS adecuado. El nombre y la clave son los que se obtienen del portal tal y como se describió en la sección anterior.

Primero, haga referencia al código del contrato `IProblemSolver` o cópielo del servicio en el proyecto del cliente.

A continuación, reemplace el código del método `Main` del cliente y vuelva a reemplazar el texto del marcador de posición por el espacio de nombres de retransmisión y la clave SAS.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Ya puede compilar el cliente y el servicio, ejecutarlos (ejecute primero el servicio), y el cliente llamará al servicio e imprimirá **9**. Puede ejecutar el cliente y el servidor en distintas máquinas, incluso entre redes, y la comunicación seguirá funcionando. El código de cliente también puede ejecutarse en la nube o localmente.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configuración de un cliente en el archivo App.config
El siguiente código muestra cómo configurar el cliente con el archivo App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Las definiciones del extremo se trasladan al archivo App.config. El siguiente ejemplo, que es el mismo que el código anterior, debe aparecer directamente debajo del elemento `<system.serviceModel>`. Aquí, como antes, debe reemplazar los marcadores de posición por el espacio de nombres de retransmisión y la clave SAS.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>pasos siguientes
Ahora que conoce los fundamentos de los temas de Azure Relay, siga estos vínculos para obtener más información.

* [¿Qué es Relay de Azure?](relay-what-is-it.md)
* [Información general sobre la arquitectura de Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Descargue ejemplos de Service Bus en [Ejemplos de Azure][Azure samples] o consulte la [información general de ejemplos de Service Bus][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
