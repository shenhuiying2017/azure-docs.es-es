---
title: SendGrid | Microsoft Docs
description: Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El proveedor de conexión SendGrid permite enviar correo electrónico y administrar listas de destinatarios.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Introducción al conector SendGrid
El proveedor de conexión SendGrid permite enviar correo electrónico y administrar listas de destinatarios.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
El conector SendGrid se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector de SendGrid tiene las siguientes acciones disponibles. No hay desencadenadores.

### Acciones de SendGrid
Puede realizar estas acciones:

| Acción | Description |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Envía un correo electrónico mediante la API de SendGrid (limitado a 10 000 destinatarios) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Agrega un destinatario individual a una lista de destinatarios |

## Creación de una conexión a SendGrid
Para crear aplicaciones lógicas con SendGrid, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| ApiKey |Sí |Proporciona la clave de API de SendGrid |

> [!INCLUDE [Pasos para crear una conexión a SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

## Referencia de SendGrid
Se aplica a la versión: 1.0

## SendEmail
Enviar correo electrónico: envía un correo electrónico mediante la API de SendGrid (limitado a 10 000 destinatarios)

```POST: /api/mail.send.json```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| request | |yes |body |Ninguna |Mensaje de correo electrónico para enviar |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 429 |Demasiadas solicitudes |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## AddRecipientToList
Agregar un destinatario a la lista: agrega un destinatario individual a una lista de destinatarios

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| listId |string |yes |path |Ninguna |Identificador único de la lista de destinatarios |
| recipientId |string |yes |path |Ninguna |Identificador único del destinatario |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## Definiciones de objeto
### EmailRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| from |string |Sí |
| fromname |string |No |
| to |string |Sí |
| toname |string |No |
| subject |string |Sí |
| body |string |Sí |
| ishtml |boolean |No |
| cc |string |No |
| ccname |string |No |
| bcc |string |No |
| bccname |string |No |
| replyto |string |No |
| fecha |string |No |
| encabezados |string |No |
| files |array |No |
| filenames |array |No |

### EmailResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| message |string |No |

### RecipientLists
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| lists |array |No |

### RecipientList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| name |string |No |
| recipient\_count |integer |No |

### Recipients
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| recipients |array |No |

### Recipient
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| email |string |No |
| last\_name |string |No |
| first\_name |string |No |
| id |string |No |

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->