---
title: "Indexación de un origen de datos de DocumentDB para Azure Search| Microsoft Docs"
description: "En este artículo se muestra cómo crear el indexador de Azure Search con DocumentDB como origen de datos."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: documentdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 01/17/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: efa4a720a306aaedcd2d5857d30d34394a76f5f6
ms.openlocfilehash: b2f8537aa58e6dc78051752a13f555331042972a


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>Conexión de DocumentDB con Búsqueda de Azure mediante indizadores

Si está pensando implementar grandes experiencias de búsqueda sobre los datos de DocumentDB, puede configurar y ejecutar un indexador de Azure Search que extraiga los datos y los inserte en un índice de Azure Search. En este artículo mostramos cómo integrar Azure DocumentDB con Azure Search sin necesidad de escribir código alguno para mantener la infraestructura de indexación.

Para configurarlo, debe tener un [servicio Azure Search](search-create-service-portal.md), además de un índice, un indexador y un origen de datos. Puede crear estos objetos por medio del [portal](search-import-data-portal.md), el [SDK de .NET](/dotnet/api/microsoft.azure.search) o la [API de REST](/rest/api/searchservice/) para todos los lenguajes que no sean de .NET. 

Si se opta por el portal, el [Asistente para importar datos](search-import-data-portal.md) le guía a través de la creación de estos objetos. Normalmente, un índice predeterminado puede generarse automáticamente.

## <a name="a-idconceptsaazure-search-indexer-concepts"></a><a id="Concepts"></a>Conceptos del indizador de Búsqueda de Azure
Búsqueda de Azure admite la creación y administración de orígenes de datos (incluida DocumentDB), así como de indizadores que se usan en dichos orígenes.

A **origen de datos** especifica los datos para indexar, las credenciales y las directivas para identificar cambios en los datos (por ejemplo, los documentos modificados o eliminados dentro de la colección). El origen de datos se define como un recurso independiente para que puedan usarlo múltiples indizadores.

Un **indizador** describe cómo fluyen los datos desde el origen de datos a un índice de búsqueda de destino. Debe planear la creación de un indizador para cada combinación de origen de datos e índice de destino. Si bien varios indizadores pueden escribir en el mismo índice, cada indizador únicamente puede escribir en un solo índice. Un indizador se usa para:

* Realizar una copia única de los datos para rellenar un índice.
* Sincronizar un índice con los cambios del origen de datos en una programación. La programación forma parte de la definición del indizador.
* Invocar actualizaciones a petición para un índice según sea necesario.

## <a name="a-idcreatedatasourceastep-1-create-a-data-source"></a><a id="CreateDataSource"></a>Paso 1: Creación de un origen de datos
Emita una solicitud HTTP POST para crear un nuevo origen de datos en el servicio Azure Search que incluya los siguientes encabezados de solicitud.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` es obligatorio. Entre los valores válidos se incluye `2015-02-28` o una versión posterior. Visite [Versiones de API en Azure Search](search-api-versions.md) para ver todas las versiones de la API de Azure Search compatibles.

El cuerpo de la solicitud contiene la definición del origen de datos, que debe incluir los siguientes campos:

* **nombre**: elija un nombre para representar la base de datos de DocumentDB.
* **type**: use `documentdb`.
* **credenciales**:
  
  * **connectionString**: obligatorio. Especifique la información de conexión a Azure DocumentDB con el formato siguiente: `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **contenedor**:
  
  * **nombre**: obligatorio. Especifique el identificador de la colección de DocumentDB que se va a indexar.
  * **consulta**: opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que Búsqueda de Azure pueda indizar.
