---
title: Colas de mensajes fallidos de Service Bus | Microsoft Docs
description: "Información general de colas de mensajes fallidos del Bus de servicio de Azure"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: e5070e225387f5d4ae9d49234b4e260a57436291
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Información general de colas de mensajes fallidos del Bus de servicio

Las colas de Service Bus y las suscripciones a temas proporcionan una subcola secundaria, llamada *cola de mensajes fallidos* (DLQ). La cola de mensajes fallidos no se necesita crear explícitamente y no se puede eliminar o administrar independientemente de la entidad principal.

En este artículo se describen las colas de mensajes fallidos de Azure Service Bus. Gran parte de la descripción se muestra en el [ejemplo de colas de mensajes fallidos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) en GitHub.
 
## <a name="the-dead-letter-queue"></a>Cola de mensajes fallidos

La finalidad de la cola de mensajes fallidos es mantener los mensajes que no se pueden entregar a ningún destinatario o los mensajes que no se pudieron procesar. Después, los mensajes se quitan de la cola de mensajes fallidos y se inspeccionan. Una aplicación podría, con ayuda de un operador, corregir los problemas y volver a enviar el mensaje, registrar el hecho de que se produjo un error y llevar a cabo medidas correctivas. 

Desde el punto de vista de API y protocolo, la cola de mensajes fallidos es muy similar a cualquier otra cola, salvo que los mensajes solo se pueden enviar a través del gesto de mensajes fallidos de la entidad primaria. Además, no se observa el período de vida, y no puede tratar como fallido un mensaje desde una cola de mensajes fallidos. La cola de mensajes fallidos es totalmente compatible con las operaciones transaccionales y de entrega de bloqueo de información.

Tenga en cuenta que no hay limpieza automática de la cola de mensajes fallidos. Los mensajes permanecen en la cola de mensajes fallidos hasta que los recupera explícitamente de dicha cola y llama a [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) en el mensaje fallido.

## <a name="moving-messages-to-the-dlq"></a>Movimiento de mensajes a la cola de mensajes fallidos

Hay varias actividades en el Bus de servicio que provocan que los mensajes se inserten en la cola de mensajes fallidos desde dentro del propio motor de mensajería. Una aplicación también puede mover mensajes explícitamente a la cola de mensajes fallidos. 

Como el agente mueve el mensaje, se agregan dos propiedades a dicho mensaje dado que el agente llama a su versión interna del método [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) en el mensaje: `DeadLetterReason` y `DeadLetterErrorDescription`.

Las aplicaciones pueden definir sus propios códigos para la propiedad `DeadLetterReason`, pero el sistema establece los valores siguientes.

| Condición | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Siempre |HeaderSizeExceeded |Se superó la cuota de tamaño de esta transmisión. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |El mensaje expiró y se consideró fallido. |
| SubscriptionDescription.RequiresSession |El identificador de sesión es null. |La entidad habilitada por sesión no permite un mensaje cuyo identificador de sesión es null. |
| !cola de mensajes fallidos |MaxTransferHopCountExceeded |Null |
| Mensajes fallidos explícitos de aplicación |Especificado por la aplicación |Especificado por la aplicación |

## <a name="exceeding-maxdeliverycount"></a>Superación de MaxDeliveryCount
Las colas y las suscripciones tienen las propiedades [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) y [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount), respectivamente; el valor predeterminado es 10. Siempre que un mensaje se ha entregado bajo un bloqueo ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), pero se ha abandonado explícitamente o el bloqueo ha expirado, la propiedad [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) del mensaje se incrementa. Cuando [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) supera a [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), el mensaje se mueve a la cola de mensajes fallidos, y se especifica el código de motivo `MaxDeliveryCountExceeded`.

Este comportamiento no se puede deshabilitar, pero puede establecer [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) en un número muy grande.

## <a name="exceeding-timetolive"></a>Superación de TimeToLive
Cuando las propiedades [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) o [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) están establecidas en **true** (el valor predeterminado es **false**), todos los mensajes que expiran se mueven a la cola de mensajes fallidos y se especifica el código de motivo `TTLExpiredException`.

Tenga en cuenta que los mensajes expirados solo se purgan y, por tanto, se mueven a la cola de mensajes fallidos cuando hay al menos un receptor activo tirando en la cola principal o suscripción; este comportamiento se produce por diseño.

## <a name="errors-while-processing-subscription-rules"></a>Errores al procesar reglas de suscripción
Cuando la propiedad [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) está habilitada en una suscripción, todos los errores que se producen mientras se ejecuta la regla de filtro SQL de una suscripción se capturan en la cola de mensajes fallidos junto con el mensaje infractor.

## <a name="application-level-dead-lettering"></a>Mensajes fallidos de nivel de aplicación
Además de las características de mensajes fallidos proporcionadas por el sistema, las aplicaciones pueden usar la cola de mensajes fallidos para rechazar explícitamente mensajes inaceptables. Esto puede incluir mensajes que no se puede procesar correctamente debido a cualquier tipo problema del sistema, mensajes que mantienen cargas con formato incorrecto o mensajes que no superan la autenticación cuando se utiliza algún esquema de seguridad de nivel de mensaje.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Colas de mensajes fallidos en escenarios ForwardTo o SendVia

Los mensajes se enviarán a la cola de mensajes fallidos de transferencia en las siguientes condiciones:

- Un mensaje pasa a través de más de 3 colas o temas que están [encadenados](service-bus-auto-forwarding.md).
- La cola de destino o el tema se han deshabilitado o eliminado.
- El tema o la cola de destino superan el tamaño máximo de entidad.

Para recuperar estos mensajes fallidos, puede crear un receptor con el método de utilidad [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath).

## <a name="example"></a>Ejemplo
El siguiente fragmento de código crea un receptor de mensajes. En el bucle de recepción de la cola principal, el código recupera el mensaje con [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), que solicita al agente que devuelva al instante cualquier mensaje disponible, o que vuelva sin ningún resultado. Si el código recibe un mensaje, inmediatamente lo abandona, lo que incrementa `DeliveryCount`. Una vez que el sistema mueve el mensaje a la cola de mensajes fallidos, la cola principal se vacía y se sale del bucle, dado que [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) devuelve **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes artículos para más información sobre las colas de Bus de servicio:

* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Comparación de colas de Azure y colas de Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

