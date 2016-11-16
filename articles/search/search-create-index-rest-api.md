---
title: "Creación de una consulta en el Índice de Azure Search mediante la API de REST | Microsoft Docs"
description: "Creación de un índice en código con Búsqueda de Azure y la API de REST de HTTP."
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
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 6ff31940f3a4e7557e0caf3d9d3740590be3bc04
ms.openlocfilehash: 6d3bbea1a891e1d2f41eedccd9b9a591dfe13855


---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Creación de un índice de Búsqueda de Azure con la API de REST
> [!div class="op_single_selector"]
> * [Información general](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artículo le guiará a través del proceso de creación de un [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de Búsqueda de Azure mediante la API de REST de Búsqueda de Azure.

Antes de seguir con esta guía y crear un índice, debe haber [creado ya un servicio de Búsqueda de Azure](search-create-service-portal.md).

Para crear un índice de Búsqueda de Azure mediante la API de REST, emita una solicitud HTTP POST al punto de conexión de la dirección URL del servicio Búsqueda de Azure. La definición del índice se incluirá en el cuerpo de la solicitud como contenido JSON con formato correcto.

## <a name="i-identify-your-azure-search-services-admin-apikey"></a>I. Identificación de la clave de API de administración del servicio de Búsqueda de Azure
Ahora que ha aprovisionado un servicio de Búsqueda de Azure, puede emitir solicitudes HTTP en el punto de conexión de la dirección URL de su servicio mediante la API de REST. Sin embargo, *todas* las solicitudes de API deben incluir la clave de API que se generó para el servicio de Búsqueda que aprovisionó. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a la hoja de servicio de Búsqueda de Azure
3. Haga clic en el icono "Claves"

El servicio tendrá *claves de administración* y *claves de consulta*.

* Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
* Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para crear un índice, puede usar su clave de administración principal o la secundaria.

## <a name="ii-define-your-azure-search-index-using-wellformed-json"></a>II. Definición del índice de Búsqueda de Azure mediante JSON con formato correcto
Una única solicitud HTTP POST al servicio permitirá crear el índice. El cuerpo de la solicitud HTTP POST contendrá un único objeto JSON que define el índice de Búsqueda de Azure.

1. La primera propiedad de este objeto JSON es el nombre del índice.
2. La segunda propiedad de este objeto JSON es una matriz JSON denominada `fields` que contiene un objeto JSON independiente para cada campo en el índice. Cada uno de estos objetos JSON contiene varios pares de nombre/valor para cada uno de los atributos del campo incluido "name", "type", etc.

Es importante que tenga en cuenta sus necesidades de negocio y experiencia de búsqueda como usuario al diseñar el índice ya que debe asignar a cada campo los [atributos adecuados](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estos atributos controlan qué características de Búsqueda (filtrado, facetas, ordenación, búsqueda de texto completo, etc.) se aplican a qué campos. Para cualquier atributo que no especifique, el valor predeterminado será habilitar la característica de búsqueda correspondiente a menos que la deshabilite específicamente.

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

La definición del índice anterior utiliza un analizador de lenguaje personalizado para el campo `description_fr` porque está diseñado para almacenar texto en francés. Consulte [el tema de la compatibilidad de idiomas en MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) así como la correspondiente [entrada de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para más información acerca de los analizadores de idiomas.

## <a name="iii-issue-the-http-request"></a>III. Emisión de la solicitud HTTP
1. Mediante el uso de la definición del índice como cuerpo de la solicitud, emita una solicitud HTTP POST a la URL del punto de conexión de servicio de Búsqueda de Azure. En la dirección URL, asegúrese de usar el nombre del servicio como nombre de host y ponga la `api-version` adecuada como parámetro de la cadena de consulta (la versión actual de la API es `2015-02-28` en el momento de publicar este documento).
2. En los encabezados de solicitud, especifique el `Content-Type` como `application/json`. También necesitará proporcionar la clave de administración del servicio que identificó en el paso I en el encabezado `api-key` .

Tendrá que proporcionar su propia clave de API y su nombre de servicio para emitir la solicitud siguiente:

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Para una solicitud correcta, debería ver el código de estado "201 (Created)". Para más información sobre la creación de un índice mediante la API de REST, visite la referencia sobre la API en [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Para más información sobre otros códigos de estado HTTP que se devuelven en caso de error, consulte [Códigos de estado HTTP (Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Cuando haya terminado con un índice y desee eliminarlo, simplemente emita una solicitud HTTP DELETE. Por ejemplo, así es cómo se eliminaría el índice "hoteles":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Pasos siguientes
Después de crear un índice de Búsqueda de Azure, ya podrá [cargar el contenido en el índice](search-what-is-data-import.md) y empezar la búsqueda de los datos.




<!--HONumber=Nov16_HO2-->


