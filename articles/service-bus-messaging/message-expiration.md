---
title: "Expiración de mensajes de Azure Service Bus | Microsoft Docs"
description: "Expiración y período de vida de los mensajes de Azure Service Bus"
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
ms.date: 09/29/2017
ms.author: sethm
ms.openlocfilehash: 504010a39a4012b9a9edb60bb9a5b33ac20499c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="message-expiration-time-to-live"></a>Expiración de mensajes (período de vida)

La carga dentro de un mensaje, o un comando o una consulta que transmite un mensaje a un receptor, casi siempre está sujeta a alguna forma de fecha límite de expiración de nivel de aplicación. Después de esta fecha límite, el contenido ya no se entrega o la operación solicitada ya no se ejecuta.

En los entornos de desarrollo y pruebas en los que las colas y los temas se usan a menudo en el contexto de ejecuciones parciales de aplicaciones o partes de aplicaciones, también conviene que los mensajes de prueba perdidos se recopilen automáticamente como elementos no utilizados para que la siguiente serie de pruebas pueda comenzar de forma limpia.

La expiración de los mensajes individuales puede controlarse mediante la propiedad del sistema [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive), que especifica una duración relativa. La expiración se convierte en un instante absoluto cuando el mensaje se pone en cola en la entidad. En ese momento, la propiedad [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) toma el valor [**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Tras el instante **ExpiresAtUtc**, los mensajes se vuelven inteligibles para la recuperación. La expiración no afecta a los mensajes que están bloqueados actualmente para la entrega; esos mensajes aún se gestionan normalmente. Si el bloqueo expira o se abandona el mensaje, la expiración surte efecto inmediato.

Mientras el mensaje está bajo bloqueo, la aplicación puede estar en posesión de un mensaje que ha expirado nominalmente. Es decisión del implementador que la aplicación esté dispuesta a seguir adelante con el procesamiento o elija abandonar el mensaje.

## <a name="entity-level-expiration"></a>Expiración de nivel de entidad

Todos los mensajes enviados a una cola o tema están sujetos a una expiración predeterminada que se establece en el nivel de entidad con la propiedad [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) y que también se puede establecer en el portal durante la creación y ajustarla más adelante. La expiración predeterminada se usa con todos los mensajes enviados a la entidad donde [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) no se establece explícitamente. La expiración predeterminada también funciona como un límite superior para el valor **TimeToLive**. Los mensajes que tienen una expiración de **TimeToLive** superior al valor predeterminado se ajustan de forma silenciosa al valor **defaultMessageTimeToLive** antes de ponerse en cola.

Los mensajes expirados se pueden mover opcionalmente a una [cola de mensajes fallidos](service-bus-dead-letter-queues.md) mediante la propiedad [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) o si se marca la casilla respectiva en el portal. Si la opción se deja deshabilitada, se quitan los mensajes que han expirado. Los mensajes expirados movidos a la cola de mensajes fallidos se pueden distinguir de otros mensajes fallidos mediante la evaluación de la propiedad [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que almacena el agente en la sección de propiedades del usuario; en este caso, el valor es [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq).

En el caso mencionado anteriormente en el que el mensaje está protegido de la expiración mientras está bajo bloqueo y si la marca se establece en la entidad, el mensaje se mueve a la cola de mensajes fallidos cuando se abandone el bloqueo o este expire. Sin embargo, no se mueve si se ha liquidado correctamente, y se asume entonces que la aplicación ha podido gestionarlo correctamente, a pesar de la expiración nominal.

La combinación de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) y las colas automáticas (y transaccionales) de mensajes fallidos cuando ocurre la expiración es una valiosa herramienta para establecer confianza en si un trabajo proporcionado a un controlador o un grupo de controladores con una fecha límite se recupera para el procesamiento cuando se alcance dicha fecha límite.

Por ejemplo, imagine un sitio web que necesita ejecutar trabajos de forma confiable en un back-end con restricción de escala, y que experimenta de vez en cuando picos de tráfico o quiere aislarse contra episodios de ese back-end. En el caso normal, el controlador del lado servidor de los datos de usuario enviados inserta la información en una cola y, como consecuencia, recibe una respuesta para confirmar que se ha gestionado correctamente la transacción a una cola de respuestas. Si hay un aumento del tráfico y el controlador de back-end no puede procesar sus elementos de trabajo pendiente a tiempo, los trabajos que han expirado se devuelven a la cola de mensajes fallidos. El usuario interactivo puede recibir la notificación de que la operación solicitada tardará un poco más de lo normal, y la solicitud se puede colocar entonces en una cola diferente en una ruta de acceso de procesamiento donde el resultado final de dicho procesamiento se envía al usuario por correo electrónico. 

## <a name="temporary-entities"></a>Entidades temporales

Las colas, los temas y las suscripciones de Service Bus se pueden crear como entidades temporales, que se eliminan automáticamente cuando no se han usado durante un período de tiempo especificado.
 
La limpieza automática es útil en escenarios de desarrollo y pruebas en el que las entidades se crean dinámicamente y no se limpian tras su uso, debido a que se ha interrumpido la ejecución de la depuración o prueba. También es útil cuando una aplicación crea entidades dinámicas, como una cola de respuestas, para volver a recibir respuestas en un proceso de servidor web, o en otro objeto de duración relativamente corta donde resulta difícil limpiar esas entidades de forma confiable cuando la instancia del objeto desaparece.

La característica se habilita mediante la propiedad [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues), que se establece en el período durante el cual una entidad debe estar inactiva (sin usar) antes de que se elimine automáticamente. La duración mínima es de 5 minutos.
 
La propiedad debe establecerse mediante una operación de Azure Resource Manager o a través de las API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del cliente de .NET Framework. No se puede establecer mediante el portal.


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)