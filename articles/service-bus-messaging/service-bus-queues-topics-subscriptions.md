---
title: "Información general sobre colas de mensajes, temas y suscripciones de Azure Service Bus | Microsoft Docs"
description: "Información general de las entidades de mensajería de Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Colas, temas y suscripciones de Service Bus

Microsoft Azure Service Bus admite un conjunto de tecnologías de middleware basadas en la nube y orientadas a mensajes, incluidas una cola de mensajes de confianza y una mensajería de publicación/suscripción duradera. Estas capacidades de mensajería asíncrona pueden considerarse como características de mensajería desacopladas que admiten la publicación-suscripción, el desacoplamiento temporal y los escenarios de equilibrio de carga mediante la carga de trabajo de mensajería de Service Bus. La comunicación desacoplada ofrece muchas ventajas; por ejemplo, los clientes y servidores pueden conectarse según sea necesario y realizar sus operaciones de forma asincrónica.

Las entidades de mensajería que forman el núcleo de las funcionalidades de mensajería de Service Bus son las colas, los temas y suscripciones y las reglas o acciones.

## <a name="queues"></a>Colas

Las colas ofrecen una entrega de mensajes según el modelo *primero en entrar, primero en salir (FIFO [PEPS])* a uno o más destinatarios de la competencia. Es decir, normalmente los receptores reciben y procesan los mensajes en el orden en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes. La principal ventaja del uso de colas es conseguir un "desacoplamiento temporal" de los componentes de la aplicación. En otras palabras, los productores (remitentes) y los consumidores (receptores) no tienen que enviar y recibir mensajes al mismo tiempo, ya que los mensajes se almacenan de forma duradera en la cola. El productor no tiene que esperar una respuesta del destinatario para continuar el proceso y el envío de más mensajes.

Una ventaja relacionada es la "nivelación de la carga", lo que permite a los productores y consumidores enviar y recibir mensajes con distintas velocidades. En muchas aplicaciones, la carga del sistema varía con el tiempo, mientras que el tiempo de procesamiento requerido por cada unidad de trabajo suele ser constante. La intermediación de productores y consumidores de mensajes con una cola implica que la aplicación consumidora solo necesita ser aprovisionada para administrar una carga promedio en lugar de una carga pico. La profundidad de la cola aumenta y se contrae a medida que varíe la carga entrante, lo que permite ahorrar dinero directamente en función de la cantidad de infraestructura requerida para dar servicio a la carga de la aplicación. A medida que aumenta la carga, se pueden agregar más procesos de trabajo para que puedan leerse desde la cola. Cada mensaje se procesa únicamente por uno de los procesos de trabajo. Es más, este equilibrio de carga basado en la extracción permite el uso óptimo de los equipos de trabajo aunque estos equipos difieran en términos de capacidad de procesamiento, ya que extraerán mensajes a su frecuencia máxima propia. Este patrón con frecuencia se denomina "patrón de consumo de competidor".

El uso de colas para intermediar entre los consumidores y productores de mensajes proporciona un acoplamiento no estricto inherente entre los componentes. Dado que los productores y consumidores no están relacionados entre sí, un consumidor puede actualizarse sin tener ningún efecto en el productor.

La creación de una cola es un proceso de varios pasos. Las operaciones de administración de las entidades de mensajería de Service Bus (colas y temas) se realizan a través la clase [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager), que se construye mediante el suministro de la dirección base del espacio de nombres de Service Bus y las credenciales de usuario. La clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) proporciona métodos para crear, enumerar y eliminar entidades de mensajería. Después de crear un objeto [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) a partir del nombre y la clave SAS y un objeto de administración del espacio de nombres de servicios, puede usar el método [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) para crear la cola. Por ejemplo:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

A continuación, puede crear un objeto de cola y una factoría de mensajes con el URI de Service Bus como argumento. Por ejemplo:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

A continuación, puede enviar mensajes a la cola. Por ejemplo, si tiene una lista de mensajes asincrónicos denominada `MessageList`, el código es similar al ejemplo siguiente:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

A continuación, recibe mensajes de la cola, como se indica a continuación:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Al usar el modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), la operación de recepción consta de una sola fase; es decir, cuando Service Bus recibe una solicitud, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que la aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como Service Bus marca el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode), la operación de recepción se convierte en una operación de dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando Service Bus recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) en el mensaje recibido. Cuando Service Bus ve la llamada **Complete**, marca el mensaje como consumido.

