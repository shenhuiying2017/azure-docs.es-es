---
title: Tutorial de Azure Service Bus WCF Relay | Microsoft Docs
description: "Compile una aplicación cliente y servicio con WCF Relay."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: a0b06c32cf5f154cf5eb01842d9b917dcb35f7b3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="azure-wcf-relay-tutorial"></a>Tutorial de Azure WCF Relay

En este tutorial se describe cómo crear un servicio y una aplicación cliente de WCF Relay sencillos mediante Azure Relay. Para ver un tutorial parecido donde se usa [Mensajería de Service Bus](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), consulte [Introducción a las colas de Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Al trabajar con este tutorial adquirirá unos conocimientos sobre los pasos necesarios para crear una aplicación cliente y de servicio de WCF Relay. Como sus homólogos WCF originales, un servicio es una construcción que expone uno o varios puntos de conexión, cada uno de los cuales expone una o varias operaciones de servicio. El extremo de un servicio especifica una dirección donde se puede encontrar el servicio, un enlace que contiene la información que un cliente debe comunicar al servicio y un contrato que define la funcionalidad que ofrece el servicio a sus clientes. La diferencia principal entre WCF y WCF Relay es que el punto de conexión se expone en la nube en lugar de localmente en su equipo.

Cuando complete la secuencia de temas de este tutorial, tendrá un servicio en ejecución y un cliente que puede invocar las operaciones de este servicio. En el primer tema se describe cómo configurar una cuenta. Los pasos siguientes explican cómo definir un servicio que usa un contrato, cómo implementar dicho servicio y cómo configurarlo en el código. También se describe cómo se hospeda y ejecuta el servicio. El servicio que se crea se hospeda a sí mismo y el cliente y el servicio se ejecutan en el mismo equipo. Puede configurar el servicio mediante código o con un archivo de configuración.

En los últimos tres pasos se describe cómo crear una aplicación cliente, cómo configurarla y cómo crear y usar un cliente que pueda tener acceso a la funcionalidad del host.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* [Microsoft Visual Studio 2015 o una versión superior](http://visualstudio.com). En este tutorial se usa Visual Studio 2017.
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/free/).

## <a name="create-a-service-namespace"></a>Creación de un espacio de nombres de servicio

El primer paso consiste en crear un espacio de nombres y obtener una clave de [firma de acceso compartido (SAS)](../service-bus-messaging/service-bus-sas.md). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través del servicio de retransmisión. El sistema genera una clave SAS automáticamente cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres de servicio y la clave SAS proporciona las credenciales para que Azure autentique el acceso a una aplicación. Siga las [instrucciones a continuación](relay-create-namespace-portal.md) para crear un espacio de nombres de retransmisión.

## <a name="define-a-wcf-service-contract"></a>Definición de un contrato de servicio de WCF

El contrato de servicio especifica las operaciones (la terminología del servicio web para funciones o métodos).que admite el servicio. Los contratos se crean mediante la definición de una interfaz de C++, C# o Visual Basic. Cada método de la interfaz corresponde a una operación de servicio específica. Todas las interfaces deben tener el atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) aplicado, y todas las operaciones deben tener el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) aplicado. Si un método en una interfaz que tiene el atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) no tiene el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), no se expone dicho método. El código para estas tareas se ofrece en el ejemplo a continuación del procedimiento. Para leer una descripción completa de los contratos y servicios, vea [Diseño e implementación de servicios](https://msdn.microsoft.com/library/ms729746.aspx), en la documentación de WCF.

### <a name="create-a-relay-contract-with-an-interface"></a>Creación de un contrato de retransmisión con una interfaz

1. Abra Visual Studio como administrador, para lo que debe hacer clic con el botón derecho en el programa del menú **Inicio** y, después, haga clic en **Ejecutar como administrador**.
2. Cree un nuevo proyecto de aplicación de consola. Haga clic en el menú **Archivo** y seleccione **Nuevo**; a continuación, haga clic en **Proyecto**. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C#** (si **Visual C#** no aparece, mire en **Otros lenguajes**). Haga clic en la plantilla **Aplicación de consola (.NET Framework)** y asígnele el nombre **EchoService**. Haga clic en **Aceptar** para crear el proyecto.

    ![][2]

3. Instale el paquete NuGet de Service Bus. Este paquete agrega automáticamente referencias a las bibliotecas de Service Bus, así como a **System.ServiceModel** de WCF. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) es el espacio de nombres que permite el acceso mediante programación a las características básicas de WCF. Service Bus utiliza muchos de los objetos y atributos de WCF para definir contratos de servicio.

    En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, luego, en **Administrar paquetes NuGet**. Haga clic en la pestaña **Examinar** y, después, busque **WindowsAzure.ServiceBus**. Asegúrese de que el nombre del proyecto está seleccionado en el cuadro **Versiones**. Haga clic en **Instalar**y acepte las condiciones de uso.

    ![][3]
