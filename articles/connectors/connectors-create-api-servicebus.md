<properties
pageTitle="Aprenda a utilizar el conector del Bus de servicio de Azure en las aplicaciones lógicas | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese al Bus de servicio de Azure para enviar y recibir mensajes. Puede realizar acciones como enviar a la cola, enviar al tema, recibir de la cola, recibir de la suscripción, etc."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/27/2016"
ms.author="deonhe"/>

# Introducción al conector del Bus de servicio de Azure

Conéctese al Bus de servicio de Azure para enviar y recibir mensajes. Puede realizar acciones como enviar a la cola, enviar al tema, recibir de la cola, recibir de la suscripción, etc.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión al Bus de servicio de Azure

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.

### Crear una conexión al Bus de servicio de Azure

>[AZURE.INCLUDE [Pasos para crear una conexión al Bus de servicio de Azure](../../includes/connectors-create-api-servicebus.md)]

## Uso de un desencadenador del Bus de servicio de Azure

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Pasos para crear un desencadenador del Bus de servicio](../../includes/connectors-create-api-servicebus-trigger.md)]

## Uso de una acción del Bus de servicio de Azure

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Pasos para crear una acción del Bus de servicio](../../includes/connectors-create-api-servicebus-action.md)]

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores del Bus de servicio de Azure

El Bus de servicio de Azure tiene el siguiente desencadenador o desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[Cuando se recibe un mensaje en una cola](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Esta operación desencadena un flujo al recibir un mensaje en una cola.|
|[Cuando se recibe un mensaje en una suscripción al tema](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Esta operación desencadena un flujo al recibir un mensaje en una suscripción al tema.|


## Acciones del Bus de servicio de Azure

El Bus de servicio de Azure tiene las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Enviar mensaje](connectors-create-api-servicebus.md#send-message)|Esta operación envía un mensaje a una cola o un tema.|
### Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:



### Enviar mensaje
Esta operación envía un mensaje a una cola o un tema.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|message*|Message|Mensaje para enviar|
|entityName*|Nombre de la cola o el tema|Nombre de la cola o del tema|

El símbolo * indica que la propiedad es obligatoria.




### Cuando se recibe un mensaje en una cola
Esta operación desencadena un flujo al recibir un mensaje en una cola.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|queueName*|Nombre de cola|Nombre de la cola|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ServiceBusMessage: este objeto tiene el contenido y las propiedades de un mensaje del Bus de servicio.


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|ContentData|cadena|Contenido del mensaje|
|ContentType|cadena|Tipo de contenido del contenido del mensaje|
|ContentTransferEncoding|cadena|Codificación de transferencia de contenido del contenido del mensaje ("none"|"base64")|
|Propiedades|objeto|Pares de clave y valor para cada propiedad asincrónica|
|MessageId|cadena|Se trata de un valor definido por el usuario que el Bus de servicio puede utilizar para identificar mensajes duplicados, si está habilitado.|
|Para|cadena|Dirección de envío|
|ReplyTo|cadena|Dirección de la cola a la que responder|
|ReplyToSessionId|cadena|Identificador de la sesión a la que responder|
|Etiqueta|cadena|Etiqueta específica de la aplicación|
|ScheduledEnqueueTimeUtc|cadena|Fecha y hora, en UTC, a las que el mensaje se agregará a la cola|
|SessionId|cadena|Identificador de la sesión|
|CorrelationId|cadena|Identificador de la correlación|
|TimeToLive|cadena|Se trata de la duración, en tics, durante la cual el mensaje es válido. La duración se inicia a partir del envío del mensaje al Bus de servicio.|




### Cuando se recibe un mensaje en una suscripción al tema
Esta operación desencadena un flujo al recibir un mensaje en una suscripción al tema.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|topicName*|Nombre del tema|Nombre del tema|
|subscriptionName*|Nombre de la suscripción al tema|Nombre de la suscripción al tema|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ServiceBusMessage: este objeto tiene el contenido y las propiedades de un mensaje del Bus de servicio.


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|ContentData|cadena|Contenido del mensaje|
|ContentType|cadena|Tipo de contenido del contenido del mensaje|
|ContentTransferEncoding|cadena|Codificación de transferencia de contenido del contenido del mensaje ("none"|"base64")|
|Propiedades|objeto|Pares de clave y valor para cada propiedad asincrónica|
|MessageId|cadena|Se trata de un valor definido por el usuario que el Bus de servicio puede utilizar para identificar mensajes duplicados, si está habilitado.|
|Para|cadena|Dirección de envío|
|ReplyTo|cadena|Dirección de la cola a la que responder|
|ReplyToSessionId|cadena|Identificador de la sesión a la que responder|
|Etiqueta|cadena|Etiqueta específica de la aplicación|
|ScheduledEnqueueTimeUtc|cadena|Fecha y hora, en UTC, a las que el mensaje se agregará a la cola|
|SessionId|cadena|Identificador de la sesión|
|CorrelationId|cadena|Identificador de la correlación|
|TimeToLive|cadena|Se trata de la duración, en tics, durante la cual el mensaje es válido. La duración se inicia a partir del envío del mensaje al Bus de servicio.|



## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido.|
|default|Error en la operación.|

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->