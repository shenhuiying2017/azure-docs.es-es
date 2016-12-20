---
title: "Incorporación del conector de Yammer a Logic Apps | Microsoft Docs"
description: "Información general del conector de Yammer con parámetros de la API de REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 274ddce2bd3586dfba9ee214055accdba52f3ba9


---
# <a name="get-started-with-the-yammer-connector"></a>Introducción al conector de Yammer
Conectarse a Yammer para acceder a conversaciones en la red empresarial.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas.
> 
> 

Con Yammer, puede:

* Compilar el flujo de negocio en función de los datos que obtiene de Yammer. 
* Usar desencadenadores cuando haya un nuevo mensaje en un grupo o en una fuente que le sigue.
* Usar acciones para publicar un mensaje, obtener todos los mensajes y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando aparece un nuevo mensaje, puede enviar un correo electrónico mediante Office 365.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Dropbox incluye los desencadenadores y las acciones siguientes. 

| Desencadenador | Acciones |
| --- | --- |
| <ul><li>Cuando hay un mensaje en un grupo</li><li>Cuando hay un mensaje nuevo en la fuente Siguiendo</li></ul> |<ul><li>Obtener todos los mensajes</li><li>Obtiene los mensajes de un grupo</li><li>Obtiene los mensajes de la fuente Siguiendo</li><li>Publicar mensaje</li><li>Cuando hay un mensaje en un grupo</li><li>Cuando hay un mensaje nuevo en la fuente Siguiendo</li></ul> |

Todos los conectores admiten datos en formato JSON y XML. 

## <a name="create-a-connection-to-yammer"></a>Creación de una conexión a Yammer
Para usar el conector de Yammer, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar credenciales de Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="yammer-rest-api-reference"></a>Referencia de API de REST de Yammer
Esta documentación corresponde a la versión: 1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obtener todos los mensajes públicos en la red Yammer del usuario conectado
Corresponde a las conversaciones "All" en la interfaz web de Yammer.  
```GET: /messages.json```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| older_then |integer |no |query |Ninguna |Devuelve mensajes más antiguos que el identificador de mensaje especificado como cadena numérica. Resulta útil para paginar los mensajes. Por ejemplo, si actualmente está viendo 20 mensajes y el más antiguo es el número 2912, puede anexar "? older_than = 2912″ a su solicitud para obtener los 20 mensajes anteriores a los que está viendo. |
| newer_then |integer |no |query |Ninguna |Devuelve los mensajes más recientes que el identificador de mensaje especificado como cadena numérica. Se debe usar al sondear en busca de nuevos mensajes. Si está buscando mensajes, y el mensaje más reciente devuelto es 3516, puede realizar una solicitud con el parámetro "? newer_than = 3516″ para asegurarse de que no se obtienen copias duplicadas de mensajes que se encuentran ya en la página. |
| limit |integer |no |query |Ninguna |Devolver solo el número especificado de mensajes. |
| page |integer |no |query |Ninguna |Obtener la página especificada. Si los datos devueltos superan el límite, puede usar este campo para acceder a las páginas siguientes |

### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 408 |Tiempo de espera de solicitud |
| 429 |Demasiadas solicitudes |
| 500 |Error interno del servidor. Error desconocido |
| 503 |Servicio de Yammer no disponible |
| 504 |Tiempo de espera de puerta de enlace |
| default |Error en la operación. |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Publicar un mensaje en un grupo o una fuente de toda la compañía
Si se proporciona el identificador de grupo el mensaje se publicará en el grupo especificado, si no, se publicará en la fuente de toda la compañía.    
```POST: /messages.json``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| input | |Sí |body |Ninguna |Solicitud de publicación de mensaje |

### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 201 |Creado |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="message-yammer-message"></a>Message: mensaje de Yammer
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |no |
| content_excerpt |string |no |
| sender_id |integer |no |
| replied_to_id |integer |no |
| created_at |string |no |
| network_id |integer |no |
| message_type |string |no |
| sender_type |string |no |
| url |cadena |no |
| web_url |string |no |
| group_id |integer |no |
| body |not defined |no |
| thread_id |integer |no |
| direct_message |boolean |no |
| client_type |string |no |
| client_url |string |no |
| language |cadena |no |
| notified_user_ids |array |no |
| privacy |cadena |no |
| liked_by |not defined |no |
| system_message |boolean |no |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: representa una solicitud post del conector de Yammer para publicar en Yammer
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| body |cadena |yes |
| group_id |integer |no |
| replied_to_id |integer |no |
| direct_to_id |integer |no |
| broadcast |boolean |no |
| topic1 |cadena |no |
| topic2 |cadena |no |
| topic3 |cadena |no |
| topic4 |cadena |no |
| topic5 |cadena |no |
| topic6 |cadena |no |
| topic7 |cadena |no |
| topic8 |cadena |no |
| topic9 |cadena |no |
| topic10 |cadena |no |
| topic11 |cadena |no |
| topic12 |cadena |no |
| topic13 |cadena |no |
| topic14 |cadena |no |
| topic15 |cadena |no |
| topic16 |cadena |no |
| topic17 |cadena |no |
| topic18 |cadena |no |
| topic19 |cadena |no |
| topic20 |cadena |no |

#### <a name="messagelist-list-of-messages"></a>MessageList: lista de mensajes
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| messages |array |no |

#### <a name="messagebody-message-body"></a>MessageBody: cuerpo del mensaje
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| parsed |cadena |no |
| plain |cadena |no |
| rich |cadena |no |

#### <a name="likedby-liked-by"></a>LikedBy: le gusta a
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| count |integer |no |
| names |array |no |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: le gusta a
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| type |cadena |no |
| id |integer |no |
| full_name |string |no |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png



<!--HONumber=Nov16_HO3-->


