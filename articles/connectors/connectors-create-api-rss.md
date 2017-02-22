---
title: RSS | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El conector RSS permite a los usuarios publicar y recuperar elementos de fuente. También permite a los usuarios desencadenar operaciones cuando se publica un nuevo elemento en la fuente."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 5e13e126fecda66a453b4ced619016121af98b2c


---
# <a name="get-started-with-the-rss-connector"></a>Introducción al conector RSS
RSS es un formato de distribución web popular que se usa para publicar contenido de frecuente actualización, como entradas de blog y titulares de noticias.  Muchos editores de contenido ofrecen una fuente RSS para permitir que los usuarios se suscriban a ella.  Use el conector RSS para recuperar la información de la fuente y desencadenar flujos cuando se publiquen nuevos elementos en una fuente RSS.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. 
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector RSS se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML. 

 El conector RSS tiene las siguientes acciones y desencadenadores disponibles:

### <a name="rss-actions"></a>Acciones de RSS
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |Obtiene todos los elementos de fuente RSS. |

### <a name="rss-triggers"></a>Desencadenadores de RSS
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se publica un nuevo elemento de fuente |Desencadena un flujo de trabajo cuando se publica una nueva fuente. |

## <a name="create-a-connection-to-rss"></a>Creación de una conexión a RSS
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="reference-for-rss"></a>Referencia de RSS
Se aplica a la versión: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Cuando se publica un nuevo elemento de fuente: desencadena un flujo de trabajo cuando se publica una nueva fuente. 

```GET: /OnNewFeed``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |yes |query |Ninguna |URL de la fuente |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="listfeeditems"></a>ListFeedItems
Enumerar todos los elementos de fuente RSS: obtiene todos los elementos de fuente RSS. 

```GET: /ListFeedItems``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |yes |query |Ninguna |URL de la fuente |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse[FeedItem]
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="feeditem"></a>FeedItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |Sí |
| título |string |Sí |
| contenido |string |Sí |
| links |array |No |
| updatedOn |string |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