* **dataChangeDetectionPolicy**: opcional. Consulte la [directiva de detección de cambios de datos](#DataChangeDetectionPolicy) más adelante.
* **dataDeletionDetectionPolicy**: opcional. Consulte la [directiva de detección de eliminación de datos](#DataDeletionDetectionPolicy) más adelante.

A continuación puede consultar un [cuerpo de solicitud de ejemplo](#CreateDataSourceExample).

### <a name="a-iddatachangedetectionpolicyacapturing-changed-documents"></a><a id="DataChangeDetectionPolicy"></a>Captura de documentos modificados
El fin de una directiva de detección de cambios de datos es identificar de forma eficaz los elementos de datos que han cambiado. Actualmente, solo es compatible la directiva `High Water Mark` que usa la propiedad de marca de tiempo de última modificación `_ts` proporcionada por DocumentDB, especificada de la siguiente forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

También tendrá que agregar `_ts` a la proyección y la cláusula `WHERE` para la consulta. Por ejemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark

### <a name="a-iddatadeletiondetectionpolicyacapturing-deleted-documents"></a><a id="DataDeletionDetectionPolicy"></a>Captura de documentos eliminados
Cuando se eliminan filas de la tabla de origen, también debe eliminar dichas filas del índice de búsqueda. El fin de una directiva de detección de eliminación de datos es identificar eficazmente los elementos de datos eliminados. Actualmente, solo es compatible la directiva `Soft Delete` (la eliminación se indica con algún tipo de marca), especificada de la siguiente forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> Si usa una proyección personalizada, debe incluir la propiedad softDeleteColumnName en la cláusula SELECT.
> 
> 

### <a name="a-idleveagingqueriesaleveraging-queries"></a><a id="LeveagingQueries"></a>Aprovechamiento de consultas
Además de capturar documentos modificados o eliminados, la especificación de una consulta de DocumentDB se puede usar para simplificar las propiedades anidadas, desenredar matrices, proyectar propiedades json y filtrar los datos que se vayan a indexar. La manipulación de los datos que se van a indexar puede mejorar el rendimiento del indexador de Azure Search.

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


### <a name="a-idcreatedatasourceexamplearequest-body-example"></a><a id="CreateDataSourceExample"></a>Ejemplo de cuerpo de solicitud
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

## <a name="a-idcreateindexastep-2-create-an-index"></a><a id="CreateIndex"></a>Paso 2: Creación de un índice
Si aún no tiene un índice de Búsqueda de Azure de destino, créelo. Puede crearlo desde la [interfaz de usuario de Azure Portal](search-create-index-portal.md) o con la [API de REST creación de índices](/rest/api/searchservice/create-index) o la [Index Class](/dotnet/api/microsoft.azure.search.models.index).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Asegúrese de que el esquema del índice de destino es compatible con el de los documentos JSON de origen o el resultado de la proyección de consultas personalizada.

> [!NOTE]
> Para las colecciones con particiones, la clave de documento predeterminada es la propiedad `_rid` de DocumentDB, que cambia su nombre a `rid` en Azure Search. Además, los valores `_rid` de DocumentDB contienen caracteres que no son válidos en las claves de Azure Search. Por este motivo, la `_rid` valores están codificados con Base64.
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

### <a name="a-idcreateindexexamplearequest-body-example"></a><a id="CreateIndexExample"></a>Ejemplo de cuerpo de solicitud
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

## <a name="a-idcreateindexerastep-3-create-an-indexer"></a><a id="CreateIndexer"></a>Paso 3: Creación de un indizador
Para crear un indizador dentro de un servicio de Búsqueda de Azure, use una solicitud HTTP POST con los siguientes encabezados:

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

El cuerpo de la solicitud contiene la definición del indizador, que debe incluir los siguientes campos:

* **nombre**: obligatorio. El nombre del indizador.
* **dataSourceName**: obligatorio. El nombre de un origen de datos existente.
* **targetIndexName**: obligatorio. El nombre de un índice existente.
* **programación**: opcional. Consulte la [programación de indización](#IndexingSchedule) a continuación.

### <a name="a-idindexingschedulearunning-indexers-on-a-schedule"></a><a id="IndexingSchedule"></a>Ejecución de indizadores en una programación
Un indizador puede especificar opcionalmente una programación. Si existe una programación, el indizador se ejecutará de forma periódica de acuerdo con la misma. Una programación tiene los siguientes atributos:

* **intervalo**: obligatorio. Valor de duración que especifica un intervalo o período durante el que se ejecuta el indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). El patrón de este es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.
* **startTime**: obligatorio. Valor de fecha y hora UTC que especifica cuándo debería empezar a ejecutarse el indizador.

### <a name="a-idcreateindexerexamplearequest-body-example"></a><a id="CreateIndexerExample"></a>Ejemplo de cuerpo de solicitud
En el ejemplo siguiente se crea un indizador que copia datos de la colección a la que hace referencia el origen de datos `myDocDbDataSource` en el índice `mySearchIndex` de acuerdo con una programación que empieza el 1 de enero de 2015 UTC y se ejecuta cada hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>Response
Si el indizador se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

## <a name="a-idrunindexerastep-4-run-an-indexer"></a><a id="RunIndexer"></a>Paso 4: Ejecución de un indizador
Además de ejecutarse periódicamente según una programación, un indizador también puede invocarse a petición mediante la emisión de la siguiente solicitud HTTP POST:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Response
Si el indizador se invoca correctamente, recibirá una respuesta HTTP 202 que indica que se ha aceptado.

## <a name="a-namegetindexerstatusastep-5-get-indexer-status"></a><a name="GetIndexerStatus"></a>Paso 5: Obtención del estado del indizador
Puede emitir una solicitud HTTP GET para recuperar el estado actual y el historial de ejecución de un indexador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Response
Se devolverá una respuesta HTTP 200 de aceptado junto con un cuerpo de respuesta que contiene información sobre el estado general del indexador, la última invocación de este, así como el historial de invocaciones recientes del mismo (si existe).

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

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>Pasos siguientes
¡Enhorabuena! En este artículo ha aprendido a integrar Azure DocumentDB con Búsqueda de Azure usando el indizador para dicha base de datos.

* Para más información sobre Azure DocumentDB, vea la [página del servicio DocumentDB](https://azure.microsoft.com/services/documentdb/).
* Para más información sobre Búsqueda de Azure, vea la [página del servicio Búsqueda](https://azure.microsoft.com/services/search/).


<!--HONumber=Jan17_HO3-->


