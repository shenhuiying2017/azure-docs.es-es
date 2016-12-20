---
title: Outlook.com | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El conector de Outlook.com permite administrar su correo, calendarios y contactos. Puede realizar diversas acciones como enviar correo, programar reuniones, agregar contactos, etc."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 13d0a71762d3cba05c3dccb647d0890862307327
ms.openlocfilehash: 9593804ba5baa4ffe4cf49e21b5698c5fd3d157e


---
# <a name="get-started-with-the-outlookcom-connector"></a>Get started with the Outlook.com connector (Introducción al conector de Outlook.com)
El conector de Outlook.com permite administrar su correo, calendarios y contactos. Puede realizar diversas acciones como enviar correo, programar reuniones, agregar contactos, etc.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas.
>
>

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector de Outlook.com se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector de Outlook.com tiene disponibles las siguientes acciones o desencadenadores:

### <a name="outlookcom-actions"></a>Acciones de Outlook.com
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |Recupera correos electrónicos de una carpeta |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |Envía un correo electrónico |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |Elimina un correo electrónico mediante el identificador |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |Marca un correo electrónico como leído |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |Responde a un correo electrónico |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |Recupera los datos adjuntos del correo electrónico mediante el identificador |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |Envío de un correo electrónico con varias opciones y espera de una respuesta del destinatario con una de las opciones |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |Envío de un correo electrónico de aprobación y espera de una respuesta del destinatario |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |Recupera calendarios |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |Recupera elementos de un calendario |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |Crea un nuevo evento |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |Recupera un elemento específico de un calendario |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |Elimina un elemento de calendario |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |Actualiza parcialmente un elemento de calendario |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |Recupera carpetas de contactos |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |Recupera contactos de una carpeta de contactos |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |Crea un nuevo contacto |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |Recupera un contacto específico de una carpeta de contactos |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |Elimina un contacto |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |Actualiza parcialmente un contacto |

### <a name="outlookcom-triggers"></a>Desencadenadores de Outlook.com
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Al iniciarse un evento pronto |Desencadena un flujo cuando se inicia un evento de calendario próximo |
| Al llegar correo electrónico nuevo |Desencadena un flujo cuando llega un correo electrónico nuevo |
| Con nuevos elementos |Se desencadena cuando se crea un nuevo elemento de calendario |
| Al actualizar elementos |Se desencadena cuando se modifica un elemento de calendario |

## <a name="create-a-connection-to-outlookcom"></a>Creación de una conexión a Outlook.com
Para crear aplicaciones lógicas con Outlook.com, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar las credenciales de Outlook.com |

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.  
>
>

## <a name="reference-for-outlookcom"></a>Referencia para Outlook.com
Se aplica a la versión: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
Al iniciarse un evento pronto: desencadena un flujo cuando se inicia un evento de calendario próximo

```GET: /Events/OnUpcomingEvents```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |yes |query |Ninguna |Identificador único del calendario |
| lookAheadTimeInMinutes |integer |no |query |15 |Tiempo (en minutos) por delante para buscar próximos eventos |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="getemails"></a>GetEmails
Obtener correos electrónicos: recupera correos electrónicos de una carpeta

```GET: /Mail```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| folderPath |cadena |no |query |Bandeja de entrada |Ruta de acceso de la carpeta para recuperar mensajes (predeterminada: 'Bandeja de entrada') |
| top |integer |no |query |10 |Número de correos electrónicos para recuperar (valor predeterminado: 10) |
| fetchOnlyUnread |boolean |no |query |true |¿Recuperar solo correos electrónicos no leídos? |
| includeAttachments |boolean |no |query |false |Si se establece en true, los datos adjuntos también se recuperarán junto con el correo electrónico |
| searchQuery |cadena |no |query |Ninguna |Consulta de búsqueda para filtrar correos electrónicos |
| skip |integer |no |query |0 |Número de correos electrónicos para omitir (valor predeterminado: 0) |
| skipToken |cadena |no |query |Ninguna |Omitir token para obtener la página nueva |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="sendemail"></a>SendEmail
Enviar correo electrónico: envía un correo electrónico

```POST: /Mail```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |yes |body |Ninguna |Email |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="deleteemail"></a>DeleteEmail
Eliminar correo electrónico: elimina un correo electrónico mediante el identificador

```DELETE: /Mail/{messageId}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| messageId |cadena |Sí |path |Ninguna |Identificador del correo electrónico que se va a eliminar |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="markasread"></a>MarkAsRead
Marcar como leído: marca un correo electrónico como leído

```POST: /Mail/MarkAsRead/{messageId}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| messageId |cadena |Sí |path |Ninguna |Identificador del correo electrónico que se va a marcar como leído |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="replyto"></a>ReplyTo
Responder al correo electrónico: responde a un correo electrónico

