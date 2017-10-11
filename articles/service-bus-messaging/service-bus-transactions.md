---
title: "Introducción al procesamiento de transacciones en Azure Service Bus| Microsoft Docs"
description: "Información general sobre las transacciones atómicas del Bus de servicio de Azure y la característica \"enviar por\""
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: clemensv;sethm
ms.openlocfilehash: a88f2d81ab43e38c9363a67aaefc178b47bfb259
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-service-bus-transaction-processing"></a>Información general sobre el procesamiento de transacciones del Bus de servicio
En este artículo se describe las funcionalidades de transacciones del Bus de servicio de Azure. Gran parte de la discusión se ilustra mediante el [ejemplo de transacciones atómicas con Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Este artículo se limita a proporcionar información general sobre el procesamiento de transacciones y la característica *Enviar por* de Service Bus, mientras que el ejemplo de transacciones atómicas es más amplio y con un ámbito más complejo.

## <a name="transactions-in-service-bus"></a>Transacciones en el Bus de servicio
Una [transacción](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) agrupa dos o más operaciones en un *ámbito de ejecución*. Por naturaleza, una transacción de este tipo debe garantizar que todas las operaciones que pertenecen a un grupo determinado de operaciones tendrán éxito o darán error de forma conjunta. En este sentido, las transacciones actúan como una unidad, lo que a menudo se conoce como *atomicidad*. 

El Bus de servicio es un agente de mensajes transaccional, y garantiza la integridad transaccional de todas las operaciones internas en sus almacenes de mensajes. Todas las transferencias de mensajes dentro de Service Bus, como mover mensajes a una [cola de mensajes fallidos](service-bus-dead-letter-queues.md) o [reenviar automáticamente](service-bus-auto-forwarding.md) mensajes entre entidades, son transaccionales. Por lo tanto, si el Bus de servicio acepta un mensaje, es que ya se ha almacenado y etiquetado con un número de secuencia. Desde ese momento, todas las transferencias de mensajes dentro del Bus de servicio son operaciones coordinadas entre entidades, y ninguna de ellas dará lugar a la pérdida (el origen tiene éxito y el destino da error) o a la desduplicación (el origen da error y el destino tiene éxito) del mensaje.

Service Bus admite operaciones de agrupación en una sola entidad de mensajería (cola, tema, suscripción) dentro del ámbito de una transacción. Por ejemplo, puede enviar varios mensajes a una cola desde dentro de un ámbito de transacción y los mensajes solo se confirmarán en el registro de la cola cuando la transacción se complete correctamente.

## <a name="operations-within-a-transaction-scope"></a>Operaciones dentro de un ámbito de transacción
Las operaciones que pueden realizarse dentro de un ámbito de transacción son las siguientes:

* **[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender), [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient)**: Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Las operaciones de recepción no se incluyen, porque se supone que la aplicación captura mensajes mediante el modo [ReceiveMode.PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode), dentro de algún bucle de recepción o con una devolución de llamada [OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_), y solo entonces se abre un ámbito de transacción para procesar el mensaje.

La disposición del mensaje (completar, abandonar, correo devuelto, aplazar), se produce entonces dentro del ámbito del resultado general de la transacción y depende de este.

## <a name="transfers-and-send-via"></a>Transferencias y "enviar por"
Para permitir la entrega transaccional de datos de una cola a un procesador y luego a otra cola, Service Bus admite *transferencias*. En una operación de transferencia, un remitente envía primero un mensaje a una "cola de transferencia" y esta mueve inmediatamente el mensaje a la cola de destino deseada usando la misma implementación de transferencias sólida que la funcionalidad de reenvío automático en la que se basa. El mensaje nunca se confirma en el registro de la cola de transferencia de forma que se vuelve visible para los consumidores de dicha cola.

La eficacia de esta funcionalidad transaccional se hace evidente cuando la propia cola de transferencia es el origen de los mensajes de entrada del remitente. En otras palabras, el Bus de servicio puede transferir el mensaje a la cola de destino "mediante" la cola de transferencia y al mismo tiempo realizar una operación completa (o de aplazamiento o correo devuelto) en el mensaje de entrada, todo en una operación atómica. 

### <a name="see-it-in-code"></a>Verlo en el código
Para configurar tales transferencias, se crea el remitente de un mensaje que se dirige a la cola de destino mediante la cola de transferencia. También habrá un receptor que extrae los mensajes de esa misma cola. Por ejemplo:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

En una transacción sencilla, se usan estos elementos, como en el ejemplo siguiente:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para más información sobre las colas de Bus de servicio:

* [Encadenamiento de entidades de Service Bus con reenvío automático](service-bus-auto-forwarding.md)
* [Ejemplo de reenvío automático](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Ejemplo de transacciones atómicas con Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Comparación de colas de Azure y colas de Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
* [Utilización de las colas del Bus de servicio](service-bus-dotnet-get-started-with-queues.md)

