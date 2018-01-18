---
title: Indexar un origen de datos de Azure Cosmos DB SQL API para Azure Search | Microsoft Docs
description: "En este artículo se muestra cómo crear el indexador de Azure Search con un origen de datos de Azure Cosmos DB (SQL API)."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 01/08/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: e449f13adcd1a3651e1cac852b23f21d0227038a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Conexión de Cosmos DB con Azure Search mediante indexadores

[Azure Cosmos DB](../cosmos-db/introduction.md) es la base de datos multimodelo de distribución global de Microsoft. Con [SQL API](../cosmos-db/sql-api-introduction.md), Azure Cosmos DB proporciona un gran número de funcionalidades de consulta SQL ya conocidas que proporcionan latencias bajas coherentes sobre datos JSON sin esquemas. Azure Search se integra perfectamente con SQL API. Puede extraer documentos JSON directamente de un índice de Azure Search mediante un [indexador de Azure Search](search-indexer-overview.md) diseñado específicamente para Azure Cosmos DB SQL API. 

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Configurar Azure Search para usar una base de datos Azure Cosmos DB SQL API como un origen de datos. Opcionalmente, podrá proporcionar una consulta para seleccionar un subconjunto.
> * Crear un índice de búsqueda con los tipos de datos compatibles con JSON.
> * Configurar un indexador para la indexación periódica y bajo demanda.
> * Actualizar incrementalmente el índice basado en cambios en los datos subyacentes.

> [!NOTE]
> Azure Cosmos DB SQL API es la nueva generación de DocumentDB. Aunque se cambie el nombre de producto, la sintaxis `documentdb` de los indexadores de Azure Search todavía existe en las páginas de Azure Portal y de las API de Azure Search para la compatibilidad con versiones anteriores. Cuando configure los indexadores, no olvide especificar la sintaxis `documentdb` tal como se indica en este artículo.

<a name="supportedAPIs"></a>

## <a name="supported-api-types"></a>Tipos admitidos de API

Aunque Azure Cosmos DB admite una amplia variedad de modelos de datos y API, el soporte técnico del indexador se extiende solamente a SQL API. 

Próximamente estará disponible la compatibilidad con API adicionales. Para ayudarnos priorizar qué API deben ser compatibles en primer lugar, vaya al sitio web UserVoice e indique lo siguiente:

