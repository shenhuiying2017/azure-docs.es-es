---
title: "Consulta de un índice (API de REST - Azure Search) | Microsoft Docs"
description: "Cree una consulta de búsqueda en Búsqueda de Azure y use los parámetros de búsqueda para filtrar y ordenar los resultados de búsqueda."
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
ms.openlocfilehash: 49062bec233ad35cd457f9665fa94c1855343582
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="query-your-azure-search-index-using-the-rest-api"></a>Realización de una consulta al índice de Búsqueda de Azure con la API de REST
> [!div class="op_single_selector"]
>
> * [Información general](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Este artículo muestra cómo realizar consultas en un índice con la [API de REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

Antes de comenzar este tutorial, debe haber [creado ya un índice de Azure Search](search-what-is-an-index.md) y [haberlo rellenado con datos](search-what-is-data-import.md). Para información preliminar, vea [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md).

## <a name="identify-your-azure-search-services-query-api-key"></a>Identificación de la clave de API de consulta del servicio de Búsqueda de Azure
Un componente clave de cada operación de búsqueda en la API de REST de Búsqueda de Azure es la *clave de API* que se generó para el servicio que ha aprovisionado. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio, puede iniciar sesión en [Azure Portal](https://portal.azure.com/)
2. Vaya a la hoja de servicio de Azure Search
3. Haga clic en el icono "Claves"

El servicio tiene *claves de administración* y *claves de consulta*.

* Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
* Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para consultar un índice, puede utilizar una de las claves de consulta. Las claves de administración también se pueden utilizar para las consultas, pero debe utilizar una clave de consulta en el código de aplicación ya que esto se adapta mejor al [principio de privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="formulate-your-query"></a>Formulación de la consulta
Hay dos maneras de [realizar búsquedas en el índice mediante la API de REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Una de ellas es emitir una solicitud HTTP POST en la que los parámetros de consulta se definen en un objeto JSON del cuerpo de la solicitud. La otra es emitir una solicitud HTTP GET en la que los parámetros de la consulta se definen en la dirección URL de la solicitud. POST tiene unos [límites más flexibles](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) en relación con el tamaño de los parámetros de la consulta que GET. Por este motivo, se recomienda usar la solicitud POST a menos que haya circunstancias especiales en las que utilizar la solicitud GET sea más adecuado.

Tanto para la solicitud POST como para la GET, deberá proporcionar el *nombre del servicio*, el *nombre del índice*, así como la *versión adecuada de la API* (la versión actual de la API es `2016-09-01` en el momento de publicar este documento) en la URL de la solicitud. En el caso de GET, la *cadena de consulta* del final de la dirección URL es donde se proporcionar los parámetros de la consulta. Consulte a continuación el formato de dirección URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

El formato de la solicitud POST es el mismo, pero solo con la versión de API en los parámetros de la cadena de consulta.

#### <a name="example-queries"></a>Consultas de ejemplo
Presentamos algunas consultas de ejemplo en un índice llamado "hoteles". Estas consultas se muestran en el formato de las solicitudes GET y POST.

Busque en todo el índice el término "presupuesto" y devuelva solo el campo `hotelName`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplique un filtro al índice para buscar hoteles con una tarifa inferior a 150 dólares por noche, y devolver `hotelId` y `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Busque en todo el índice, ordene por un campo específico (`lastRenovationDate`) en orden descendente, tome los dos primeros resultados y muestre solo `hotelName` y `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>Envío de la solicitud HTTP
Ahora que ha formulado la consulta como parte de la dirección URL de la solicitud HTTP (para GET) o del cuerpo de la solicitud (para POST), puede definir los encabezados de solicitud y enviar la consulta.

#### <a name="request-and-request-headers"></a>Solicitudes y encabezados de solicitud
Debe definir dos encabezados de solicitud para GET o tres para POST:

1. El encabezado `api-key` se debe establecer en la clave de consulta que encontró anteriormente en el paso I. También puede usar una clave de administración como encabezado `api-key`, pero se recomienda usar una clave de consulta, ya que esta concede acceso de solo lectura a los índices y documentos de forma exclusiva.
2. El encabezado `Accept` debe establecerse en `application/json`.
3. Solo para POST, el encabezado `Content-Type` debe establecerse también en `application/json`.

Consulte a continuación una solicitud HTTP GET creada para buscar en el índice "hotels" mediante la API de REST de Azure Search con una consulta sencilla que busca el término "motel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

Aquí aparece la misma consulta de ejemplo, pero esta vez utilizando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Una solicitud de consulta correcta dará como resultado un código de estado de `200 OK` y los resultados de la búsqueda se devuelven como JSON en el cuerpo de la respuesta. Aquí se muestra el aspecto de los resultados de la consulta anterior, suponiendo que el índice "hoteles" se ha rellenado con los datos de ejemplo de [Importación de datos en Búsqueda de Azure con la API de REST](search-import-data-rest-api.md) (tenga en cuenta que se ha aplicado formato a JSON para mayor claridad).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para más información, visite la sección "Respuesta" de [Buscar documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Para más información sobre otros códigos de estado HTTP que se devuelven en caso de error, consulte [Códigos de estado HTTP (Búsqueda de Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).
