<properties 
    pageTitle="Compatibilidad de AMQP 1.0 con los temas y las colas con particiones de Service Bus | Microsoft Azure" 
    description="Aprenda a usar Advanced Message Queuing Protocol (AMQP) 1.0 con los temas y las colas con particiones de Service Bus." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="hillaryc;sethm"/>


# <a name="amqp-1.0-support-for-service-bus-partitioned-queues-and-topics"></a>Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio 

Azure Service Bus es compatible con Advanced Message Queuing Protocol (**AMQP**) 1.0 para las **colas y los temas con particiones de Service Bus.**

**AMQP** es un protocolo de cola de mensajes estándar abierto que permite desarrollar aplicaciones multiplataforma usando distintos lenguajes de programación. Para obtener información general sobre la compatibilidad de AMQP en Service Bus, consulte [Soporte de AMQP 1.0 en Service Bus](service-bus-amqp-overview.md).

Las **colas y los temas con particiones**, también conocidos como *entidades con particiones*, ofrecen una mayor disponibilidad, confiabilidad y rendimiento que las colas y los temas sin particiones convencionales. Para obtener más información acerca de las entidades con particiones, consulte [Entidades de mensajería con particiones](service-bus-partitioning.md).

La incorporación de AMQP 1.0 como protocolo de comunicación con las colas y los temas con particiones le permite crear aplicaciones interoperables que pueden sacar provecho a las ventajas de la mayor disponibilidad, confiabilidad y rendimiento que ofrecen las entidades con particiones de Service Bus.

Si desea obtener una guía detallada a nivel de conexión del protocolo AMQP 1.0, que describe cómo Service Bus se implementa y se basa en la especificación técnica de OASIS AMQP, consulte la [Guía del protocolo AMQP 1.0 en Service Bus de Azure y Event Hubs](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Uso de AMQP con colas con particiones

Las colas son útiles para escenarios que requieren desacoplamiento temporal, distribución de carga, equilibrio de carga y acoplamiento flexible. Con una cola, los editores envían mensajes a la cola y los consumidores reciben mensajes desde la cola, en situaciones en las que un mensaje se puede recibir solo una vez. Un buen ejemplo de esto es un sistema de inventario donde los terminales del punto de venta publican los datos en una cola en lugar de hacerlo directamente en el sistema de administración del inventario. El sistema de administración del inventario podría consumir los datos en cualquier momento para administrar la reposición de existencias. Esto tiene varias ventajas, incluido el hecho de que el sistema de administración del inventario no tiene que estar en línea en todo momento. Para más información acerca de las colas dService Bus, consulte [Creación de aplicaciones que usan colas de Service Bus](service-bus-create-queues.md). 

Una cola con particiones aumenta la disponibilidad, la confiabilidad y el rendimiento de las aplicaciones, ya que estas colas tienen particiones para distintos almacenes y agentes de mensajes.     

### <a name="create-partitioned-queues"></a>Creación de colas con particiones

Puede crear una cola con particiones mediante el [Portal de Azure clásico][] y el SDK de Service Bus. Para crear una cola con particiones, establezca la propiedad [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) en **true** en la instancia de [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). El código siguiente muestra cómo crear una cola con particiones mediante el SDK de Bus de servicio. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Envío y recepción de mensajes mediante el uso de AMQP

Puede enviar y recibir mensajes de una cola con particiones usando AMQP como protocolo; para ello, establezca la propiedad [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) en [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) tal como se muestra en el código siguiente.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Uso de AMQP con temas con particiones

Los temas son conceptualmente similares a las colas, pero pueden enrutar una copia del mismo mensaje a varias *suscripciones*. En un tema, los editores envían mensajes al tema y los consumidores reciben mensajes de las suscripciones. En el escenario de un punto de venta del sistema de inventario, los terminales publican los datos en el tema. El sistema de administración del inventario recibe mensajes desde una suscripción. Además, un sistema de supervisión puede recibir el mismo mensaje desde una suscripción diferente. Para más información sobre los temas y las suscripciones de Service Bus, consulte [Creación de aplicaciones que usan temas y suscripciones de Service Bus](service-bus-create-topics-subscriptions.md). 

Al igual que sucede con las colas, los temas con particiones aumentan aún más la disponibilidad, la confiabilidad y el rendimiento de las aplicaciones, ya que dichos temas y sus suscripciones tienen particiones en varios agentes de mensajes y almacenes de mensajería. 

### <a name="create-partitioned-topics"></a>Creación de temas con particiones

Los temas con particiones se pueden crear mediante el [Portal de Azure clásico][] y el SDK de Service Bus. Para crear un tema con particiones, establezca la propiedad [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) en **true** en la instancia de [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). El código siguiente muestra cómo crear un tema con particiones mediante el SDK de Service Bus.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Envío y recepción de mensajes mediante el uso de AMQP

Puede enviar mensajes a una suscripción a temas con particiones y recibirlos de ella usando AMQP como protocolo. Para ello, establezca la propiedad [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) en [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), como se muestra en el código siguiente.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Pasos siguientes

Consulte la siguiente información adicional para más información sobre las entidades de mensajería con particiones así como sobre AMQP.

*    [Entidades de mensajería con particiones](service-bus-partitioning.md)
*    [Advanced Message Queuing Protocol (AMQP) de OASIS, versión 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Soporte de AMQP 1.0 en Service Bus](service-bus-amqp-overview.md)
*    [Guía del protocolo AMQP 1.0 en Azure Service Bus y Event Hubs](service-bus-amqp-protocol-guide.md)
*    [Uso de la API de Java Message Service (JMS) con Service Bus y AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Uso de AMQP 1.0 con la API .NET de Service Bus](service-bus-dotnet-advanced-message-queuing.md)

[Portal de Azure clásico]: http://manage.windowsazure.com



<!--HONumber=Oct16_HO2-->


