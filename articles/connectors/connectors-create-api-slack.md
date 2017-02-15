---
title: " Uso del conector de Slack en Logic Apps | Microsoft Docs"
description: "Introducción al uso del conector de Slack en las aplicaciones lógicas del Servicio de aplicaciones de Microsoft Azure"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cacaa4a4a0089e1c3df54ae466af251026e1a55b


---
# <a name="get-started-with-the-slack-connector"></a>Introducción al conector de Slack
Slack es una herramienta de comunicación de equipo, que reúne todas las comunicaciones del equipo en un solo lugar, inmediatamente localizables y disponibles dondequiera que vaya.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Con el conector de Slack, puede:

* Usarlo para crear aplicaciones lógicas

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Hablemos de acciones y desencadenadores
El conector de Slack puede usarse como acción; no hay desencadenadores. Todos los conectores admiten datos en formato JSON y XML. 

 El conector de Slack tiene las siguientes acciones y desencadenadores disponibles:

### <a name="slack-actions"></a>Acciones de Slack
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| PostMessage |Publicar un mensaje en un canal especificado. |

## <a name="create-a-connection-to-slack"></a>Creación de una conexión a Slack
Para usar el conector de Slack, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar credenciales de Slack |

Siga estos pasos para iniciar sesión en Slack y completar la configuración de la **conexión** de Slack en la aplicación lógica:

1. Seleccione **Periodicidad**
2. Seleccione un valor para **Frequency** (Frecuencia) y especifique el correspondiente a **Interval** (Intervalo)
3. Seleccione **Add an action**(Agregar una acción)  
   ![Configurar Slack][1]  
4. Escriba Slack en el cuadro de búsqueda y espere a que la búsqueda devuelva todas las entradas que incluyan Slack en el nombre.
5. Seleccione **Slack - exponer mensaje**
6. Seleccione **Sign in to Slack** (Iniciar sesión en Slack):  
   ![Configurar Slack][2]
7. Especifique sus credenciales de Slack para iniciar sesión y autorizar la aplicación    
   ![Configurar Slack][3]  
8. Se le redirigirá a la página de inicio de sesión de su organización. **Autorice** la interacción de Slack con la aplicación lógica:      
   ![Configurar Slack][5] 
9. Una vez completada la autorización se le redirigirá a la aplicación lógica para terminar mediante la configuración de la sección **Slack - obtener todos los mensajes** . Agregue otros desencadenadores y acciones que necesite.  
   ![Configurar Slack][6]
10. Para guardar el trabajo, seleccione **Guardar** en la barra de menús superior.

> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="slack-rest-api-reference"></a>Referencia de la API de REST de Slack
#### <a name="this-documentation-is-for-version-10"></a>Esta documentación corresponde a la versión: 1.0
### <a name="post-a-message-to-a-specified-channel"></a>Publicar un mensaje en un canal especificado.
**```POST: /chat.postMessage```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| channel |cadena |yes |query |Ninguna |Canal, grupo privado o canal de mensajería instantánea al que se envía el mensaje. Puede ser un nombre (por ejemplo: #general) o un identificador codificado. |
| text |cadena |yes |query |Ninguna |Texto del mensaje para enviar. Para ver las opciones de formato, consulte https://api.slack.com/docs/formatting. |
| nombre de usuario |cadena |no |query |Ninguna |Nombre del bot |
| as_user |boolean |no |query |Ninguna |Pasar true para publicar el mensaje como usuario autenticado y no como bot |
| parse |cadena |no |query |Ninguna |Cambie la forma en que se tratan los mensajes. Para más información, consulte https://api.slack.com/docs/formatting. |
| link_names |integer |no |query |Ninguna |Buscar y vincular nombres de usuario y nombres de canal. |
| unfurl_links |boolean |no |query |Ninguna |Pasar true para habilitar el despliegue de contenido basado principalmente en texto. |
| unfurl_media |boolean |no |query |Ninguna |Pasar false para deshabilitar el despliegue de contenido multimedia. |
| icon_url |string |no |query |Ninguna |Dirección URL de una imagen que se usa como icono para este mensaje |
| icon_emoji |string |no |query |Ninguna |Emoji que se usa como icono para este mensaje |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 408 |Tiempo de espera de solicitud |
| 429 |Demasiadas solicitudes |
| 500 |Error interno del servidor. Error desconocido |
| 503 |Servicio de Slack no disponible |
| 504 |Tiempo de espera de puerta de enlace |
| default |Error en la operación. |

- - -
## <a name="object-definitions"></a>Definiciones de objeto:
 **Mensaje**: mensaje de Yammer

Propiedades obligatorias para Message:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| id |integer |
| content_excerpt |string |
| sender_id |integer |
| replied_to_id |integer |
| created_at |string |
| network_id |integer |
| message_type |string |
| sender_type |string |
| url |string |
| web_url |string |
| group_id |integer |
| body |not defined |
| thread_id |integer |
| direct_message |boolean |
| client_type |string |
| client_url |string |
| language |string |
| notified_user_ids |array |
| privacy |string |
| liked_by |not defined |
| system_message |boolean |

 **PostOperationRequest**: representa una solicitud post del conector de Yammer para publicar en Yammer

Propiedades necesarias para PostOperationRequest:

body

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| body |string |
| group_id |integer |
| replied_to_id |integer |
| direct_to_id |integer |
| broadcast |boolean |
| topic1 |cadena |
| topic2 |cadena |
| topic3 |cadena |
| topic4 |cadena |
| topic5 |cadena |
| topic6 |cadena |
| topic7 |cadena |
| topic8 |cadena |
| topic9 |cadena |
| topic10 |cadena |
| topic11 |cadena |
| topic12 |cadena |
| topic13 |cadena |
| topic14 |cadena |
| topic15 |cadena |
| topic16 |cadena |
| topic17 |cadena |
| topic18 |cadena |
| topic19 |cadena |
| topic20 |cadena |

 **MessageList**: lista de mensajes

Propiedades obligatorias para MessageList:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| messages |array |

 **MessageBody**: cuerpo del mensaje

Propiedades obligatorias para MessageBody:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| parsed |cadena |
| plain |cadena |
| rich |cadena |

 **LikedBy**: le gusta a

Propiedades obligatorias para LikedBy:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| count |integer |
| names |array |

 **YammmerEntity**: le gusta a

Propiedades obligatorias para YammmerEntity:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| type |cadena |
| id |integer |
| full_name |string |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

## <a name="object-definitions"></a>Definiciones de objeto:
 **WebResultModel**: resultados de búsqueda en web de Bing

Propiedades obligatorias para WebResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Título |cadena |
| Descripción |cadena |
| DisplayUrl |cadena |
| id |cadena |
| FullUrl |cadena |

 **VideoResultModel**: resultados de la búsqueda de vídeo de Bing

Propiedades necesarias para VideoResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Título |cadena |
| DisplayUrl |cadena |
| id |cadena |
| MediaUrl |cadena |
| Tiempo de ejecución |integer |
| Miniatura |not defined |

 **ThumbnailModel**: propiedades de vista en miniatura del elemento multimedia

Propiedades necesarias para ThumbnailModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| MediaUrl |cadena |
| ContentType |cadena |
| Ancho |integer |
| Alto |integer |
| FileSize |integer |

 **ImageResultModel**: resultados de búsqueda de imagen de Bing

Propiedades obligatorias para ImageResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Título |cadena |
| DisplayUrl |cadena |
| id |cadena |
| MediaUrl |cadena |
| SourceUrl |cadena |
| Miniatura |not defined |

 **NewsResultModel**: resultados de búsqueda de noticias de Bing

Propiedades necesarias para NewsResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Título |cadena |
| Descripción |cadena |
| DisplayUrl |cadena |
| id |cadena |
| Origen |cadena |
| Date |cadena |

 **SpellResultModel**: resultados de sugerencias de ortografía de Bing

Propiedades necesarias para SpellResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| id |cadena |
| Valor |cadena |

 **RelatedSearchResultModel**: resultados de búsqueda relacionados con Bing

Propiedades necesarias para RelatedSearchResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Título |cadena |
| id |cadena |
| BingUrl |cadena |

 **CompositeSearchResultModel**: resultados de búsqueda compuesta de Bing

Propiedades necesarias para CompositeSearchResultModel:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| WebResultsTotal |integer |
| ImageResultsTotal |integer |
| VideoResultsTotal |integer |
| NewsResultsTotal |integer |
| SpellSuggestionsTotal |integer |
| WebResults |array |
| ImageResults |array |
| VideoResults |array |
| NewsResults |array |
| SpellSuggestionResults |array |
| RelatedSearchResults |array |

## <a name="object-definitions"></a>Definiciones de objeto:
 **PostOperationResponse**: representa la respuesta de una operación post del conector de Slack para publicar en Slack

Propiedades obligatorias para PostOperationResponse:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| OK |boolean |
| channel |cadena |
| ts |cadena |
| Mensaje |not defined |
| error |cadena |

 **MessageItem**: un mensaje de canal.

Propiedades obligatorias para MessageItem:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| text |cadena |
| id |cadena |
| user |cadena |
| created |integer |
| is_user-deleted |boolean |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png



<!--HONumber=Nov16_HO3-->


