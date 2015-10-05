<properties 
	pageTitle="Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio | Microsoft Azure" 
	description="Obtenga información sobre el uso del Advanced Message Queuing Protocol (AMQP) 1.0 con los temas y las colas con particiones del Bus de servicio." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="hillaryc" 
	editor="hillaryc"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="hillaryc"/>



# Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio 

El Bus de servicio de Azure ahora es compatible con Advanced Message Queuing Protocol (**AMQP**) 1.0 para las **colas y los temas con particiones** del Bus de servicio de Azure.

**AMQP** es un protocolo de cola de mensajes estándar que permite desarrollar aplicaciones multiplataforma usando distintos lenguajes de programación. Encontrará más información acerca de la compatibilidad general del Bus de servicio de AMQP en: [Compatibilidad de AMQP 1.0 en el Bus de servicio](service-bus-amqp-overview.md).

Las **colas y los temas con particiones**, también conocidos como "entidades con particiones", ofrecen una mayor disponibilidad, confiabilidad y rendimiento que las colas y los temas sin particiones convencionales. Encontrará más información sobre las entidades con particiones en [Particionamiento de entidades de mensajería](https://msdn.microsoft.com/library/azure/dn520246.aspx).

La incorporación de AMQP 1.0 como protocolo para comunicarse con las colas y los temas con particiones permite a los clientes crear aplicaciones interoperables que aprovechan las ventajas de la mayor disponibilidad, confiabilidad y rendimiento que ofrecen las entidades con particiones del Bus de servicio.

### Uso de AMQP con colas con particiones

Las colas son útiles para escenarios que requieren desacoplamiento temporal, distribución de carga, equilibrio de carga y acoplamiento flexible. En una cola, los editores envían mensajes a la cola y los consumidores reciben mensajes desde la cola donde un mensaje se puede recibir solo una vez. Un ejemplo clásico de esto es un sistema de inventario donde los terminales del punto de venta publican los datos en una cola en lugar de hacerlo directamente en el sistema de administración del inventario. El sistema de administración de inventario podría consumir los datos en cualquier momento para administrar la reposición de existencias. Esto tiene varias ventajas, incluido el hecho de que el sistema de administración de inventario no tiene que estar en línea en todo momento. Aquí puede obtener más información acerca de las colas del Bus de servicio: [Creación de aplicaciones que usan colas del Bus de servicio](https://msdn.microsoft.com/library/azure/hh689723.aspx)

Una cola con particiones aumentaría la disponibilidad, la confiabilidad y el rendimiento de las aplicaciones, ya que estas colas tienen particiones para distintos almacenes y agentes de mensajes.

#### Creación de colas con particiones

Puede crear una cola con particiones mediante el Portal de Azure y el SDK del Bus de servicio. Para crear una cola con particiones, la propiedad EnablePartitioning debe establecerse en true en la instancia de QueueDescription. El siguiente fragmento de código muestra cómo crear una mediante el SDK del Bus de servicio.
 
	// Create partitioned queue
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var queueDescription = new QueueDescription("myQueue");
	queueDescription.EnablePartitioning = true;
	nm.CreateQueue(queueDescription);

#### Envío y recepción de mensajes mediante AMQP

Para enviar mensajes a una cola con particiones y recibir mensajes procedentes de una cola con particiones usando AMQP como protocolo, hay que configurar TransportType como TransportType.Amqp, tal y como se muestra en el siguiente fragmento de código.

	// Sending and receiving messages to and from a Queue
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


### Uso de AMQP con temas con particiones

Al igual que las colas, los temas son útiles para escenarios que requieren desacoplamiento temporal, distribución de carga, equilibrio de carga y acoplamiento flexible. A diferencia de las colas, los temas pueden dirigir una copia del mismo mensaje a varios suscriptores. En un tema, los editores envían mensajes al tema y los consumidores reciben mensajes procedentes de las suscripciones. En el ejemplo de un sistema de inventario, los terminales de punto de venta publicarían los datos en el tema. El sistema de administración de inventario recibiría mensajes desde una suscripción. Además, un sistema de supervisión puede recibir el mismo mensaje desde una suscripción diferente. Puede obtener más información acerca de los temas del Bus de servicio aquí: [Creación de aplicaciones que usan temas y suscripciones del Bus de servicio](https://msdn.microsoft.com/library/azure/hh699844.aspx)

Un tema con particiones aumentaría la disponibilidad, la confiabilidad y el rendimiento de las aplicaciones, ya que estos temas y sus suscripciones tienen particiones para distintos almacenes y agentes de mensajes.

#### Creación de temas con particiones

Puede crear un tema con particiones mediante el Portal de Azure y el SDK del Bus de servicio. Para crear un tema con particiones, la propiedad EnablePartitioning debe establecerse en true en la instancia de TopicDescription. El siguiente fragmento de código muestra cómo crear una mediante el SDK del Bus de servicio.
	
	// Create partitioned topic
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var topicDescription = new TopicDescription("myTopic");
	topicDescription.EnablePartitioning = true;
	nm.CreateTopic(topicDescription);

	var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
	nm.CreateSubscription(subscriptionDescription);

#### Envío y recepción de mensajes mediante AMQP

Para enviar mensajes a un tema con particiones y recibir mensajes procedentes de la suscripción del tema con particiones usando AMQP como protocolo, hay que configurar TransportType como TransportType.Amqp, tal y como se muestra en el siguiente fragmento de código.

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


## Referencias

*    [Particionado de entidades de mensajería](https://msdn.microsoft.com/library/azure/dn520246.aspx)
*    [Advanced Message Queuing Protocol (AMQP) de OASIS, versión 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Soporte de AMQP 1.0 en el Bus de servicio](service-bus-amqp-overview.md)
*    [AMQP del Bus de servicio: guía para desarrolladores]("https://msdn.microsoft.com/library/azure/jj841071.aspx")
*    [Uso de la API de Java Message Service (JMS) con el Bus de servicio y AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Uso de AMQP 1.0 con la API .NET del bus de servicio](service-bus-dotnet-advanced-message-queuing.md)

<!---HONumber=Sept15_HO4-->