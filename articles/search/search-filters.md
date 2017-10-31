---
title: Filtros en Azure Search | Microsoft Docs
description: "Puede filtrar por identidad de seguridad del usuario, idioma, geolocalización o valores numéricos para reducir los resultados de búsqueda para las consultas de Azure Search, un servicio de búsqueda en la nube hospedado de Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/19/2017
ms.author: heidist
ms.openlocfilehash: 2e8721684b1d4ed0e7392d85ea1df0f595860a05
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="filters-in-azure-search"></a>Filtros de Azure Search 

Un *filtro* proporciona criterios para seleccionar los documentos que se usan en una consulta de Azure Search. La búsqueda sin filtrar incluye todos los documentos del índice. Un filtro delimita el ámbito de una consulta de búsqueda a un subconjunto de documentos. Por ejemplo, un filtro puede restringir la búsqueda de texto completo a aquellos productos que tienen una marca o un color específicos y un precio de venta al público superior a un umbral determinado.

Algunas experiencias de búsqueda imponen los requisitos de filtro como parte de la implementación, pero puede usar filtros siempre que quiera restringir la búsqueda mediante criterios *basados en valores*; por ejemplo, puede delimitar la búsqueda al tipo de producto "libros" de la categoría "no ficción" publicados por "Simon & Schuster".

Si su objetivo es la búsqueda orientada a *estructuras* de datos específicos (limitar la búsqueda a un campo de opiniones de cliente, por ejemplo), existen métodos alternativos y se describen a continuación.

## <a name="when-to-use-a-filter"></a>Cuándo se debe usar un filtro

Los filtros son fundamentales para varias experiencias de búsqueda, como la búsqueda de servicios cercanos, la navegación por facetas y los filtros de seguridad que muestran únicamente los documentos que un usuario tiene permiso para ver. Si implementa alguna de estas experiencias, se necesita un filtro. El filtro asociado a la consulta de búsqueda proporciona coordenadas de geolocalización, la categoría de faceta que seleccionó el usuario o el id. de seguridad del solicitante.

A continuación se incluyen algunos escenarios de ejemplo:

1. Use un filtro para segmentar el índice según los valores de datos del índice. A partir de un esquema con valores de ciudad, tipo de alojamiento y servicios, puede crear un filtro para seleccionar explícitamente los documentos que cumplan los criterios (en Seattle, pisos, frente al mar). 

  Las búsquedas de texto completo con las mismas entradas a menudo generan resultados similares, pero un filtro es más preciso, ya que requiere a una coincidencia exacta del término del filtro en el contenido del índice. 

2. Use un filtro si la experiencia de búsqueda tiene un requisito de filtro:

 * La [navegación por facetas](search-faceted-navigation.md) usa un filtro para devolver la categoría de faceta que seleccionó el usuario.
 * La búsqueda georreferenciada usa un filtro para pasar las coordenadas de la ubicación actual en aplicaciones que buscan servicios cercanos. 
 * Los filtros de seguridad pasan los identificadores de seguridad como criterios de filtro, donde una coincidencia en el índice actúa como un proxy para los derechos de acceso al documento.

3. Use un filtro para aplicar criterios de búsqueda a un campo numérico. 

  Los campos numéricos se pueden recuperar en el documento y pueden aparecer en los resultados de búsqueda, pero no se pueden hacer búsquedas (de texto completo) en ellos individualmente. Si necesita criterios de selección basados en datos numéricos, use un filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reducir el ámbito

Si quiere un efecto de restricción en los resultados de búsqueda, los filtros no son la única opción. Estas alternativas podrían ser una solución mejor, en función de su objetivo:

 + El parámetro de consulta `searchFields` delimita la consulta a unos campos específicos. Por ejemplo, si el índice proporciona campos independientes para descripciones en inglés y español, puede usar searchFields para definir como destino los campos que quiera usar para la búsqueda de texto completo. 

+ El parámetro `$select` se usa para especificar qué campos se deben incluir en un conjunto de resultados, lo que recorta de forma eficaz la respuesta antes de enviarla a la aplicación que realiza la llamada. Este parámetro no refina la consulta ni reduce la colección de documentos, pero, si su objetivo es una respuesta granular, considere la opción de usar este parámetro. 

