---
title: "Incorporación del conector de Facebook a Logic Apps | Microsoft Docs"
description: "Información general del conector de Facebook con parámetros de la API de REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 7ac9d7eb9e322d8c44434add381030b51c9e9a4b


---
# <a name="get-started-with-the-facebook-connector"></a>Introducción al conector de Facebook
Conéctese a Facebook y publique en una biografía, obtenga una fuente de página y mucho más. 

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Con Facebook, puede hacer lo siguiente:

* Compilar el flujo de negocio en función de los datos que obtiene de Facebook. 
* Utilizar un desencadenador cuando se reciba un nuevo mensaje.
* Usar acciones para publicar en la biografía, obtener una fuente de página y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando haya un nuevo mensaje en su biografía, puede tomar ese mensaje e insertarlo en su fuente de Twitter. 

Para agregar una operación a las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector de Facebook incluye los siguientes desencadenadores y acciones. 

| Desencadenadores | Acciones |
| --- | --- |
| <ul><li>Cuando haya un nuevo mensaje en mi biografía</li></ul> |<ul><li>Obtener fuente de mi biografía</li><li>Publicar en mi biografía</li><li>Cuando haya un nuevo mensaje en mi biografía</li><li>Obtener fuente de página</li><li>Obtener biografía de usuario</li><li>Publicar en la página</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.

## <a name="create-a-connection-to-facebook"></a>Creación de una conexión a Facebook
Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Facebook.

1. Inicie sesión en su cuenta de Facebook.
2. Seleccione **Authorize**(Autorizar) y permita que sus aplicaciones lógicas se conecten y utilicen su aplicación de Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> Puede usar esta misma conexión de Facebook en otras aplicaciones lógicas.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referencia de API de REST de Swagger
Se aplica a la versión: 1.0.

### <a name="get-feed-from-my-timeline"></a>Obtener fuente de mi biografía
Obtiene las fuentes de la escala de tiempo del usuario que ha iniciado sesión.  
```GET: /me/feed```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| fields |cadena |no |query |Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture). |
| limit |integer |no |query |Ninguna |Número máximo de mensajes que se van a recuperar |
| por |cadena |no |query |Ninguna |Restringir la lista de mensajes a solo aquellos con ubicación adjuntada. |
| filter |cadena |no |query |Ninguna |Recuperar solo los mensajes que coincidan con un filtro de transmisión en particular. |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="post-to-my-timeline"></a>Publicar en mi biografía
Publica un mensaje de estado en la escala de tiempo del usuario que ha iniciado la sesión.  
```POST: /me/feed```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post |cadena |Sí |body |Ninguna |Nuevo mensaje que se va a publicar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>Cuando haya un nuevo mensaje en mi biografía
Desencadena un nuevo flujo cuando hay un nuevo mensaje en la escala de tiempo del usuario que ha iniciado la sesión.  
```GET: /trigger/me/feed```

No hay ningún parámetro. 

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-page-feed"></a>Obtener fuente de página
Obtener mensajes de la fuente de una página especificada.  
```GET: /{pageId}/feed```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| pageId |cadena |Sí |path |Ninguna |Identificador de la página de la que se deben recuperar los mensajes. |
| limit |integer |no |query |Ninguna |Número máximo de mensajes que se van a recuperar |
| include_hidden |boolean |no |query |Ninguna |Si se incluirán o no los mensajes que haya ocultado la página |
| fields |cadena |no |query |Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture). |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-user-timeline"></a>Obtener biografía de usuario
Obtener mensajes de la escala de tiempo de un usuario.  
```GET: /{userId}/feed```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| userId |cadena |Sí |path |Ninguna |Id. del usuario cuya biografía se debe recuperar. |
| limit |integer |no |query |Ninguna |Número máximo de mensajes que se van a recuperar |
| por |cadena |no |query |Ninguna |Restringir la lista de mensajes a solo aquellos con ubicación adjuntada. |
| filter |cadena |no |query |Ninguna |Recuperar solo los mensajes que coincidan con un filtro de transmisión en particular. |
| fields |cadena |no |query |Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture). |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="post-to-page"></a>Publicar en la página
Publicar un mensaje en una página de Facebook con el usuario que inició sesión.  
```POST: /{pageId}/feed```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| pageId |cadena |Sí |path |Ninguna |Id. de la página que se va a publicar |
| post |many |Sí |body |Ninguna |Nuevo mensaje para publicar. |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="getfeedresponse"></a>GetFeedResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| data |array |no |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| data |array |no |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: una sola entrada en la fuente de un perfil
El perfil puede ser un usuario, una página, una aplicación o un grupo. 

| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| admin_creator |array |no |
| caption |cadena |no |
| created_time |string |no |
| Descripción |cadena |no |
| feed_targeting |not defined |no |
| from |not defined |no |
| icon |cadena |no |
| is_hidden |boolean |no |
| is_published |boolean |no |
| link |cadena |no |
| message |cadena |no |
| Nombre |cadena |no |
| object_id |string |no |
| picture |cadena |no |
| place |not defined |no |
| privacy |not defined |no |
| propiedades |array |no |
| de origen |cadena |no |
| status_type |string |no |
| story |cadena |no |
| targeting |not defined |no |
| to |array |no |
| type |cadena |no |
| updated_time |string |no |
| with_tags |not defined |no |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: una sola entrada en la fuente de un perfil
El perfil puede ser un usuario, una página, una aplicación o un grupo.

| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| created_time |string |no |
| from |not defined |no |
| message |cadena |no |
| type |cadena |no |

#### <a name="adminitem"></a>AdminItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| link |cadena |no |

#### <a name="propertyitem"></a>PropertyItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |cadena |no |
| text |cadena |no |
| href |cadena |no |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| message |cadena |yes |
| link |cadena |no |
| picture |cadena |no |
| Nombre |cadena |no |
| caption |cadena |no |
| Descripción |cadena |no |
| place |cadena |no |
| etiquetas |cadena |no |
| privacy |not defined |no |
| object_attachment |string |no |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| message |cadena |yes |
| link |cadena |no |
| picture |cadena |no |
| Nombre |cadena |no |
| caption |cadena |no |
| Descripción |cadena |no |
| Acciones |array |no |
| place |cadena |no |
| etiquetas |cadena |no |
| object_attachment |string |no |
| targeting |not defined |no |
| feed_targeting |not defined |no |
| published |boolean |no |
| scheduled_publish_time |string |no |
| backdated_time |string |no |
| backdated_time_granularity |string |no |
| child_attachments |array |no |
| multi_share_end_card |boolean |no |

#### <a name="postfeedresponse"></a>PostFeedResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |

#### <a name="profilecollection"></a>ProfileCollection
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| data |array |no |

#### <a name="useritem"></a>UserItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| first_name |string |no |
| last_name |string |no |
| Nombre |cadena |no |
| gender |cadena |no |
| about |cadena |no |

#### <a name="actionitem"></a>ActionItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |cadena |no |
| link |cadena |no |

#### <a name="targetitem"></a>TargetItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| countries |array |no |
| locales |array |no |
| regions |array |no |
| cities |array |no |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: objeto que controla la dirección de la fuente de noticias de este mensaje
Es más probable que cualquier miembro de estos grupos vea este mensaje y existe una menor probabilidad en el caso de otras personas. Solo se aplica a las páginas.

| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| countries |array |no |
| regions |array |no |
| cities |array |no |
| age_min |integer |no |
| age_max |integer |no |
| genders |array |no |
| relationship_statuses |array |no |
| interested_in |array |no |
| college_years |array |no |
| interests |array |no |
| relevant_until |integer |no |
| education_statuses |array |no |
| locales |array |no |

#### <a name="placeitem"></a>PlaceItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| Nombre |cadena |no |
| overall_rating |número |no |
| location |not defined |no |

#### <a name="locationitem"></a>LocationItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| city |cadena |no |
| country |cadena |no |
| latitude |número |no |
| located_in |string |no |
| longitude |número |no |
| Nombre |cadena |no |
| region |cadena |no |
| state |cadena |no |
| street |cadena |no |
| zip |cadena |no |

#### <a name="privacyitem"></a>PrivacyItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Descripción |cadena |no |
| value |cadena |yes |
| allow |cadena |no |
| deny |cadena |no |
| friends |cadena |no |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| link |cadena |no |
| picture |cadena |no |
| image_hash |string |no |
| Nombre |cadena |no |
| Descripción |cadena |no |

#### <a name="postphotorequest"></a>PostPhotoRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| url |cadena |yes |
| caption |cadena |no |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |yes |
| post_id |string |yes |

#### <a name="postvideorequest"></a>PostVideoRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| videoData |cadena |yes |
| Descripción |cadena |yes |
| título |cadena |yes |
| uploadedVideoName |cadena |no |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| data |not defined |yes |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| url |cadena |yes |
| is_silhouette |boolean |yes |
| height |cadena |no |
| width |cadena |no |

#### <a name="geteventresponse"></a>GetEventResponse
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| data |array |yes |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |yes |
| Nombre |cadena |yes |
| start_time |string |no |
| end_time |string |no |
| timezone |cadena |no |
| location |cadena |no |
| Descripción |cadena |no |
| ticket_uri |string |no |
| rsvp_status |string |Sí |

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).




<!--HONumber=Nov16_HO3-->


