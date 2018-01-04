---
title: Espacios de nombres emparejados de Azure Service Bus | Microsoft Docs
description: "Detalles de la implementación y costos de los espacios de nombres emparejados"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Detalles de implementación y costos asociados de los espacios de nombres emparejados

El método [PairNamespaceAsync][PairNamespaceAsync], que usa una instancia de [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], realiza tareas visibles en su nombre. Dado que el uso de esta característica tiene ciertos costes asociados, resulta útil conocer dichas tareas para que su comportamiento no le pille desprevenido. La API interactúa con el siguiente comportamiento automático en su nombre:

* Creación de colas de trabajos pendientes.
* Creación de un objeto [MessageSender][MessageSender] que se comunique con colas o temas.
* Cuando una entidad de mensajería deja de estar disponible, envía mensajes Ping a la entidad para intentar detectar cuándo vuelve a estar disponible dicha entidad.
* Opcionalmente, crea un conjunto de "suministros de mensajes" que mueven los mensajes de las colas de trabajos pendientes a las colas principales.
* Coordina el cierre o error de las instancias de [MessagingFactory][MessagingFactory] principal y secundaria.

A un alto nivel, la característica funciona del siguiente modo: cuando la entidad principal es correcta, se producen cambios de comportamiento. Si transcurre la duración de [FailoverInterval][FailoverInterval] y la entidad principal no ve envíos correctos después de unas excepciones [MessagingException][MessagingException] o [TimeoutException no transitorias][TimeoutException], se produce el siguiente comportamiento:

1. Las operaciones de envío a la entidad principal están deshabilitadas y el sistema hace ping a la entidad principal hasta que los pings se puedan entregar correctamente.
2. Se selecciona una cola de trabajos pendientes aleatoria.
3. Los objetos [BrokeredMessage][BrokeredMessage] se enrutan a la cola de trabajos pendientes elegida.
4. Si se produce un error en una operación de envío a la cola de trabajos pendientes elegidas, dicha cola se extrae de la rotación y se selecciona una nueva cola. Todos los remitentes de la instancia de [MessagingFactory][MessagingFactory] reciben información del error.

Las ilustraciones siguientes muestran la secuencia. En primer lugar, el remitente envía los mensajes.

![Espacios de nombres emparejados][0]

Si se produce un error en el envío a la cola principal, el remitente comienza a enviar los mensajes a una cola de trabajos pendientes elegida aleatoriamente. Simultáneamente, se inicia una tarea de ping.

![Espacios de nombres emparejados][1]

En este momento, los mensajes siguen en la cola secundaria y no se han entregado a la cola principal. Una vez que la cola principal es correcta de nuevo, al menos un proceso debe ejecutar el sifón. El sifón entrega los mensajes de las distintas colas de trabajos pendientes a las entidades de destino apropiadas (colas y temas).

![Espacios de nombres emparejados][2]

En el resto de este tema se tratan los detalles concretos del funcionamiento de estas partes.

## <a name="creation-of-backlog-queues"></a>Creación de colas de trabajos pendientes
El objeto [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] pasado al método [PairNamespaceAsync][PairNamespaceAsync] indica el número de colas de trabajos pendientes que desea usar. A continuación, se crean las colas de trabajos pendientes con las siguientes propiedades definidas explícitamente (en los restantes valores se seleccionan los valores predeterminados de [QueueDescription][QueueDescription]):

