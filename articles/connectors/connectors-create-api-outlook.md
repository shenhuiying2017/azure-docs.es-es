<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El conector de Outlook.com permite administrar su correo, calendarios y contactos. Puede realizar diversas acciones como enviar correo, programar reuniones, agregar contactos, etc."
services="logic-apps"	
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# Get started with the Outlook.com connector (Introducción al conector de Outlook.com)

El conector de Outlook.com permite administrar su correo, calendarios y contactos. Puede realizar diversas acciones como enviar correo, programar reuniones, agregar contactos, etc.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector de Outlook.com se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector de Outlook.com tiene disponibles las siguientes acciones o desencadenadores:

### Acciones de Outlook.com
Puede realizar estas acciones:

|Acción|Description|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Recupera correos electrónicos de una carpeta|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Envía un correo electrónico|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Elimina un correo electrónico mediante el identificador|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marca un correo electrónico como leído|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Responde a un correo electrónico|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Recupera los datos adjuntos del correo electrónico mediante el identificador|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Envío de un correo electrónico con varias opciones y espera de una respuesta del destinatario con una de las opciones|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Envío de un correo electrónico de aprobación y espera de una respuesta del destinatario|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Recupera calendarios|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Recupera elementos de un calendario|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Crea un nuevo evento|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Recupera un elemento específico de un calendario|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Elimina un elemento de calendario|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Actualiza parcialmente un elemento de calendario|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Recupera carpetas de contactos|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Recupera contactos de una carpeta de contactos|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Crea un nuevo contacto|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Recupera un contacto específico de una carpeta de contactos|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Elimina un contacto|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Actualiza parcialmente un contacto|
### Desencadenadores de Outlook.com
Se pueden escuchar estos eventos:

|Desencadenador | Description|
|--- | ---|
|Al iniciarse un evento pronto|Desencadena un flujo cuando se inicia un evento de calendario próximo|
|Al llegar correo electrónico nuevo|Desencadena un flujo cuando llega un correo electrónico nuevo|
|Con nuevos elementos|Se desencadena cuando se crea un nuevo elemento de calendario|
|Al actualizar elementos|Se desencadena cuando se modifica un elemento de calendario|


