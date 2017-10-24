---
title: Examen de los mensajes de Azure Service Bus | Microsoft Docs
description: "Examen e inspección de mensajes de Service Bus"
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
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: b0bc1ef7570ccac07975e2560a1d0501d3cde2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="message-browsing"></a>Exploración de mensajes

La exploración de mensajes ("inspección") permite a un cliente enumerar todos los mensajes que residen en una cola o una suscripción, normalmente con fines de diagnóstico o depuración.

Las operaciones de inspección devuelven todos los mensajes que existen en el registro de mensajes de la cola o suscripción, no solo aquellos disponibles para la adquisición inmediata con *Receive()* o el bucle *OnMessage()*. La propiedad *State* de cada mensaje indica si el mensaje está activo (disponible para su recuperación), se ha aplazado (consulte Aplazamiento [vínculo de TBD]) o está programado (consulte Mensajes programados [vínculo de TBD]).

Los mensajes consumidos y expirados se limpian mediante la ejecución asincrónica de la "recolección de elementos no utilizados" y no necesariamente de forma exacta cuando los mensajes expiran; por lo tanto, Peek puede devolver de hecho mensajes que ya han expirado y se eliminarán o se considerarán fallidos cuando se invoque una operación de recepción en la cola o la suscripción.

Es especialmente importante tener esto en cuenta al intentar recuperar los mensajes aplazados de la cola. Un mensaje en el que el instante [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) ha pasado ya no es apto para la recuperación normal por algún otro medio, incluso cuando se devuelve mediante Peek. Estos mensajes se devuelven a propósito dado que Peek es una herramienta de diagnóstico que refleja el estado actual del registro.

Peek también devuelve los mensajes que estaban bloqueados y que actualmente están procesando otros receptores, pero que aún no se han completado. Sin embargo, como Peek devuelve una instantánea desconectada, el estado de bloqueo de un mensaje no se puede observar en los mensajes inspeccionados y las propiedades [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) y [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) producen una excepción [InvalidOperationException](/dotnet/api/system.invalidoperationexception) cuando la aplicación intenta leerlos.

## <a name="peek-apis"></a>API de Peek

Los métodos [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) y [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existen en todas las bibliotecas de cliente de .NET y Java y en todos los objetos receptores: **MessageReceiver**, **MessageSession**, **QueueClient** y **SubscriptionClient**. Peek funciona en todas las colas y suscripciones y en sus respectivas colas de mensajes fallidos.

Cuando se llama de manera repetida, el método Peek enumera todos los mensajes que existen en el registro de la cola o suscripción, en orden de número de secuencia, desde el número de secuencia más bajo disponible hasta el más alto. Este es el orden en que los mensajes se ponen en cola; no es el orden en que finalmente se podrían recuperar.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera varios mensajes y los devuelve como una enumeración. Si no hay mensajes disponibles, el objeto de enumeración está vacío, no es null.

También puede inicializar una sobrecarga del método con un valor de [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) con el que comenzar y, luego, llamar a la sobrecarga del método sin parámetros para enumerar más. **PeekBatch** funciona de forma equivalente, pero recupera un conjunto de mensajes a la vez.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)