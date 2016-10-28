<properties
pageTitle="Incorporación del conector de Twilio a las aplicaciones lógicas | Microsoft Azure"
description="Información general del conector de Twilio con parámetros de la API de REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="09/19/2016"
ms.author="mandia"/>

# Introducción al conector de Twilio

Conectarse a Twilio para enviar y recibir mensajes SMS, MMS y IP globales.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Con Twilio, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de Twilio.
- Usar acciones que obtienen un mensaje, enumeran mensajes y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando reciba un nuevo mensaje de Twilio, puede tomar este mensaje y usarlo como flujo de trabajo de Bus de servicio.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
El conector de Twilio incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones|
| --- | --- |
|None| <ul><li>Obtener mensaje</li><li>Enumerar mensajes</li><li>Enviar mensaje</li></ul>|

Todos los conectores admiten datos en formato JSON y XML.

## Creación de una conexión a Twilio
Cuando agregue este conector a las aplicaciones lógicas, escriba los siguientes valores de Twilio:

|Propiedad| Obligatorio|Description|
| ---|---|---|
|Id. de cuenta|Sí|Escriba el identificador de cuenta de Twilio|
|Token de acceso|Sí|Escriba el token de acceso de Twilio|

>[AZURE.INCLUDE [Pasos para crear una conexión a Twilio](../../includes/connectors-create-api-twilio.md)]

Si no ve ninguna, consulte [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) para crear un token de acceso.


>[AZURE.TIP] Puede usar esta misma conexión de Twilio en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
#### Esta documentación corresponde a la versión: 1.0

### Obtener mensaje
Devuelve un único mensaje especificado mediante el identificador de mensaje proporcionado. ```GET: /Messages/{MessageId}.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|MessageId|string|yes|path|Ninguna|Id. de mensaje|

### Response
|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|404|Mensaje no encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


### Enumerar mensajes
Devuelve una lista de mensajes asociados a su cuenta. ```GET: /Messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|Para|string|no|query|Ninguna|Al número de teléfono|
|De|string|no|query|Ninguna|Desde número de teléfono|
|DateSent|string|no|query|Ninguna|Mostrar solo los mensajes enviados en esa fecha (en formato GMT), como AAAA-MM-DD. Ejemplo: DateSent=2009-07-06. También puede especificar la desigualdad, como DateSent <=AAAA-MM-DD para los mensajes enviados en o antes de medianoche en una fecha y DateSent>=AAAA-MM-DD para los mensajes enviados en o después de medianoche en una fecha.|
|PageSize|integer|no|query|50|La cantidad de recursos para devolver en cada página de lista. Valor predeterminado: 50|
|Page|integer|no|query|0|Número de página. El valor predeterminado es 0.|

### Response
|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|



### Enviar mensaje
Envía un nuevo mensaje a un número de teléfono móvil. ```POST: /Messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|sendMessageRequest| |yes|body|Ninguna|Mensaje para enviar|

### Response
|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## Definiciones de objeto

#### SendMessageRequest: modelo de solicitud para la operación de envío de mensajes

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|from|string|yes|
|to|string|yes|
|body|string|yes|
|media\_url|array|no|
|status\_callback|string|no|
|messaging\_service\_sid|string|no|
|application\_sid|string|no|
|max\_price|string|no|


#### Message: modelo de mensaje

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|body|string|no|
|from|string|no|
|to|string|no|
|status|string|no|
|sid|string|no|
|account\_sid|string|no|
|api\_version|string|no|
|num\_segments|string|no|
|num\_media|string|no|
|date\_created|string|no|
|date\_sent|string|no|
|date\_updated|string|no|
|dirección|string|no|
|error\_code|string|no|
|error\_message|string|no|
|price|string|no|
|price\_unit|string|no|
|uri|string|no|
|subresource\_uris|array|no|
|messaging\_service\_sid|string|no|

#### MessageList: modelo de respuesta para la operación de enumeración de mensajes

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|messages|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|uri|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|
|total|integer|no|
|previous\_page\_uri|string|no|

#### IncomingPhoneNumberList: modelo de respuesta para la operación de enumeración de mensajes

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|incoming\_phone\_numbers|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|uri|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|


#### AddIncomingPhoneNumberRequest: modelo de solicitud para la operación de agregar un número entrante

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|PhoneNumber|string|yes|
|AreaCode|string|no|
|FriendlyName|string|no|


#### IncomingPhoneNumber: número de teléfono entrante

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|sid|string|no|
|account\_sid|string|no|
|date\_created|string|no|
|date\_updated|string|no|
|capabilities|not defined|no|
|status\_callback|string|no|
|status\_callback\_method|string|no|
|api\_version|string|no|


#### Capabilities: funcionalidades de número de teléfono

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|mms|boolean|no|
|sms|boolean|no|
|voice|boolean|no|

#### AvailablePhoneNumbers: números de teléfono disponibles

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|lata|string|no|
|latitude|string|no|
|longitude|string|no|
|postal\_code|string|no|
|rate\_center|string|no|
|region|string|no|
|MMS|boolean|no|
|SMS|boolean|no|
|voice|boolean|no|


#### UsageRecords: clase de registros de uso

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|categoría|string|no|
|usage|string|no|
|usage\_unit|string|no|
|description|string|no|
|price|número|no|
|price\_unit|string|no|
|count|string|no|
|count\_unit|string|no|
|start\_date|string|no|
|end\_date|string|no|


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0921_2016-->