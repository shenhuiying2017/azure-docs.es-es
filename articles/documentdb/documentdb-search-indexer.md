---
title: "Conexión de Azure Cosmos DB con Azure Search con indexadores | Microsoft Docs"
description: "En este artículo se muestra cómo usar el indexador de Azure Search con Azure Cosmos DB como origen de datos."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: fdef3d1d-b814-4161-bdb8-e47d29da596f
ms.service: cosmosdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/10/2017
ms.author: mimig
redirect_url: https://docs.microsoft.com/azure/search/search-howto-index-documentdb
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b3a62c693ff672458955789cde9d5be96cac9c58
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="connecting-azure-cosmos-db-with-azure-search-using-indexers"></a>Conexión de Azure Cosmos DB con Azure Search mediante indexadores
Si desea mejorar las experiencias de búsqueda en los datos de Cosmos DB, use el indexador de Azure Search para Cosmos DB. En este artículo, mostraremos cómo integrar Azure Cosmos DB con Azure Search sin necesidad de escribir código alguno para mantener la infraestructura de indexación.

Para establecer esta opción necesita [configurar una cuenta de Azure Search](../search/search-create-service-portal.md) (no es necesario actualizar a la búsqueda estándar) y, después, llamar a la [API de REST de Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) para crear un **origen de datos** de Cosmos DB y un **indexador** para ese origen de datos.

En las peticiones de envío de pedidos para interactuar con las API de REST puede usar [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler) o cualquier otra herramienta que prefiera.

## <a id="Concepts"></a>Conceptos del indizador de Búsqueda de Azure
Azure Search admite la creación y administración de orígenes de datos (incluido Cosmos DB), así como de los indexadores que se usan en dichos orígenes.

Un **origen de datos** especifica los datos que es necesario indizar, las credenciales para obtener acceso a estos y las directivas que posibilitan que Búsqueda de Azure identifique cambios en los datos de forma eficaz (por ejemplo, los documentos modificados o eliminados dentro de una colección). El origen de datos se define como un recurso independiente para que puedan usarlo múltiples indizadores.

Un **indizador** describe cómo fluyen los datos desde el origen de datos a un índice de búsqueda de destino. Debe planear la creación de un indizador para cada combinación de origen de datos e índice de destino. Si bien varios indizadores pueden escribir en el mismo índice, cada indizador únicamente puede escribir en un solo índice. Un indizador se usa para:

* Realizar una copia única de los datos para rellenar un índice.
* Sincronizar un índice con los cambios del origen de datos en una programación. La programación forma parte de la definición del indizador.
* Invocar actualizaciones a petición para un índice según sea necesario.

