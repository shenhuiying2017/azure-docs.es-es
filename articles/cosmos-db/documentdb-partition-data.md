---
title: "Creación de particiones y escalado en Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo funciona la creación de particiones en Azure Cosmos DB, cómo configurar la creación de particiones y las claves de partición y cómo seleccionar la clave de partición correcta para su aplicación."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81010d91ac7fe8fa7149c52ed56af304cf4e83d9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="partitioning-in-azure-cosmos-db-using-the-documentdb-api"></a>Creación de particiones en Azure Cosmos DB con la API de DocumentDB

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos distribuido de varios modelos global diseñado para ayudarle a lograr un rendimiento rápido y predecible. Además, permite escalar sin problemas a medida que la aplicación crece. 

En este artículo se proporciona información general sobre cómo trabajar con las particiones de contenedores de Cosmos DB con la API de DocumentDB. Consulte [la creación de particiones y el escalado horizontal](../cosmos-db/partition-data.md) para obtener una información general de los conceptos y procedimientos recomendados para crear particiones con cualquier API de Azure Cosmos DB. 

Para empezar a trabajar con código, descargue el proyecto de [GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Después de leer este artículo, podrá responder a las siguientes preguntas:   

* ¿Cómo funcionan las particiones en Azure Cosmos DB?
* ¿Cómo se configuran las particiones en Azure Cosmos DB?
* ¿Qué son las claves de partición y cómo se elige la clave de partición correcta para una aplicación?

Para empezar a trabajar con código, descargue el proyecto del [ejemplo de versión de prueba de rendimiento de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Claves de partición

En la API de DocumentDB, especifique la definición de la clave de partición en el formulario de una ruta de acceso JSON. En la tabla siguiente se muestran ejemplos de definiciones de clave de partición y los valores correspondientes a cada una de ellas. La clave de partición se especifica como una ruta de acceso; por ejemplo, `/department` representa el departamento de propiedad. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Clave de partición</strong></p></td>
            <td valign="top"><p><strong>Descripción</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corresponde al valor de doc.department, donde doc es el elemento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corresponde al valor de doc.properties.name, donde doc es el elemento (propiedad anidada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corresponde al valor de doc.id (el identificador y la clave de partición son la misma propiedad).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nombre de departamento"</p></td>
            <td valign="top"><p>Corresponde al valor de doc["nombre de departamento"], donde doc es el elemento.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> La sintaxis de la clave de partición es similar a la especificación de rutas de acceso para la indexación de rutas de acceso de directivas con la importante diferencia de que la ruta de acceso corresponde a la propiedad en lugar de al valor, es decir, no hay ningún carácter comodín al final. ¿Por ejemplo, especificaría /department/? para indexar los valores bajo departament, pero especificaría /department como la definición de clave de partición. La clave de partición está indexada de forma implícita y no se puede excluir de la indexación mediante invalidaciones de directiva de indexación.
> 
> 

Examinemos cómo afecta la elección de la clave de partición al rendimiento de una aplicación.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Uso de los SDK de Azure Cosmos DB
A partir de la [versión de la API de REST de 16-12-2015](/rest/api/documentdb/), Azure Cosmos DB admite la creación automática de particiones. Para poder crear contenedores con particiones, es necesario descargar la versión del SDK 1.6.0 (o posteriores) en una de las plataformas admitidas del SDK (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Creación de contenedores
En el ejemplo siguiente se muestra un fragmento de código .NET mediante el que se crea un contenedor que almacena los datos de telemetría de dispositivos con un procesamiento de 20 000 unidades de solicitud por segundo. El SDK establece el valor OfferThroughput (que a su vez establece el encabezado de solicitud `x-ms-offer-throughput` en la API de REST). En este caso, la clave de partición es `/deviceId` . La elección de la clave de partición se guarda junto con el resto de los metadatos del contenedor, como nombre y la directiva de indexación.

Para este ejemplo, elegimos `deviceId` por dos motivos: en primer lugar, sabemos que al haber un gran número de dispositivos, las escrituras pueden distribuirse entre las particiones de manera uniforme, lo que nos permite escalar la base de datos para introducir grandes volúmenes de datos; en segundo lugar, muchas de las solicitudes, como la obtención de la última lectura correspondiente a un dispositivo, se limitan a un único deviceId y se pueden recuperar desde una única partición.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Este método realiza una llamada API de REST a Cosmos DB, tras lo cual el servicio proporciona un número de particiones que está determinado en función del procesamiento que se solicite. Puede cambiar el procesamiento de un contenedor a medida que evolucionen sus necesidades de rendimiento. 

### <a name="reading-and-writing-items"></a>Lectura y escritura de elementos
Ahora, insertemos los datos en Cosmos DB. La clase de ejemplo siguiente contiene una lectura de dispositivo y una llamada a CreateDocumentAsync para insertar una nueva lectura de dispositivo en un contenedor. Este es un ejemplo que aprovecha la API de DocumentDB:

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

A continuación leeremos el elemento por su clave de partición e identificador, lo actualizaremos y, como paso final, lo eliminaremos por la clave de partición e identificador. Tenga en cuenta que las lecturas incluyen un valor PartitionKey (correspondiente al encabezado de solicitud `x-ms-documentdb-partitionkey` de la API de REST).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Consulta de contenedores con particiones
Al consultar datos en los contenedores con particiones, Azure Cosmos DB enruta automáticamente la consulta a las particiones correspondientes a los valores de clave de partición especificados en el filtro (en caso de que los haya). Por ejemplo, esta consulta se enruta únicamente a la partición cuya clave es "XMS-0001".

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
La consulta siguiente no tiene filtro en la clave de partición (DeviceId) y por ello se despliega por todas las particiones, en las que se ejecuta según el índice de la partición. Tenga en cuenta que debe especificar EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` en la API de REST) para que el SDK ejecute una consulta en todas las particiones.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB admite las [funciones de agregado](documentdb-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` y `AVG` en contenedores con particiones mediante SQL a partir del SDK 1.12.0, y versiones posteriores. Las consultas deben incluir un único operador de agregado y deben incluir un valor individual en la proyección.

### <a name="parallel-query-execution"></a>Ejecución de consultas en paralelo
Los SDK de Cosmos DB de la versión 1.9.0 y posterior admiten opciones de ejecución de consultas en paralelo, que permiten realizar consultas de baja latencia en colecciones con particiones, incluso cuando tienen que acceder a un gran número de particiones. Por ejemplo, la siguiente consulta está configurada para ejecutarse en paralelo en particiones.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Puede administrar la ejecución de consultas en paralelo ajustando los parámetros siguientes:

* Al establecer `MaxDegreeOfParallelism`, puede controlar el grado de paralelismo; es decir, el número máximo de conexiones de red simultáneas en las particiones del contenedor. Si lo establece en -1, el grado de paralelismo lo administra el SDK. Si el parámetro `MaxDegreeOfParallelism` no se especifica o está establecido en 0, que es el valor predeterminado, habrá una única conexión de red a las particiones del contenedor.
* Al establecer `MaxBufferedItemCount`, puede compensar el uso de memoria por parte del cliente y la latencia en las consultas. Si se omite este parámetro o lo establece en -1, el número de elementos almacenados en búfer durante la ejecución de consultas en paralelo lo administrará el SDK.

Como se trata del mismo estado de la colección, una consulta en paralelo devolverá resultados en el mismo orden que la ejecución en serie. Al realizar una consulta entre particiones que incluye la ordenación (ORDER BY o TOP), el SDK de Azure Cosmos DB emite la consulta en paralelo entre las particiones y combina los resultados ordenados parcialmente en el lado del cliente para generar resultados ordenados globalmente.

### <a name="executing-stored-procedures"></a>Ejecución de procedimientos almacenados
También puede ejecutar transacciones atómicas en relación con documentos con el mismo identificador de dispositivo, por ejemplo, si desea mantener los agregados o el estado más reciente de un dispositivo en un único elemento. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
En la siguiente sección, veremos cómo puede moverse a contenedores con particiones desde contenedores de partición única.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se proporciona información general sobre cómo trabajar con las particiones de contenedores de Azure Cosmos DB con la API de DocumentDB. Consulte también [la creación de particiones y el escalado horizontal](../cosmos-db/partition-data.md) para obtener una información general de los conceptos y procedimientos recomendados para crear particiones con cualquier API de Azure Cosmos DB. 

* Realice pruebas de escala y de rendimiento con Azure Cosmos DB. Consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md) para ver ejemplos.
* Introducción a la codificación con [SDK](documentdb-sdk-dotnet.md) o la [API de REST](/rest/api/documentdb/)
* Información sobre el [procesamiento aprovisionado en Azure Cosmos DB](request-units.md)