## Creación de una conexión a Outlook.com
Para crear aplicaciones lógicas con Outlook.com, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Description|
| ---|---|---|
|SWT|Sí|Proporcionar las credenciales de Outlook.com|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.INCLUDE [Pasos para crear una conexión a Outlook.com](../../includes/connectors-create-api-outlook.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia para Outlook.com
Se aplica a la versión: 1.0

## OnUpcomingEvents
Al iniciarse un evento pronto: desencadena un flujo cuando se inicia un evento de calendario próximo

```GET: /Events/OnUpcomingEvents```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|query|Ninguna|Identificador único del calendario|
|lookAheadTimeInMinutes|integer|no|query|15|Tiempo (en minutos) por delante para buscar próximos eventos|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## GetEmails
Obtener correos electrónicos: recupera correos electrónicos de una carpeta

```GET: /Mail```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Bandeja de entrada|Ruta de acceso de la carpeta para recuperar mensajes (predeterminada: 'Bandeja de entrada')|
|top|integer|no|query|10|Número de correos electrónicos para recuperar (valor predeterminado: 10)|
|fetchOnlyUnread|boolean|no|query|true|¿Recuperar solo correos electrónicos no leídos?|
|includeAttachments|boolean|no|query|false|Si se establece en true, los datos adjuntos también se recuperarán junto con el correo electrónico|
|searchQuery|string|no|query|Ninguna|Consulta de búsqueda para filtrar correos electrónicos|
|skip|integer|no|query|0|Número de correos electrónicos para omitir (valor predeterminado: 0)|
|skipToken|string|no|query|Ninguna|Omitir token para obtener la página nueva|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## SendEmail
Enviar correo electrónico: envía un correo electrónico

```POST: /Mail```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|Ninguna|Email|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## DeleteEmail
Eliminar correo electrónico: elimina un correo electrónico mediante el identificador

```DELETE: /Mail/{messageId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|Ninguna|Identificador del correo electrónico que se va a eliminar|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## MarkAsRead
Marcar como leído: marca un correo electrónico como leído

```POST: /Mail/MarkAsRead/{messageId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|Ninguna|Identificador del correo electrónico que se va a marcar como leído|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## ReplyTo
Responder al correo electrónico: responde a un correo electrónico

```POST: /Mail/ReplyTo/{messageId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|Ninguna|Identificador del correo electrónico al que se va a responder|
|comment|string|yes|query|Ninguna|Comentario de respuesta|
|replyAll|boolean|no|query|false|Responder a todos los destinatarios|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## GetAttachment
Obtener datos adjuntos: recupera los datos adjuntos del correo electrónico mediante el identificador

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|Ninguna|Identificador del correo electrónico|
|attachmentId|string|yes|path|Ninguna|Identificador de los datos adjuntos para descargar|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## OnNewEmail
Al llegar correo electrónico nuevo: desencadena un flujo cuando llega un nuevo correo electrónico

```GET: /Mail/OnNewEmail```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Bandeja de entrada|Carpeta de correo electrónico para recuperar (predeterminada: Bandeja de entrada)|
|to|string|no|query|Ninguna|Direcciones de correo electrónico de destinatarios|
|from|string|no|query|Ninguna|Dirección De|
|importance|string|no|query|Normal|Importancia del correo electrónico (alta, normal o baja) (predeterminada: Normal)|
|fetchOnlyWithAttachment|boolean|no|query|false|Recuperar solo correos electrónicos con datos adjuntos|
|includeAttachments|boolean|no|query|false|Incluir datos adjuntos|
|subjectFilter|string|no|query|Ninguna|Cadena que se va a buscar en el asunto|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Accepted|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## SendMailWithOptions
Enviar correo electrónico con opciones: envío de un correo electrónico con varias opciones y espera de una respuesta del destinatario con una de las opciones

```POST: /mailwithoptions/$subscriptions```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|Ninguna|Solicitud de suscripción para correo electrónico con opciones|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|201|Suscripción creada|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## SendApprovalMail
Enviar correo electrónico de aprobación: envío de un correo electrónico de aprobación y espera de una respuesta del destinatario

```POST: /approvalmail/$subscriptions```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|Ninguna|Solicitud de suscripción para correo electrónico de aprobación|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|201|Suscripción creada|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


## CalendarGetTables
Obtener calendarios: recupera calendarios

```GET: /datasets/calendars/tables```

No hay parámetros para esta llamada
#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarGetItems
Obtener eventos: recupera elementos de un calendario

```GET: /datasets/calendars/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único del calendario que se va a recuperar|
|$filter|string|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|string|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarPostItem
Crear evento: crea un nuevo evento

```POST: /datasets/calendars/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de un calendario|
|item| |yes|body|Ninguna|Elemento de calendario para crear|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarGetItem
Obtener evento: recupera un elemento específico de un calendario

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de un calendario|
|id|string|yes|path|Ninguna|Identificador único de un elemento de calendario que se va a recuperar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarDeleteItem
Eliminar evento: elimina un elemento de calendario

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de un calendario|
|id|string|yes|path|Ninguna|Identificador único de elemento de calendario para eliminar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarPatchItem
Actualizar evento: actualiza parcialmente un elemento de calendario

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de un calendario|
|id|string|yes|path|Ninguna|Identificador único de elemento de calendario para actualizar|
|item| |yes|body|Ninguna|Elemento de calendario para actualizar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarGetOnNewItems
Con nuevos elementos: se desencadena cuando se crea un nuevo elemento de calendario

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de un calendario|
|$filter|string|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|string|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CalendarGetOnUpdatedItems
Al actualizar elementos: se desencadena cuando se modifica un elemento de calendario

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de un calendario|
|$filter|string|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|string|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ContactGetTables
Obtener carpetas de contactos: recupera carpetas de contactos

```GET: /datasets/contacts/tables```

No hay parámetros para esta llamada
#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ContactGetItems
Obtener contactos: recupera contactos de una carpeta de contactos

```GET: /datasets/contacts/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de la carpeta de contactos que se va a recuperar|
|$filter|string|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|string|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ContactPostItem
Crear contacto: crea un nuevo contacto

```POST: /datasets/contacts/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|item| |yes|body|Ninguna|Contacto para crear|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ContactGetItem
Obtener contacto: recupera un contacto específico de una carpeta de contactos

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|id|string|yes|path|Ninguna|Identificador único de un contacto para recuperar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ContactDeleteItem
Eliminar contacto: elimina un contacto

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|id|string|yes|path|Ninguna|Identificador único de contacto para eliminar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ContactPatchItem
Actualizar contacto: actualiza parcialmente un contacto

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|id|string|yes|path|Ninguna|Identificador único de contacto para actualizar|
|item| |yes|body|Ninguna|Elemento de contacto para actualizar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto 

### TriggerBatchResponse[IDictionary[String,Object]]


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|



### SendMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Datos adjuntos|array|No |
|De|string|No |
|Cc|string|No |
|CCO|string|No |
|Asunto|string|Sí |
|Cuerpo|string|Sí |
|Importancia|string|No |
|IsHtml|boolean|No |
|Para|string|Sí |



### SendAttachment


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|@odata.type|string|No |
|Nombre|string|Sí |
|ContentBytes|string|Sí |



### ReceiveMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id|string|No |
|IsRead|boolean|No |
|HasAttachment|boolean|No |
|DateTimeReceived|string|No |
|Datos adjuntos|array|No |
|De|string|No |
|Cc|string|No |
|CCO|string|No |
|Asunto|string|Sí |
|Cuerpo|string|Sí |
|Importancia|string|No |
|IsHtml|boolean|No |
|Para|string|Sí |



### ReceiveAttachment


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id|string|Sí |
|ContentType|string|Sí |
|@odata.type|string|No |
|Nombre|string|Sí |
|ContentBytes|string|Sí |



### DigestMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Asunto|string|Sí |
|Cuerpo|string|No |
|Importancia|string|No |
|Digest|array|Sí |
|Datos adjuntos|array|No |
|Para|string|Sí |



### TriggerBatchResponse[ReceiveMessage]


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### DataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|tabular|not defined|No |
|blob|not defined|No |



### TabularDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|string|No |
|DisplayName|string|No |
|urlEncoding|string|No |
|tableDisplayName|string|No |
|tablePluralName|string|No |



### BlobDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|string|No |
|DisplayName|string|No |
|urlEncoding|string|No |



### TableMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|name|string|No |
|título|string|No |
|x-ms-permission|string|No |
|x-ms-capabilities|not defined|No |
|schema|not defined|No |



### TableCapabilitiesMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|sortRestrictions|not defined|No |
|filterRestrictions|not defined|No |
|filterFunctions|array|No |



### TableSortRestrictionsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|sortable|boolean|No |
|unsortableProperties|array|No |
|ascendingOnlyProperties|array|No |



### TableFilterRestrictionsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|filterable|boolean|No |
|nonFilterableProperties|array|No |
|requiredProperties|array|No |



### OptionsEmailSubscription


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|NotificationUrl|string|No |
|Message|not defined|No |



### MessageWithOptions


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Asunto|string|Sí |
|Opciones|string|Sí |
|Cuerpo|string|No |
|Importancia|string|No |
|Datos adjuntos|array|No |
|Para|string|Sí |



### SubscriptionResponse


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|string|No |
|resource|string|No |
|notificationType|string|No |
|notificationUrl|string|No |



### ApprovalEmailSubscription


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|NotificationUrl|string|No |
|Message|not defined|No |



### ApprovalMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Asunto|string|Sí |
|Opciones|string|Sí |
|Cuerpo|string|No |
|Importancia|string|No |
|Datos adjuntos|array|No |
|Para|string|Sí |



### ApprovalEmailResponse


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|SelectedOption|string|No |



### TablesList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### Tabla


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|string|No |
|DisplayName|string|No |



### Elemento


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|string|No |



### CalendarItemsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### CalendarItem


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|string|No |



### ContactItemsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### ContactItem


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|string|No |



### DataSetsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### DataSet


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|string|No |
|DisplayName|string|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->