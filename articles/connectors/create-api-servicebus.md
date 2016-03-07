<properties
pageTitle="Usar la API del Bus de servicio de Azure en las aplicaciones lógicas | Microsoft Azure"
description="Introducción al uso de la API del Bus de servicio de Azure (conector) en las aplicaciones lógicas del Servicio de aplicaciones de Microsoft Azure"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/18/2016"
ms.author="deonhe"/>

# Introducción a la API del Bus de servicio de Azure

Conéctese al Bus de servicio de Azure para enviar y recibir mensajes. Puede realizar acciones como enviar a la cola, enviar al tema, recibir de la cola, recibir de la suscripción, etc.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Bus de servicio de Azure](../app-service-logic/app-service-logic-connector-Azure Service Bus.md).

Con el Bus de servicio de Azure, puede:

* Usarlo para crear aplicaciones lógicas  

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Hablemos de desencadenadores y acciones

La API del Bus de servicio de Azure puede usarse como una acción; tiene desencadenadores. Todas las API admiten datos en formato JSON y XML.

 La API del Bus de servicio de Azure tiene las siguientes acciones o desencadenadores disponibles:

### Acciones del Bus de servicio de Azure
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|SendMessage|Envía el mensaje a la cola o al tema del Bus de servicio de Azure.|
### Desencadenadores del Bus de servicio de Azure
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|GetMessageFromQueue|Obtiene un nuevo mensaje de la cola del Bus de servicio de Azure.|
|GetMessageFromTopic|Obtiene un nuevo mensaje de la suscripción a un tema del Bus de servicio de Azure.|


## Crear una conexión al Bus de servicio de Azure
Para usar la API del Bus de servicio de Azure, cree primero una **conexión** y después proporcione los detalles para estas propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|ConnectionString|Sí|Proporcionar una cadena de conexión del Bus de servicio de Azure|  

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de la API de REST del Bus de servicio de Azure
#### Esta documentación es para la versión: 1.0


### Envía el mensaje a la cola o al tema del Bus de servicio de Azure.
**```POST: /{entityName}/messages```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|message| |yes|body|Ninguna|Mensaje del Bus de servicio|
|entityName|cadena|yes|path|Ninguna|Nombre de la cola o del tema|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtiene un nuevo mensaje de la cola del Bus de servicio de Azure.
**```GET: /{queueName}/messages/head```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|queueName|cadena|yes|path|Ninguna|Nombre de la cola.|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtiene un nuevo mensaje de la suscripción a un tema del Bus de servicio de Azure.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|topicName|cadena|yes|path|Ninguna|Nombre del tema.|
|subscriptionName|cadena|yes|path|Ninguna|Nombre de la suscripción al tema.|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



## Definiciones de objeto: 

 **ServiceBusMessage**: El mensaje consta de contenido y propiedades

Propiedades necesarias para ServiceBusMessage:

ContentTransferEncoding

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|ContentData|cadena|
|ContentType|cadena|
|ContentTransferEncoding|cadena|
|Propiedades|objeto|


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->