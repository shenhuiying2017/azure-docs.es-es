<properties
    pageTitle="Consultas en el índice de Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Cree una consulta de búsqueda en Búsqueda de Azure y use los parámetros de búsqueda para filtrar y ordenar los resultados de búsqueda."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Consultas en el índice de Búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cuando envía solicitudes de búsqueda a Búsqueda de Azure, hay una serie de parámetros que se pueden especificar junto con las palabras reales que se escriben en el cuadro de búsqueda de la aplicación. Estos parámetros de consulta permiten lograr un mayor control de la experiencia de búsqueda de texto completo.

A continuación se muestra una lista que explica brevemente los usos habituales de los parámetros de consulta de Búsqueda de Azure. Para obtener información detallada de los parámetros de consulta y su comportamiento, consulte las páginas con información detallada para la [API de REST](https://msdn.microsoft.com/library/azure/dn798927.aspx) y el [SDK de .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## Tipos de consultas

Búsqueda de Azure ofrece muchas opciones para crear consultas extremadamente eficaces. Los dos tipos principales de consultas que usará son `search` y `filter`. Una consulta `search` busca uno o más términos en todos los campos _habilitados para búsqueda_ del índice, y funciona tal y como cabría esperar de un motor de búsqueda como Google o Bing. Una consulta `filter` evalúa una expresión booleana en todos los campos _filtrables_ de un índice. A diferencia de las consultas `search`, las consultas `filter` buscan el contenido exacto de un campo, lo que significa que distinguen mayúsculas de minúsculas en el caso de los campos de cadena.

Puede usar los filtros y las búsquedas conjuntamente o por separado. Si los usa conjuntamente, el filtro se aplica primero a todo el índice y, después, se realiza la búsqueda en los resultados del filtro. Por lo tanto, los filtros pueden ser una técnica útil para mejorar el rendimiento porque reducen el conjunto de documentos en los que se debe procesar la consulta de búsqueda.

La sintaxis de las expresiones de filtro es un subconjunto del [lenguaje de filtro de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Para las consultas de búsqueda, puede usar la [sintaxis simplificada](https://msdn.microsoft.com/library/azure/dn798920.aspx) o la [sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) que se explica más abajo.

### Sintaxis de consulta simplificada
La [sintaxis de consulta simplificada](https://msdn.microsoft.com/library/azure/dn798920.aspx) es el lenguaje de consulta predeterminado que se usa en Búsqueda de Azure. La sintaxis de consulta simplificada admite varios operadores de búsqueda comunes entre los que se incluyen los operadores de precedencia AND, OR, NOT, phrase, suffix.

### Sintaxis de consulta de Lucene
La [sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) le permite usar el expresivo lenguaje de consulta adoptado ampliamente desarrollado como parte de [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

El uso de esta sintaxis de consulta le permite conseguir fácilmente las siguientes funcionalidades: [consultas de ámbito de campo](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [búsqueda aproximada](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [búsqueda de proximidad](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [priorización de términos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [búsqueda de expresiones regulares](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [búsqueda con caracteres comodín](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [fundamentos de la sintaxis](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax), y [consultas con operadores booleanos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## Ordenación de los resultados
Al recibir los resultados de una consulta de búsqueda, puede solicitar que Búsqueda de Azure muestre los resultados ordenados según los valores de un campo específico. De forma predeterminada, Búsqueda de Azure ordena los resultados de búsqueda basándose en el rango de puntuación de búsqueda de cada documento, que se deriva del valor [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Si desea que Búsqueda de Azure devuelva los resultados ordenados según un valor distinto de la puntuación de búsqueda, puede usar el parámetro `orderby`. Puede especificar el valor del parámetro `orderby` para que incluya nombres de campo y llamadas a la [`geo.distance()` función](https://msdn.microsoft.com/library/azure/dn798921.aspx) para los valores geoespaciales. Cada expresión puede ir seguida de `asc` para indicar que se solicitan los resultados en orden ascendente y de `desc` para indicar que se solicitan los resultados en orden descendente. El valor predeterminado es el orden ascendente.

## Paginación
Búsqueda de Azure facilita la implementación de la paginación de los resultados de búsqueda. Mediante el uso de los parámetros `top` y `skip`, puede emitir fácilmente solicitudes de búsqueda que le permiten recibir el conjunto total de resultados de búsqueda dividido en subconjuntos ordenados, sencillos de administrar que permiten habilitar fácilmente prácticas eficaces de interfaz de usuario de búsqueda. Al recibir estos subconjuntos más pequeños de resultados, también puede recibir el número de documentos del conjunto total de resultados de la búsqueda.

Para más información acerca de la paginación de resultados de búsqueda, consulte el artículo [Cómo paginar los resultados de la búsqueda en Búsqueda de Azure](search-pagination-page-layout.md).


## Resaltado de referencias
En Búsqueda de Azure, resaltar la parte exacta de los resultados de búsqueda que coincide con la consulta de búsqueda es fácil mediante el uso de los parámetros `highlight`, `highlightPreTag`, y `highlightPostTag`. Puede especificar qué campos _habilitados para búsquedas_ deben tener su texto coincidente resaltado, así como especificar las etiquetas de cadena exactas que se anexarán al comienzo y al final del texto coincidente que devuelve Búsqueda de Azure.

<!---HONumber=AcomDC_0831_2016-->