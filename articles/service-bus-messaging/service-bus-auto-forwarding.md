---
title: Entidades de mensajería del Bus de servicio con reenvío automático | Microsoft Docs
description: Encadenamiento de una cola o suscripción a otra cola u otro tema.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm

---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Encadenamiento de entidades de Bus de servicio con reenvío automático
La característica de *reenvío automático* permite encadenar una cola o suscripción a otra cola o tema que forme parte del mismo espacio de nombres. Cuando el reenvío automático está habilitado, Service Bus elimina automáticamente los mensajes que se colocan en la primera cola o suscripción (origen) y los coloca en la segunda cola o en el segundo tema (destino). Tenga en cuenta que todavía se puede enviar un mensaje a la entidad de destino directamente. Además, no es posible encadenar una subcola (como una cola de mensajes fallidos) a otra cola o tema.

## <a name="using-auto-forwarding"></a>Uso del reenvío automático
Para habilitar el reenvío automático, establezca las propiedades [QueueDescription.ForwardTo][QueueDescription.ForwardTo] o [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] de los objetos [QueueDescription][QueueDescription] o [SubscriptionDescription][SubscriptionDescription] para el origen, como en el siguiente ejemplo.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

La entidad de destino debe existir en el momento en que se creó la entidad de origen. Si la entidad de destino no existe, el Bus de servicio devuelve una excepción cuando se le pide que cree la entidad de origen.

El reenvío automático se puede utilizar para escalar horizontalmente un tema individual. Service Bus limita el [número de suscripciones de un tema dado](../service-bus/service-bus-quotas.md) a 2000. Para alojar suscripciones adicionales, cree temas de segundo nivel. Tenga en cuenta que aunque no tenga la limitación de Service Bus sobre el número de suscripciones, el hecho de agregar un segundo nivel de temas puede mejorar el rendimiento general del tema.

![Escenario de reenvío automático][0]

También puede utilizar el reenvío automático para desacoplar los remitentes de los destinatarios. Por ejemplo, suponga que un sistema ERP consta de tres módulos: procesamiento de pedidos, administración de inventario y administración de relaciones con clientes. Cada uno de estos módulos genera mensajes que se ponen en cola en el tema correspondiente. Alice y Bob son representantes de ventas que están interesados en todos los mensajes relacionados con sus clientes. Para recibir dichos mensajes, Alice y Bob crean una cola personal y una suscripción en cada uno de los temas de ERP que reenvían automáticamente todos los mensajes a su cola.

![Escenario de reenvío automático][1]

Si Alice se va de vacaciones, se llena su cola personal, en lugar del tema de ERP. En este escenario, como un representante de ventas no ha recibido ningún mensaje, ninguno de los temas de ERP alcanza la cuota.

## <a name="auto-forwarding-considerations"></a>Consideraciones sobre el reenvío automático
Si la entidad de destino ha acumulado muchos mensajes y supera la cuota (o la entidad de destino está deshabilitada), la entidad de origen agrega los mensajes a su [cola de mensajes fallidos](service-bus-dead-letter-queues.md) hasta que haya espacio en el destino (o hasta que se vuelva a habilitar la entidad). Esos mensajes seguirán estando en la cola de correo devuelto, por lo que debe recibirlos y procesarlos explícitamente desde de la cola de correo devuelto.

Al encadenar temas individuales para obtener un tema compuesto con muchas suscripciones, se recomienda tener un número moderado de suscripciones en el tema de primer nivel y muchas suscripciones en los temas de segundo nivel. Por ejemplo, un tema de primer nivel con 20 suscripciones, cada una de ellas encadenada a un tema de segundo nivel con 200 suscripciones, ofrece un mayor rendimiento que un tema de primer nivel con 200 suscripciones, cada una de ellas encadenada a un tema de segundo nivel con 20 suscripciones.

Service Bus factura una operación por cada mensaje reenviado. Por ejemplo, el envío de un mensaje a un tema con 20 suscripciones, cada una de ellas configurada para reenviar automáticamente mensajes a otra cola, o a otro tema, se factura como 21 operaciones si todas las suscripciones del primer nivel reciben una copia del mensaje.

Para crear una suscripción encadenada a otra cola o a otro tema, el creador debe tener permisos de **administración** tanto en la entidad de origen como en la de destino. Para enviar mensajes al tema de origen, solo se requieren permisos de **envío** en el tema de origen.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el reenvío automático, consulte los siguientes temas de referencia:

* [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Para más información sobre las mejoras de rendimiento de Service Bus, consulte [Entidades de mensajería con particiones][Entidades de mensajería con particiones].

[QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
[SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
[QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
[SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Entidades de mensajería con particiones]: service-bus-partitioning.md


<!--HONumber=Oct16_HO2-->