| Ruta de acceso | [espacio de nombres principal] / x-servicebus-transfer / [index] donde [index] es un valor de [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 minuto |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

Por ejemplo, la primera cola de trabajos pendientes creada para el espacio de nombres **contoso** se denomina `contoso/x-servicebus-transfer/0`.

Cuando se crean las colas, el código comprueba primero si existe dicha cola. Si la cola no existe, se crea. El código no limpiar la colas de trabajos pendientes "adicionales". En concreto, si la aplicación con el espacio de nombres principal **contoso** solicita cinco colas de trabajos pendientes, pero existe una cola con la ruta de acceso `contoso/x-servicebus-transfer/7`, dicha cola de trabajos pendientes adicional está presente pero no se usa. El sistema permite explícitamente que existan colas de trabajos pendientes adicionales que no se usan. El propietario del espacio de nombres, es el responsable de limpiar las colas de trabajos pendientes sin usar o no deseadas. La razón para tomar esta decisión es que Service Bus no puede saber qué fines tienen todas las colas del espacio de nombres. Además, si existe una cola con el nombre especificado, pero NO cumple la [QueueDescription][QueueDescription] asumida, las razones son las suyas propias para cambiar el comportamiento predeterminado. No se garantizan las modificaciones en las colas de registros pendientes realizadas por el código. Asegúrese de probar exhaustivamente los cambios.

## <a name="custom-messagesender"></a>MessageSender personalizado
Cuando se envían, todos los mensajes atraviesan un objeto [MessageSender][MessageSender] interno que se comporta con normalidad cuando todo funciona y se redirigen a las colas de trabajos pendiente cuando algo "deja de funcionar". Al recibir un error no transitorio, se inicia un temporizador. Después de un período [TimeSpan][TimeSpan] que consta del valor de la propiedad [FailoverInterval][FailoverInterval] durante el que no se envían mensajes correctos, se activa la conmutación por error. En este punto, esto es lo que ocurre en cada entidad:

* Se ejecuta una tarea de ping cada [PingPrimaryInterval][PingPrimaryInterval] para comprobar si la entidad está disponible. Una vez que esta tarea se realiza correctamente, todo el código de cliente que usa la entidad inmediatamente comienza a enviar mensajes nuevos al espacio de nombres principal.
* Las solicitudes de envío a la misma entidad que se realicen en el futuro desde otros remitentes provocarán que el [BrokeredMessage][BrokeredMessage] enviado se modifique para permanecer en la cola de trabajos pendientes. La modificación elimina algunas de las propiedades del objeto [BrokeredMessage][BrokeredMessage] y las almacena en otro lugar. Las siguientes propiedades se borran y se agregan con un nuevo alias, lo que permite que Service Bus y el SDK procesen los mensajes de manera uniforme:

| Nombre de propiedad anterior | Nombre de propiedad nuevo |
| --- | --- |
| SessionId |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

La ruta de acceso de destino original se almacena también en el mensaje como una propiedad denominada x-ms-path. Este diseño permite que coexistan mensajes de varias entidades en una única cola de trabajos pendientes. El sifón vuelve a convertir las propiedades.

El objeto [MessageSender][MessageSender] personalizado puede encontrar problemas cuando los mensajes se acerquen al límite de 256 KB y se active la conmutación por error. El objeto [MessageSender][MessageSender] personalizado almacena los mensajes de todas las colas y temas juntos en las colas de trabajos pendientes. Este objeto mezcla los mensajes de muchas entidades principales en las colas de trabajos pendientes. Para controlar el equilibrio de carga entre muchos clientes que no se conocen entre sí, el SDK elige aleatoriamente una cola de trabajos pendientes para cada [QueueClient][QueueClient] o [TopicClient que se crea en el código][TopicClient].

## <a name="pings"></a>Pings
Un mensaje Ping es un [BrokeredMessage][BrokeredMessage] vacío con su propiedad [ContentType][ContentType] establecida en application/vnd.ms-servicebus-ping y un valor de [TimeToLive][TimeToLive] de 1 segundo. Este ping tiene una característica especial en Service Bus: el servidor nunca entrega un ping cuando algún llamador solicita un [BrokeredMessage][BrokeredMessage]. Por lo tanto, nunca tendrá que aprender a recibir e ignorar estos mensajes. Se hará ping a todas las entidades (cola o tema únicos) por instancia de [MessagingFactory][MessagingFactory] por cliente cuando se considere que no están disponibles. De forma predeterminada, esto ocurre una vez por minuto. Se considera que los mensajes Ping son mensajes normales de Service Bus y pueden dar lugar a gastos de ancho de banda y mensajes. En cuanto los clientes detectan que el sistema está disponible, los mensajes se detienen.

## <a name="the-syphon"></a>El sifón
Al menos uno de los programas ejecutables de la aplicación debe ejecutar activamente el sifón. El sifón realiza una recepción de sondeo largo que dura 15 minutos. Si todas las entidades están disponibles y tiene 10 colas de trabajos pendientes, la aplicación que hospeda el sifón llama a la operación de recepción 40 veces por hora, 960 veces al día y 28800 veces en 30 días. Cuando el sifón mueve activamente mensajes desde la cola de trabajos pendientes a la cola principal, cada mensaje experimenta los siguientes cargos (se aplican cargos estándar según el tamaño del mensaje y el ancho de banda en todas las fases):

1. Enviar a la cola de trabajos pendientes.
2. Recibir de la cola de trabajos pendientes.
3. Enviar a la cola principal.
4. Recibir de la cola principal.

## <a name="closefault-behavior"></a>Comportamiento de cierre o error
Dentro de una aplicación que hospeda el sifón, cuando el elemento principal o secundario [MessagingFactory][MessagingFactory] genera un error o se cierra sin que su asociado genere un error o se cierre, y el sifón detecta este estado, el sifón actúa. Si el otro elemento [MessagingFactory][MessagingFactory] no se cierra en 5 segundos, el sifón generará un error en el otro elemento [MessagingFactory][MessagingFactory] que aún está abierto.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre la mensajería asincrónica de Service Bus, consulte [Patrones de mensajería asincrónica y alta disponibilidad][Asynchronous messaging patterns and high availability]. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
