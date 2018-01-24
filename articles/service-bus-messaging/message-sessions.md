---
title: Sesiones de mensajes de Azure Service Bus | Microsoft Docs
description: Administre secuencias de mensajes de Azure Service Bus con sesiones.
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
ms.date: 01/02/2018
ms.author: sethm
ms.openlocfilehash: 16f641c7b6fdd1d6730d2ae229c93ce4a33b9492
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sesiones de mensajes: primero en entrar, primero en salir (FIFO) 

Las sesiones de Microsoft Azure Service Bus permiten la administración ordenada y conjunta de secuencias sin enlace de mensajes relacionados. Para realizar una garantía FIFO en Service Bus, use sesiones. Service Bus no prescribe sobre la naturaleza de la relación entre los mensajes, y tampoco define un modelo en particular para determinar dónde comienza o termina una secuencia de mensajes.

Cualquier remitente puede crear una sesión al enviar mensajes en un tema o una cola si se establece la propiedad [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) en algún identificador definido por la aplicación que sea único para la sesión. En el nivel de protocolo AMQP 1.0, este valor se asigna a la propiedad *group-id*.

En las colas o suscripciones basadas en sesiones, las sesiones existen cuando hay al menos un mensaje en [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) de la sesión. Una vez que una sesión existe, no hay ningún tiempo o API definidos para que la sesión expire o desaparezca. En teoría, se puede recibir un mensaje para una sesión hoy, el mensaje siguiente en un momento del año y, si el valor de **SessionId** coincide, la sesión es la misma desde la perspectiva de Service Bus.

Por lo general, sin embargo, una aplicación tiene una noción clara de dónde comienza o termina un conjunto de mensajes relacionados. Service Bus no establece ninguna regla específica.

Un ejemplo de cómo delinear una secuencia para transferir un archivo es establecer la propiedad **Label** del primer mensaje en **start**, de los mensajes intermedios en **content** y del último mensaje en **end**. La posición relativa de los mensajes de contenido se puede calcular como la diferencia de *SequenceNumber* del mensaje actual a partir del valor **SequenceNumber** del mensaje de *inicio*.

La característica de sesión de Service Bus permite una operación de recepción específica, en forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) en las API de Java y C#. Para habilitar la característica, se establece la propiedad [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) de la cola o la suscripción mediante Azure Resource Manager o la marca del portal. Esto es necesario antes de intentar usar las operaciones de API relacionadas.

En el portal, establezca la marca con la casilla siguiente:

![][2]

Las API de las sesiones existen en los clientes de colas y suscripciones. Hay un modelo imperativo donde controlar cuándo se reciben mensajes y sesiones, y un modelo basado en controlador, similar a *OnMessage*, que oculta la complejidad de la administración del bucle de recepción.

## <a name="session-features"></a>Características de las sesiones

Las sesiones proporcionan la demultiplexación simultánea de flujos de mensajes intercalados, al tiempo que se conserva y garantiza la entrega ordenada.

![][1]

El cliente que acepta la sesión crea un receptor [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession). El cliente llama a [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) o [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) en C#. En el modelo de devolución de llamada reactivo, se registra un controlador de sesión, tal y como se describe más adelante.

Cuando el objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) se acepta y mientras tiene lugar en el cliente, ese cliente mantiene un bloqueo exclusivo sobre todos los mensajes con ese valor de [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) de la sesión, que existe en la cola o la suscripción y también sobre todos los mensajes con ese valor de **SessionId** que sigan llegando mientras tiene lugar la sesión.

El bloqueo se libera cuando se llama a **Close** o **CloseAsync**, o cuando el bloqueo expira en caso de que la aplicación sea incapaz de llevar a cabo la operación de cerrado. El bloqueo de sesión debe tratarse como un bloqueo exclusivo en un archivo, lo que significa que la aplicación debería cerrar la sesión en cuanto ya no lo necesite o no espere ningún otro mensaje.

Cuando varios receptores simultáneos se extraen de la cola, los mensajes que pertenecen a una sesión determinada se envían al receptor específico que actualmente mantiene el bloqueo en esa sesión. Con esa operación, una secuencia de mensajes intercalados que reside en una cola o suscripción se demultiplexa limpiamente en receptores diferentes, y esos receptores también pueden encontrarse en distintas máquinas cliente, puesto que la administración del bloqueo tiene lugar en el servidor, dentro de Service Bus.

