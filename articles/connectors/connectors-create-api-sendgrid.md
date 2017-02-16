---
title: SendGrid | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El proveedor de conexión SendGrid permite enviar correo electrónico y administrar listas de destinatarios."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: e30231bd576436ae69f4fa42d0e2ab312c3938d6


---
# <a name="get-started-with-the-sendgrid-connector"></a>Introducción al conector SendGrid
El proveedor de conexión SendGrid permite enviar correo electrónico y administrar listas de destinatarios.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. 
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector SendGrid se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML. 

 El conector de SendGrid tiene las siguientes acciones disponibles. No hay desencadenadores.

### <a name="sendgrid-actions"></a>Acciones de SendGrid
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Envía un correo electrónico mediante la API de SendGrid (limitado a 10 000 destinatarios) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Agrega un destinatario individual a una lista de destinatarios |

## <a name="create-a-connection-to-sendgrid"></a>Creación de una conexión a SendGrid
Para crear aplicaciones lógicas con SendGrid, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| ApiKey |Sí |Proporciona la clave de API de SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

## <a name="reference-for-sendgrid"></a>Referencia de SendGrid
Se aplica a la versión: 1.0

## <a name="sendemail"></a>SendEmail
Enviar correo electrónico: envía un correo electrónico mediante la API de SendGrid (limitado a 10 000 destinatarios) 

```POST: /api/mail.send.json``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| request | |yes |body |Ninguna |Mensaje de correo electrónico para enviar |

#### <a name="response"></a>Response
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

## <a name="addrecipienttolist"></a>AddRecipientToList
Agregar un destinatario a la lista: agrega un destinatario individual a una lista de destinatarios 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| listId |string |Sí |path |Ninguna |Identificador único de la lista de destinatarios |
| recipientId |string |Sí |path |Ninguna |Identificador único del destinatario |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="emailrequest"></a>EmailRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| De |string |Sí |
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

### <a name="emailresponse"></a>EmailResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| message |string |No |

### <a name="recipientlists"></a>RecipientLists
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| lists |array |No |

### <a name="recipientlist"></a>RecipientList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| name |string |No |
| recipient_count |integer |No |

### <a name="recipients"></a>Recipients
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| recipients |array |No |

### <a name="recipient"></a>Recipient
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| email |string |No |
| last_name |string |No |
| first_name |string |No |
| id |string |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