Para obtener más información acerca de estos parámetros, consulte [Search Documents > Request > Query parameters](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) (Buscar en documentos > Solicitud > Parámetros de consulta).


## <a name="filters-in-the-query-pipeline"></a>Filtros en la canalización de consulta

En el momento de la consulta, un analizador de filtro acepta criterios como entrada, convierte la expresión en expresiones booleanas atómicas y genera un árbol de filtro, que, a continuación, se evalúa a través de campos por los que se puede filtrar en un índice.  

El filtrado se produce antes de la búsqueda y define los documentos que se incluirán en un procesamiento descendente para la recuperación de documentos y la puntuación de importancia. Combinado con la cadena de búsqueda, el filtro reduce de forma eficaz el área de la operación de búsqueda posterior. Cuando se usa solo (por ejemplo, cuando la cadena de consulta está vacía en `search=*`), los criterios de filtro son la única entrada. 

## <a name="filter-definition"></a>Definición de filtro

Los filtros son expresiones de OData, articuladas mediante un [subconjunto de sintaxis de OData V4 admitido en Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Puede especificar un filtro para cada operación **search**, pero el filtro en sí puede incluir varios campos, varios criterios y, si usa una función **ismatch**, varias expresiones. En una expresión de filtro de varias partes, puede especificar predicados en cualquier orden. No hay ninguna mejora apreciable del rendimiento si intenta reorganizar los predicados en una secuencia determinada.

El límite máximo en una expresión de filtro es el límite máximo de la solicitud. La solicitud completa, incluido el filtro, puede tener un máximo de 16 MB para POST u 8 KB para GET. Los límites flexibles están correlacionados con el número de cláusulas de la expresión de filtro. Como regla general, si tiene cientos de cláusulas, está en riesgo de alcanzar el límite. Es recomendable diseñar la aplicación de manera que no genere filtros de tamaño ilimitado.

Los ejemplos siguientes representan definiciones de filtro prototípicas en varias API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

```

## <a name="filter-design-patterns"></a>Patrones de diseño de filtros

En los ejemplos siguientes se muestran varios patrones de diseño para escenarios de filtro. Para obtener más ideas, consulte [OData expression syntax > Examples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples) (Sintaxis de expresiones de OData > Ejemplos).

+ La expresión **$filter** independiente, sin una cadena de consulta, resulta útil cuando la expresión de filtro puede definir completamente los documentos de interés. Sin una cadena de consulta, no hay ningún análisis lingüístico ni léxico, ninguna puntuación y ninguna clasificación. Tenga en cuenta que la cadena de búsqueda está vacía.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinación de una cadena de consulta y **$filter**, donde el filtro crea el subconjunto y la cadena de consulta proporciona las entradas de términos para la búsqueda de texto completo en el subconjunto filtrado. Usar un filtro con una cadena de consulta es el patrón de código más común.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Consultas compuestas, separadas por "or", cada una con sus propios criterios de filtro (por ejemplo, 'beagles' en 'perro' o 'siamés' en 'gato'). Las expresiones OR'd se evalúan individualmente y las respuestas de cada una se combinan en una respuesta que se devuelve a la aplicación que realiza la llamada. Este patrón de diseño se consigue mediante la función search.ismatch. Puede usar la versión sin puntuación (search.ismatch) o con puntuación (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Consulte estos artículos para obtener una guía completa de casos de uso específicos:

+ [Filtros de faceta](search-filters-facets.md)
+ [Filtros de idioma](search-filters-language.md)
+ [Recorte de seguridad](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtrar

En la API de REST, el filtrado está *activado* de forma predeterminada. Los campos que se pueden filtrar aumentan el tamaño del índice. Asegúrese de definir `filterable=FALSE` para los campos que no tiene previsto usar en un filtro. Para obtener más información sobre la configuración de definiciones de campos, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creación de índices).

En el SDK de. NET, el filtrado está *desactivado* de forma predeterminada. La API para definir la propiedad filtrable es [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). En el ejemplo siguiente, se ha definido en la definición de campo BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Requisitos de nueva indexación

Si un campo no es filtrable y quiere que se pueda filtrar, debe agregar un campo nuevo o volver a generar el campo existente. Cambiar una definición de campo modifica la estructura física del índice. En Azure Search, se indexan todas las rutas de acceso permitidas para acelerar el proceso de consulta, lo que requiere que se vuelvan a generar las estructuras de datos cuando cambian las definiciones de campo. 

La regeneración de campos individuales puede ser una operación de bajo impacto, ya que solo requiere una operación de combinación que envía la clave de documento existente y los valores asociados al índice, y deja intacto el resto de cada documento. Si se produce un requisito de regeneración, consulte los vínculos siguientes para obtener instrucciones:

 + [Acciones de indexación mediante el SDK de .NET](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Acciones de indexación mediante la API de REST](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Conceptos básicos de filtro de texto

Los filtros de texto son válidos para los campos de cadena, desde los que se puede extraer una colección arbitraria de documentos basados en valores del corpus de búsqueda.

Para los filtros de texto compuestos de cadenas, no hay ningún análisis léxico ni separación de palabras, por lo que las comparaciones se aplican solo a coincidencias exactas. Por ejemplo, si un campo *f* contiene "sunny day", `$filter=f eq 'Sunny'` no coincide, pero `$filter=f eq 'Sunny day'` sí. 

Las cadenas de texto distinguen mayúsculas de minúsculas. Las palabras en mayúsculas no se buscan en minúsculas: `$filter=f eq 'Sunny day'` no encontrará 'sunny day'.


| Enfoque | Descripción | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Una función que proporciona una lista delimitada por comas de cadenas para un campo determinado. Las cadenas comprenden los criterios de filtro, que se aplican a todos los campos del ámbito de la consulta. <br/><br/>`search.in(f, ‘a, b, c’)` es semánticamente equivalente a `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, excepto en que se ejecuta mucho más rápido cuando la lista de valores es grande.<br/><br/>Es recomendable usar la función **search.in** para los [filtros de seguridad](search-security-trimming-for-azure-search.md) y para que los filtros compuestos de texto sin formato coincidan con los valores de un campo determinado. Este método está diseñado para acelerar el proceso. Puede esperar un tiempo de respuesta inferior a un segundo para cientos o miles de valores. Aunque no hay ningún límite explícito en el número de elementos que se pueden pasar a la función, la latencia aumenta en proporción al número de cadenas que proporciona. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Una función que permite combinar las operaciones de búsqueda de texto completo con operaciones de filtro estrictamente booleano en la misma expresión de filtro. Permite varias combinaciones de consulta y filtro en una solicitud. También puede usarla para que un filtro *contains* filtre una cadena parcial en una cadena mayor. |  
| [$filter=campo operador cadena](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Expresión definida por el usuario compuesta de campos, operadores y valores. | 

## <a name="numeric-filter-fundamentals"></a>Conceptos básicos de filtro numérico

Los campos numéricos no se pueden definir como `searchable` en el contexto de búsqueda de texto completo. Solo las cadenas están sujetas a la búsqueda de texto completo. Por ejemplo, si escribe 99.99 como término de búsqueda, no obtendrá artículos con un precio de 99.99 USD. Lo que se devolverá son elementos con el número 99 en los campos de cadena del documento. Por lo tanto, si tiene datos numéricos, se asume que los va a usar para los filtros, como rangos, facetas, grupos, etc. 

Los documentos que contienen campos numéricos (precio, tamaño, SKU, id.) proporcionan esos valores en los resultados de la búsqueda si el campo está marcado como `retrievable`. En resumen, la búsqueda de texto completo no es aplicable a los tipos de campo numéricos.

## <a name="next-steps"></a>Pasos siguientes

En primer lugar, pruebe el **Explorador de búsqueda**  en el portal para enviar consultas con parámetros **$filter**. El [índice real-estate-sample](search-get-started-portal.md) proporciona resultados interesantes para las siguiente consultas filtradas al pegarlas en la barra de búsqueda:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Para trabajar con más ejemplos, consulte [OData Filter Expression Syntax > Examples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples) (Sintaxis de expresión de filtro de OData > Ejemplos).

## <a name="see-also"></a>Otras referencias

+ [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md)
+ [API de REST de documentos de búsqueda](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Sintaxis de consulta simplificada](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxis de consulta de Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Supported data types](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) (Tipos de datos admitidos)
