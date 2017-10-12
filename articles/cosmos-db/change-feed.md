---
title: Compatibilidad con la fuente de cambios en Azure Cosmos DB | Microsoft Docs
description: "Use la compatibilidad con la fuente de cambios de Azure Cosmos DB para controlar los cambios en documentos y realizar el procesamiento basado en eventos tales como desencadenadores y mantener actualizados las cachés y los sistemas de análisis."
keywords: fuente de cambios
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.openlocfilehash: 16bd85065f77612ac342ae4a8b500e0c7fa2a078
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Compatibilidad con la fuente de cambios en Azure Cosmos DB
[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos rápido y flexible, replicado globalmente, que se usa para almacenar elevados volúmenes de datos de transacciones y operaciones con una latencia predecible inferior a 10 milisegundos en lecturas y escrituras. Esto hace que sea adecuado para IoT, juegos y aplicaciones de registro de operaciones. Un patrón de diseño habitual en estas aplicaciones es controlar los cambios realizados en datos de Azure Cosmos DB y actualizar las vistas materializadas, realizar análisis en tiempo real, archivar los datos en almacenamiento en frío y desencadenar notificaciones ante determinados eventos en función de estos cambios. La **compatibilidad con la fuente de cambios** en Azure Cosmos DB le permite crear soluciones eficientes y escalables para cada uno de estos patrones.

Gracias a la compatibilidad con la fuente de cambios, Azure Cosmos DB proporciona una lista ordenada de documentos de una colección de Azure Cosmos DB en el orden en que se modificaron. Esta fuente se puede usar para estar al tanto de las modificaciones en los datos dentro de la colección y realizar acciones tales como:

* Desencadenar una llamada a una API cuando se inserta o modifica un documento
* Realizar el procesamiento en tiempo real (secuencia) sobre las actualizaciones
* Sincronizar datos con una caché, un motor de búsqueda o un almacén de datos

Los cambios en Azure Cosmos DB se conservan y se pueden procesar de manera asincrónica y distribuirse entre uno o varios clientes para un procesamiento en paralelo. Echemos un vistazo a las API de fuente de cambios y cómo se pueden usar para crear soluciones en tiempo real escalables. En este artículo se muestra cómo trabajar con la fuente de cambios de Azure Cosmos DB y la API de DocumentDB. 

![Uso de la fuente de cambios de Azure Cosmos DB para aumentar la eficacia de los escenarios de informática orientada a eventos y análisis en tiempo real](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> En este momento, solo se ofrece compatibilidad con la fuente de cambios para la API de DocumentDB; la API Graph y Table API no se admiten actualmente.

## <a name="use-cases-and-scenarios"></a>Casos de uso y escenarios
La fuente de cambios permite procesar con eficacia grandes conjuntos de datos con un elevado volumen de escrituras, y ofrece una alternativa a la consulta de conjuntos de datos enteros para identificar lo que ha cambiado. Por ejemplo, puede realizar las siguientes tareas de manera eficaz:

* Actualizar una caché, un índice de búsqueda o un almacenamiento de datos con los datos almacenados en Azure Cosmos DB.
* Implementar archivado y niveles de datos de aplicación, es decir, almacenar "datos activos" en Azure Cosmos DB y "jubilar datos inactivos" en [Azure Blob Storage](../storage/common/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar análisis por lotes sobre los datos mediante [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Implementar [canalizaciones Lambda en Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB proporciona una solución de base de datos escalable que puede hacer frente tanto a la ingesta como a la consulta, e implementar arquitecturas Lambda con un bajo TCO. 
* Realizar migraciones con cero tiempo de inactividad a otra cuenta de Azure Cosmos DB con otro esquema de partición.

**Canalizaciones Lambda con Azure Cosmos DB para ingesta y consulta:**

![Canalización Lambda basada en Azure Cosmos DB para ingesta y consulta](./media/change-feed/lambda.png)

Puede usar Azure Cosmos DB para recibir y almacenar datos de eventos de dispositivos, sensores, infraestructuras y aplicaciones y luego procesarlos en tiempo real con [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) o [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

En las aplicaciones web y móviles [sin servidor](http://azure.com/serverless), puede realizar el seguimiento de eventos, como cambios en el perfil del cliente, preferencias o ubicación para desencadenar determinadas acciones como enviar notificaciones push a sus dispositivos mediante [Azure Functions](../azure-functions/functions-bindings-documentdb.md) o [App Services](https://azure.microsoft.com/services/app-service/). Si usa Azure Cosmos DB para compilar un juego, puede usar la fuente de cambios para, por ejemplo, implementar marcadores en tiempo real basados en las puntuaciones de los juegos completados.

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Funcionamiento de la fuente de cambios en Azure Cosmos DB
Azure Cosmos DB ofrece la posibilidad de leer las actualizaciones realizadas de manera incremental en una colección de Azure Cosmos DB. Esta fuente de cambios tiene las siguientes propiedades:

* Los cambios son persistentes en Azure Cosmos DB y pueden procesarse de forma asincrónica.
* Los cambios en los documentos dentro de una colección están disponibles inmediatamente en la fuente de cambios.
* Cada cambio realizado en un documento aparece exactamente una vez en la fuente de cambios y los clientes administran su lógica de puntos de comprobación. La biblioteca de procesadores de fuentes de cambio proporciona semántica de puntos de comprobación automáticos y "al menos una vez".
* Solo el cambio más reciente en un documento determinado se incluye en el registro de cambios. Puede que los cambios intermedios no estén disponibles.
* La fuente de cambios está clasificada por orden de modificación dentro de cada valor de clave de partición. No hay ningún orden garantizado entre valores de clave de partición.
* Los cambios se pueden sincronizar desde cualquier punto en el tiempo, es decir, no hay ningún período fijo de retención de datos en el que los cambios estén disponibles.
* Los cambios están disponibles en fragmentos de intervalos de claves de partición. Esta funcionalidad permite que los cambios de colecciones grandes se procesen en paralelo por medio de varios consumidores/servidores.
* Las aplicaciones pueden solicitar varias fuentes de cambios a la vez en la misma colección.

La fuente de cambios de Azure DB Cosmos está habilitada de forma predeterminada para todas las cuentas. Puede usar el [procesamiento aprovisionado](request-units.md) en su región de escritura o en cualquier [región de lectura](distribute-data-globally.md) para leer la fuente de cambios, igual que con cualquier otra operación de Azure Cosmos DB. La fuente de cambios incluye inserciones y operaciones de actualización realizadas en los documentos dentro de la colección. Puede capturar eliminaciones estableciendo un indicador "eliminación temporal" dentro de los documentos en lugar de eliminaciones. Como alternativa, puede establecer un período finito de caducidad para los documentos mediante la [funcionalidad TTL](time-to-live.md), por ejemplo, 24 horas, y usar el valor de esa propiedad para capturar las eliminaciones. Con esta solución, tiene que procesar los cambios dentro de un intervalo de tiempo más corto que el período de expiración de TTL. La fuente de cambios está disponible para cada intervalo de claves de partición dentro de la colección de documentos y, por tanto, se puede distribuir entre uno o varios consumidores para el procesamiento en paralelo. 

![Procesamiento distribuido de la fuente de cambios de Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Tiene algunas opciones para implementar una fuente de cambios en el código de cliente. En las secciones siguientes se describe cómo implementar la fuente de cambios mediante la API de REST de Azure Cosmos DB y los SDK de DocumentDB. Sin embargo, para las aplicaciones. NET, se recomienda usar la nueva [biblioteca de procesadores de fuentes de cambio](#change-feed-processor) para procesar los eventos de la fuente de cambios, ya que simplifica la lectura de los cambios en las distintas particiones y permite varios subprocesos en paralelo. 

## <a id="rest-apis"></a>Trabajo con la API de REST y los SDK de DocumentDB
Azure Cosmos DB proporciona contenedores elásticos de almacenamiento y rendimiento denominados **colecciones**. Los datos dentro de las colecciones están agrupados de manera lógica mediante [claves de partición](partition-data.md) para mejorar la escalabilidad y el rendimiento. Azure Cosmos DB proporciona varias API para acceder a estos datos, entre las que se incluyen búsqueda por identificador (leer/obtener), consulta y fuentes de lectura (exámenes). La fuente de cambios se puede obtener rellenando dos nuevos encabezados de solicitud para la API `ReadDocumentFeed` de DocumentDB; luego se puede procesar en paralelo entre intervalos de claves de partición.

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
Examinemos brevemente cómo funciona ReadDocumentFeed. Azure Cosmos DB admite la lectura de una fuente de documentos dentro de una colección mediante la API `ReadDocumentFeed`. Por ejemplo, la siguiente solicitud devuelve una página de documentos dentro de la colección `serverlogs`. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Se pueden limitar los resultados mediante el encabezado `x-ms-max-item-count`, y se pueden resumir las lecturas reenviando la solicitud con un encabezado `x-ms-continuation` devuelto en la respuesta anterior. Cuando se realiza desde un único cliente, `ReadDocumentFeed` realiza la iteración de los resultados entre particiones en serie. 

**Fuente de documento de lectura en serie**

También puede recuperar la fuente de documentos mediante uno de los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) admitidos. Por ejemplo, el fragmento de código siguiente muestra cómo usar el [método ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet) en. NET.

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

### <a name="distributed-execution-of-readdocumentfeed"></a>Ejecución distribuida del ReadDocumentFeed
En el caso de colecciones que contienen terabytes de datos o mayores, o que realizan la ingesta de grandes volúmenes de actualizaciones, la ejecución en serie de la fuente de lectura desde una única máquina cliente puede que no sea una solución práctica. Para estos escenarios de macrodatos, Azure Cosmos DB proporciona varias API que distribuyen las llamadas a `ReadDocumentFeed` de manera transparente entre varios lectores/consumidores cliente. 

**Fuente de documentos de lectura distribuida**

Para proporcionar procesamiento escalable de cambios incrementales, Azure Cosmos DB admite un modelo de escalado horizontal para la API de fuente de cambios que se basa en intervalos de claves de partición.

* Puede obtener una lista de intervalos de claves de partición para una colección mediante la ejecución de una llamada a `ReadPartitionKeyRanges`. 
* Para cada intervalo de claves de partición, puede ejecutar una API `ReadDocumentFeed` para leer los documentos con las claves de partición incluidas dentro de ese intervalo.

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>Recuperación de los intervalos de claves de partición para una colección
Puede recuperar los intervalos de claves de partición solicitando el recurso `pkranges` dentro de una colección. Por ejemplo, la siguiente solicitud recupera la lista de intervalos de claves de partición para la colección `serverlogs`:

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

Esta solicitud devuelve la siguiente respuesta que contiene metadatos sobre los intervalos de claves de partición:

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**Propiedades del intervalo de claves de partición**: Cada intervalo de claves de partición incluye las propiedades de metadatos en la tabla siguiente:

<table>
    <tr>
        <th>Nombre de encabezado</th>
        <th>Descripción</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>El identificador del intervalo de claves de partición. Se trata de un identificador estable y único dentro de cada colección.</p>
            <p>Debe usarse en la llamada siguiente para leer los cambios por intervalo de claves de partición.</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>El valor de hash de la clave de partición máxima para el intervalo de claves de partición. Solo para uso interno.</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>El valor de hash de clave de partición mínimo para el intervalo de claves de partición. Solo para uso interno.</td>
    </tr>       
</table>

Puede realizar esta tarea mediante uno de los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) admitidos. Por ejemplo, el fragmento de código siguiente muestra cómo recuperar intervalos de claves de partición en. NET mediante el método [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet).

```csharp
string pkRangesResponseContinuation = null;
List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

do
{
    FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri, 
        new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
}
while (pkRangesResponseContinuation != null);
```

Azure Cosmos DB admite la recuperación de documentos por intervalo de claves de partición mediante el establecimiento del encabezado `x-ms-documentdb-partitionkeyrangeid` opcional. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Realización de una operación ReadDocumentFeed incremental
ReadDocumentFeed admite los siguientes escenarios o tareas para el procesamiento incremental de los cambios en las colecciones de Azure Cosmos DB:

* Leer todos los cambios en los documentos desde el principio, es decir, desde la creación de la colección.
* Leer todos los cambios en las futuras actualizaciones de documentos desde el momento actual o los cambios desde un momento especificado por el usuario.
* Leer todos los cambios en los documentos desde una versión lógica de la colección (ETag). Puede controlar a sus clientes según las etiquetas ETag devueltas por las solicitudes de fuente de lectura incremental.

Los cambios incluyen inserciones y actualizaciones de documentos. Para capturar las eliminaciones, debe usar una propiedad de "eliminación temporal" dentro de los documentos, o bien la [propiedad TTL integrada](time-to-live.md) para señalar una eliminación pendiente en la fuente de cambios.

En la tabla siguiente se muestran los [encabezados de solicitud](/rest/api/documentdb/common-documentdb-rest-request-headers.md) y [respuesta](/rest/api/documentdb/common-documentdb-rest-response-headers.md) para las operaciones ReadDocumentFeed.

**Encabezados de solicitud para ReadDocumentFeed incremental**:

<table>
    <tr>
        <th>Nombre de encabezado</th>
        <th>Descripción</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>Debe establecerse en "fuente incremental" u omitirse.</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>Ningún encabezado: devuelve todos los cambios desde el principio (creación de la colección).</p>
            <p>"*": devuelve todos los cambios nuevos en los datos dentro de la colección.</p>           
            <p>&lt;etag&gt;: si está establecido en una ETag de colección, devuelve todos los cambios realizados desde esa marca de tiempo lógica.</p>
        </td>
    </tr>
    <tr>    
        <td>If-Modified-Since</td> 
        <td>Formato de hora RFC 1123; se omite si se especifica If-None-Match</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>Id. de intervalo de claves de partición para la lectura de datos.</td>
    </tr>
</table>

**Encabezados de respuesta para ReadDocumentFeed incremental**:

<table> <tr>
        <th>Nombre de encabezado</th>
        <th>Descripción</th>
    </tr>
    <tr>
        <td>ETag</td>
        <td>
            <p>El número de secuencia lógica (LSN) del último documento devuelto en la respuesta.</p>
            <p>La operación ReadDocumentFeed incremental se puede reanudar reenviando este valor en If-None-Match.</p>
        </td>
    </tr>
</table>

Este es un ejemplo de solicitud para devolver todos los cambios incrementales realizados en la colección desde la versión lógica/ETag `28535` y el intervalo de claves de partición = `16`:

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Los cambios están ordenados por tiempo dentro de cada valor de clave de partición del intervalo de claves de partición. No hay ningún orden garantizado entre valores de clave de partición. Si hay más resultados que pueden caber en una sola página, puede leer la página siguiente de resultados reenviando la solicitud con el encabezado `If-None-Match` con un valor igual a la etiqueta `etag` de la respuesta anterior. Si varios documentos se han insertado o actualizado de manera transaccional dentro de un procedimiento almacenado o un desencadenador, todos se devolverán dentro de la misma página de respuesta.

> [!NOTE]
> Con la fuente de cambios, se pueden devolver más elementos en una página que los especificados en `x-ms-max-item-count` en el caso de que varios documentos se inserten o actualicen en los procedimientos almacenados o desencadenadores. 

Cuando use el SDK de .NET (1.17.0), establezca el campo `StartTime` en `ChangeFeedOptions` para devolver directamente los documentos cambiados desde `StartTime` al llamar a `CreateDocumentChangeFeedQuery`. Si especifica que `If-Modified-Since` use la API de REST, la solicitud devolverá no los propios documentos, sino más bien el token de continuación o `etag` en el encabezado de respuesta. Para devolver los documentos modificados a la hora especificada, debe usarse el token de continuación `etag` en la siguiente solicitud con `If-None-Match` para devolver los documentos reales. 

El SDK de .NET proporciona las clases auxiliares [CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) y [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) para acceder a los cambios realizados en una colección. El fragmento de código siguiente muestra cómo recuperar todos los cambios desde el principio con el SDK de .NET desde un solo cliente.

```csharp
private async Task<Dictionary<string, string>> GetChanges(
    DocumentClient client,
    string collection,
    Dictionary<string, string> checkpoints)
{
    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

    foreach (PartitionKeyRange pkRange in partitionKeyRanges)
    {
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);

        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collection,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = 1
            });

        while (query.HasMoreResults)
        {
            FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>();

            foreach (DeviceReading changedDocument in readChangesResponse)
            {
                Console.WriteLine(changedDocument.Id);
            }

            checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
        }
    }

    return checkpoints;
}
```
Además, el fragmento de código siguiente muestra cómo procesar los cambios en tiempo real con Azure Cosmos DB mediante la compatibilidad con la fuente de cambios y la función anterior. La primera llamada devuelve todos los documentos de la colección, y el segundo devuelve solo los dos documentos que se crearon desde el último punto de comprobación.

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

También puede filtrar la fuente de cambios usando la lógica del lado cliente para procesar los eventos de forma selectiva. Por ejemplo, este es un fragmento de código LINQ del lado cliente para procesar solo los eventos de cambio de temperatura de los sensores de dispositivo.

```csharp
FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>Biblioteca de procesadores de fuente de cambios
Otra opción es usar la [biblioteca de procesadores de fuente de cambios de Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed), que puede ayudarle a distribuir el procesamiento de eventos fácilmente desde una fuente de cambios entre muchos consumidores. La biblioteca es excelente para crear lectores de fuentes de cambios en la plataforma .NET. Algunos flujos de trabajo que se simplificarían mediante la biblioteca de procesadores de fuente de cambios a través de los métodos incluidos en los otros SDK de Cosmos DB incluyen: 

* Extracción de actualizaciones de fuente de cambios cuando los datos se almacenan en varias particiones
* Movimiento o replicación de datos de una colección a otra
* Ejecución en paralelo de acciones desencadenadas por actualizaciones en datos y la fuente de cambios 

Mientras que el uso de las API en los SDK de Cosmos proporciona acceso a las actualizaciones de la fuente de cambios en cada partición, el uso de la biblioteca de procesadores de fuente de cambios simplifica la lectura de cambios en las particiones y en varios subprocesos que trabajen en paralelo. En lugar de leer manualmente los cambios de cada contenedor y guardar un token de continuación para cada partición, el procesador de fuente de cambios administra automáticamente la lectura de los cambios en las particiones que usan un mecanismo de concesión.

La biblioteca está disponible como un paquete NuGet [Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) y desde el código fuente como un [ejemplo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor) de Github. 

### <a name="understanding-change-feed-processor-library"></a>Descripción de la biblioteca de procesadores de fuente de cambios 

Hay cuatro componentes principales en la implementación del procesador de fuente de cambios: la colección supervisada, la colección de concesión, el host de procesador y los consumidores. 

**Colección supervisada:** La colección supervisada consiste en los datos desde los que se genera la fuente de cambios. Todas las inserciones y cambios realizados en la colección supervisada se reflejan en la fuente de cambios de la colección. 

**Colección de concesión:** La colección de concesión coordina el procesamiento de la fuente de cambios entre varios trabajadores. Se utiliza una colección independiente para almacenar las concesiones con una concesión por partición. Resulta ventajoso almacenar esta colección de concesión en una cuenta diferente con la región de escritura más cerca de donde se está ejecutando el procesador de fuente de cambios. Un objeto de concesión contiene los siguientes atributos: 
* Propietario: Especifica el host que posee la concesión.
* Continuación: Especifica la posición (token de continuación) en la fuente de cambios para una partición determinada.
* Marca de tiempo: Última vez que se actualizó la concesión; la marca de tiempo se puede utilizar para comprobar si la concesión se considera expirada. 

**Host de procesador:** Cada host determina cuántas particiones se van a procesar según la cantidad de otras instancias de host que tienen concesiones activas. 
1.  Cuando se inicia un host, adquiere concesiones para equilibrar la carga de trabajo en todos los hosts. Un host renueva periódicamente concesiones, por lo que las concesiones permanecen activas. 
2.  Un host establece puntos de control en el último token de continuación para su concesión para cada lectura. Para garantizar la seguridad de simultaneidad, un host comprueba ETag para cada actualización de concesión. También se admiten otras estrategias de puntos de control.  
3.  Tras el cierre, un host libera todas las concesiones pero mantiene la información de continuación, por lo que puede reanudar la lectura más adelante desde el punto de control almacenado. 

En este momento, el número de hosts no puede ser mayor que el número de particiones (concesiones).

**Consumidores:** Los consumidores, o trabajadores, son subprocesos que realizan el procesamiento de fuentes de cambios iniciado por cada host. Cada host de procesador puede tener varios consumidores. Cada consumidor lee la fuente de cambios de la partición a la que se ha asignado y notifica a su host los cambios y las concesiones expiradas.

Para comprender mejor cómo funcionan estos cuatro elementos del procesador de fuente de cambios juntos, echemos un vistazo a un ejemplo en el diagrama siguiente. La colección supervisada almacena documentos y utiliza "city" como la clave de partición. Vemos que la partición azul contiene documentos con el campo "city" de "A-e", etc. Hay dos hosts, cada uno con dos consumidores, que leen las cuatro particiones en paralelo. Las flechas muestran a los consumidores leyendo un punto específico de la fuente de cambios. En la primera partición, el azul más oscuro representa los cambios no leídos, mientras que el azul claro representa los cambios ya leídos de la fuente de cambios. Los hosts utilizan la colección de concesión para almacenar un valor de "continuación" para realizar un seguimiento de la posición de lectura actual para cada consumidor. 

![Uso del host de procesador de fuente de cambios de Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="using-change-feed-processor-library"></a>Uso de la biblioteca de procesadores de fuente de cambios 
En la siguiente sección se explica cómo usar la biblioteca de procesadores de fuente de cambios en el contexto de replicación de cambios de una colección de origen a una colección de destino. En este caso, la colección de origen es la colección supervisada en el procesador de fuente de cambios. 

**Instalación e inclusión del paquete NuGet del procesador de fuente de cambios** 

Antes de instalar el paquete NuGet del procesador de fuente de cambios, instale primero: 
* Microsoft.Azure.DocumentDB, versión 1.13.1 o posterior 
* Newtonsoft.Json, versión 9.0.1 o una versión posterior. Instale `Microsoft.Azure.DocumentDB.ChangeFeedProcessor` e inclúyalo como una referencia.

**Creación una colección supervisada, de concesión y de destino**  

Para poder usar la biblioteca de procesadores de fuente de cambios, la colección de concesión debe crearse antes de ejecutar los hosts de procesador. Una vez más, es recomendable almacenar una colección de concesión en una cuenta diferente con una región de escritura más cerca de donde se está ejecutando el procesador de fuente de cambios. En este ejemplo de movimiento de datos, es necesario crear la colección de destino antes de ejecutar el host del procesador de fuente de cambios. En el código de ejemplo, llamamos a un método auxiliar para crear las colecciones supervisadas, concedidas y de destino si aún no existen. 

> [!WARNING]
> Crear una colección implica precios, debido a que reserva rendimiento para que la aplicación se comunique con Azure Cosmos DB. Para más detalles, visite la [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

*Creación de un host de procesador*

La clase `ChangeFeedProcessorHost` proporciona un entorno de tiempo de ejecución seguro, seguro para subprocesos y de varios procesos para las implementaciones de procesadores de eventos que también ofrecen administración de concesión de puntos de comprobación y particiones. Para usar la clase `ChangeFeedProcessorHost`, puede implementar `IChangeFeedObserver`. Esta interfaz contiene tres métodos:

* `OpenAsync`: Esta función se llama cuando se abre el observador de la fuente de cambios. Se puede modificar para llevar a cabo una acción específica cuando se abre el consumidor u observador.  
* `CloseAsync`: Esta función se llama cuando se termina el observador de la fuente de cambios. Se puede modificar para llevar a cabo una acción específica cuando se cierra el consumidor u observador.  
* `ProcessChangesAsync`: Esta función se llama cuando hay disponibles nuevos cambios de documento en la fuente de cambios. Se puede modificar para llevar a cabo una acción específica en todas las actualizaciones de fuentes de cambios.  

En nuestro ejemplo, se implementa la interfaz `IChangeFeedObserver` a través de la clase `DocumentFeedObserver`. En este caso, la función `ProcessChangesAsync` realiza una operación upsert (de actualización) en un documento desde la fuente de cambios en la colección de destino. Este ejemplo es útil para mover datos de una colección a otra con el fin de cambiar la clave de partición de un conjunto de datos. 

*Ejecución del host de procesador*

Antes de iniciar el procesamiento de eventos, se pueden personalizar tanto las opciones de fuente de cambios como las opciones de host de fuente de cambios. 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
En las tablas siguientes se resumen los campos específicos que se pueden personalizar. 

**Opciones de fuente de cambio**:
<table>
    <tr>
        <th>Nombre de propiedad</th>
        <th>Descripción</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>Obtiene o establece el número máximo de elementos que se devolverán en la operación de enumeración en el servicio de base de datos de Azure Cosmos DB.</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>Obtiene o establece el identificador de intervalo de claves de partición para la solicitud actual en el servicio de base de datos de Azure Cosmos DB.</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>Obtiene o establece el token de continuación de solicitud en el servicio de base de datos de Azure Cosmos DB.</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>Obtiene o establece el token de sesión para su uso con coherencia de sesión en el servicio de base de datos de Azure Cosmos DB.</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>Obtiene o establece si la fuente de cambios del servicio de base de datos de Azure Cosmos DB debe empezar desde el principio (true) o desde la posición actual (false). De forma predeterminada, se inicia desde la posición actual (false).</td>
    </tr>
</table>

**Opciones de host de fuente de cambios**:
<table>
    <tr>
        <th>Nombre de propiedad</th>
        <th>Tipo</th>
        <th>Descripción</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>TimeSpan</td>
        <td>Intervalo para todas las concesiones para las particiones que la instancia de ChangeFeedEventHost mantiene actualmente.</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>TimeSpan</td>
        <td>Intervalo para iniciar una tarea para calcular si las particiones se distribuyen uniformemente entre las instancias de host conocidas.</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>TimeSpan</td>
        <td>Intervalo para el que se toma la concesión en una concesión que representa una partición. Si la concesión no se renueva dentro de este intervalo, expira y la propiedad de la partición se mueve a otra instancia de ChangeFeedEventHost.</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>TimeSpan</td>
        <td>Retraso entre sondeos de una partición en busca de nuevos cambios en la fuente, después de que todos los cambios actuales se purguen.</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>Frecuencia para establecer puntos de control en la concesión.</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>Número mínimo de particiones para el host.</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>Número máximo de particiones a las que el host puede dar servicio.</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>Booleano</td>
        <td>Si en el inicio del host se deben eliminar o no todas las concesiones existentes y el host debe empezar desde cero.</td>
    </tr>
</table>


Para iniciar el procesamiento de eventos, cree una instancia de `ChangeFeedProcessorHost`, proporcionando los parámetros adecuados para la colección de Azure Cosmos DB. A continuación, llame a `RegisterObserverAsync` para registrar su implementación `IChangeFeedObserver` (DocumentFeedObserver en este ejemplo) con el entorno de ejecución. En este punto, el host intenta adquirir una concesión en cada intervalo de claves de partición en la colección de Azure Cosmos DB con un algoritmo "expansivo". Estas concesiones duran un período de tiempo determinado y después deben renovarse. A medida que nuevos nodos, instancias de trabajo en este caso, pasan a estar en línea, colocan reservas de concesión y, con el tiempo, la carga cambia entre los nodos a medida que cada host trata de adquirir más concesiones. 

En el código de ejemplo, utilizamos una clase de fábrica (DocumentFeedObserverFactory.cs) para crear un observador y `RegistObserverFactoryAsync` para registrar dicho observador. 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
Con el tiempo, se establece un equilibrio. Esta funcionalidad dinámica permite la aplicación del escalado automático basado en CPU a los consumidores para escalar y reducir verticalmente. Si los cambios están disponibles en Azure Cosmos DB a una mayor velocidad de la que los consumidores pueden procesar, el aumento de la CPU en los consumidores puede usarse para producir un escalado automático en el recuento de instancias de trabajo.

## <a name="next-steps"></a>Pasos siguientes
* Pruebe los [ejemplos de código de fuente de cambios de Azure Cosmos DB incluidos en GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).
* Introducción a la programación con los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) o la [API de REST](/rest/api/documentdb/).
