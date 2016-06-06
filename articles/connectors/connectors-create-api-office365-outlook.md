<properties
    pageTitle="Incorporación del conector de Office 365 Outlook a PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de Office 365 Outlook con parámetros de la API de REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="mandia"/>

# Introducción al conector de Office 365 Outlook 

Conéctese a Office 365 Outlook para obtener correo electrónico, responder a un correo electrónico, actualizar su calendario y contactos y mucho más. El conector de Office 365 Outlook puede usarse desde:

- Aplicaciones lógicas 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Con Office 365 Outlook, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de Office 365 Outlook. 
- Usar un desencadenador cuando haya un nuevo correo electrónico, al crear un contacto y mucho más.
- Usar acciones que respondan a un correo electrónico, creen un evento de calendario y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando hay un nuevo objeto de Salesforce, puede tomar ese objeto y actualizar los contactos de Office 365 Outlook. 
- Agregar el conector de Office 365 Outlook a PowerApps Enterprise. Así, los usuarios pueden utilizar este conector en sus aplicaciones. 

Si desea obtener información sobre cómo agregar un conector a PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector de Office 365 Outlook tiene los siguientes desencadenadores y acciones disponibles.

| Desencadenadores | Acciones|
| --- | --- |
|<ul><li>Al iniciarse un evento pronto</li><li>Al llegar correo electrónico nuevo</li><li>Con nuevos elementos</li><li>Al actualizar elementos</li></ul>| <ul><li>Crear contacto</li><li>Crear evento</li><li>Enviar correo electrónico de aprobación</li><li>Enviar correo electrónico</li><li>Eliminar contacto</li><li>Eliminar correo electrónico</li><li>Eliminar evento</li><li>Obtener datos adjuntos</li><li>Obtener calendarios</li><li>Obtener contacto</li><li>Obtener carpetas de contactos</li><li>Obtener contactos</li><li>Obtener correos electrónicos</li><li>Obtener evento</li><li>Obtener eventos</li><li>Marcar como leído</li><li>Al iniciarse un evento pronto</li><li>Al llegar correo electrónico nuevo</li><li>Con nuevos elementos</li><li>Al actualizar elementos</li><li>Responder al mensaje</li><li>Enviar correo electrónico con opciones</li><li>Actualizar contacto</li><li>Actualizar evento</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.


## Creación de una conexión a Office365

Al agregar este conector a las aplicaciones lógicas, debe iniciar sesión en su cuenta de Office 365 Outlook y permitir que estas se conecten a su cuenta.

1. Inicie sesión en su cuenta de Office 365 Outlook.
2. Permita que las aplicaciones lógicas se conecten a su cuenta de Office 365 y la usen. 

Después de crear la conexión, especifique las propiedades de Office 365 Outlook, como la ruta de acceso a la carpeta Bandeja de entrada o el mensaje de correo. En la **referencia de la API de REST** de este tema, se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Office 365 Outlook en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.


### Al iniciarse un evento pronto 
Desencadena un flujo cuando se inicia un evento de calendario próximo. ```GET: /Events/OnUpcomingEvents```

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


### Obtener correos electrónicos 
Recupera correos electrónicos de una carpeta. ```GET: /Mail```

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


### Enviar correo electrónico 
Envía un mensaje de correo electrónico. ```POST: /Mail```

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


### Eliminar correo electrónico 
Elimina un mensaje de correo electrónico por identificador. ```DELETE: /Mail/{messageId}```

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


### Marcar como leído 
Marca un mensaje de correo electrónico como leído. ```POST: /Mail/MarkAsRead/{messageId}```

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


### Responder al mensaje 
Responde a un mensaje de correo electrónico. ```POST: /Mail/ReplyTo/{messageId}```

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


### Obtener datos adjuntos 
Recupera los datos adjuntos del mensaje por identificador. ```GET: /Mail/{messageId}/Attachments/{attachmentId}```

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


### Al llegar correo electrónico nuevo 
Desencadena un flujo cuando llega un nuevo correo electrónico. ```GET: /Mail/OnNewEmail```

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


### Enviar correo electrónico con opciones 
Envía un correo electrónico con varias opciones y espera a que el destinatario responda con una de las opciones. ```POST: /mailwithoptions/$subscriptions```

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


### Enviar correo electrónico de aprobación 
Envía un correo electrónico de aprobación y espera una respuesta del destinatario del campo Para. ```POST: /approvalmail/$subscriptions```

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




### Obtener calendarios 
Recupera calendarios. ```GET: /datasets/calendars/tables```

No hay parámetros para esta llamada.

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Obtención de eventos 
Recupera elementos de un calendario. ```GET: /datasets/calendars/tables/{table}/items```

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