Si por cualquier motivo la aplicación no puede procesar el mensaje, realice la llamada al método [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) del mensaje recibido (en lugar de [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Esto permite que Service Bus desbloquee el mensaje y esté disponible para que el mismo consumidor u otro vuelvan a recibirlo. En segundo lugar, hay un tiempo de espera asociado con el bloqueo y, si la aplicación no puede procesar el mensaje antes de que el tiempo de espera del bloqueo expire (por ejemplo, si la aplicación se bloquea), Service Bus desbloquea el mensaje y hace que esté disponible para recibirse de nuevo (básicamente realizando una operación [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) de forma predeterminada).

Tenga en cuenta que, en caso de que la aplicación se bloquee después de procesar el mensaje, pero antes de que se emita la solicitud **Complete**, se volverá a enviar el mensaje a la aplicación cuando esta se reinicie. Esto se conoce como *Al menos un procesamiento*; es decir, cada mensaje se procesa como mínimo una vez. Sin embargo, en determinadas situaciones, es posible que se vuelva a entregar el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, se requiere una lógica adicional en la aplicación para detectar duplicados que se puedan conseguir de acuerdo con la propiedad **MessageId** del mensaje, que permanece constante en los intentos de entrega. Esto se conoce como procesamiento *Exactamente una vez*.

## <a name="topics-and-subscriptions"></a>Temas y suscripciones
A diferencia de las colas, en las que un solo consumidor procesa cada mensaje, los *temas* y las *suscripciones* proporcionan una forma de comunicación de uno a varios mediante un patrón de *publicación o suscripción*. Cada mensaje publicado se pone a disposición de todas las suscripciones registradas en el tema, lo que es útil para escalar a un gran número de destinatarios. Los mensajes se envían a un tema y se entregan a una o varias suscripciones asociadas, según las reglas de filtro que se pueden establecer por suscripción. Las suscripciones pueden usar filtros adicionales para restringir los mensajes que desean recibir. Los mensajes se envían a un tema de la misma manera que se envían a una cola, pero no se reciben del tema directamente. En su lugar, se reciben de las suscripciones. Una suscripción al tema se parece a una cola virtual en que recibe copias de los mensajes que se envían al tema. Los mensajes se reciben de una suscripción exactamente de la misma forma en que se reciben de una cola.

Por medio de la comparación, la funcionalidad de envío de mensajes de una cola los asigna directamente a un tema y su funcionalidad de recepción de mensajes a una suscripción. Entre otras cosas, esto significa que las suscripciones admiten los mismos patrones que se han descrito en esta misma sección con respecto a las colas: consumidor en competencia, desacoplamiento temporal, nivelación de carga y equilibrio de la carga.

La creación de un tema es similar a la creación de una cola, como se muestra en el ejemplo de la sección anterior. Cree el URI de servicio y, a continuación, use la clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) para crear el cliente de espacio de nombres. A continuación, puede crear un tema mediante el método [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_). Por ejemplo:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Después, agregue las suscripciones que desee:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Luego puede crear un cliente del tema. Por ejemplo:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Con el remitente del mensaje puede enviar mensajes al tema o recibirlos de este, como se muestra en la sección anterior. Por ejemplo:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Al igual que las colas, los mensajes se reciben de una suscripción mediante un objeto [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient), en lugar de un objeto [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient). Cree el cliente de la suscripción y pase el nombre del tema, el nombre de la suscripción y (opcionalmente) el modo de recepción como parámetros. Por ejemplo, en el caso de la suscripción **Inventory**:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Reglas y acciones
En muchos escenarios, los mensajes que tienen características específicas deben procesarse de maneras diferentes. Para permitirlo, puede configurar suscripciones para buscar los mensajes que tengan las propiedades deseadas y, después, realizar determinadas modificaciones en dichas propiedades. Mientras que las suscripciones del Service Bus ven todos los mensajes enviados al tema, solo se puede copiar un subconjunto de dichos mensajes en la cola de suscripción virtual. Esto se consigue mediante los filtros de suscripción. Dichas modificaciones se denominan *acciones de filtrado*. Al crear una suscripción, se puede incluir una expresión de filtro que opere en las propiedades del mensaje, tanto en las propiedades del sistema (por ejemplo, **Label**) como en las propiedades de la aplicación personalizada (por ejemplo, **StoreName**). La expresión de filtro SQL es opcional en este caso; sin una expresión de filtro SQL, todas las acciones de filtro definidas en una suscripción se realizarán en todos los mensajes de dicha suscripción.

En el ejemplo anterior, para filtrar los mensajes que procedan solo de **Store1**, sería preciso crear la suscripción Dashboard como sigue:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Con este filtro de suscripción activado, solo se copiarán en la cola virtual de la suscripción `Dashboard` los mensajes que tengan la propiedad `StoreName` establecida en `Store1`.

Para más información sobre los valores de filtro posibles, vea la documentación de las clases [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) y [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction). Vea también los ejemplos de [Brokered Messaging: Advanced Filters](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Mensajería asincrónica: filtros avanzados) y [filtros de temas](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters).

## <a name="next-steps"></a>Pasos siguientes
Para más información y ejemplos del uso de la mensajería de Service Bus, consulte los siguientes temas avanzados.

* [Introducción a la mensajería de Service Bus](service-bus-messaging-overview.md)
* [Tutorial de .NET de mensajería asincrónica de Service Bus](service-bus-brokered-tutorial-dotnet.md)
* [Tutorial de REST de mensajería asincrónica de Service Bus](service-bus-brokered-tutorial-rest.md)
* [Mensajería asincrónica: ejemplo de filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