```POST: /Mail/ReplyTo/{messageId}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| messageId |cadena |Sí |path |Ninguna |Identificador del correo electrónico al que se va a responder |
| comment |cadena |yes |query |Ninguna |Comentario de respuesta |
| replyAll |boolean |no |query |false |Responder a todos los destinatarios |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="getattachment"></a>GetAttachment
Obtener datos adjuntos: recupera los datos adjuntos del correo electrónico mediante el identificador

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| messageId |cadena |Sí |path |Ninguna |Identificador del correo electrónico |
| attachmentId |cadena |Sí |path |Ninguna |Identificador de los datos adjuntos para descargar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="onnewemail"></a>OnNewEmail
Al llegar correo electrónico nuevo: desencadena un flujo cuando llega un nuevo correo electrónico

```GET: /Mail/OnNewEmail```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| folderPath |cadena |no |query |Bandeja de entrada |Carpeta de correo electrónico para recuperar (predeterminada: Bandeja de entrada) |
| to |cadena |no |query |Ninguna |Direcciones de correo electrónico de destinatarios |
| from |cadena |no |query |Ninguna |Dirección De |
| importance |cadena |no |query |Normal |Importancia del correo electrónico (alta, normal o baja) (predeterminada: Normal) |
| fetchOnlyWithAttachment |boolean |no |query |false |Recuperar solo correos electrónicos con datos adjuntos |
| includeAttachments |boolean |no |query |false |Incluir datos adjuntos |
| subjectFilter |cadena |no |query |Ninguna |Cadena que se va a buscar en el asunto |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Accepted |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
Enviar correo electrónico con opciones: envío de un correo electrónico con varias opciones y espera de una respuesta del destinatario con una de las opciones

```POST: /mailwithoptions/$subscriptions```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |yes |body |Ninguna |Solicitud de suscripción para correo electrónico con opciones |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 201 |Suscripción creada |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="sendapprovalmail"></a>SendApprovalMail
Enviar correo electrónico de aprobación: envío de un correo electrónico de aprobación y espera de una respuesta del destinatario

```POST: /approvalmail/$subscriptions```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |yes |body |Ninguna |Solicitud de suscripción para correo electrónico de aprobación |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 201 |Suscripción creada |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="calendargettables"></a>CalendarGetTables
Obtener calendarios: recupera calendarios

```GET: /datasets/calendars/tables```

No hay parámetros para esta llamada

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendargetitems"></a>CalendarGetItems
Obtener eventos: recupera elementos de un calendario

```GET: /datasets/calendars/tables/{table}/items```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único del calendario que se va a recuperar |
| $filter |cadena |no |query |None |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |cadena |no |query |None |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |integer |no |query |None |Número de entradas para omitir (valor predeterminado = 0) |
| $top |integer |no |query |None |Número máximo de entradas para recuperar (valor predeterminado = 256) |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendarpostitem"></a>CalendarPostItem
Crear evento: crea un nuevo evento

```POST: /datasets/calendars/tables/{table}/items```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de un calendario |
| item | |Sí |body |Ninguna |Elemento de calendario para crear |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendargetitem"></a>CalendarGetItem
Obtener evento: recupera un elemento específico de un calendario

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de un calendario |
| id |cadena |Sí |path |Ninguna |Identificador único de un elemento de calendario que se va a recuperar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
Eliminar evento: elimina un elemento de calendario

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de un calendario |
| id |cadena |Sí |path |Ninguna |Identificador único de elemento de calendario para eliminar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendarpatchitem"></a>CalendarPatchItem
Actualizar evento: actualiza parcialmente un elemento de calendario

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de un calendario |
| id |cadena |Sí |path |Ninguna |Identificador único de elemento de calendario para actualizar |
| item | |Sí |body |Ninguna |Elemento de calendario para actualizar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Con nuevos elementos: se desencadena cuando se crea un nuevo elemento de calendario

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de un calendario |
| $filter |cadena |no |query |None |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |cadena |no |query |None |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |integer |no |query |None |Número de entradas para omitir (valor predeterminado = 0) |
| $top |integer |no |query |None |Número máximo de entradas para recuperar (valor predeterminado = 256) |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
Al actualizar elementos: se desencadena cuando se modifica un elemento de calendario

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de un calendario |
| $filter |cadena |no |query |None |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |cadena |no |query |None |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |integer |no |query |None |Número de entradas para omitir (valor predeterminado = 0) |
| $top |integer |no |query |None |Número máximo de entradas para recuperar (valor predeterminado = 256) |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="contactgettables"></a>ContactGetTables
Obtener carpetas de contactos: recupera carpetas de contactos

```GET: /datasets/contacts/tables```

No hay parámetros para esta llamada

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="contactgetitems"></a>ContactGetItems
Obtener contactos: recupera contactos de una carpeta de contactos

```GET: /datasets/contacts/tables/{table}/items```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de la carpeta de contactos que se va a recuperar |
| $filter |cadena |no |query |None |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |cadena |no |query |None |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |integer |no |query |None |Número de entradas para omitir (valor predeterminado = 0) |
| $top |integer |no |query |None |Número máximo de entradas para recuperar (valor predeterminado = 256) |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="contactpostitem"></a>ContactPostItem
Crear contacto: crea un nuevo contacto

```POST: /datasets/contacts/tables/{table}/items```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de una carpeta de contactos |
| item | |Sí |body |Ninguna |Contacto para crear |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="contactgetitem"></a>ContactGetItem
Obtener contacto: recupera un contacto específico de una carpeta de contactos

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de una carpeta de contactos |
| id |cadena |Sí |path |Ninguna |Identificador único de un contacto para recuperar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="contactdeleteitem"></a>ContactDeleteItem
Eliminar contacto: elimina un contacto

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de una carpeta de contactos |
| id |cadena |Sí |path |Ninguna |Identificador único de contacto para eliminar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="contactpatchitem"></a>ContactPatchItem
Actualizar contacto: actualiza parcialmente un contacto

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| table |cadena |Sí |path |Ninguna |Identificador único de una carpeta de contactos |
| id |cadena |Sí |path |Ninguna |Identificador único de contacto para actualizar |
| item | |Sí |body |Ninguna |Elemento de contacto para actualizar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="object"></a>Objeto
### <a name="sendmessage"></a>SendMessage
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Datos adjuntos |array |No |
| De |string |No |
| Cc |string |No |
| CCO |string |No |
| Asunto |string |Sí |
| Cuerpo |string |Sí |
| Importancia |string |No |
| IsHtml |boolean |No |
| Para |string |Sí |

### <a name="sendattachment"></a>SendAttachment
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| @odata.type |string |No |
| Nombre |string |Sí |
| ContentBytes |string |Sí |

### <a name="receivemessage"></a>ReceiveMessage
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| IsRead |boolean |No |
| HasAttachment |boolean |No |
| DateTimeReceived |string |No |
| Datos adjuntos |array |No |
| De |string |No |
| Cc |string |No |
| CCO |string |No |
| Asunto |string |Sí |
| Cuerpo |string |Sí |
| Importancia |string |No |
| IsHtml |boolean |No |
| Para |string |Sí |

### <a name="receiveattachment"></a>ReceiveAttachment
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |Sí |
| ContentType |string |Sí |
| @odata.type |string |No |
| Nombre |string |Sí |
| ContentBytes |string |Sí |

### <a name="digestmessage"></a>DigestMessage
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Asunto |string |Sí |
| Cuerpo |string |No |
| Importancia |string |No |
| Digest |array |Sí |
| Datos adjuntos |array |No |
| Para |string |Sí |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| tabular |not defined |No |
| blob |not defined |No |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| de origen |string |No |
| DisplayName |string |No |
| urlEncoding |string |No |
| tableDisplayName |string |No |
| tablePluralName |string |No |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| de origen |string |No |
| DisplayName |string |No |
| urlEncoding |string |No |

### <a name="tablemetadata"></a>TableMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |No |
| título |string |No |
| x-ms-permission |string |No |
| x-ms-capabilities |not defined |No |
| schema |not defined |No |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| sortRestrictions |not defined |No |
| filterRestrictions |not defined |No |
| filterFunctions |array |No |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| sortable |boolean |No |
| unsortableProperties |array |No |
| ascendingOnlyProperties |array |No |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| filterable |boolean |No |
| nonFilterableProperties |array |No |
| requiredProperties |array |No |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| NotificationUrl |string |No |
| Message |not defined |No |

### <a name="messagewithoptions"></a>MessageWithOptions
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Asunto |string |Sí |
| Opciones |string |Sí |
| Cuerpo |string |No |
| Importancia |string |No |
| Datos adjuntos |array |No |
| Para |string |Sí |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| resource |string |No |
| notificationType |string |No |
| notificationUrl |string |No |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| NotificationUrl |string |No |
| Message |not defined |No |

### <a name="approvalmessage"></a>ApprovalMessage
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Asunto |string |Sí |
| Opciones |string |Sí |
| Cuerpo |string |No |
| Importancia |string |No |
| Datos adjuntos |array |No |
| Para |string |Sí |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| SelectedOption |string |No |

### <a name="tableslist"></a>TablesList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="table"></a>Tabla
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |No |
| DisplayName |string |No |

### <a name="item"></a>Elemento
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ItemInternalId |string |No |

### <a name="calendaritemslist"></a>CalendarItemsList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="calendaritem"></a>CalendarItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ItemInternalId |string |No |

### <a name="contactitemslist"></a>ContactItemsList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="contactitem"></a>ContactItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ItemInternalId |string |No |

### <a name="datasetslist"></a>DataSetsList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="dataset"></a>DataSet
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |No |
| DisplayName |string |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)



<!--HONumber=Nov16_HO3-->


