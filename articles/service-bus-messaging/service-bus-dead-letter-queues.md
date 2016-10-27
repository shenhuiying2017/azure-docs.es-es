<properties 
    pageTitle="Colas de mensajes fallidos del Bus de servicio | Microsoft Azure" 
    description="Información general de colas de mensajes fallidos del Bus de servicio de Azure" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/03/2016"
    ms.author="clemensv;sethm"/>


# <a name="overview-of-service-bus-dead-letter-queues"></a>Información general de colas de mensajes fallidos del Bus de servicio

Las colas de Service Bus y las suscripciones a temas proporcionan una subcola secundaria, llamada *cola de mensajes fallidos* (DLQ). La cola de mensajes fallidos no se necesita crear explícitamente y no se puede eliminar o administrar independientemente de la entidad principal.

La finalidad de la cola de mensajes fallidos es mantener los mensajes que no se pueden entregar a ningún destinatario o simplemente los mensajes que no se pudieron procesar. Después, los mensajes se quitan de la cola de mensajes fallidos y se inspeccionan. Una aplicación podría, con ayuda de un operador, corregir los problemas y volver a enviar el mensaje, registrar el hecho de que se produjo un error o llevar a cabo medidas correctivas. 

Desde el punto de vista de API y protocolo, la cola de mensajes fallidos es muy similar a cualquier otra cola, salvo que los mensajes solo se pueden enviar a través del gesto de mensajes fallidos de la entidad primaria. Además, no se observa el período de vida, y no puede tratar como fallido un mensaje desde una cola de mensajes fallidos. La cola de mensajes fallidos es totalmente compatible con las operaciones transaccionales y de entrega de bloqueo de información.

Tenga en cuenta que no hay limpieza automática de la cola de mensajes fallidos. Los mensajes permanecen en la cola de mensajes fallidos hasta que los recupera explícitamente de dicha cola y llama a [Complete()](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.completeasync.aspx) en el mensaje fallido.

## <a name="moving-messages-to-the-dlq"></a>Movimiento de mensajes a la cola de mensajes fallidos

Hay varias actividades en el Bus de servicio que provocan que los mensajes se inserten en la cola de mensajes fallidos desde dentro del propio motor de mensajería. Una aplicación también puede insertar mensajes explícitamente en la cola de mensajes fallidos. 

Como el agente mueve el mensaje, se agregan dos propiedades a dicho mensaje dado que el agente llama a su versión interna del método [DeadLetter](https://msdn.microsoft.com/library/azure/hh291941.aspx) en el mensaje: `DeadLetterReason` y `DeadLetterErrorDescription`.

Las aplicaciones pueden definir sus propios códigos para la propiedad `DeadLetterReason`, pero el sistema establece los valores siguientes.

| Condición                                                                                                                             | DeadLetterReason            | DeadLetterErrorDescription                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------------------------------------------------------------------------------|
| Siempre                                                                                                                                | HeaderSizeExceeded          | Se superó la cuota de tamaño de esta transmisión.                                |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions | exception.GetType().Name    | exception.Message                                                                |
| EnableDeadLetteringOnMessageExpiration                                                                                                | TTLExpiredException         | El mensaje expiró y se consideró fallido.                                       |
| SubscriptionDescription.RequiresSession                                                                                               | El identificador de sesión es null.         | La entidad habilitada por sesión no permite un mensaje cuyo identificador de sesión es null. |
| !cola de mensajes fallidos                                                                                                                    | MaxTransferHopCountExceeded | Null                                                                             |
| Mensajes fallidos explícitos de aplicación                                                                                                   | Especificado por la aplicación    | Especificado por la aplicación                                                         |

## <a name="exceeding-maxdeliverycount"></a>Superación de MaxDeliveryCount

Las colas y las suscripciones tienen las propiedades [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) y [SubscriptionDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount.aspx), respectivamente; el valor predeterminado es 10. Siempre que un mensaje se ha entregado bajo un bloqueo ([ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)), pero se ha abandonado explícitamente o el bloqueo ha expirado, la propiedad [BrokeredMessage.DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) del mensaje se incrementa. Cuando [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) supera a [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx), el mensaje se mueve a la cola de mensajes fallidos, y se especifica el código de motivo `MaxDeliveryCountExceeded`.

Este comportamiento no se puede deshabilitar, pero puede establecer [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) en un número muy grande.

## <a name="exceeding-timetolive"></a>Superación de TimeToLive

Cuando las propiedades [QueueDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration.aspx) o [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonmessageexpiration.aspx) están establecidas en **true** (el valor predeterminado es **false**), todos los mensajes que expiran se mueven a la cola de mensajes fallidos y se especifica el código de motivo `TTLExpiredException`.

Tenga en cuenta que los mensajes expirados solo se purgan y, por tanto, se mueven a la cola de mensajes fallidos cuando hay al menos un receptor activo tirando en la cola principal o suscripción; este comportamiento se produce por diseño.

## <a name="errors-while-processing-subscription-rules"></a>Errores al procesar reglas de suscripción

Cuando la propiedad [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonfilterevaluationexceptions.aspx) está habilitada en una suscripción, todos los errores que se producen mientras se ejecuta la regla de filtro SQL de una suscripción se capturan en la cola de mensajes fallidos junto con el mensaje infractor.

## <a name="application-level-dead-lettering"></a>Mensajes fallidos de nivel de aplicación

Además de las características de mensajes fallidos proporcionadas por el sistema, las aplicaciones pueden usar la cola de mensajes fallidos para rechazar explícitamente mensajes inaceptables. Esto puede incluir mensajes que no se puede procesar correctamente debido a cualquier tipo problema del sistema, mensajes que mantienen cargas con formato incorrecto o mensajes que no superan la autenticación cuando se utiliza algún esquema de seguridad de nivel de mensaje.

## <a name="example"></a>Ejemplo

El siguiente fragmento de código crea un receptor de mensajes. En el bucle de recepción de la cola principal, el código recupera el mensaje con [Receive(TimeSpan.Zero)](https://msdn.microsoft.com/library/azure/dn130350.aspx), que solicita al agente que devuelva al instante cualquier mensaje disponible, o que vuelva sin ningún resultado. Si el código recibe un mensaje, inmediatamente lo abandona, lo que incrementa `DeliveryCount`. Una vez que el sistema mueve el mensaje a la cola de mensajes fallidos, la cola principal se vacía y se sale del bucle, dado que [ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130350.aspx) devuelve **null**.

```
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

- [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Comparación de colas de Azure y colas de Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)



<!--HONumber=Oct16_HO2-->


