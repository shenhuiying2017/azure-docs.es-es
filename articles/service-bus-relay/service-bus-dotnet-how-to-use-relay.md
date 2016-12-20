---
title: Uso de WCF Relay de Service Bus | Microsoft Docs
description: "Obtenga información acerca de cómo usar el servicio de retransmisión del Bus de servicio de Azure para conectar dos aplicaciones hospedadas en diferentes ubicaciones."
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
ms.date: 09/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f7f9dc2eb6332c8f179fc35c9f746cbe5a7985e


---
# <a name="how-to-use-the-service-bus-wcf-relay-with-net"></a>Uso de WCF Relay de Service Bus con .NET
En este artículo se describe cómo usar el servicio de Retransmisión de bus de servicio. Los ejemplos se escriben en C# y usan la API de Windows Communication Foundation (WCF) con extensiones contenidas en el conjunto del bus de servicio. Para obtener más información sobre Service Bus Relay, consulte la información general de [Mensajería retransmitida de Service Bus](service-bus-relay-overview.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>¿Qué es la retransmisión de bus de servicio?
El servicio [Retransmisión* de Service Bus*](service-bus-relay-overview.md) permite crear aplicaciones híbridas que se pueden ejecutar en los centros de datos de Azure y en un entorno empresarial local. La retransmisión de bus de servicio facilita esta posibilidad, ya que le permite exponer de forma segura los servicios Windows Communication Foundation (WCF) que se encuentran en una red corporativa en la nube pública sin tener que abrir una conexión de firewall ni realizar cambios intrusivos en una infraestructura de red corporativa.