* [Compatibilidad del origen de datos de Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Compatibilidad del origen de datos de Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Compatibilidad del origen de datos de MongoDB API](https://feedback.azure.com/forums/263029-azure-search/suggestions/18861421-documentdb-indexer-should-be-able-to-index-mongodb)
* [Compatibilidad del origen de datos de Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Requisitos previos

Para configurar un indexador de Azure Cosmos DB, es preciso tener un [servicio Azure Search](search-create-service-portal.md) y crear un índice, un origen de datos y, por último, el indexador. Para crear estos objetos, se pueden usar el [portal](search-import-data-portal.md), el [SDK de .NET](/dotnet/api/microsoft.azure.search) o la [API de REST](/rest/api/searchservice/) en todos los lenguajes que no sean de .NET. 

Si opta por usar el portal, el [Asistente para la importación de datos ](search-import-data-portal.md) le guiará a través de la creación de estos recursos, incluyendo el índice.

> [!TIP]
> Puede iniciar el asistente para **importar datos** desde el panel de Azure Cosmos DB para simplificar la indexación para ese origen de datos. En el panel de navegación de la izquierda, vaya a **Colecciones** > **Add Azure Search** (Añadir búsqueda de Azure) para empezar.

<a name="Concepts"></a>

## <a name="azure-search-indexer-concepts"></a>Conceptos del indexador de Azure Search
Azure Search admite la creación y administración de orígenes de datos (incluido Azure Cosmos DB SQL API), así como de los indexadores que se usan en dichos orígenes de datos.

A **origen de datos** especifica los datos para indexar, las credenciales y las directivas para identificar cambios en los datos (por ejemplo, los documentos modificados o eliminados dentro de la colección). El origen de datos se define como un recurso independiente para que puedan usarlo múltiples indizadores.

Un **indizador** describe cómo fluyen los datos desde el origen de datos a un índice de búsqueda de destino. Los indexadores se pueden utilizar para:

* Realizar una copia única de los datos para rellenar un índice.
* Sincronizar un índice con los cambios del origen de datos en una programación. La programación forma parte de la definición del indizador.
* Invocar actualizaciones a petición para un índice según sea necesario.

<a name="CreateDataSource"></a>

## <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos
Pasos para crear un origen de datos, realice un POST:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

El cuerpo de la solicitud contiene la definición del origen de datos, que debe incluir los siguientes campos:

* **nombre**: elija un nombre para representar la base de datos.
* **type**: debe ser `documentdb`.
* **credenciales**:
  
  * **connectionString**: obligatorio. Especifique la información de conexión a la base de datos de Azure Cosmos DB con el formato siguiente: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **contenedor**:
  
  * **nombre**: obligatorio. Especifique el id. de la colección de la base de datos que se va a indexar.
  * **consulta**: opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que Azure Search pueda indizar.
* **dataChangeDetectionPolicy**: se recomienda. Consulte la sección [Indexación de documentos modificados](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: opcional. Consulte la sección [Indexación de documentos eliminados](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Uso de consultas para dar forma a los datos indizados
Puede especificar una consulta de SQL para eliminar el formato de las propiedades o matrices anidadas y de las propiedades JSON del proyecto, y para filtrar los datos que se van a indexar. 

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

Consulta de filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Consulta sin formato:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de proyección:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta sin formato de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Paso 2: Creación de un índice
Si aún no tiene un índice de Azure Search de destino, créelo. Puede crearlo mediante la [interfaz de usuario de Azure Portal](search-create-index-portal.md), la [API de REST de Create Index](/rest/api/searchservice/create-index) o la [clase Index](/dotnet/api/microsoft.azure.search.models.index).

En el ejemplo siguiente se crea un índice con un campo de identificador y de descripción:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

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

Asegúrese de que el esquema del índice de destino es compatible con el de los documentos JSON de origen o el resultado de la proyección de consultas personalizada.

> [!NOTE]
> En cuanto a las colecciones con particiones, la clave de documento predeterminada es la propiedad `_rid` de Azure Cosmos DB, que cambia su nombre a `rid` en Azure Search. Además, los valores `_rid` de Azure Cosmos DB contienen caracteres que no son válidos en las claves de Azure Search. Por este motivo, la `_rid` valores están codificados con Base64.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>asignación entre tipos de datos de JSON y de Azure Search
| Tipo de datos de JSON | Tipos de campos de índice de destino compatibles |
| --- | --- |
| Booleano |Edm.Boolean, Edm.String |
| Números que parecen enteros |Edm.Int32, Edm.Int64, Edm.String |
| Números que parecen puntos flotantes |Edm.Double, Edm.String |
| string |Edm.String |
| Matrices de tipos primitivos, por ejemplo ["a", "b", "c"] |Collection(Edm.String) |
| Cadenas que parecen fechas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por ejemplo, {"tipo": "Punto", "coordenadas": [long, lat]} |Edm.GeographyPoint |
| Otros objetos JSON |N/D |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Paso 3: Creación de un indexador

Una vez creados el origen de datos y los índices, ya podrá crear el indizador:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador se ejecuta cada dos horas (el intervalo de programación se establece en "PT2H"). Para ejecutar un indizador cada 30 minutos, establézcalo en PT30M. El intervalo más breve que se admite es de 5 minutos. La programación es opcional: si se omite, el indizador solo se ejecuta una vez cuando se crea. Sin embargo, puede ejecutarlo a petición en cualquier momento.   

Para más información sobre la API Create Indexer, consulte [Crear indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Ejecución del indexador a petición
Además de ejecutarse periódicamente de forma programada, un indexador también puede invocarse a petición:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> Cuando la API de Run se devuelve correctamente, se ha programado la invocación del indexador, pero el procesamiento real se produce de forma asincrónica. 

El estado del indexador se puede supervisar en el portal o mediante la API de Get Indexer Status, lo que se describe a continuación. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Obtención del estado del indexador
Puede recuperar el estado y el historial de ejecución de un indexador:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

La respuesta contiene el estado general del indexador, la última vez que se invocó al indexador (o en curso) y el historial de las recientes invocaciones al indexador.

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

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexación de documentos modificados
El fin de una directiva de detección de cambios de datos es identificar de forma eficaz los elementos de datos que han cambiado. Actualmente, `High Water Mark` es la única directiva compatible que usa la propiedad `_ts` (marca de tiempo) que proporciona Azure Cosmos DB, y se especifica de la siguiente forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Se recomienda encarecidamente usar esta directiva para garantizar un buen rendimiento del indexador. 

Si usa una consulta personalizada, asegúrese de que la consulta proyecta la propiedad `_ts`.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Progreso incremental y consultas personalizadas
El progreso incremental durante la indexación garantiza que si se interrumpe la ejecución del indexador por errores transitorios o por el límite de tiempo de ejecución, dicho indexador puede retomar la ejecución por donde la dejó la próxima vez que se ejecute, en lugar de tener que volver a indexar toda la colección desde el principio. Esto es especialmente importante cuando se indexan colecciones grandes. 

Para habilitar el progreso incremental cuando se usa una consulta personalizada, asegúrese de que la consulta ordena los resultados por la columna `_ts`. Esto permite la creación de puntos de comprobación, que Azure Search usa para proporcionar el progreso incremental en presencia de errores.   

En algunos casos, incluso si la consulta contiene una cláusula `ORDER BY [collection alias]._ts`, Azure Search puede no deducir que la consulta se ordene por `_ts`. Puede indicar a Azure Search que los resultados se ordenen mediante el uso de la propiedad de configuración `assumeOrderByHighWaterMarkColumn`. Para especificar esta sugerencia, cree o actualice el indexador como se indica a continuación: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexación de documentos eliminados
Cuando se eliminan filas de la recopilación, lo habitual es que también se deseen eliminar del índice de búsqueda. El fin de una directiva de detección de eliminación de datos es identificar eficazmente los elementos de datos eliminados. Actualmente, solo es compatible la directiva `Soft Delete` (la eliminación se indica con algún tipo de marca), especificada de la siguiente forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Si usa una consulta personalizada, asegúrese de que la consulta proyecta la propiedad a la que `softDeleteColumnName` hace referencia.

En el ejemplo siguiente se crea un origen de datos con una directiva de eliminación temporal:

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
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

## <a name="NextSteps"></a>Pasos siguientes
Felicidades. Ha aprendido a integrar Azure Cosmos DB con Azure Search mediante un indexador para rastrear y cargar los documentos de un modelo de datos SQL.

* Para más información acerca de Azure Cosmos DB, consulte la [página de servicio de Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para obtener más información sobre Azure Search, consulte la [página del servicio Search](https://azure.microsoft.com/services/search/).
