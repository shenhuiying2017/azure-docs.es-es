---
title: "Incorporación del conector de Búsqueda de Bing a aplicaciones lógicas | Microsoft Docs"
description: "Información general del conector de Búsqueda de Bing con parámetros de la API de REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405


---
# <a name="get-started-with-the-bing-search-connector"></a>Introducción al conector de Búsqueda de Bing
Conéctese a Búsqueda de Bing para buscar noticias, vídeos y mucho más. Con Búsqueda de Bing, puede hacer lo siguiente: 

* Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda. 
* Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Búsqueda de Bing incluye las siguientes acciones. No hay desencadenadores. 

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Buscar en web</li><li>Buscar vídeos</li><li>Buscar imágenes</li><li>Buscar noticias</li><li>Buscar relacionados</li><li>Buscar ortografías</li><li>Buscar todo</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### <a name="search-web"></a>Buscar en web
Recupera los sitios web de una búsqueda de Bing.  
```GET: /Web```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |
| webFileType |cadena |no |query |None |Tipo de archivo para restringir la búsqueda (ejemplo, 'DOC') |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="search-videos"></a>Buscar vídeos
Recupera vídeos de una búsqueda de Bing.  
```GET: /Video```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |
| videoFilters |cadena |no |query |None |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos.  Valores válidos:  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Por ejemplo: "Duration:Short+Resolution:High" |
| videoSortBy |cadena |no |query |None |Criterio de ordenación de los resultados. Valores válidos:  <ul><li>Date</li><li>Relevancia</li></ul> <p>El orden de clasificación de fecha implica descendente.</p> |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="search-images"></a>Buscar imágenes
Recupera las imágenes de una búsqueda de Bing.  
```GET: /Image```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |
| imageFilters |cadena |no |query |None |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos:  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Por ejemplo: "Size:Small+Aspect:Square" |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="search-news"></a>Buscar noticias
Recupera los resultados de noticias de una búsqueda de Bing.  
```GET: /News```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |
| newsSortBy |cadena |no |query |None |Criterio de ordenación de los resultados. Valores válidos:  <ul><li>Date</li><li>Relevancia</li></ul> <p>El orden de clasificación de fecha implica descendente.</p> |
| newsCategory |cadena |no |query | |Categoría de noticias para restringir la búsqueda (ejemplo: 'rt_Business') |
| newsLocationOverride |cadena |no |query |None |Invalida la detección de ubicación de Bing. Este parámetro solo es aplicable en el mercado de en-US. El formato de entrada es US./<state /> (ejemplo: "US.WA") |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="search-spellings"></a>Buscar ortografías
Recupera sugerencias de ortografía.  
```GET: /SpellingSuggestions```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="search-related"></a>Buscar relacionados
Recupera los resultados de la búsqueda relacionados de una búsqueda de Bing.  
```GET: /RelatedSearch```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="search-all"></a>Buscar todo
Recupera todos los sitios web, vídeos, imágenes, etc., de una búsqueda de Bing.  
```GET: /CompositeSearch```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que se va a buscar (ejemplo, 'xbox') |
| maxResult |integer |no |query |None |El número máximo de resultados que se devolverán |
| startOffset |integer |no |query |None |Número de resultados que se van a omitir |
| adultContent |cadena |no |query |None |Filtro de contenido para adultos. Valores válidos:  <ul><li>Off</li><li>Moderado</li><li>Strict</li></ul> |
| market |cadena |no |query |None |Mercado o región para restringir la búsqueda (ejemplo, en-US) |
| longitude |número |no |query |None |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450) |
| latitude |número |no |query |None |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696) |
| webFileType |cadena |no |query |None |Tipo de archivo para restringir la búsqueda (ejemplo, 'DOC') |
| videoFilters |cadena |no |query |None |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos.  Valores válidos:  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Por ejemplo: "Duration:Short+Resolution:High" |
| videoSortBy |cadena |no |query |None |Criterio de ordenación de los resultados. Valores válidos:  <ul><li>Date</li><li>Relevancia</li></ul> <p>El orden de clasificación de fecha implica descendente.</p> |
| imageFilters |cadena |no |query |None |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos:  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Por ejemplo: "Size:Small+Aspect:Square" |
| newsSortBy |cadena |no |query |None |Criterio de ordenación de los resultados. Valores válidos:  <ul><li>Date</li><li>Relevancia</li></ul> <p>El orden de clasificación de fecha implica descendente.</p> |
| newsCategory |cadena |no |query |Ninguna |Categoría de noticias para restringir la búsqueda (ejemplo: 'rt_Business') |
| newsLocationOverride |cadena |no |query |None |Invalida la detección de ubicación de Bing. Este parámetro solo es aplicable en el mercado de en-US. El formato de entrada es US./<state /> (ejemplo: "US.WA") |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel: resultados de búsqueda en web de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Título |cadena |no |
| Descripción |cadena |no |
| DisplayUrl |cadena |no |
| Id |cadena |no |
| FullUrl |cadena |no |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel: resultados de la búsqueda de vídeo de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Título |cadena |no |
| DisplayUrl |cadena |no |
| Id |cadena |no |
| MediaUrl |cadena |no |
| Tiempo de ejecución |integer |no |
| Miniatura |not defined |no |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel: propiedades de vista en miniatura del elemento multimedia
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| MediaUrl |cadena |no |
| ContentType |cadena |no |
| Ancho |integer |no |
| Alto |integer |no |
| FileSize |integer |no |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel: resultados de búsqueda de imagen de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Título |cadena |no |
| DisplayUrl |cadena |no |
| Id |cadena |no |
| MediaUrl |cadena |no |
| SourceUrl |cadena |no |
| Miniatura |not defined |no |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel: resultados de búsqueda de noticias de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Título |cadena |no |
| Descripción |cadena |no |
| DisplayUrl |cadena |no |
| Id |cadena |no |
| Origen |cadena |no |
| Date |cadena |no |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel: resultados de sugerencias de ortografía de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| Valor |cadena |no |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel: resultados de búsqueda relacionados de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Título |cadena |no |
| Id |cadena |no |
| BingUrl |cadena |no |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel: resultados de búsqueda compuesta de Bing
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| WebResultsTotal |integer |no |
| ImageResultsTotal |integer |no |
| VideoResultsTotal |integer |no |
| NewsResultsTotal |integer |no |
| SpellSuggestionsTotal |integer |no |
| WebResults |array |no |
| ImageResults |array |no |
| VideoResults |array |no |
| NewsResults |array |no |
| SpellSuggestionResults |array |no |
| RelatedSearchResults |array |no |

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).




<!--HONumber=Jan17_HO3-->


