<properties
pageTitle="Incorporación de la API de Outlook de Office 365 a sus aplicaciones lógicas | Microsoft Azure"
description="Información general de la API de Outlook de Office 365 con parámetros de la API de REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introducción a la API de Outlook de Office365

La API de Office 365 permite la interacción con Office 365. Por ejemplo: crear, editar y actualizar contactos y elementos de calendario.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [API de Office 365](../app-service-logic/app-service-logic-connector-Office365.md).

Con Office 365, puede:

* Usarlo para crear aplicaciones lógicas
* Usarlo para crear aplicaciones versátiles

Para obtener información sobre cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Hablemos de acciones y desencadenadores

La API de Office365 se puede usar como una acción; tiene desencadenadores. Todas las API admiten datos en formato JSON y XML.

 La API de Office365 tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de Office365
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|GetEmails|Recupera correos electrónicos de una carpeta|
|SendEmail|Envía un mensaje de correo electrónico|
|DeleteEmail|Elimina un mensaje de correo electrónico por identificador|
|MarkAsRead|Marca un mensaje de correo electrónico como leído|
|ReplyTo|Responde a un mensaje de correo electrónico|
|GetAttachment|Recupera datos adjuntos del mensaje por identificador|
|SendMailWithOptions|Envíe un correo electrónico con varias opciones y espere a que el destinatario responda con una de las opciones.|
|SendApprovalMail|Envíe un correo electrónico de aprobación y espere una respuesta del destinatario Para.|
|CalendarGetTables|Recupera calendarios|
|CalendarGetItems|Recupera elementos de un calendario|
|CalendarPostItem|Crea un nuevo evento|
|CalendarGetItem|Recupera un elemento específico de un calendario|
|CalendarDeleteItem|Elimina un elemento de calendario|
|CalendarPatchItem|Actualiza parcialmente un elemento de calendario|
|ContactGetTables|Recupera carpetas de contactos|
|ContactGetItems|Recupera contactos de una carpeta de contactos|
|ContactPostItem|Crea un nuevo contacto|
|ContactGetItem|Recupera un contacto específico de una carpeta de contactos|
|ContactDeleteItem|Elimina un contacto|
|ContactPatchItem|Actualiza parcialmente un contacto|
### Desencadenadores de Office365
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|OnUpcomingEvents|Desencadena un flujo cuando se inicia un evento de calendario próximo|
|OnNewEmail|Desencadena un flujo cuando llega un correo electrónico nuevo|
|CalendarGetOnNewItems|Se desencadena cuando se crea un nuevo elemento de calendario|
|CalendarGetOnUpdatedItems|Se desencadena cuando se modifica un elemento de calendario|


## Creación de una conexión a Office365
Para usar la API de Office365, cree primero una **conexión** y después proporcione los detalles para estas propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporcionar las credenciales de Office365|


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de API de REST de Office365
#### Esta documentación corresponde a la versión: 1.0


### Al iniciarse un evento pronto 


 Desencadena un flujo cuando se inicia un evento de calendario próximo ```GET: /Events/OnUpcomingEvents```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|query|Ninguna|Identificador único del calendario|
|lookAheadTimeInMinutes|integer|no|query|15|Tiempo (en minutos) por delante para buscar próximos eventos.|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener correos electrónicos 


 Recupera correos electrónicos de una carpeta ```GET: /Mail```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|no|query|Bandeja de entrada|Ruta de acceso de la carpeta para recuperar mensajes (predeterminada: 'Bandeja de entrada')|
