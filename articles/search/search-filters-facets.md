---
title: Filtros de faceta en Azure Search | Microsoft Docs
description: "Puede filtrar los criterios por identidad de seguridad del usuario, idioma, geolocalización o valores numéricos para reducir los resultados de búsqueda para las consultas Azure Search, un servicio de búsqueda en la nube hospedado de Microsoft Azure."
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
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 02a027845e56407bc8cc95f54a46d9534cb6de92
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Cómo crear un filtro de faceta en Azure Search 

La navegación por facetas se usa para aplicar filtros autodirigidos en resultados de consulta en una aplicación de búsqueda, donde la aplicación ofrece controles de UI para limitar la búsqueda a grupos de documentos (por ejemplo, categorías o marcas) y Azure Search proporciona la estructura de datos necesaria para la experiencia. En este artículo, se revisan brevemente los pasos básicos para crear una estructura de navegación por facetas que respalde la experiencia de búsqueda que desea proporcionar. 

> [!div class="checklist"]
> * Elegir los campos para los filtros y las facetas
> * Definir atributos en el campo
> * Crear el índice y cargar los datos
> * Agregar filtros por faceta a una consulta
> * Administrar los resultados

Las facetas son dinámicas y se devuelven en una consulta. Las respuestas de búsqueda incluyen las categorías de faceta que se usan para navegar por los resultados. Si no está familiarizado con las facetas, el ejemplo siguiente es una ilustración de una estructura de navegación por facetas.

  ![](./media/search-filters-facets/facet-nav.png)

¿No conoce la navegación por facetas y quiere más detalles? Consulte [Procedimiento para implementar la navegación por facetas en Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Elegir los campos

Las facetas se pueden calcular tanto para campos de valor único como para colecciones. Los campos que funcionan mejor en la navegación por facetas tienen una cardinalidad baja: un pequeño número de valores distintos que se repiten a lo largo de los documentos del corpus de búsqueda (por ejemplo, una lista de colores, países o nombres de marca). 

Las facetas se habilitan para cada campo al crear un índice y definir los atributos siguientes como TRUE: `filterable`, `facetable`. Solo los campos que se pueden filtrar se pueden clasificar.

Cualquier [tipo de campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que se pueda usar en la navegación por facetas se marca como "facetable":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Tipos de campo numéricos: Edm.Int32, Edm.Int64, Edm.Double

No se puede usar Edm.GeographyPoint en la navegación por facetas. Las facetas se crean a partir de números o texto que los humanos pueden leer. Como tales, las facetas no se admiten para coordenadas geográficas. Para crear una faceta de ubicación, se necesita un campo de ciudad o región.

## <a name="set-attributes"></a>Definir atributos

Los atributos de índice que controlan cómo se usa un campo se agregan a las definiciones de campo individuales del índice. En el ejemplo siguiente, los campos con cardinalidad baja, útiles para la creación de facetas, son: categoría (hotel, motel, albergue), servicios y clasificaciones. 

En la API. de NET, los atributos de filtro se deben establecer de forma explícita. En la API de REST, las facetas y los filtros están habilitados de forma predeterminada, lo que significa que solo debe definir explícitamente los atributos cuando quiera desactivarlos. Aunque, técnicamente, no es necesario, mostramos las atribuciones en el siguiente ejemplo de REST con fines didácticos. 

> [!Tip]
> Como procedimiento recomendado para optimizar el rendimiento y el almacenamiento, desactive las facetas para los campos que nunca se deban usar como facetas. En concreto, los campos de cadena para valores singleton, tales como un identificador o un nombre de producto, deben definirse como "Facetable": false para evitar su uso accidental (e inefectivo) en la navegación por facetas.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Esta definición de índice se copia de [Creación de un índice de Azure Search con la API de REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Es idéntico, salvo en diferencias superficiales en las definiciones de campo. Los atributos filterable y facetable se agregan explícitamente a los campos category, tags, parkingIncluded, smokingAllowed y rating. En la práctica, se aplican filtros y facetas de forma generalizada a los tipos de campo Edm.String, Edm.Boolean y Edm.Int32. 

## <a name="build-and-load-an-index"></a>Crear y cargar un índice

Un paso intermedio (y, quizás, obvio) es que tiene que [crear y completar el índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) antes de formular una consulta. Hemos mencionado este paso aquí para mayor integridad. Una manera de determinar si el índice está disponible es comprobar la lista de índices en el [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Agregar filtros por faceta a una consulta

En el código de la aplicación, cree una consulta que especifique todas las partes de una consulta válida, incluidas las expresiones de búsqueda, las facetas, los filtros o los perfiles de puntuación: todo lo que se usa para formular una solicitud. En el ejemplo siguiente, se crea una solicitud que crea navegación por facetas a partir del tipo de alojamiento, clasificación y otros servicios.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Devolver resultados filtrados en eventos de clic

La expresión de filtro controla el evento de clic en el valor de faceta. Dada una faceta Category, un clic en la categoría "motel" se implementa a través de una expresión `$filter` que selecciona alojamientos de ese tipo. Cuando un usuario hace clic en "motels" para indicar que solo se deben mostrar los moteles, la consulta siguiente que la aplicación envíe incluirá $filter=category eq ‘motels’.

El fragmento de código siguiente agrega la categoría al filtro si un usuario selecciona un valor de la faceta de categoría.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Con la API de REST, la solicitud se articula así: `$filter=category eq 'c1'`. Para hacer de category un campo de valores múltiples, use la sintaxis siguiente: `$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Sugerencias y soluciones alternativas

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializar una página con facetas definidas

Si quiere inicializar una página con facetas definidas, puede enviar una consulta como parte de la inicialización de la página para proveer a la página con una estructura de facetas inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Conservar una estructura de navegación por facetas de forma asincrónica respecto a los resultados filtrados

Uno de los retos de la navegación por facetas de Azure Search es que las facetas solo existen para los resultados actuales. En la práctica, se suele conservar un conjunto estático de facetas para que el usuario pueda navegar en orden inverso y reconstruir los pasos para explorar rutas alternativas a través del contenido de búsqueda. 

Aunque se trata de un caso de uso común, no es algo que la estructura de navegación por facetas proporcione de forma predeterminada actualmente. Para solucionar esta limitación, los desarrolladores que quieren facetas estáticas suelen emitir dos consultas filtradas: una centrada en los resultados y otra para crear una lista estática de facetas con fines de navegación.

## <a name="see-also"></a>Otras referencias

+ [Filtros de Azure Search](search-filters.md)
+ [Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creación de índices [API de REST])
+ [API de REST de documentos de búsqueda](https://docs.microsoft.com/rest/api/searchservice/search-documents)