4. En el Explorador de soluciones, haga doble clic en el archivo Program.cs para abrirlo en el editor en caso de que no esté ya abierto.
5. Agregue las siguientes instrucciones using en la parte superior del archivo:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Cambie el nombre de espacio de nombres de su nombre predeterminado **EchoService** a **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > En este tutorial se usa el espacio de nombres de C# **Microsoft.ServiceBus.Samples**, que es el espacio de nombres del tipo administrado por contrato que se utiliza en el archivo de configuración del paso [Configurar el cliente de WCF](#configure-the-wcf-client). Puede especificar el espacio de nombres que quiera al compilar este ejemplo; no obstante, el tutorial no funcionará a menos que también modifique los espacios de nombres del contrato y el servicio de manera acorde, en el archivo de configuración de la aplicación. El espacio de nombres especificado en el archivo App.config debe ser el mismo que el que se especifique en los archivos de C#.
   >
   >
7. Inmediatamente después de la declaración del espacio de nombres `Microsoft.ServiceBus.Samples`, pero dentro del espacio de nombres, defina una nueva interfaz denominada `IEchoContract` y aplique el atributo `ServiceContractAttribute` a la interfaz con un valor de espacio de nombres de `http://samples.microsoft.com/ServiceModel/Relay/`. El valor del espacio de nombres difiere del espacio de nombres que utiliza en todo el ámbito de su código. En su lugar, el valor del espacio de nombres se usa como identificador único para este contrato. La especificación del espacio de nombres impide explícitamente que el valor del espacio de nombres predeterminado se agregue al nombre del contrato. Pegue el siguiente fragmento de código después de la declaración de espacios de nombres:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Normalmente, el espacio de nombres del contrato de servicio contiene un esquema de nomenclatura que incluye información de versión. Al incluirse la información de versión en el espacio de nombres del contrato de servicio, los servicios pueden aislar los cambios más importantes mediante la definición de un nuevo contrato de servicio con un nuevo espacio de nombres y su exposición en un nuevo extremo. De esta manera, los clientes pueden seguir usando el contrato de servicio anterior sin tener que actualizarse. La información de versión puede constar de una fecha o un número de compilación. Para obtener más información, vea [Control de versiones del servicio](http://go.microsoft.com/fwlink/?LinkID=180498). Para los fines de este tutorial, el esquema de nomenclatura del espacio de nombres del contrato de servicio no contiene información de versión.
   >
   >
8. En la interfaz de `IEchoContract`, declare un método para la operación sencilla que el contrato `IEchoContract` expone en la interfaz y aplique el atributo `OperationContractAttribute` al método que quiere exponer como parte del contrato público de Relay WCF de la siguiente forma:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Justo después de la definición de interfaz `IEchoContract`, declare un canal que herede de `IEchoContract` y también de la interfaz de `IClientChannel`, como se muestra a continuación:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canal es el objeto de WCF a través del cual el host y el cliente se pasan información entre sí. Más tarde, escribirá código para el canal a fin de enviar información entre las dos aplicaciones.
10. En el menú **Compilar**, haga clic en **Compilar solución** o presione **Ctrl+Mayús+B** para confirmar la precisión del trabajo realizado.

### <a name="example"></a>Ejemplo

En el ejemplo de código siguiente se muestra una interfaz básica que define un contrato de WCF Relay.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Ahora que la interfaz se ha creado, puede implementarla.

## <a name="implement-the-wcf-contract"></a>Implementación del contrato de WCF

La creación de una retransmisión de Azure requiere que primero cree el contrato, que se define con una interfaz. Para más información sobre cómo crear la interfaz, consulte el paso anterior. El siguiente paso es implementar la interfaz. Esto implica la creación de una clase denominada `EchoService` que implemente la interfaz `IEchoContract` definida por el usuario. Después de implementar la interfaz, se debe configurar con un archivo App.config. El archivo de configuración contiene la información necesaria para la aplicación, como el nombre del servicio, el nombre del contrato y el tipo de protocolo que se utiliza para comunicarse con el servicio de retransmisión. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento. Para obtener información más general sobre cómo implementar un contrato de servicio, vea [Implementación de contratos de servicio](https://msdn.microsoft.com/library/ms733764.aspx) en la documentación de WCF.

1. Cree una nueva clase denominada `EchoService` directamente después de la definición de la interfaz `IEchoContract`. La clase `EchoService` implementa la interfaz `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Al igual que otras implementaciones de interfaz, puede implementar la definición en un archivo diferente. Sin embargo, para este tutorial, la implementación se ubica en el mismo archivo que la definición de la interfaz y el método `Main`.
2. Aplique el atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) en la interfaz de `IEchoContract`. El atributo especifica el nombre del servicio y el espacio de nombres. Después de hacerlo, aparecerá la clase `EchoService` de la siguiente manera:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implemente el método `Echo` definido en la interfaz `IEchoContract` de la clase `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Haga clic en **Compilar** y luego en **Compilar solución** para confirmar la precisión del trabajo realizado.

### <a name="define-the-configuration-for-the-service-host"></a>Definición de la configuración del host de servicios

1. El archivo de configuración es muy similar a un archivo de configuración de WCF. Incluye el nombre del servicio, el punto de conexión (es decir, la ubicación que Azure Relay expone para que clientes y hosts se comuniquen entre sí) y el enlace (el tipo de protocolo que se usa para la comunicación). La principal diferencia es que el punto de conexión de servicio configurado hace referencia a un enlace [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) que no forma parte de .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) es uno de los enlaces que define el servicio.
2. En el **Explorador de soluciones**, haga doble clic en el archivo App.config para abrirlo en el editor de Visual Studio.
3. En el elemento `<appSettings>`, reemplace los marcadores de posición con el nombre de su espacio de nombres de servicio y la clave SAS que copió en el paso anterior.
4. Dentro de las etiquetas `<system.serviceModel>`, agregue un elemento `<services>`. Puede definir varias aplicaciones de retransmisión en un único archivo de configuración. Sin embargo, este tutorial solo define una.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Dentro del elemento `<services>`, agregue un elemento `<service>` para definir el nombre del servicio.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Dentro del elemento `<service>`, defina la ubicación del contrato del punto de conexión y también el tipo de enlace del extremo.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    El extremo define dónde buscará el cliente la aplicación host. Más adelante en el tutorial, se usa este paso para crear un identificador URI que expone completamente el host a través de Azure Relay. El enlace declara que se está usando TCP como el protocolo para comunicarse con el servicio de retransmisión.
7. En el menú **Compilar**, haga clic en **Compilar solución** para confirmar la precisión del trabajo realizado.

### <a name="example"></a>Ejemplo

En el código siguiente se muestra la implementación del contrato de servicio.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

En el código siguiente se muestra el formato básico del archivo App.config asociado al servicio.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-the-relay-service"></a>Hospedaje y ejecución de un servicio web básico para registrarse con el servicio de retransmisión

Este paso describe cómo ejecutar un servicio de Azure Relay.

### <a name="create-the-relay-credentials"></a>Creación de las credenciales de retransmisión

1. En `Main()`, cree dos variables para almacenar el espacio de nombres y la clave de SAS que se leen desde la ventana de la consola.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    La clave de SAS se usará más adelante para obtener acceso al proyecto. El espacio de nombres se pasa como parámetro a `CreateServiceUri` para crear un URI de servicio.
2. Con un objeto [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior), declare que usará una clave de SAS como tipo de credencial. Agregue el código siguiente directamente después del código de que se agregó en el último paso.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Creación de una dirección base para el servicio

Siguiendo el código que agregó en el último paso, cree una instancia de `Uri` para la dirección base del servicio. Este identificador URI especifica el esquema de Service Bus, el espacio de nombres y la ruta de acceso de la interfaz del servicio.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" es una abreviatura para el esquema de Service Bus e indica que se usa TCP como protocolo. Esto también se indicó anteriormente en el archivo de configuración, cuando se especificó [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) como el enlace.

Para este tutorial, el identificador URI es `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Creación y configuración del host de servicios

1. Establezca el modo de conectividad a `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    El modo de conectividad describe el protocolo que usa el servicio para comunicarse con el servicio de retransmisión; HTTP o TCP. Con la configuración predeterminada `AutoDetect`, el servicio intenta conectarse a Azure Relay a través de TCP si está disponible, y HTTP si no lo está. Tenga en cuenta que esto difiere del protocolo que especifica el servicio para la comunicación del cliente. Dicho protocolo viene determinado por el enlace utilizado. Por ejemplo, un servicio puede usar el enlace [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), que especifica que su punto de conexión se comunica con los clientes a través de HTTP. Ese mismo servicio podría especificar **ConnectivityMode.AutoDetect** para que el servicio se comunique con Azure Relay mediante TCP.
2. Cree el host del servicio, con el identificador URI creado anteriormente en esta sección.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    El host del servicio es el objeto de WCF que crea una instancia del servicio. En este caso, se le pasa el tipo de servicio que quiere crear (un tipo `EchoService`) y también a la dirección en la que quiere exponer el servicio.
3. En la parte superior del archivo Program.cs, agregue referencias a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) y [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. De nuevo en `Main()`, configure el punto de conexión para permitir el acceso público.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    En este paso se informa al servicio de retransmisión de que la aplicación se puede encontrar públicamente al examinar la fuente ATOM del proyecto. Si establece **DiscoveryType** en **privada**, un cliente podría seguir teniendo acceso al servicio. Sin embargo, el servicio no aparecería cuando buscase el espacio de nombres de Relay. En su lugar, el cliente tendría que conocer de antemano la ruta de acceso del extremo.
5. Aplique las credenciales de servicio a los extremos de servicio definidos en el archivo App.config:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Como se indicó en el paso anterior, podría haber declarado varios servicios y extremos en el archivo de configuración. Si lo hubiera hecho, este código recorrería el archivo de configuración y buscaría todos los extremos a los que se deban aplicar las credenciales. Sin embargo, para este tutorial, el archivo de configuración tiene un solo extremo.

### <a name="open-the-service-host"></a>Apertura del host de servicios

1. Abra el servicio.

    ```csharp
    host.Open();
    ```
2. Informe al usuario de que se está ejecutando el servicio y explíquele cómo cerrarlo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Cuando termine, cierre el host del servicio.

    ```csharp
    host.Close();
    ```
4. Presione **Ctrl+Mayús+B** para compilar el proyecto.

### <a name="example"></a>Ejemplo

El código de servicio completado debería tener el siguiente formato. En el código se incluye el contrato de servicio y la implementación de los pasos anteriores del tutorial. Además, hospeda el servicio en una aplicación de consola.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Crear un cliente WCF para el contrato de servicio

El siguiente paso consiste en crear una aplicación cliente y definir el contrato de servicio que se implementará en pasos posteriores. Tenga en cuenta que muchos de estos pasos son similares a los que se usaron para crear un servicio: definir un contrato, editar un archivo App.config, usar credenciales para conectarse al servicio de retransmisión, etc. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

1. Cree un nuevo proyecto en la solución actual de Visual Studio para el cliente mediante los pasos siguientes:

   1. En el Explorador de soluciones, en la misma solución que contenga el servicio, haga clic con el botón derecho en la solución actual (no el proyecto) y haga clic en **Agregar**. Después, haga clic en **Nuevo proyecto**.
   2. En el cuadro de diálogo **Agregar nuevo proyecto**, haga clic en **Visual C#** (si **Visual C#** no aparece, mire en **Otros lenguajes**), seleccione la plantilla **Aplicación de consola (.NET Framework)** y asígnele el nombre **EchoClient**.
   3. Haga clic en **Aceptar**.
      <br />
2. En el Explorador de soluciones, haga doble clic en el archivo Program.cs del proyecto **EchoClient** para abrirlo en el editor en caso de que no esté ya abierto.
3. Cambie el nombre del espacio de nombres de su nombre predeterminado de `EchoClient` a `Microsoft.ServiceBus.Samples`.
4. Instale el [paquete NuGet de Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus): en el Explorador de soluciones, haga clic con el botón derecho en el proyecto **EchoClient** y luego haga clic en **Administrar paquetes NuGet**. Haga clic en la pestaña **Examinar** y luego busque `Microsoft Azure Service Bus`. Haga clic en **Instalar**y acepte las condiciones de uso.

    ![][3]
5. Agregue una instrucción `using` para el espacio de nombres [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) en el archivo Program.cs.

    ```csharp
    using System.ServiceModel;
    ```
6. Agregue la definición del contrato de servicio al espacio de nombres, como se muestra en el ejemplo siguiente. Tenga en cuenta que esta definición es idéntica a la definición que se usa en el proyecto **Service**. Debe agregar este código en la parte superior del espacio de nombres `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Presione **Ctrl+Shift+B** para compilar el cliente.

### <a name="example"></a>Ejemplo

En el código siguiente se muestra el estado actual del archivo Program.cs en el proyecto **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar el cliente de WCF

En este paso, creará un archivo App.config para una aplicación cliente básica que accede al servicio creado anteriormente en este tutorial. Este archivo App.config define el contrato, el enlace y el nombre del extremo. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

1. En el Explorador de soluciones, en el proyecto **EchoClient**, haga doble clic en **App.config** para abrir el archivo en el editor de Visual Studio.
2. En el elemento `<appSettings>`, reemplace los marcadores de posición con el nombre de su espacio de nombres de servicio y la clave SAS que copió en el paso anterior.
3. Dentro del elemento system.serviceModel, agregue un elemento `<client>`.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    En este paso se declara que está definiendo una aplicación cliente de estilo WCF.
4. Dentro del elemento `client`, defina el nombre, el contrato y el tipo de enlace para el punto de conexión.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    En este paso se define el nombre del punto de conexión, el contrato definido en el servicio y el hecho de que la aplicación cliente use TCP para comunicarse con Azure Relay. El nombre del extremo se usa en el paso siguiente para vincular la configuración de este extremo con el identificador URI del servicio.
5. Haga clic en **Archivo** y luego en **Guardar todo**.

## <a name="example"></a>Ejemplo

En el código siguiente se muestra el archivo App.config del cliente de eco.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementación del cliente de WCF
En este paso, implementará una aplicación cliente básica que accede al servicio que creó anteriormente en este tutorial. De forma similar al servicio, el cliente realiza muchas de las mismas operaciones para acceder a Azure Relay:

1. Establece el modo de conectividad.
2. Crea el identificador URI que localiza el servicio de host.
3. Define las credenciales de seguridad.
4. Aplica a las credenciales para la conexión.
5. Abre la conexión.
6. Realiza las tareas específicas de la aplicación.
7. Cierra la conexión.

Sin embargo, una de las principales diferencias es que la aplicación cliente usa un canal para conectarse al servicio de retransmisión, mientras que el servicio utiliza una llamada a **ServiceHost**. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

### <a name="implement-a-client-application"></a>Implementación de una aplicación cliente
1. Establezca el modo de conectividad en **AutoDetect**. Agregue el código siguiente dentro del método `Main()` de la aplicación **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Defina variables para contener los valores del espacio de nombres del servicio y la clave SAS que se leen desde la consola.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Cree el identificador URI que define la ubicación del host en el proyecto de Relay.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Cree el objeto de credenciales para el extremo del espacio de nombres de servicio.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Cree el generador de canales que carga la configuración que se describe en el archivo App.config.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Un generador de canales es un objeto de WCF que crea un canal a través del cual se comunican las aplicaciones cliente y de servicio.
6. Aplique las credenciales.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Cree y abra el canal al servicio.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Escriba la interfaz de usuario básica y la funcionalidad del eco.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Tenga en cuenta que el código usa la instancia del objeto de canal como un proxy para el servicio.
9. Cierre el canal y el generador.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

## <a name="example"></a>Ejemplo

El código completado debería tener el siguiente formato y mostrar cómo crear una aplicación cliente, cómo llamar a las operaciones del servicio y cómo cerrar el cliente cuando finaliza la llamada a la operación.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

1. Presione **Ctrl+Mayús+B** para compilar la solución. Esto compila el proyecto de cliente y el proyecto de servicio que creó en los pasos anteriores.
2. Antes de ejecutar la aplicación cliente, debe asegurarse de que se está ejecutando la aplicación del servicio. En el Explorador de soluciones en Visual Studio, haga clic con el botón derecho en la solución **EchoService** y después haga clic en **Propiedades**.
3. En el cuadro de diálogo de propiedades de la solución haga clic en **Proyecto de inicio** y después haga clic en el botón **Proyectos de inicio múltiples**. Asegúrese de que **EchoService** aparece en primer lugar en la lista.
4. Defina el cuadro **Acción** para los proyectos **EchoService** y **EchoClient** en **Iniciar**.

    ![][5]
5. Haga clic en **Dependencias del proyecto**. En el cuadro **Proyectos**, seleccione **EchoClient**. En el cuadro **Depende de:**, asegúrese de que **EchoService** está seleccionado.

    ![][6]
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades**.
7. Presione **F5** para ejecutar ambos proyectos.
8. Ambas ventanas de consola se abrirán y le solicitarán el espacio de nombres. Primero debe ejecutarse el servicio, por ello, en la ventana de la consola de **EchoService** escriba el espacio de nombres y después presione **Entrar**.
9. A continuación, se le solicitará su clave de SAS. Escríbala y presione ENTRAR.

    Este un ejemplo del resultado de la ventana de consola. Tenga en cuenta que los valores que se proporcionan aquí se ofrecen solamente como un ejemplo.

    `Your Service Namespace: myNamespace``Your SAS Key: <SAS key value>`

    La aplicación del servicio imprime en la ventana de la consola la dirección en la que está escuchando, tal y como se muestra en el ejemplo siguiente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/``Press [Enter] to exit`
10. En la ventana de la consola de **EchoClient** escriba la misma información que especificó anteriormente para la aplicación de servicio. Siga los pasos anteriores para especificar los mismos valores de espacio de nombres de servicio y clave SAS para la aplicación cliente.
11. Tras especificar estos valores, el cliente abrirá un canal al servicio y se le solicitará que escriba algo de texto, como se muestra en el siguiente ejemplo de salida de consola.

    `Enter text to echo (or [Enter] to exit):`

    Escriba algo de texto para enviarlo a la aplicación de servicio y presione ENTRAR. Este texto se enviará al servicio a través de la operación de servicio de eco y aparecerá en la ventana de la consola del servicio, como se muestra en el siguiente ejemplo de salida.

    `Echoing: My sample text`

    La aplicación cliente recibe el valor devuelto de la operación `Echo`, que es el texto original, y lo imprime en la ventana de consola. A continuación se muestra un ejemplo del resultado de la ventana de consola del cliente.

    `Server echoed: My sample text`
12. Puede continuar enviando mensajes de texto desde el cliente al servicio de esta manera. Cuando haya terminado, presione ENTRAR en las ventanas de las consolas del cliente y el servicio para finalizar ambas aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se ha mostrado cómo crear un servicio y una aplicación cliente de Azure Relay mediante las funcionalidades de WCF Relay de Service Bus. Para ver un tutorial parecido donde se usa [mensajería de Service Bus](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), consulte [Introducción a las colas de Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Para más información sobre Azure Relay, consulte los siguientes temas:

* [Información general sobre la arquitectura de Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Introducción a Azure Relay](relay-what-is-it.md)
* [Uso de WCF Relay de Service Bus con .NET](relay-wcf-dotnet-get-started.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