|top|integer|no|query|10|Número de correos electrónicos para recuperar (valor predeterminado: 10)|
|fetchOnlyUnread|boolean|no|query|true|¿Recuperar solo mensajes no leídos?|
|includeAttachments|boolean|no|query|false|Si se establece en true, los datos adjuntos también se recuperarán junto con el mensaje de correo electrónico.|
|searchQuery|cadena|no|query|Ninguna|Consulta de búsqueda para filtrar correos electrónicos|
|skip|integer|no|query|0|Número de correos electrónicos para omitir (valor predeterminado: 0)|
|skipToken|cadena|no|query|Ninguna|Omitir token para obtener la página nueva|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Enviar correo electrónico 


 Envía un mensaje de correo electrónico ```POST: /Mail```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|Ninguna|Instancia de mensaje de correo electrónico|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Eliminar correo electrónico 


 Elimina un mensaje de correo electrónico por identificador ```DELETE: /Mail/{messageId}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|messageId|cadena|yes|path|Ninguna|Identificador del mensaje que se va a eliminar.|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Marcar como leído 


 Marca un mensaje de correo electrónico como leído ```POST: /Mail/MarkAsRead/{messageId}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|messageId|cadena|yes|path|Ninguna|Identificador del mensaje que se va a marcar como leído|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Responder al mensaje 


 Responde a un mensaje de correo electrónico ```POST: /Mail/ReplyTo/{messageId}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|messageId|cadena|yes|path|Ninguna|Identificador del mensaje al que se va a responder|
|comment|cadena|yes|query|Ninguna|Comentario de respuesta|
|replyAll|boolean|no|query|false|Responder a todos los destinatarios|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener datos adjuntos 


 Recupera los datos adjuntos del mensaje por identificador ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|messageId|cadena|yes|path|Ninguna|Identificador del mensaje|
|attachmentId|cadena|yes|path|Ninguna|Identificador de los datos adjuntos para descargar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Al llegar correo electrónico nuevo 


 Desencadena un flujo cuando llega un correo electrónico nuevo ```GET: /Mail/OnNewEmail```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|no|query|Bandeja de entrada|Carpeta de correo electrónico para recuperar (predeterminada: Bandeja de entrada)|
|to|cadena|no|query|Ninguna|Direcciones de correo electrónico de destinatarios|
|from|cadena|no|query|Ninguna|Dirección De|
|importance|cadena|no|query|Normal|Importancia del correo electrónico (alta, normal o baja) (predeterminada: Normal)|
|fetchOnlyWithAttachment|boolean|no|query|false|Recuperar solo correos electrónicos con datos adjuntos|
|includeAttachments|boolean|no|query|false|Incluir datos adjuntos|
|subjectFilter|cadena|no|query|Ninguna|Cadena que se va a buscar en el asunto.|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente.|
|202|Accepted|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Enviar correo electrónico con opciones 


 Envíe un correo electrónico con varias opciones y espere a que el destinatario responda con una de las opciones. ```POST: /mailwithoptions/$subscriptions```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|Ninguna|Solicitud de suscripción para correo electrónico con opciones|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|201|Suscripción creada|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Enviar correo electrónico de aprobación 


 Envíe un correo electrónico de aprobación y espere una respuesta del destinatario Para. ```POST: /approvalmail/$subscriptions```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|Ninguna|Solicitud de suscripción para correo electrónico de aprobación.|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|201|Suscripción creada|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener calendarios 


 Recupera calendarios ```GET: /datasets/calendars/tables```

No hay parámetros para esta llamada
#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtención de eventos 


 Recupera elementos de un calendario ```GET: /datasets/calendars/tables/{table}/items```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único del calendario que se va a recuperar|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Crear evento 


 Crea un nuevo evento ```POST: /datasets/calendars/tables/{table}/items```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|item| |yes|body|Ninguna|Elemento de calendario para crear|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtener evento 


 Recupera un elemento específico de un calendario ```GET: /datasets/calendars/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|id|cadena|yes|path|Ninguna|Identificador único de un elemento de calendario que se va a recuperar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Eliminar evento 


 Elimina un elemento de calendario ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario.|
|id|cadena|yes|path|Ninguna|Identificador único de elemento de calendario para eliminar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Actualizar evento 


 Actualiza parcialmente un elemento de calendario ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|id|cadena|yes|path|Ninguna|Identificador único de elemento de calendario para actualizar|
|item| |yes|body|Ninguna|Elemento de calendario para actualizar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Con nuevos elementos 


 Se desencadena cuando se crea un nuevo elemento de calendario ```GET: /datasets/calendars/tables/{table}/onnewitems```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Al actualizar elementos 


 Se desencadena cuando se modifica un elemento de calendario ```GET: /datasets/calendars/tables/{table}/onupdateditems```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtener carpetas de contactos 


 Recupera carpetas de contactos ```GET: /datasets/contacts/tables```

