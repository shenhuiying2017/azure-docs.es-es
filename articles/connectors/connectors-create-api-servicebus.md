---
title: "Aprenda a utilizar el conector de Azure Service Bus en las aplicaciones lógicas | Microsoft Docs"
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese al Bus de servicio de Azure para enviar y recibir mensajes. Puede realizar acciones como enviar a la cola, enviar al tema, recibir de la cola, recibir de la suscripción, etc."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Introducción al conector del Bus de servicio de Azure
Conéctese al Bus de servicio de Azure para enviar y recibir mensajes. Puede realizar acciones como enviar a la cola, enviar al tema, recibir de la cola, recibir de la suscripción, etc.

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Conexión al Bus de servicio
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una conexión con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Uso de un desencadenador del Bus de servicio
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Uso de una acción del Bus de servicio
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>Detalles técnicos
Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión.

### <a name="service-bus-triggers"></a>Desencadenadores del Bus de servicio
El Bus de servicio tiene los siguientes desencadenadores:  

| Desencadenador | Descripción |
| --- | --- |
| [Cuando se recibe un mensaje en una cola](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |Esta operación desencadena un flujo al recibir un mensaje en una cola. |
| [Cuando se recibe un mensaje en una suscripción al tema](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |Esta operación desencadena un flujo al recibir un mensaje en una suscripción al tema. |

### <a name="service-bus-actions"></a>Acciones del Bus de servicio
El Bus de servicio tiene las siguientes acciones:

| Acción | Descripción |
| --- | --- |
| [Enviar mensaje](connectors-create-api-servicebus.md#send-message) |Esta operación envía un mensaje a una cola o un tema. |

### <a name="action-and-trigger-details"></a>Detalles de las acciones y los desencadenadores
Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas.

#### <a name="send-message"></a>Enviar mensaje
| Nombre de propiedad | Nombre para mostrar | Descripción |
| --- | --- | --- |
| ContentData* |Contenido |Contenido del mensaje |
| ContentType |Tipo de contenido |Tipo de contenido del mensaje |
| Propiedades |Propiedades |Pares de clave y valor para cada propiedad asincrónica |
| entityName* |Nombre de la cola o el tema |Nombre de la cola o del tema |

Estos parámetros avanzados también están disponibles:

| Nombre de propiedad | Nombre para mostrar | Descripción |
| --- | --- | --- |
| MessageId |Id. de mensaje |Se trata de un valor definido por el usuario que el Bus de servicio puede utilizar para identificar mensajes duplicados, si está habilitado. |
| Para |Para |La dirección de envío |
| ReplyTo |Responder a |Dirección de la cola a la que responder |
| ReplyToSessionId |Responder al identificador de sesión |Identificador de la sesión a la que responder |
| Etiqueta |Etiqueta |Etiqueta específica de aplicación |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |Fecha y hora, en UTC, a las que el mensaje se agregará a la cola |
| SessionId |Identificador de sesión |Identificador de la sesión |
| CorrelationId |Identificador de correlación |Identificador de la correlación |
| TimeToLive |Período de vida |Se trata de la duración, en tics, durante la cual el mensaje es válido. La duración se inicia a partir del envío del mensaje al Bus de servicio. |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="when-a-message-is-received-in-a-queue"></a>Cuando se recibe un mensaje en una cola
| Nombre de propiedad | Nombre para mostrar | Descripción |
| --- | --- | --- |
| queueName* |Nombre de cola |Nombre de la cola. |

El símbolo * indica que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ServiceBusMessage: este objeto tiene el contenido y las propiedades de un mensaje del Bus de servicio.

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| ContentData |string |Contenido del mensaje |
| ContentType |string |Tipo de contenido del mensaje |
| Propiedades |objeto |Pares de clave y valor para cada propiedad asincrónica |
| MessageId |string |Se trata de un valor definido por el usuario que el Bus de servicio puede utilizar para identificar mensajes duplicados, si está habilitado. |
| Para |string |Dirección de envío |
| ReplyTo |string |Dirección de la cola a la que responder |
| ReplyToSessionId |string |Identificador de la sesión a la que responder |
| Etiqueta |string |Etiqueta específica de aplicación |
| ScheduledEnqueueTimeUtc |string |Fecha y hora, en UTC, a las que el mensaje se agregará a la cola |
| SessionId |string |Identificador de la sesión |
| CorrelationId |string |Identificador de la correlación |
| TimeToLive |string |Se trata de la duración, en tics, durante la cual el mensaje es válido. La duración se inicia a partir del envío del mensaje al Bus de servicio. |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Cuando se recibe un mensaje en una suscripción al tema
| Nombre de propiedad | Nombre para mostrar | Descripción |
| --- | --- | --- |
| topicName* |Nombre del tema |Nombre del tema. |
| subscriptionName* |Nombre de la suscripción al tema |Nombre de la suscripción al tema. |

El símbolo * indica que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ServiceBusMessage: este objeto tiene el contenido y las propiedades de un mensaje del Bus de servicio.

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| ContentData |string |Contenido del mensaje |
| ContentType |string |Tipo de contenido del mensaje |
| Propiedades |objeto |Pares de clave y valor para cada propiedad asincrónica |
| MessageId |string |Se trata de un valor definido por el usuario que el Bus de servicio puede utilizar para identificar mensajes duplicados, si está habilitado. |
| Para |string |Dirección de envío |
| ReplyTo |string |Dirección de la cola a la que responder |
| ReplyToSessionId |string |Identificador de la sesión a la que responder |
| Etiqueta |string |Etiqueta específica de aplicación |
| ScheduledEnqueueTimeUtc |string |Fecha y hora, en UTC, a las que el mensaje se agregará a la cola |
| SessionId |string |Identificador de la sesión |
| CorrelationId |string |Identificador de la correlación |
| TimeToLive |string |Se trata de la duración, en tics, durante la cual el mensaje es válido. La duración se inicia a partir del envío del mensaje al Bus de servicio. |

### <a name="http-responses"></a>Respuestas HTTP
Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Solicitud incorrecta |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor Error desconocido. |
| default |Error en la operación |

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Jan17_HO3-->