![Conceptos del relé WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

La Retransmisión de bus de servicio le permite hospedar servicios WCF en su entorno empresarial existente. A continuación, puede delegar la escucha de las sesiones entrantes y las solicitudes a esos servicios WCF al bus de servicio que se ejecuta en Azure. Esto le permite exponer esos servicios en el código de aplicación que se ejecuta en Azure o en entornos móviles de trabajo o asociados de extranet. El Bus de servicio permite controlar de forma segura quién puede obtener acceso a estos servicios en un nivel específico. Proporciona una forma segura y eficaz de exponer los datos y la funcionalidad de aplicaciones de las soluciones empresariales existentes y aprovecharlos desde la nube.

En este artículo se muestra cómo usar la Retransmisión de bus de servicio para crear un servicio web de WCF, expuesto mediante el enlace de canales TCP, que implementa una conversación segura entre dos partes.

## <a name="create-a-service-namespace"></a>Creación de un espacio de nombres de servicio
Para comenzar a usar la retransmisión de bus de servicio en Azure, primero debe crear un espacio de nombres. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtenga el paquete NuGet del bus de servicio
El [paquete NuGet del bus de servicio](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio. Realice los pasos siguientes para instalar el paquete NuGet en su proyecto:

1. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** y, a continuación, en **Administrar paquetes NuGet**.
2. Busque "Bus de servicio" y seleccione el elemento **Bus de servicio de Microsoft Azure** . Haga clic en **Instalar** para completar la instalación y, a continuación, cierre el siguiente cuadro de diálogo:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Uso de Bus de servicio para exponer y consumir un servicio web SOAP con TCP
Para exponer un servicio web WCF (SOAP) para consumo externo, debe realizar cambios en las direcciones y enlaces de los servicios. Esto puede requerir cambios en el archivo de configuración o en el código, según la forma en la que se hayan establecido y configurado los servicios WCF. Tenga en cuenta que WCF le permite disponer de varios extremos de red en el mismo servicio, por lo que puede retener los extremos internos existentes mientras agrega extremos del bus de servicio para el acceso externo al mismo tiempo.

En esta tarea, creará un servicio WCF simple y le agregará un agente de escucha del bus de servicio. En este ejercicio se presupone que está familiarizado con Visual Studio y, por lo tanto, no se proporcionarán todos los detalles relacionados con la creación de un proyecto. El ejercicio se centra en el código.

Antes comenzar a realizar estos pasos, complete el procedimiento que aparece a continuación para configurar su entorno:

1. En Visual Studio, cree una aplicación de consola que contenga dos proyectos en la solución: "Client" y "Service".
2. Agregue el paquete NuGet de Bus de servicio de Microsoft Azure a ambos proyectos. Este paquete agrega a los proyectos todas las referencias al conjunto necesarias.

### <a name="how-to-create-the-service"></a>Creación del servicio
Primero cree el servicio. Los servicios WCF cuentan con al menos tres partes distintas:

* Definición de un contrato que describe qué mensajes se intercambian y qué operaciones se van a invocar.
* Implementación del contrato establecido.
* Host que hospeda el servicio WCF y expone varios puntos de conexión.

Los ejemplos de código de esta sección están relacionados con cada uno de estos componentes.

El contrato define una única operación, `AddNumbers`, que agrega dos números y devuelve el resultado. La interfaz de `IProblemSolverChannel` permite al cliente administrar más fácilmente la duración del proxy. La creación de esta interfaz se considera una práctica recomendada. Es buena idea poner la definición del contrato en un archivo independiente para poder hacer referencia a ese archivo desde los proyectos "Client" y "Service". También puede copiar el código en los dos proyectos:

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Con el contrato en su lugar, la implementación es trivial:

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configuración de un host de servicio mediante programación
Con el contrato y la implementación en su lugar, puede hospedar el servicio. El hospedaje se produce dentro de un objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx), que se encarga de administrar las instancias del servicio y hospeda los extremos que escuchan mensajes. El código siguiente configura el servicio con un punto de conexión local normal y un punto de conexión del Bus de servicio para mostrar la apariencia, en paralelo, de los puntos de conexión internos y externos. Reemplace la cadena *namespace* por el nombre del espacio de nombres y *yourKey* por la clave SAS obtenida en el paso anterior de la configuración.

```
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

En el ejemplo, se crean dos extremos que se encuentran en la misma implementación del contrato. Uno es local y el otro se proyecta a través del Bus de servicio. Las principales diferencias entre ellos son los enlaces; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) para el local y [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) para el extremo de Service Bus y las direcciones. El extremo local dispone de una dirección de red local con un puerto distinto. El extremo del Bus de servicio cuenta con una dirección del extremo compuesta por la cadena `sb`, el nombre del espacio de nombres y la ruta "solver". Esto genera el URI `sb://[serviceNamespace].servicebus.windows.net/solver`, que identifica el extremo de servicio como extremo TCP del Bus de servicio con un nombre de DNS externo completo. Si coloca el código que reemplaza a los marcadores de posición en la función `Main` de la aplicación **Service**, tendrá un servicio funcional. Si quiere que el servicio escuche exclusivamente en el bus de servicio, quite la declaración del extremo local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configuración de un host de servicio en el archivo App.config
También puede configurar el host con el archivo App.config. El servicio que aloja el código en este caso aparece en el ejemplo siguiente.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Las definiciones del extremo se trasladan al archivo App.config. El paquete NuGet ya agregó un rango de definiciones al archivo App.config, que son las extensiones de configuración requeridas para Service Bus. El siguiente ejemplo, que es el equivalente exacto del ejemplo anterior, debe aparecer directamente debajo del elemento **system.serviceModel**. Este ejemplo de código presupone que el espacio de nombres C# del proyecto tiene el nombre de **Service**.
Reemplace los marcadores de posición por el espacio de nombres del servicio de Bus de servicio y la clave SAS.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
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
Para consumir el servicio, puede construir un cliente WCF mediante un objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). El Bus de servicio usa un modelo basado en tokens de seguridad implementado mediante SAS. La clase [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa un proveedor de tokens de seguridad con patrones de diseño Factory Method integrados que devuelven algunos proveedores de tokens conocidos. El ejemplo siguiente usa el método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para controlar la adquisición del token SAS adecuado. El nombre y la clave son los que se obtienen del portal tal y como se describió en la sección anterior.

Primero, haga referencia al código del contrato `IProblemSolver` o cópielo del servicio en el proyecto del cliente.

A continuación, reemplace el código del método `Main` del cliente, y vuelva a reemplazar el texto del marcador de posición por el espacio de nombres del Bus de servicio y la clave SAS.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

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

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Las definiciones del extremo se trasladan al archivo App.config. El siguiente ejemplo, que es el mismo que el código anterior, debe aparecer directamente debajo del elemento **system.serviceModel**. Aquí, como antes, debe reemplazar los marcadores de posición por el espacio de nombres del Bus de servicio y la clave SAS.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
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

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos del servicio Retransmisión de bus de servicio, siga estos vínculos para obtener más información.

* [Mensajería retransmitida de Service Bus](service-bus-relay-overview.md)
* [Información general sobre la arquitectura de Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Descargue ejemplos de Service Bus en [Ejemplos de Azure][Ejemplos de Azure] o vea la [información general de ejemplos de Service Bus][información general de ejemplos de Service Bus].

[Autenticación con firma de acceso compartido en Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Ejemplos de Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[información general de ejemplos de Service Bus]: ../service-bus-messaging/service-bus-samples.md



<!--HONumber=Nov16_HO3-->


