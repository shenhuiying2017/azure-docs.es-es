<properties
pageTitle="SendGrid | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El proveedor de conexión SendGrid permite enviar correo electrónico y administrar listas de destinatarios."
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introducción al conector SendGrid



El conector SendGrid puede usarse desde:

- [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flujo](http://flows.microsoft.com)

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector SendGrid se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector SendGrid tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de SendGrid
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Envía un correo electrónico mediante la API de SendGrid (limitado a 10 000 destinatarios)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Agrega un destinatario individual a una lista de destinatarios|
### Desencadenadores de SendGrid
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|


## Creación de una conexión a SendGrid
Para crear aplicaciones lógicas con SendGrid, primero debe crear una **conexión** y, a continuación, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|ApiKey|Sí|Proporciona la clave de API de SendGrid|
 

>[AZURE.INCLUDE [Pasos para crear una conexión a SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

## Referencia de SendGrid
Se aplica a la versión: 1.0

## SendEmail
Enviar correo electrónico: envía un correo electrónico mediante la API de SendGrid (limitado a 10 000 destinatarios)

```POST: /api/mail.send.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|request| |yes|body|Ninguna|Mensaje de correo electrónico para enviar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|429|Demasiadas solicitudes|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## AddRecipientToList
Agregar un destinatario a la lista: agrega un destinatario individual a una lista de destinatarios

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|listId|cadena|yes|path|Ninguna|Identificador único de la lista de destinatarios|
|recipientId|cadena|yes|path|Ninguna|Identificador único del destinatario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## Definiciones de objeto 

### EmailRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|from|cadena|Sí |
|fromname|cadena|No |
|to|cadena|Sí |
|toname|cadena|No |
|subject|cadena|Sí |
|body|cadena|Sí |
|ishtml|boolean|No |
|cc|cadena|No |
|ccname|cadena|No |
|bcc|cadena|No |
|bccname|cadena|No |
|replyto|cadena|No |
|fecha|cadena|No |
|encabezados|cadena|No |
|files|array|No |
|filenames|array|No |



### EmailResponse


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|message|cadena|No |



### RecipientLists


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|lists|array|No |



### RecipientList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|name|cadena|No |
|recipient\_count|integer|No |



### Recipients


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|recipients|array|No |



### Recipient


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|email|cadena|No |
|last\_name|cadena|No |
|first\_name|cadena|No |
|id|cadena|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->