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
ms.date: 03/23/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: d04d1240fb353a973953b2a90eadc65705219edb
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Compatibilidad con la fuente de cambios en Azure Cosmos DB
[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos rápido y flexible, replicado globalmente, que se usa para almacenar elevados volúmenes de datos de transacciones y operaciones con una latencia predecible inferior a 10 milisegundos en lecturas y escrituras. Esto hace que sea adecuado para IoT, juegos y aplicaciones de registro de operaciones. Un patrón de diseño habitual en estas aplicaciones es controlar los cambios realizados en datos de Azure Cosmos DB y actualizar las vistas materializadas, realizar análisis en tiempo real, archivar los datos en almacenamiento en frío y desencadenar notificaciones ante determinados eventos en función de estos cambios. La **compatibilidad con la fuente de cambios** en Azure Cosmos DB le permite crear soluciones eficientes y escalables para cada uno de estos patrones.

Gracias a la compatibilidad con la fuente de cambios, Azure Cosmos DB proporciona una lista ordenada de documentos de una colección de Azure Cosmos DB en el orden en que se modificaron. Esta fuente se puede usar para estar al tanto de las modificaciones en los datos dentro de la colección y realizar acciones tales como:

* Desencadenar una llamada a una API cuando se inserta o modifica un documento
* Realizar el procesamiento en tiempo real (secuencia) sobre las actualizaciones
* Sincronizar datos con una caché, un motor de búsqueda o un almacén de datos

Los cambios en Azure Cosmos DB se conservan y se pueden procesar de manera asincrónica y distribuirse entre uno o varios clientes para un procesamiento en paralelo. Echemos un vistazo a las API de fuente de cambios y cómo se pueden usar para crear soluciones en tiempo real escalables. En este artículo se muestra cómo trabajar con datos espaciales con la API de DocumentDB de Azure Cosmos DB. 

![Uso de la fuente de cambios de Azure Cosmos DB para aumentar la eficacia de los escenarios de informática orientada a eventos y análisis en tiempo real](./media/change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>Casos de uso y escenarios
La fuente de cambios permite procesar con eficacia grandes conjuntos de datos con un elevado volumen de escrituras, y ofrece una alternativa a la consulta de conjuntos de datos enteros para identificar lo que ha cambiado. Por ejemplo, puede realizar las siguientes tareas de manera eficaz:

* Actualizar una caché, un índice de búsqueda o un almacenamiento de datos con los datos almacenados en Azure Cosmos DB.
* Implementar archivado y niveles de datos de aplicación, es decir, almacenar "datos activos" en Azure Cosmos DB y "jubilar datos inactivos" en [Azure Blob Storage](../storage/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar análisis por lotes sobre los datos mediante [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Implementar [canalizaciones Lambda en Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB proporciona una solución de base de datos escalable que puede hacer frente tanto a la ingesta como a la consulta, e implementar arquitecturas Lambda con un bajo TCO. 
* Realizar migraciones con cero tiempo de inactividad a otra cuenta de Azure Cosmos DB con otro esquema de partición.

**Canalizaciones Lambda con Azure Cosmos DB para ingesta y consulta:**

![Canalización Lambda basada en Azure Cosmos DB para ingesta y consulta](./media/change-feed/lambda.png)

Puede usar Azure Cosmos DB para recibir y almacenar datos de eventos de dispositivos, sensores, infraestructuras y aplicaciones y luego procesarlos en tiempo real con [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) o [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

Dentro de aplicaciones web y móviles, puede realizar el seguimiento de eventos, como cambios en el perfil del cliente, preferencias o ubicación para desencadenar determinadas acciones como enviar notificaciones push a sus dispositivos mediante [Azure Functions](../azure-functions/functions-bindings-documentdb.md) o [App Services](https://azure.microsoft.com/services/app-service/). Si usa Azure Cosmos DB para compilar un juego, puede usar la fuente de cambios para, por ejemplo, implementar marcadores en tiempo real basados en las puntuaciones de los juegos completados.

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Funcionamiento de la fuente de cambios en Azure Cosmos DB
Azure Cosmos DB ofrece la posibilidad de leer las actualizaciones realizadas de manera incremental en una colección de Azure Cosmos DB. Esta fuente de cambios tiene las siguientes propiedades:

* Los cambios son persistentes en Azure Cosmos DB y pueden procesarse de forma asincrónica.
* Los cambios en los documentos dentro de una colección están disponibles inmediatamente en la fuente de cambios.
* Cada cambio realizado en un documento aparece una sola vez en la fuente de cambios. Solo el cambio más reciente en un documento determinado se incluye en el registro de cambios. Puede que los cambios intermedios no estén disponibles.
* La fuente de cambios está clasificada por orden de modificación dentro de cada valor de clave de partición. No hay ningún orden garantizado entre valores de clave de partición.
* Los cambios se pueden sincronizar desde cualquier punto en el tiempo, es decir, no hay ningún período fijo de retención de datos en el que los cambios estén disponibles.
* Los cambios están disponibles en fragmentos de intervalos de claves de partición. Esta funcionalidad permite que los cambios de colecciones grandes se procesen en paralelo por medio de varios consumidores/servidores.
* Las aplicaciones pueden solicitar varias fuentes de cambios a la vez en la misma colección.

La fuente de cambios de Azure Cosmos DB está habilitada de forma predeterminada para todas las cuentas y no implica gastos adicionales en su cuenta. Puede usar el [procesamiento aprovisionado](request-units.md) en su región de escritura o en cualquier [región de lectura](distribute-data-globally.md) para leer la fuente de cambios, igual que con cualquier otra operación de Azure Cosmos DB. La fuente de cambios incluye inserciones y operaciones de actualización realizadas en los documentos dentro de la colección. Puede capturar eliminaciones estableciendo un indicador "eliminación temporal" dentro de los documentos en lugar de eliminaciones. Como alternativa, puede establecer un período finito de caducidad para los documentos mediante la [funcionalidad TTL](time-to-live.md), por ejemplo, 24 horas, y usar el valor de esa propiedad para capturar las eliminaciones. Con esta solución, tiene que procesar los cambios dentro de un intervalo de tiempo más corto que el período de expiración de TTL. La fuente de cambios está disponible para cada intervalo de claves de partición dentro de la colección de documentos y, por tanto, se puede distribuir entre uno o varios consumidores para el procesamiento en paralelo. 

![Procesamiento distribuido de la fuente de cambios de Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

En la sección siguiente, se describe cómo acceder a la fuente de cambios mediante la API de REST y los SDK de Azure Cosmos DB. Para las aplicaciones .NET, se recomienda usar la [biblioteca de procesadores de fuente de cambios]() para procesar los eventos desde la fuente de cambios.

## <a id="rest-apis"></a>Trabajo con la API de REST y el SDK
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

También puede recuperar la fuente de documentos mediante uno de los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) admitidos. Por ejemplo, el fragmento de código siguiente muestra cómo ejecutar ReadDocumentFeed en. NET.

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

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


**Propiedades del intervalo de claves de partición**: cada intervalo de claves de partición incluye las propiedades de metadatos en la tabla siguiente:

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

Puede realizar esta tarea mediante uno de los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) admitidos. Por ejemplo, el fragmento de código siguiente muestra cómo recuperar intervalos de claves de partición en. NET.

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

Azure Cosmos DB admite la recuperación de documentos por intervalo de claves de partición mediante el establecimiento del encabezado `x-ms-documentdb-partitionkeyrangeid` opcional. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Realización de una operación ReadDocumentFeed incremental
ReadDocumentFeed admite los siguientes escenarios o tareas para el procesamiento incremental de los cambios en las colecciones de Azure Cosmos DB:

* Leer todos los cambios en los documentos desde el principio, es decir, desde la creación de la colección.
* Leer todos los cambios en las futuras actualizaciones de documentos desde el momento actual.
* Leer todos los cambios en los documentos desde una versión lógica de la colección (ETag). Puede controlar a sus clientes según las etiquetas ETag devueltas por las solicitudes de fuente de lectura incremental.

Los cambios incluyen inserciones y actualizaciones de documentos. Para capturar las eliminaciones, debe usar una propiedad de "eliminación temporal" dentro de los documentos, o bien la [propiedad TTL integrada](time-to-live.md) para señalar una eliminación pendiente en la fuente de cambios.

En la tabla siguiente se muestran los encabezados de solicitud y respuesta para las operaciones ReadDocumentFeed.

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
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>Id. de intervalo de claves de partición para la lectura de datos.</td>
    </tr>
</table>

**Encabezados de respuesta para ReadDocumentFeed incremental**:

<table>
    <tr>
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

El SDK de .NET proporciona las clases auxiliares [CreateDocumentChangeFeedQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) y [ChangeFeedOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.changefeedoptions.aspx) para acceder a los cambios realizados en una colección. El fragmento de código siguiente muestra cómo recuperar todos los cambios desde el principio con el SDK de .NET desde un solo cliente.

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
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

Además, el fragmento de código siguiente muestra cómo procesar los cambios en tiempo real con Azure Cosmos DB mediante la compatibilidad con la fuente de cambios y la función anterior. La primera llamada devuelve todos los documentos de la colección, y el segundo devuelve solo los dos documentos que se crearon desde el último punto de comprobación.

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


También puede filtrar la fuente de cambios usando la lógica del lado cliente para procesar los eventos de forma selectiva. Por ejemplo, este es un fragmento de código LINQ del lado cliente para procesar solo los eventos de cambio de temperatura de los sensores de dispositivo.

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

## <a id="change-feed-processor"></a>Biblioteca de procesadores de fuente de cambios
La [biblioteca de procesadores de fuente de cambios de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor) se puede usar para distribuir el procesamiento de eventos, desde la fuente de cambios, entre muchos consumidores. Debe usar esta implementación para compilar los lectores de eventos de cambios en la plataforma .NET. La clase `ChangeFeedProcessorHost` proporciona un entorno de tiempo de ejecución seguro, seguro para subprocesos y de varios procesos para las implementaciones de procesadores de eventos que también ofrecen administración de concesión de puntos de comprobación y particiones.

Para usar la clase [`ChangeFeedProcessorHost`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/ChangeFeedEventHost.cs), puede implementar [`IChangeFeedObserver`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/IChangeFeedObserver.cs). Esta interfaz contiene tres métodos:

* OpenAsync
* CloseAsync
* ProcessEventsAsync

Para iniciar el procesamiento de eventos, cree una instancia de ChangeFeedProcessorHost, proporcionando los parámetros adecuados para la colección de Azure Cosmos DB. Luego, llame a `RegisterObserverAsync` para registrar su implementación de `IChangeFeedObserver` con el entorno de tiempo de ejecución. En este punto, el host intentará adquirir una concesión en cada intervalo de claves de partición en la colección de Azure Cosmos DB con un algoritmo "expansivo". Estas concesiones durarán un período de tiempo determinado y, a continuación, deben renovarse. A medida que nuevos nodos, instancias de trabajo en este caso, pasan a estar en línea, colocan reservas de concesión y, con el tiempo, la carga cambia entre los nodos a medida que cada una trata de adquirir más concesiones.

![Uso del host de procesador de fuente de cambios de Azure Cosmos DB](./media/change-feed/changefeedprocessor.png)

Con el tiempo, se establece un equilibrio. Esta capacidad dinámica permite la aplicación del escalado automático basado en CPU a los consumidores para escalar vertical y horizontalmente. Si los cambios están disponibles en Azure Cosmos DB a una mayor velocidad de la que los consumidores pueden procesar, el aumento de la CPU en los consumidores puede usarse para producir un escalado automático en el recuento de instancias de trabajo.

La clase `ChangeFeedProcessorHost` también implementa un mecanismo de punto de comprobación con una colección independiente de concesiones de Azure Cosmos DB. Este mecanismo almacena el desplazamiento en función de la partición, para que cada consumidor pueda determinar cuál fue el último punto de comprobación del cliente anterior. A medida que las particiones pasan de un nodo a otro a través de concesiones, este es el mecanismo de sincronización que facilita el desplazamiento de cargas.


A continuación, se muestra un fragmento de código para un host de procesador de fuente de cambios simple que imprime los cambios en la consola:

```cs
    class DocumentFeedObserver : IChangeFeedObserver
    {
        private static int s_totalDocs = 0;
        public Task OpenAsync(ChangeFeedObserverContext context)
        {
            Console.WriteLine("Worker opened, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;  // Requires targeting .NET 4.6+.
        }
        public Task CloseAsync(ChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.WriteLine("Worker closed, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }
        public Task ProcessEventsAsync(IReadOnlyList<Document> docs, ChangeFeedObserverContext context)
        {
            Console.WriteLine("Change feed: total {0} doc(s)", Interlocked.Add(ref s_totalDocs, docs.Count));
            return Task.CompletedTask;
        }
    }
```

En el siguiente fragmento de código se muestra la forma de registrar un nuevo host para que escuche los cambios de una colección de Azure Cosmos DB. Aquí se configura una colección independiente para administrar las concesiones a las particiones entre varios clientes:

```cs
    string hostName = Guid.NewGuid().ToString();
    DocumentCollectionInfo documentCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "documents"
    };

    DocumentCollectionInfo leaseCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "leases"
    };

    ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation);
    await host.RegisterObserverAsync<DocumentFeedObserver>();
```

En este artículo se ofrece un tutorial sobre la compatibilidad con la fuente de cambios de Azure Cosmos DB y cómo controlar los cambios efectuados en datos de Azure Cosmos DB mediante la API de REST o los SDK. 

## <a name="next-steps"></a>Pasos siguientes
* Pruebe los [ejemplos de código de fuente de cambios de Azure Cosmos DB incluidos en GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).
* Introducción a la codificación con los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) o la [API de REST](/rest/api/documentdb/)

