<properties
    pageTitle="Incorporación del conector de Búsqueda de Bing a aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de Búsqueda de Bing con parámetros de la API de REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Introducción al conector de Búsqueda de Bing 
Conéctese a Búsqueda de Bing para buscar noticias, vídeos y mucho más. Con Búsqueda de Bing, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda.
- Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Búsqueda de Bing incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Buscar en web</li><li>Buscar vídeos</li><li>Buscar imágenes</li><li>Buscar noticias</li><li>Buscar relacionados</li><li>Buscar ortografías</li><li>Buscar todo</li></ul>

Todos los conectores admiten datos en formato JSON y XML.


## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Buscar en web 
Recupera los sitios web de una búsqueda de Bing. ```GET: /Web```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query| Ninguna|Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query| Ninguna|Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|webFileType|string|no|query|Ninguna |Tipo de archivo para restringir la búsqueda (ejemplo, 'DOC')|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar vídeos 
Recupera vídeos de una búsqueda de Bing. ```GET: /Video```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query| Ninguna|El número máximo de resultados que se devolverán|
|startOffset|integer|no|query|Ninguna |Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|videoFilters|string|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Por ejemplo: "Duration:Short+Resolution:High"|
|videoSortBy|string|no|query|Ninguna |Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar imágenes    
Recupera las imágenes de una búsqueda de Bing. ```GET: /Image```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query|Ninguna |Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query| Ninguna|Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|imageFilters|string|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[ancho]</li><li>Size:Height:[alto]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Por ejemplo: "Size:Small+Aspect:Square"|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar noticias    
Recupera los resultados de noticias de una búsqueda de Bing. ```GET: /News```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|newsSortBy|string|no|query| Ninguna|Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|
|newsCategory|string|no|query| |Categoría de noticias para restringir la búsqueda (ejemplo: 'rt\_Business')|
|newsLocationOverride|string|no|query|Ninguna |Invalida la detección de ubicación de Bing. Este parámetro solo es aplicable en el mercado de es-ES. El formato de entrada es US./<estado/> (ejemplo: "US.WA")|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar ortografías    
Recupera sugerencias de ortografía. ```GET: /SpellingSuggestions```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query| Ninguna|Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query| Ninguna|Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar relacionados    
Recupera los resultados de búsqueda relacionados de una búsqueda de Bing. ```GET: /RelatedSearch```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query| Ninguna|Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar todo    
Recupera todos los sitios web, vídeos, imágenes, etc., de una búsqueda de Bing. ```GET: /CompositeSearch```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query|Ninguna |Número de resultados que se van a omitir|
|adultContent|string|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|string|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|webFileType|string|no|query|Ninguna |Tipo de archivo para restringir la búsqueda (ejemplo, 'DOC')|
|videoFilters|string|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Por ejemplo: "Duration:Short+Resolution:High"|
|videoSortBy|string|no|query|Ninguna |Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|
|imageFilters|string|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[ancho]</li><li>Size:Height:[alto]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Por ejemplo: "Size:Small+Aspect:Square"|
|newsSortBy|string|no|query|Ninguna |Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|
|newsCategory|string|no|query|Ninguna |Categoría de noticias para restringir la búsqueda (ejemplo: 'rt\_Business')|
|newsLocationOverride|string|no|query|Ninguna |Invalida la detección de ubicación de Bing. Este parámetro solo es aplicable en el mercado de es-ES. El formato de entrada es US./<estado/> (ejemplo: "US.WA")|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto

#### WebResultModel: resultados de búsqueda en web de Bing

|Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Título|string|no|
|Description|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|FullUrl|string|no|

#### VideoResultModel: resultados de la búsqueda de vídeo de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|MediaUrl|string|no|
|Tiempo de ejecución|integer|no|
|Miniatura|not defined|no|

#### ThumbnailModel: propiedades de vista en miniatura del elemento multimedia

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|MediaUrl|string|no|
|ContentType|string|no|
|Ancho|integer|no|
|Alto|integer|no|
|FileSize|integer|no|

#### ImageResultModel: resultados de búsqueda de imagen de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|MediaUrl|string|no|
|SourceUrl|string|no|
|Miniatura|not defined|no|

#### NewsResultModel: resultados de búsqueda de noticias de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|string|no|
|Description|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|Origen|string|no|
|Date|string|no|

#### SpellResultModel: resultados de sugerencias de ortografía de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Id|string|no|
|Valor|string|no|

#### RelatedSearchResultModel: resultados de búsqueda relacionados de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|string|no|
|Id|string|no|
|BingUrl|string|no|

#### CompositeSearchResultModel: resultados de búsqueda compuesta de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|WebResultsTotal|integer|no|
|ImageResultsTotal|integer|no|
|VideoResultsTotal|integer|no|
|NewsResultsTotal|integer|no|
|SpellSuggestionsTotal|integer|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

Volver a la [lista de API](apis-list.md).

<!---HONumber=AcomDC_0824_2016-->