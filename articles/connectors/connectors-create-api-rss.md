---
title: RSS | Microsoft Docs
description: Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El conector RSS permite a los usuarios publicar y recuperar elementos de fuente. También permite a los usuarios desencadenar operaciones cuando se publica un nuevo elemento en la fuente.
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
# Introducción al conector RSS
RSS es un formato de distribución web popular que se usa para publicar contenido de frecuente actualización, como entradas de blog y titulares de noticias. Muchos editores de contenido ofrecen una fuente RSS para permitir que los usuarios se suscriban a ella. Use el conector RSS para recuperar la información de la fuente y desencadenar flujos cuando se publiquen nuevos elementos en una fuente RSS.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
El conector RSS se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector RSS tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de RSS
Puede realizar estas acciones:

| Acción | Description |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |Obtiene todos los elementos de fuente RSS. |

### Desencadenadores de RSS
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se publica un nuevo elemento de fuente |Desencadena un flujo de trabajo cuando se publica una nueva fuente. |

## Creación de una conexión a RSS
> [!INCLUDE [Pasos para crear una conexión a una fuente RSS](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## Referencia de RSS
Se aplica a la versión: 1.0

## OnNewFeed
Cuando se publica un nuevo elemento de fuente: desencadena un flujo de trabajo cuando se publica una nueva fuente.

```GET: /OnNewFeed```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |yes |query |Ninguna |URL de la fuente |

#### Response
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

## ListFeedItems
Enumerar todos los elementos de fuente RSS: obtiene todos los elementos de fuente RSS.

```GET: /ListFeedItems```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |yes |query |Ninguna |URL de la fuente |

#### Response
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

## Definiciones de objeto
### TriggerBatchResponse[FeedItem]
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### FeedItem
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |Sí |
| título |string |Sí |
| contenido |string |Sí |
| links |array |No |
| updatedOn |string |No |

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->