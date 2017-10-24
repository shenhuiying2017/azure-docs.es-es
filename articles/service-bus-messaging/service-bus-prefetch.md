---
title: Captura previa de mensajes de Azure Service Bus | Microsoft Docs
description: Mejore el rendimiento mediante la captura previa de mensajes de Azure Service Bus.
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: sethm
ms.openlocfilehash: 4a4a06f90c2c48d35d836f0be89fec9cc47f32c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="prefetch-azure-service-bus-messages"></a>Captura previa de mensajes de Azure Service Bus

Cuando está habilitada la opción *Captura previa* en alguno de los clientes oficiales de Service Bus, el receptor adquiere silenciosamente más mensajes, hasta el límite de la propiedad [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount), más allá de lo que ha solicitado inicialmente la aplicación.

Una sola llamada inicial a los métodos [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) o [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) adquiere, por tanto, un mensaje para su consumo inmediato que se devuelve tan pronto como sea disponible. El cliente después adquiere más mensajes en segundo plano, para llenar el búfer de captura previa.

## <a name="enable-prefetch"></a>Habilitación de la captura previa

En. NET, puede habilitar la característica de captura previa estableciendo la propiedad [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) de un método **MessageReceiver**, **QueueClient** o **SubscriptionClient** en un número mayor que cero. Si se establece el valor en cero, se desactivará la captura previa.

Puede agregar fácilmente esta configuración en el receptor de la configuración de los ejemplos [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) o [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) para ver el efecto de estos contextos.

Aunque los mensajes están disponibles en el búfer de captura previa, las llamadas **Receive**/**ReceiveAsync** subsiguientes se realizan directamente desde el búfer, y este se repone en segundo plano a medida que haya espacio disponible. Si no hay ningún mensaje disponible para la entrega, la operación de recepción vacía el búfer y, después, lo espera o bloquea, como esté previsto.

La captura previa también funciona de la misma manera con las API [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) y [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync).

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Si es más rápida, ¿por qué la captura previa no es la opción predeterminada?

La captura previa acelera el flujo de mensajes al hacer que el mensaje esté fácilmente disponible para la recuperación local si la aplicación lo solicita y antes de ello. Esta mejora de rendimiento es el resultado de una toma de decisión sobre las ventajas y desventajas que el autor de la aplicación debe realizar explícitamente:

Con modo de recepción [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete), ya no están disponibles en la cola todos los mensajes que se adquieren en el búfer de captura previa y solo se encuentran en el búfer de captura previa en memoria hasta que se reciben en la aplicación a través de las API **Receive**/**ReceiveAsync** o **OnMessage**/**OnMessageAsync**. Si la aplicación finaliza antes de que se reciban los mensajes en la aplicación, estos se pierden de manera permanente.

En el modo de recepción [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock), los mensajes capturados en el búfer de captura previa se adquieren en el búfer en un estado bloqueado y se selecciona el reloj de tiempo de espera para el bloqueo. Si el búfer de captura previa es grande y el procesamiento tarda tanto tiempo que expiran los bloqueos de ese mensaje mientras reside en el búfer de captura previa o incluso mientras la aplicación está procesando el mensaje, puede haber algunos eventos confusos que aplicación tenga que controlar.

La aplicación podría adquirir un mensaje con un bloqueo que haya expirado o que esté a punto a punto de expirar. Si es así, la aplicación puede procesar el mensaje, pero, después, encuentra que no puede completarlo debido a la expiración de un bloqueo. La aplicación puede comprobar la propiedad [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) (que está sujeta a desfases del reloj entre el reloj del agente y de la máquina local). Si el bloqueo del mensaje ha expirado, la aplicación debe ignorar el mensaje; no debe realizarse ninguna llamada de API en el mensaje o con él. Si el mensaje no ha expirado pero la expiración es inminente, el bloqueo se puede renovar y ampliar durante otro período de bloqueo predeterminado mediante una llamada a [message. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_).

Si el bloqueo expira en modo silencioso en el búfer de captura previa, el mensaje se trata como abandonado y de nuevo estará disponible para su recuperación desde la cola. Esto puede provocar que se capture en el búfer de captura previa; colocado al final. Si no se suele trabajar en el búfer de captura previa durante la expiración de los mensajes, esto provoca que los mensajes se capturen de manera previa repetidamente pero que nunca se entreguen de manera eficaz en un estado utilizable (legítimamente bloqueado) y finalmente se mueven en la cola de mensajes fallidos una vez que se ha superado el número máximo de entregas.

Si necesita un alto grado de confiabilidad para procesar los mensajes y el procesamiento requiere un trabajo y tiempo considerable, se recomienda usar la característica de captura previa de manera conservadora o no utilizarla en absoluto.

Si necesita un elevado rendimiento y el procesamiento de mensajes es normalmente barato, la captura previa aporta ventajas de rendimiento significativas.

El número máximo de captura previa y la duración del bloqueo configurado en la cola o suscripción deben equilibrarse de forma que el tiempo de espera del bloqueo supere al menos el tiempo de procesamiento de mensajes acumulativo para el tamaño máximo del búfer de captura previa, además de un mensaje. Al mismo tiempo, el tiempo de espera de bloqueo no debe ser tan prolongado que los mensajes superen su método [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) máximo cuando se quitan accidentalmente, lo que requiere que su bloqueo expire antes de que se vaya a volver a entregar.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
