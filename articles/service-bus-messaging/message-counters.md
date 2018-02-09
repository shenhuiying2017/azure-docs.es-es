---
title: Recuento de mensajes de Azure Service Bus | Microsoft Docs
description: Recupere el recuento de mensajes de Azure Service Bus.
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: e6524fe056ee2a1d81c9cccf257008b2369352b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="message-counters"></a>Contadores de mensajes

Puede recuperar el recuento de mensajes contenidos en las colas y las suscripciones mediante las API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) de Azure Resource Manager y Service Bus del SDK de .NET Framework.

Con PowerShell, puede obtener el recuento de la manera siguiente:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalles del recuento de mensajes

Conocer el número de mensajes activos resulta de utilidad para determinar si una cola crea un trabajo pendiente que requiere procesar más recursos que los que se han implementado actualmente. Los detalles de contador siguientes están disponibles en la clase [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails):

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): mensajes de la cola o la suscripción que se encuentran en estado activo y listo para la entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): mensajes de la cola fallidos.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): mensajes del estado programado.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): mensajes que no se pudieron transferir a otra cola u otro tema y se han movido a la cola de errores de transferencia.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): mensajes pendientes de transferencia en otra cola u otro tema.

Si una aplicación desea escalar recursos en función de la longitud de la cola, debe hacerlo con un ritmo muy medido. La adquisición de los contadores de mensajes es una operación costosa en el agente de mensajes y ejecutarla con frecuencia afecta de forma directa y adversa al rendimiento de la entidad.

## <a name="next-steps"></a>pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)