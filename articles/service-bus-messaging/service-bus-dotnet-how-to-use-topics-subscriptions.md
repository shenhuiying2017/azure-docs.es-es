---
title: Uso de temas de Azure Service Bus con .NET | Microsoft Docs
description: "Aprenda a usar los temas y las suscripciones de Service Bus con .NET en Azure. Los ejemplos de código están escritos para aplicaciones .NET."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Uso de temas y suscripciones del Bus de servicio
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este artículo se describe cómo usar los temas y las suscripciones de Service Bus. Los ejemplos están escritos en C# y utilizan las API de .NET. Entre los escenarios tratados se incluye la creación de temas y suscripciones, la creación de filtros de suscripción, el envío de mensajes a un tema, la recepción de mensajes de una suscripción y la eliminación de temas y suscripciones. Para más información acerca de los temas y las suscripciones, consulte la sección [Pasos siguientes](#next-steps).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configuración de la aplicación para usar el bus de servicio
Cuando cree una aplicación que use el bus de servicio, deberá agregar una referencia al conjunto del bus de servicio e incluir los espacios de nombres correspondientes. La manera más fácil de hacerlo es descargar el paquete [NuGet](https://www.nuget.org) correspondiente.

## <a name="get-the-service-bus-nuget-package"></a>Obtenga el paquete NuGet del bus de servicio
El [paquete NuGet de Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la forma más sencilla de configurar una aplicación con todas las dependencias necesarias de Service Bus. Realice los pasos siguientes para instalar el paquete NuGet de Service Bus en el proyecto:

1. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** y, a continuación, en **Administrar paquetes NuGet**.
2. Haga clic en **Examinar**, busque "Azure Service Bus" y seleccione el elemento **Microsoft Azure Service Bus**. Haga clic en **Instalar** para completar la instalación y, después, cierre el cuadro de diálogo:
   
   ![][7]

De este modo ya estará listo para escribir código para el bus de servicio.

## <a name="create-a-service-bus-connection-string"></a>Creación de una cadena de conexión de Service Bus
El bus de servicio usa una cadena de conexión para almacenar extremos y credenciales. Puede poner la cadena de conexión en un archivo de configuración en vez de codificarla de forma rígida:

* Si usa servicios de Azure, es aconsejable que almacene la cadena de conexión con el sistema de configuración de servicios de Azure (archivos *.csdef y *.cscfg).
* Al usar Sitios web Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión con el sistema de configuración .NET (por ejemplo, el archivo Web.config).

En ambos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se muestra más adelante en este artículo.

### <a name="configure-your-connection-string"></a>Configuración de una cadena de conexión
El mecanismo de configuración de servicios le permite cambiar dinámicamente la configuración desde [Azure Portal][Azure portal] sin volver a implementar la aplicación. Por ejemplo, agregue una etiqueta `Setting` al archivo de definición de servicio (**.csdef**), como se indica en el siguiente ejemplo.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

A continuación, especifique los valores del archivo de configuración de servicio (.cscfg):

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Use el nombre y los valores de clave de la firma de acceso compartido (SAS) recuperados del portal, como se ha descrito anteriormente.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configuración de la cadena de conexión al usar Sitios web de Azure o Máquinas virtuales de Azure
Al usar Sitios web o Máquinas virtuales, se recomienda usar el sistema de configuración de .NET (por ejemplo, Web.config). Almacene la cadena de conexión usando el elemento `<appSettings>`.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Use el nombre y los valores de clave de SAS recuperados de [Azure Portal][Azure portal], como se ha descrito anteriormente.

## <a name="create-a-topic"></a>Creación de un tema
Puede realizar operaciones de administración en los temas y las suscripciones de Service Bus a través de la clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Esta clase proporciona métodos para crear, enumerar y eliminar temas.

En el ejemplo siguiente, se construye un objeto `NamespaceManager` mediante la clase `CloudConfigurationManager` de Azure con una cadena de conexión que consta de la dirección base de un espacio de nombres de Service Bus y las credenciales de SAS pertinentes con permisos para administrarlo. Esta cadena de conexión tiene la forma siguiente:

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Use el siguiente ejemplo con las opciones de configuración de la sección anterior.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Hay sobrecargas del método [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager) que le permiten establecer las propiedades del tema, por ejemplo, para configurar el valor predeterminado del período de vida (TTL) de forma que se aplique a los mensajes enviados al tema. Estas opciones de configuración se aplican usando la clase [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription). En el ejemplo siguiente se muestra cómo crear un tema denominado **TestTopic** con un tamaño máximo de 5 GB y un período de vida predeterminado de mensaje de un minuto.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> Puede usar el método [TopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) en los objetos [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) para comprobar si ya existe un tema con un nombre especificado en un espacio de nombres.
> 
> 

## <a name="create-a-subscription"></a>una suscripción
También puede crear suscripciones de temas con la clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). A las suscripciones se les puede asignar un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a la cola virtual de la suscripción.

> [!IMPORTANT]
> Para que una suscripción reciba mensajes, debe crear dicha suscripción antes de enviar mensajes al tema. Si no hay ninguna suscripción a un tema, este descartará los mensajes.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creación de una suscripción con el filtro predeterminado (MatchAll)
El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Si se usa el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "AllMessages" que usa el filtro predeterminado **MatchAll**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Creación de suscripciones con filtros
También puede configurar filtros que le permitan especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción a un tema determinado.

El tipo de filtro más flexible compatible con suscripciones es la clase [SqlFilter][SqlFilter], que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para más información sobre las expresiones que se pueden usar con un filtro de SQL, vea la sintaxis de [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

En el ejemplo siguiente, se crea una suscripción denominada **HighMessages** con un objeto [SqlFilter][SqlFilter] que solo selecciona los mensajes con una propiedad **MessageNumber** personalizada con un valor superior a 3.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageId > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Del mismo modo, el ejemplo siguiente crea una suscripción llamada **LowMessages** con [SqlFilter][SqlFilter] que solo selecciona los mensajes que tengan una propiedad **MessageNumber** cuyo valor sea menor o igual a 3.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageId <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Cuando se envíe un mensaje a `TestTopic`, este se entregará siempre a los receptores suscritos al tema **AllMessages**, y se entregará de forma selectiva a los receptores suscritos a los temas **HighMessages** y **LowMessages** (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Envío de mensajes a un tema
Para enviar un mensaje a un tema de Service Bus, su aplicación crea un objeto [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) mediante la cadena de conexión.

El código que aparece a continuación muestra cómo crear un objeto [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) para el tema **TestTopic** creado anteriormente con la llamada de la API [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_).

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Los mensajes enviados a los temas de Service Bus son instancias de la clase [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Los objetos **BrokeredMessage** cuentan con un conjunto de propiedades estándar (como [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) y [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor del objeto **BrokeredMessage** y, a continuación, se usará el **DataContractSerializer** adecuado para serializar el objeto. También se puede proporcionar un objeto **System.IO.Stream**.

En el ejemplo siguiente se muestra cómo enviar cinco mensajes de prueba al objeto **TopicClient** de [TestTopic](/dotnet/api/microsoft.servicebus.messaging.topicclient) obtenido en el ejemplo de código anterior. Tenga en cuenta que el valor de la propiedad [MessageNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) de cada mensaje varía en función de la iteración del bucle (así se determinará qué suscripciones lo reciben).

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

El tamaño máximo de mensaje que admiten los temas de Service Bus es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de los temas se define en el momento de la creación (el límite máximo es de 5 GB). Si está habilitada la división en particiones, el límite superior es más elevado. Para más información, consulte [Entidades de mensajería con particiones](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Recepción de mensajes de una suscripción
La forma recomendada de recibir mensajes de una suscripción es usar un objeto [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient). Los objetos **SubscriptionClient** pueden funcionar en dos modos distintos: [*ReceiveAndDelete* y *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** es el valor predeterminado.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando Service Bus recibe una solicitud de lectura de un mensaje de una suscripción, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como Service Bus ha marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock** (el predeterminado), el proceso de recepción es una operación en dos fases, lo que permite admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) en el mensaje recibido. Cuando Service Bus ve la llamada **Complete**, marca el mensaje como consumido y lo elimina de la suscripción.

En el ejemplo siguiente se muestra cómo se pueden recibir y procesar mensajes con el modo **PeekLock** predeterminado. Para especificar un valor [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) diferente, puede usar otra sobrecarga para [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_). En este ejemplo se usa la devolución de llamada [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) para procesar los mensajes conforme llegan a la suscripción **HighMessages**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Este ejemplo configura la devolución de llamada [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) usando un objeto [OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions). [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) se establece en **false** para permitir controlar manualmente cuándo llamar a [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) en el mensaje recibido. [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) se establece en un minuto, lo que hace que el cliente espere hasta un minuto antes de terminar la característica de renovación automática y que el cliente realice una nueva llamada para comprobar los mensajes. Este valor de propiedad reduce el número de veces que el cliente hace llamadas facturables que no recuperan mensajes.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer
El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realice la llamada al método [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__) del mensaje recibido (en lugar del método [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Esto hace que Service Bus desbloquee el mensaje de la suscripción y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la suscripción y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces Service Bus desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

Si la aplicación se bloquea después de procesar el mensaje y antes de emitir la solicitud [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina *Al menos un procesamiento*, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) del mensaje, que permanece constante en todos los intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminación de temas y suscripciones
En el ejemplo siguiente, se muestra cómo eliminar el tema **TestTopic** del espacio de nombres de servicio **HowToSample**.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente. El código siguiente indica cómo eliminar una suscripción llamada **HighMessages** del tema **TestTopic**.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos de los temas y las suscripciones de Service Bus, siga estos vínculos para más información.

* [Colas, temas y suscripciones][Queues, topics, and subscriptions].
* [Ejemplo de filtros de tema][Topic filters sample]
* Referencia de API para [SqlFilter][SqlFilter].
* Si quiere crear una aplicación de trabajo que envíe mensajes a una cola de Service Bus y que reciba mensajes de dicha cola, consulte [Tutorial de .NET de mensajería asíncrona de Service Bus][Service Bus brokered messaging .NET tutorial].
* Ejemplos de Service Bus: descárguelos de [Ejemplos de Azure][Azure samples] o consulte la [información general](service-bus-samples.md).

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