## <a id="CreateDataSource"></a>Paso 1: Creación de un origen de datos
Emita una solicitud HTTP POST para crear un nuevo origen de datos en el servicio Búsqueda de Azure que incluya los siguientes encabezados de solicitud.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` es obligatorio. Entre los valores válidos se incluye `2015-02-28` o una versión posterior. Visite [Versiones de API en Azure Search](../search/search-api-versions.md) para ver todas las versiones de la API de Azure Search compatibles.

El cuerpo de la solicitud contiene la definición del origen de datos, que debe incluir los siguientes campos:

* **nombre**: elija un nombre para representar la base de datos de Cosmos DB.
* **type**: use `documentdb`.
* **credenciales**:
  
  * **connectionString**: obligatorio. Especifique la información de conexión a la base de datos de Azure Cosmos DB con el formato siguiente: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **contenedor**:
  
  * **nombre**: obligatorio. Especifique el identificador de la colección de la API de DocumentDB que se va a indexar.
  * **consulta**: opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que Búsqueda de Azure pueda indizar.
* **dataChangeDetectionPolicy**: opcional. Consulte la [directiva de detección de cambios de datos](#DataChangeDetectionPolicy) más adelante.
* **dataDeletionDetectionPolicy**: opcional. Consulte la [directiva de detección de eliminación de datos](#DataDeletionDetectionPolicy) más adelante.

A continuación puede consultar un [cuerpo de solicitud de ejemplo](#CreateDataSourceExample).

### <a id="DataChangeDetectionPolicy"></a>Captura de documentos modificados
El fin de una directiva de detección de cambios de datos es identificar de forma eficaz los elementos de datos que han cambiado. Actualmente, solo es compatible la directiva `High Water Mark` que usa la propiedad de marca de tiempo de última modificación `_ts` proporcionada por Cosmos DB, especificada de la siguiente forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

También tendrá que agregar `_ts` a la proyección y la cláusula `WHERE` para la consulta. Por ejemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark

### <a id="DataDeletionDetectionPolicy"></a>Captura de documentos eliminados
Cuando se eliminan filas de la tabla de origen, también debe eliminar dichas filas del índice de búsqueda. El fin de una directiva de detección de eliminación de datos es identificar eficazmente los elementos de datos eliminados. Actualmente, solo es compatible la directiva `Soft Delete` (la eliminación se indica con algún tipo de marca), especificada de la siguiente forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> Si usa una proyección personalizada, deberá incluir la propiedad softDeleteColumnName en la cláusula SELECT.
> 
> 

### <a id="LeveagingQueries"></a>Aprovechamiento de consultas
Además de capturar documentos modificados o eliminados, la especificación de una consulta de la API de DocumentDB se puede usar para simplificar las propiedades anidadas, desenredar matrices, proyectar propiedades json y filtrar los datos que se vayan a indexar. La manipulación de los datos que se van a indexar puede mejorar el rendimiento del indexador de Azure Search.

Documento de ejemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }


Consulta para simplificar:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
Consulta de proyección:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


Consulta para desenredar la matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark
    
    
Consulta de filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark


### <a id="CreateDataSourceExample"></a>Ejemplo de cuerpo de solicitud
En el ejemplo siguiente se crea un origen de datos con una consulta personalizada y sugerencias de directiva:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

### <a name="response"></a>Response
Si el origen de datos se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

## <a id="CreateIndex"></a>Paso 2: Creación de un índice
Si aún no tiene un índice de Búsqueda de Azure de destino, créelo. Puede crearlo desde la [interfaz de usuario de Azure Portal](../search/search-create-index-portal.md) o con la [API de creación de índices](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Asegúrese de que el esquema del índice de destino es compatible con el de los documentos JSON de origen o el resultado de la proyección de consultas personalizada.

> [!NOTE]
> Para las colecciones con particiones, la clave de documento predeterminada es la propiedad `_rid` de DocumentDB, que cambia su nombre a `rid` en Azure Search. Además, los valores de `_rid` de DocumentDB contienen caracteres que no son válidos en las claves de Azure Search y, por lo tanto, los valores de `_rid` se codifican con Base 64.
> 
> 

### <a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figura A: asignación entre tipos de datos de JSON y de Búsqueda de Azure
| TIPO DE DATOS DE JSON | TIPOS DE CAMPOS DE ÍNDICE DE DESTINO COMPATIBLES |
| --- | --- |
| Booleano |Edm.Boolean, Edm.String |
| Números que parecen enteros |Edm.Int32, Edm.Int64, Edm.String |
| Números que parecen puntos flotantes |Edm.Double, Edm.String |
| Cadena |Edm.String |
| Matrices de tipos primitivos, por ejemplo, "a", "b", "c" |Collection(Edm.String) |
| Cadenas que parecen fechas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por ejemplo, {"tipo": "Punto", "coordenadas": [long, lat]} |Edm.GeographyPoint |
| Otros objetos JSON |N/D |

### <a id="CreateIndexExample"></a>Ejemplo de cuerpo de solicitud
En el ejemplo siguiente se crea un índice con un campo de identificador y de descripción:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

### <a name="response"></a>Response
Si el índice se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

## <a id="CreateIndexer"></a>Paso 3: Creación de un indizador
Para crear un indizador dentro de un servicio de Búsqueda de Azure, use una solicitud HTTP POST con los siguientes encabezados:

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

El cuerpo de la solicitud contiene la definición del indizador, que debe incluir los siguientes campos:

* **nombre**: obligatorio. El nombre del indizador.
* **dataSourceName**: obligatorio. El nombre de un origen de datos existente.
* **targetIndexName**: obligatorio. El nombre de un índice existente.
* **programación**: opcional. Consulte la [programación de indización](#IndexingSchedule) a continuación.

### <a id="IndexingSchedule"></a>Ejecución de indizadores en una programación
Un indizador puede especificar opcionalmente una programación. Si existe una programación, el indizador se ejecutará de forma periódica de acuerdo con la misma. Una programación tiene los siguientes atributos:

* **intervalo**: obligatorio. Valor de duración que especifica un intervalo o período durante el que se ejecuta el indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). El patrón de este es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.
* **startTime**: obligatorio. Valor de fecha y hora UTC que especifica cuándo debería empezar a ejecutarse el indizador.

### <a id="CreateIndexerExample"></a>Ejemplo de cuerpo de solicitud
En el ejemplo siguiente se crea un indizador que copia datos de la colección a la que hace referencia el origen de datos `myDocDbDataSource` en el índice `mySearchIndex` de acuerdo con una programación que empieza el 1 de enero de 2015 UTC y se ejecuta cada hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>Response
Si el indizador se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

## <a id="RunIndexer"></a>Paso 4: Ejecución de un indizador
Además de ejecutarse periódicamente según una programación, un indizador también puede invocarse a petición mediante la emisión de la siguiente solicitud HTTP POST:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Response
Si el indizador se invoca correctamente, recibirá una respuesta HTTP 202 que indica que se ha aceptado.

## <a name="GetIndexerStatus"></a>Paso 5: Obtención del estado del indizador
Puede emitir una solicitud HTTP GET para recuperar el estado actual y el historial de ejecución de un indizador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Response
Se devolverá una respuesta HTTP 200 de aceptado junto con un cuerpo de respuesta que contiene información sobre el estado general del indizador, la última invocación de este, así como el historial de invocaciones recientes del mismo (si existe).

La respuesta debe ser similar a la siguiente:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

El historial de ejecución contiene como máximo las 50 ejecuciones completadas más recientemente en orden cronológico inverso (la ejecución más reciente aparece en primer lugar).

## <a name="NextSteps"></a>Pasos siguientes
¡Enhorabuena! En este artículo aprendió a integrar Azure Cosmos DB con Azure Search mediante el indexador de Cosmos DB.

* Para más información sobre Cosmos DB, consulte la [página de servicio de Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/).
* Para más información sobre Azure Search, vea la [página del servicio Search](https://azure.microsoft.com/services/search/).