Una cola, sin embargo, sigue siendo una cola: no hay acceso aleatorio. Si varios receptores simultáneos esperan para aceptar sesiones específicas o esperan mensajes de sesiones específicas y hay un mensaje en la parte superior de una cola que pertenece a una sesión que ningún receptor todavía ha reclamado, las entregas se mantienen hasta que un receptor de la sesión reclame esa sesión.

La ilustración anterior muestra tres receptores de sesión simultáneos, los cuales deben tomar todos activamente los mensajes de la cola para que cada receptor avance. La sesión anterior con `SessionId` = 4 no tiene ningún cliente propietario activo, lo que significa que no se entregan mensajes a nadie hasta que un receptor de sesión propietario creado recientemente tome ese mensaje.

Si bien esta situación puede parecer restrictiva, un único proceso de receptor puede controlar muchas sesiones simultáneas a la vez de forma fácil, en especial cuando se escriben con código estrictamente asincrónico; apañárselas con varias docenas de sesiones simultáneas es en realidad automático con el modelo de devolución de llamada.

La estrategia para controlar muchas sesiones simultáneas, en la que cada sesión solo recibe mensajes de forma esporádica, es descartar la sesión tras algún tiempo de inactividad y reanudar el procesamiento cuando la sesión se acepte tan pronto llegue la próxima sesión.

El bloqueo de sesión mantenido por el receptor de sesión sirve de protección para los bloqueos de mensaje usados en el modo de usado en el modo de liquidación *bloqueo de inspección*. Un receptor no puede tener dos mensajes simultáneamente "en proceso", sino que los mensajes se deben procesar en orden. Solo se puede obtener un nuevo mensaje cuando el mensaje anterior ha finalizado o es fallido. Abandonar un mensaje hace que el mismo mensaje se sirva de nuevo en la siguiente operación de recepción.

## <a name="message-session-state"></a>Estado de la sesión de mensaje

Cuando se procesan flujos de trabajo en sistemas de nube de gran escala y alta disponibilidad, el controlador del flujo de trabajo asociado con una sesión determinada debe ser capaz de recuperarse de errores inesperados y también de reanudar el trabajo finalizado parcialmente en un proceso o una máquina diferentes de aquellos donde comienza el trabajo.

El servicio de estado de sesión permite una anotación definida por la aplicación de una sesión de mensajes en el agente, de modo que el estado de procesamiento registrado con respecto a esa sesión deja de estar disponible al instante cuando un nuevo procesador adquiere la sesión.

Desde el punto de vista de Service Bus, el estado de la sesión de mensajes es un objeto binario opaco que puede contener datos del tamaño de un mensaje, que es de 256 KB para Service Bus Standard y 1 MB para Service Bus Premium. El estado de procesamiento relativo a una sesión puede estar contenido en el estado de sesión, o el estado de sesión puede apuntar a alguna ubicación de almacenamiento o registro de base de datos que contiene dicha información.

Las API para administrar el estado de sesión, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) y [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), se encuentran en el objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) en las API de C# y Java. Una sesión que no tenía anteriormente un estado de sesión definido devuelve una referencia **null** para **GetState**. Para borrar el estado de sesión establecido previamente se usa [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Todas las sesiones existentes de una cola o suscripción se pueden enumerar con el método **SessionBrowser** de la API de Java y con [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) y [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) en el cliente .NET.

El estado de sesión mantenido en una cola o en que una suscripción se tiene en cuenta en la cuota de almacenamiento de esa entidad. Cuando la aplicación se termina con una sesión, se recomienda por lo tanto que dicha aplicación limpie su estado retenido para evitar costos de administración externos.

## <a name="next-steps"></a>pasos siguientes

- [Un ejemplo completo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) de enviar y recibir mensajes basados en sesión de colas de Service Bus mediante la biblioteca .NET Standard.
- [Un ejemplo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) que usa el cliente de .NET Framework para controlar los mensajes basados en la sesión. 

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png