No hay parámetros para esta llamada
#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtener contactos 


 Recupera contactos de una carpeta de contactos ```GET: /datasets/contacts/tables/{table}/items```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de la carpeta de contactos que se va a recuperar|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Crear contacto 


 Crea un nuevo contacto ```POST: /datasets/contacts/tables/{table}/items```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|item| |yes|body|Ninguna|Contacto para crear|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Obtener contacto 


 Recupera un contacto específico de una carpeta de contactos ```GET: /datasets/contacts/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|id|cadena|yes|path|Ninguna|Identificador único de un contacto para recuperar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Eliminar contacto 


 Elimina un contacto ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos.|
|id|cadena|yes|path|Ninguna|Identificador único de contacto para eliminar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Actualizar contacto 


 Actualiza parcialmente un contacto ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|id|cadena|yes|path|Ninguna|Identificador único de contacto para actualizar|
|item| |yes|body|Ninguna|Elemento de contacto para actualizar|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



## Definiciones de objeto: 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

Propiedades necesarias para TriggerBatchResponse[IDictionary[String,Object]]:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **SendMessage**: Enviar mensaje de correo electrónico

Propiedades obligatorias para SendMessage:

Subject, Body, To

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Datos adjuntos|array|
|De|cadena|
|Cc|cadena|
|CCO|cadena|
|Asunto|cadena|
|Cuerpo|cadena|
|Importancia|cadena|
|IsHtml|boolean|
|Para|cadena|



 **SendAttachment**: Datos adjuntos

Propiedades obligatorias para SendAttachment:

Name, ContentBytes

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|@odata.type|cadena|
|Nombre|cadena|
|ContentBytes|cadena|



 **ReceiveMessage**: Recibir mensaje de correo electrónico

Propiedades obligatorias para ReceiveMessage:

Subject, Body, To

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Id|cadena|
|IsRead|boolean|
|HasAttachment|boolean|
|DateTimeReceived|cadena|
|Datos adjuntos|array|
|De|cadena|
|Cc|cadena|
|CCO|cadena|
|Asunto|cadena|
|Cuerpo|cadena|
|Importancia|cadena|
|IsHtml|boolean|
|Para|cadena|



 **ReceiveAttachment**: Recibir datos adjuntos

Propiedades obligatorias para ReceiveAttachment:

Id, ContentType, Name, ContentBytes

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Id|cadena|
|ContentType|cadena|
|@odata.type|cadena|
|Nombre|cadena|
|ContentBytes|cadena|



 **DigestMessage**: Enviar mensaje de correo electrónico

Propiedades obligatorias para DigestMessage:

Subject, Digest, To

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Asunto|cadena|
|Cuerpo|cadena|
|Importancia|cadena|
|Digest|array|
|Datos adjuntos|array|
|Para|cadena|



 **TriggerBatchResponse[ReceiveMessage]**:

Propiedades obligatorias para TriggerBatchResponse[ReceiveMessage]:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **DataSetsMetadata**:

Propiedades obligatorias para DataSetsMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|tabular|not defined|
|blob|not defined|



 **TabularDataSetsMetadata**:

Propiedades obligatorias para TabularDataSetsMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|de origen|cadena|
|DisplayName|cadena|
|urlEncoding|cadena|
|tableDisplayName|cadena|
|tablePluralName|cadena|



 **BlobDataSetsMetadata**:

Propiedades obligatorias para BlobDataSetsMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|de origen|cadena|
|DisplayName|cadena|
|urlEncoding|cadena|



 **TableMetadata**:

Propiedades obligatorias para TableMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|name|cadena|
|título|cadena|
|x-ms-permission|cadena|
|schema|not defined|



 **OptionsEmailSubscription**: Modelo de suscripción de correo electrónico con opciones

Propiedades obligatorias para OptionsEmailSubscription:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|NotificationUrl|cadena|
|Message|not defined|



 **MessageWithOptions**: Mensajes de correo electrónico con opciones del usuario. Este es el mensaje que se espera como parte de la entrada del usuario

Propiedades obligatorias para MessageWithOptions:

Subject, Options, To

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Asunto|cadena|
|Opciones|cadena|
|Cuerpo|cadena|
|Importancia|cadena|
|Datos adjuntos|array|
|Para|cadena|



 **SubscriptionResponse**: Modelo de suscripción de correo electrónico de aprobación

Propiedades obligatorias para SubscriptionResponse:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|id|cadena|
|resource|cadena|
|notificationType|cadena|
|notificationUrl|cadena|



 **ApprovalEmailSubscription**: Modelo de suscripción de correo electrónico de aprobación

Propiedades obligatorias para ApprovalEmailSubscription:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|NotificationUrl|cadena|
|Message|not defined|



 **ApprovalMessage**: Mensaje de correo electrónico de aprobación. Este es el mensaje que se espera como parte de la entrada del usuario

Propiedades obligatorias para ApprovalMessage:

Subject, Options, To

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Asunto|cadena|
|Opciones|cadena|
|Cuerpo|cadena|
|Importancia|cadena|
|Datos adjuntos|array|
|Para|cadena|



 **ApprovalEmailResponse**: Respuesta de correo electrónico de aprobación

Propiedades obligatorias para ApprovalEmailResponse:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|SelectedOption|cadena|



 **TablesList**:

Propiedades obligatorias para TablesList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **Tabla**:

Propiedades obligatorias para Table:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Nombre|cadena|
|DisplayName|cadena|



 **Item**:

Propiedades obligatorias para Item:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|ItemInternalId|cadena|



 **CalendarItemsList**: Lista de elementos de calendario

Propiedades obligatorias para CalendarItemsList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **CalendarItem**: Representa un elemento de tabla de calendario

Propiedades obligatorias para CalendarItem:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|ItemInternalId|cadena|



 **ContactItemsList**: Lista de elementos de contacto

Propiedades obligatorias para ContactItemsList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **ContactItem**: Representa un elemento de tabla de contactos

Propiedades obligatorias para ContactItem:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|ItemInternalId|cadena|



 **DataSetsList**:

Propiedades obligatorias para DataSetsList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **DataSet**:

Propiedades obligatorias para DataSet:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Nombre|cadena|
|DisplayName|cadena|


## Pasos siguientes
Después de agregar la API de Office 365 a PowerApps Enterprise, [conceda permisos a los usuarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar dicha API en sus aplicaciones.

[Cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->