### Crear evento 
Crea un evento. ```POST: /datasets/calendars/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|item| |yes|body|Ninguna|Elemento de calendario para crear|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener evento 
Recupera un elemento específico de un calendario. ```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario|
|id|cadena|yes|path|Ninguna|Identificador único de un elemento de calendario que se va a recuperar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar evento 
Elimina un elemento de calendario. ```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de un calendario.|
|id|cadena|yes|path|Ninguna|Identificador único de elemento de calendario para eliminar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar evento 
Actualiza parcialmente un elemento de calendario. ```PATCH: /datasets/calendars/tables/{table}/items/{id}```

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


### Con nuevos elementos 
Se desencadena cuando se crea un nuevo elemento de calendario. ```GET: /datasets/calendars/tables/{table}/onnewitems```

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


### Al actualizar elementos 
Se desencadena cuando se modifica un elemento de calendario. ```GET: /datasets/calendars/tables/{table}/onupdateditems```

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


### Obtener carpetas de contactos 
Recupera carpetas de contactos. ```GET: /datasets/contacts/tables```

No hay parámetros para esta llamada.

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contactos 
Recupera contactos de una carpeta de contactos. ```GET: /datasets/contacts/tables/{table}/items```

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


### Crear contacto 
Crea un contacto. ```POST: /datasets/contacts/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|item| |yes|body|Ninguna|Contacto para crear|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contacto 
Recupera un contacto específico de una carpeta de contactos. ```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos|
|id|cadena|yes|path|Ninguna|Identificador único de un contacto para recuperar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar contacto 
Elimina un contacto. ```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Identificador único de una carpeta de contactos.|
|id|cadena|yes|path|Ninguna|Identificador único de contacto para eliminar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar contacto 
Actualiza parcialmente un contacto. ```PATCH: /datasets/contacts/tables/{table}/items/{id}```

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


## Definiciones de objeto

#### TriggerBatchResponse[IDictionary[String,Object]]

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### SendMessage: Enviar correo electrónico

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Datos adjuntos|array|no|
|De|cadena|no|
|Cc|cadena|no|
|CCO|cadena|no|
|Asunto|cadena|yes|
|Cuerpo|cadena|yes|
|Importancia|cadena|no|
|IsHtml|boolean|no|
|Para|cadena|yes|

#### SendAttachment: Datos adjuntos

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|@odata.type|cadena|no|
|Nombre|cadena|yes|
|ContentBytes|cadena|yes|


#### ReceiveMessage: Recibir mensaje de correo electrónico

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Id|cadena|no|
|IsRead|boolean|no|
|HasAttachment|boolean|no|
|DateTimeReceived|cadena|no|
|Datos adjuntos|array|no|
|De|cadena|no|
|Cc|cadena|no|
|CCO|cadena|no|
|Asunto|cadena|yes|
|Cuerpo|cadena|yes|
|Importancia|cadena|no|
|IsHtml|boolean|no|
|Para|cadena|yes|


#### ReceiveAttachment: Recibir datos adjuntos

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Id|cadena|yes|
|ContentType|cadena|yes|
|@odata.type|cadena|no|
|Nombre|cadena|no|
|ContentBytes|cadena|yes|


#### DigestMessage: Enviar correo electrónico

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Asunto|cadena|yes|
|Cuerpo|cadena|no|
|Importancia|cadena|no|
|Digest|array|yes|
|Datos adjuntos|array|no|
|Para|cadena|yes|

#### TriggerBatchResponse[ReceiveMessage]

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### DataSetsMetadata

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|


#### TabularDataSetsMetadata

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|


#### BlobDataSetsMetadata

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|


#### TableMetadata

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|


#### OptionsEmailSubscription: Modelo de suscripción de correo electrónico con opciones

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|NotificationUrl|cadena|no|
|Message|not defined|no|

#### MessageWithOptions: Mensaje de correo electrónico con opciones del usuario. Este es el mensaje que se espera como parte de la entrada del usuario

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Asunto|cadena|yes|
|Opciones|cadena|yes|
|Cuerpo|cadena|no|
|Importancia|cadena|no|
|Datos adjuntos|array|no|
|Para|cadena|yes|

#### SubscriptionResponse: Modelo de suscripción de correo electrónico de aprobación

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|
|resource|cadena|no|
|notificationType|cadena|no|
|notificationUrl|cadena|no|


#### ApprovalEmailSubscription: Modelo de suscripción de correo electrónico de aprobación

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|NotificationUrl|cadena|no|
|Message|not defined|no|


#### ApprovalMessage: Mensaje de correo electrónico de aprobación Este es el mensaje que se espera como parte de la entrada del usuario

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Asunto|cadena|yes|
|Opciones|cadena|yes|
|Cuerpo|cadena|no|
|Importancia|cadena|no|
|Datos adjuntos|array|no|
|Para|cadena|yes|

#### ApprovalEmailResponse: Respuesta de correo electrónico de aprobación

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|SelectedOption|cadena|no|

#### TablesList

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### Tabla

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|


#### Elemento

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|


#### CalendarItemsList: Lista de elementos de calendario

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### CalendarItem: Representa un elemento de tabla de calendario

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|


#### ContactItemsList: Lista de elementos de contacto

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### ContactItem: Representa un elemento de tabla de contactos

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|


#### DataSetsList

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### DataSet

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|


## Pasos siguientes

[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

Volver a la [lista de API](apis-list.md)

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/connectors-create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0525_2016-->