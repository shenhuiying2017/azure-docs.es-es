---
title: "Creación de un índice (API de REST - Azure Search) | Microsoft Docs"
description: "Creación de un índice en código con Azure Search y la API de REST de HTTP."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: ac6c5fba-ad59-492d-b715-d25a7a7ae051
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: 9a64d1436471e406b7d9b700257d3dd96b5edcde
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Creación de un índice de Azure Search con la API de REST
> [!div class="op_single_selector"]
>
> * [Información general](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Este artículo le guiará a través del proceso de creación de un [índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) de Azure Search mediante la API de REST de Azure Search.

Antes de seguir con esta guía y crear un índice, ya debe haber [creado un servicio Azure Search](search-create-service-portal.md).

Para crear un índice de Azure Search mediante la API de REST, emita una solicitud HTTP POST al punto de conexión de la dirección URL del servicio Azure Search. La definición del índice se incluirá en el cuerpo de la solicitud como contenido JSON con formato correcto.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificación de la clave de API de administración del servicio Azure Search
Ahora que ha aprovisionado un servicio Azure Search, puede emitir solicitudes HTTP en el punto de conexión de la dirección URL de su servicio mediante la API de REST. *Todas* las solicitudes de API deben incluir la clave de API que se generó para el servicio Search que aprovisionó. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio, debe iniciar sesión en [Azure Portal](https://portal.azure.com/).
2. Vaya a la hoja de servicio Azure Search
3. Haga clic en el icono "Claves"

El servicio tendrá *claves de administración* y *claves de consulta*.

* Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
* Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para crear un índice, puede usar su clave de administración principal o la secundaria.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definición del índice de Azure Search mediante JSON con formato correcto
Una única solicitud HTTP POST al servicio permitirá crear el índice. El cuerpo de la solicitud HTTP POST contendrá un único objeto JSON que define el índice de Azure Search.

1. La primera propiedad de este objeto JSON es el nombre del índice.
2. La segunda propiedad de este objeto JSON es una matriz JSON denominada `fields` que contiene un objeto JSON independiente para cada campo en el índice. Cada uno de estos objetos JSON contiene varios pares de nombre/valor para cada uno de los atributos del campo incluido "name", "type", etc.

Es importante que tenga en cuenta sus necesidades de negocio y experiencia de búsqueda como usuario al diseñar el índice ya que debe asignar a cada campo los [atributos adecuados](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Estos atributos controlan qué características de Búsqueda (filtrado, facetas, ordenación, búsqueda de texto completo, etc.) se aplican a qué campos. Para cualquier atributo que no especifique, el valor predeterminado será habilitar la característica de búsqueda correspondiente a menos que la deshabilite específicamente.

En nuestro ejemplo, hemos llamado a nuestro índice "hoteles" y definido los campos como sigue:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Hemos elegido cuidadosamente los atributos de índice para cada campo en función de lo que pensamos acerca de cómo se van a utilizar en una aplicación. Por ejemplo, `hotelId` es una clave única que los usuarios que buscan hoteles probablemente no conocen, por lo que deshabilitaremos la búsqueda de texto completo para ese campo estableciendo `searchable` en `false`, lo que ahorra espacio en el índice.

Tenga en cuenta que solo puede designar un campo en el tipo de índice `Edm.String` como campo 'clave'.

La definición del índice anterior usa un analizador de lenguaje para el campo `description_fr` porque está diseñado para almacenar texto en francés. Consulte [el tema de compatibilidad de idiomas](https://docs.microsoft.com/rest/api/searchservice/Language-support) así como la correspondiente [entrada de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para más información sobre los analizadores de idiomas.

## <a name="issue-the-http-request"></a>Emisión de la solicitud HTTP
1. Mediante el uso de la definición del índice como cuerpo de la solicitud, emita una solicitud HTTP POST a la URL del punto de conexión de servicio Azure Search. En la dirección URL, asegúrese de usar el nombre del servicio como nombre de host y ponga la `api-version` adecuada como parámetro de la cadena de consulta (la versión actual de la API es `2016-09-01` en el momento de publicar este documento).
2. En los encabezados de solicitud, especifique el `Content-Type` como `application/json`. También necesitará proporcionar la clave de administración del servicio que identificó en el paso I en el encabezado `api-key` .

Tendrá que proporcionar su propia clave de API y su nombre de servicio para emitir la solicitud siguiente:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [api-key]


Para una solicitud correcta, debería ver el código de estado "201 (Created)". Para más información sobre la creación de un índice mediante la API de REST, visite la [referencia sobre la API aquí](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Para más información sobre otros códigos de estado HTTP que se devuelven en caso de error, consulte [Códigos de estado HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Cuando haya terminado con un índice y desee eliminarlo, simplemente emita una solicitud HTTP DELETE. Por ejemplo, así es cómo se eliminaría el índice "hoteles":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2016-09-01
    api-key: [api-key]


## <a name="next-steps"></a>Pasos siguientes
Después de crear un índice de Azure Search, ya podrá [cargar el contenido en el índice](search-what-is-data-import.md) y empezar la búsqueda de los datos.
