---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/12/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 74732008b336dc1b95ec96e8550d218105973ca4
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
En la siguiente tabla se muestra la información de cuotas específica de la mensajería de Service Bus. Para obtener información sobre los precios y otras cuotas de Service Bus, consulte la información general sobre los [precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) .

| Nombre de cuota | Scope | Notas | Valor |
| --- | --- | --- | --- | --- |
| Número máximo de espacios de nombres básicos o estándar por suscripción de Azure |Espacio de nombres |El portal rechaza las solicitudes posteriores de espacios de nombres básicos o estándar adicionales. |100|
| Número máximo de espacios de nombres premium por suscripción de Azure |Espacio de nombres |El portal rechaza las solicitudes posteriores de espacios de nombres premium adicionales. |10 |
| Tamaño de cola o tema |Entidad |Definido al crear una cola o un tema. <br/><br/> Los sucesivos mensajes entrantes se rechazan y el código de llamada recibe una excepción. |1, 2, 3, 4 o 5 GB.<br /><br />Si se habilitan las [particiones](../articles/service-bus-messaging/service-bus-partitioning.md) , el tamaño máximo de las colas y temas es 80 GB. |
| Número de conexiones simultáneas en un espacio de nombres |Espacio de nombres |Las solicitudes posteriores de conexiones adicionales se rechazarán y el código que realizó la llamada recibe una excepción. Las operaciones REST no se cuentan en las conexiones de TCP simultáneas. |NetMessaging: 1.000<br /><br />AMQP: 5.000 |
| Número de solicitudes de recepción simultáneas en una cola, un tema o una entidad de suscripción |Entidad |Las solicitudes de recepción posteriores se rechazan y el código de llamada recibe una excepción. Esta cuota se aplica a un número combinado de operaciones de recepción simultáneas en todas las suscripciones de un tema. |5.000 |
| Número de temas o colas por espacio de nombres del servicio |Espacio de nombres |Se rechazan las posteriores solicitudes de creación colas o temas nuevos en el espacio de nombres de servicio. Como resultado, si se configuran mediante [Azure Portal][Azure portal], se genera un mensaje de error. Si se realiza una llamada desde la API de administración, el código de llamada recibe una excepción. |10.000<br /><br />El número total de temas, más las colas de un espacio de nombres, debe ser menor o igual a 10 000.<br/>Esto no se puede aplicar a Premium, ya que todas las entidades tienen particiones. |
| Número de temas o colas con particiones por espacio de nombres de servicio |Espacio de nombres |Se rechazan las posteriores solicitudes de creación colas o temas con particiones nuevos en el espacio de nombres de servicio. Como resultado, si se configuran mediante [Azure Portal][Azure portal], se genera un mensaje de error. Si se realiza una llamada desde la API de administración, el código de llamada recibe una excepción **QuotaExceededException**. |Niveles Basic y Estándar: 100<br />[Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md): 1000 (por unidad de mensajería)<br/><br />Cada cola o tema con particiones se tiene en cuenta para la cuota de 10 000 entidades por espacio de nombres. |
| Tamaño máximo de cualquier ruta de entidad de mensajería: cola o tema |Entidad |- |260 caracteres |
| Tamaño máximo de cualquier nombre de entidad de mensajería: espacio de nombres, suscripción o regla de suscripción |Entidad |- |50 caracteres |
| Tamaño de mensaje de cola, tema o entidad de suscripción |Entidad |Los mensajes entrantes que superen estas cuotas se rechazarán y el código que realizó la llamada recibe una excepción. |Tamaño máximo del mensaje: 256 KB ([nivel estándar](../articles/service-bus-messaging/service-bus-premium-messaging.md))/1 MB ([nivel Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />**Nota** Debido a la sobrecarga del sistema, este límite suele ser ligeramente inferior.<br /><br />Tamaño de encabezado máximo: 64 KB<br /><br />Número máximo de propiedades de encabezado en el contenedor de propiedades: **byte/int.MaxValue**<br /><br />Tamaño máximo de la propiedad en el contenedor de propiedades: sin límite explícito. Limitado por tamaño de encabezado máximo. |
| Tamaño de propiedad de mensaje para una cola, un tema o una entidad de suscripción |Entidad |Se genera una excepción **SerializationException**. |El tamaño máximo de cada propiedad del mensaje es 32 K. El tamaño acumulado de todas las propiedades no puede superar 64 K. Esto se aplica al encabezado entero de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tiene tanto propiedades de usuario como propiedades del sistema (como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid), etc.). |
| Número de suscripciones por tema |Entidad |Se rechazan las posteriores solicitudes de creación de suscripciones adicionales para el tema. Como resultado, si se configura a través del portal, se muestra un mensaje de error. Si se realiza una llamada desde la API de administración, el código de llamada recibe una excepción. |2.000 |
| Número de filtros SQL por tema |Entidad |Se rechazan las posteriores solicitudes de creación de filtros adicionales en el tema y el código de llamada recibe una excepción. |2.000 |
| Número de filtros de correlación por tema |Entidad |Se rechazan las posteriores solicitudes de creación de filtros adicionales en el tema y el código de llamada recibe una excepción. |100 000 |
| Tamaño de filtros SQL/acciones |Espacio de nombres |Se rechazan las posteriores solicitudes de creación de filtros adicionales y el código de llamada recibe una excepción. |Longitud máxima de cadena de condición de filtro: 1024 (1 KB).<br /><br />Longitud máxima de cadena de acción de regla: 1024 (1 KB).<br /><br />Número máximo de expresiones por acción de regla: 32. |
| Número de reglas [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) por espacio de nombres, cola o tema |Entidad, espacio de nombres |Se rechazan las posteriores solicitudes de creación de reglas adicionales en el tema y el código de llamada recibe una excepción. |Número máximo de reglas: 12. <br /><br /> Las reglas que se configuran en un espacio de nombres de Service Bus se aplican a todas las colas y temas de dicho espacio de nombres. |
| Número de mensajes por transacción | Transacción | Los mensajes entrantes adicionales se rechazan y el código de llamada recibe una excepción que indica "Cannot send more than 100 messages in a single transaction" (No se pueden enviar más de 100 mensajes en una sola transacción). | 100 <br /><br /> Para ambas operaciones **Send()** y **SendAsync()**. |

[Azure portal]: https://portal.azure.com