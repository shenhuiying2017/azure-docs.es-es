---
title: 'Azure Cosmos DB: desarrollo con SQL API en .NET | Microsoft Docs'
description: "Obtenga información sobre cómo desarrollar con SQL API de Azure Cosmos DB mediante .NET"
services: cosmos-db
documentationcenter: 
author: rafats
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: e37a0993567b6cec7ed6a91e6dad1f2e2c097198
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmosdb-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: desarrollo con SQL API en .NET

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En este tutorial se muestra cómo crear una cuenta de Azure Cosmos DB mediante Azure Portal y cómo crear a continuación una colección y base de datos de documentos con una [clave de partición](sql-api-partition-data.md#partition-keys) mediante [SQL .NET API](sql-api-introduction.md). Si define una clave de partición en el momento de crear una colección, la aplicación está preparada para escalar fácilmente a medida que aumentan los datos. 

En este tutorial se describen las siguientes tareas mediante el uso de [SQL .NET API](sql-api-sdk-dotnet.md):

> [!div class="checklist"]
> * Creación de una cuenta de Azure Cosmos DB
> * Creación de una base de datos y colección con una clave de partición
> * Creación de documentos JSON
> * Actualización de un documento
> * Consulta de colecciones con particiones
> * Ejecución de procedimientos almacenados
> * Eliminar un documento
> * Eliminación de una base de datos

## <a name="prerequisites"></a>requisitos previos
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

## <a name="create-an-azure-cosmos-db-account"></a>Creación de una cuenta de Azure Cosmos DB

Para comenzar, creemos una cuenta de Azure Cosmos DB en Azure Portal.

> [!TIP]
> * ¿Ya tiene una cuenta de Azure Cosmos DB? Si es así, vaya a [Configuración de la solución de Visual Studio](#SetupVS)
> * Si usa el Emulador de Azure Cosmos DB, siga los pasos que se indican en [Emulador de Azure Cosmos DB](local-emulator.md) para configurar el emulador y vaya directamente a [Configuración de la solución de Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configuración de la solución de Visual Studio
1. Abra **Visual Studio** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas** / **Visual C#** / **Aplicación de consola (.NET Framework)**, asigne un nombre al proyecto y haga clic en **Aceptar**.
   ![Captura de pantalla de la ventana Nuevo proyecto](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. En el **Explorador de soluciones**, haga clic con el botón derecho en la nueva aplicación de la consola, que se encuentra en la solución de Visual Studio y, a continuación, haga clic en **Administrar paquetes NuGet...**
    
    ![Captura de pantalla del menú contextual del proyecto](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. En la pestaña **NuGet**, haga clic en **Examinar** y escriba **documentdb** en el cuadro de búsqueda.
<!---stopped here--->
6. En los resultados, busque **Microsoft.Azure.DocumentDB** y haga clic en **Instalar**.
   El identificador del paquete de la biblioteca de cliente de Azure Cosmos DB es [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Captura de pantalla del menú NuGet para encontrar el SDK de cliente de Azure Cosmos DB](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Si recibe un mensaje sobre cómo revisar los cambios en la solución, haga clic en **Aceptar**. Si recibe un mensaje acerca de la aceptación de licencia, haga clic en **Acepto**.

## <a id="Connect"></a>Incorporación de referencias en el proyecto
Los pasos restantes de este tutorial proporcionan los fragmentos de código de SQL API necesarios para crear y actualizar los recursos de Azure Cosmos DB en el proyecto.

En primer lugar, agregue estas referencias a la aplicación.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Conexión de la aplicación

A continuación, agregue estas dos constantes y la variable *client* en la aplicación.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

A continuación, vuelva a [Azure Portal](https://portal.azure.com) para recuperar la dirección URL del punto de conexión y la clave principal. La dirección URL del punto de conexión y la clave principal son necesarias para que la aplicación sepa a dónde debe conectarse y para que Azure Cosmos DB confíe en la conexión de la aplicación.

En Azure Portal, vaya a la cuenta de Azure Cosmos DB, haga clic en **Claves** y, luego, en **Claves de lectura y escritura**.

Copie el URI desde el portal y péguelo en `<your endpoint URL>` en el archivo program.cs. Después, copie la CLAVE PRINCIPAL del portal y péguelo en `<your primary key>`. Asegúrese de quitar `<` y `>` de los valores.

![Captura de pantalla de Azure Portal, usado por el tutorial de NoSQL para crear una aplicación de consola de C#. Muestra una cuenta de Azure Cosmos DB, con las claves resaltadas en la hoja de la cuenta de Azure Cosmos DB y los valores de URI y PRIMARY KEY resaltados en la hoja Claves.](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Creación de instancia de DocumentClient

Ahora, cree una instancia nueva de **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Creación de una base de datos

A continuación, cree una [base de datos](sql-api-resources.md#databases) de Azure Cosmos DB con el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) o el método [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) de la clase **DocumentClient** desde el [SDK de .NET de SQL](sql-api-sdk-dotnet.md). Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Decisión sobre una clave de partición 

Las colecciones son contenedores para almacenar documentos. Son recursos lógicos y pueden [abarcar una o varias particiones físicas](partition-data.md). Una [clave de partición](sql-api-partition-data.md) es una propiedad (o ruta de acceso) dentro de los documentos que se usa para distribuir los datos entre los servidores o las particiones. Todos los documentos que tengan la misma clave de partición se almacenan en la misma partición. 

Determinar una clave de partición es una decisión importante de tomar antes de crear una colección. Las claves de partición son una propiedad (o ruta de acceso) dentro de los documentos que puede usar Azure Cosmos DB para distribuir los datos entre los distintos servidores o particiones. Cosmos DB aplica un algoritmo hash al valor de clave de partición y usa el resultado al que se ha aplicado un algoritmo hash para determinar en qué partición se va a almacenar el documento. Todos los documentos que tengan la clave de partición se almacenan en la misma partición y las claves de partición no se pueden modificar una vez que se crea una colección. 

En este tutorial se establecerá la clave de partición en `/deviceId`, para que todos los datos de un único dispositivo se almacenen en una sola partición. Desea elegir una clave de partición con una gran cantidad de valores, cada uno de los cuales se usa casi con la misma frecuencia para garantizar que Cosmos DB puede equilibrar la carga a medida que aumentan los datos para alcanzar el rendimiento total de la colección. 

Para más información sobre la creación de particiones, consulte [Partición y escala en Azure Cosmos DB](partition-data.md) 

## <a id="CreateColl"></a>Creación de una colección 

Ahora que conocemos la clave de partición, `/deviceId`, vamos a crear una [colección](sql-api-resources.md#collections) a través del método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) o el método [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON y cualquier lógica de aplicación JavaScript asociada. 

> [!WARNING]
> Crear una colección implica precios, debido a que reserva rendimiento para que la aplicación se comunique con Azure Cosmos DB. Para más detalles, visite la [página sobre precios](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Este método realiza una llamada API de REST a Azure Cosmos DB y el servicio aprovisiona una cantidad de particiones según el rendimiento que se solicite. Puede cambiar el rendimiento de una colección a medida que evolucionan las necesidades de rendimiento con el SDK o [Azure Portal](set-throughput.md).

## <a id="CreateDoc"></a>Creación de documentos JSON
Vamos a insertar algunos documentos JSON en Azure Cosmos DB. Un [documento](sql-api-resources.md#documents) puede crearse mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Esta clase de ejemplo contiene una lectura de dispositivo y una llamada a CreateDocumentAsync para insertar una nueva lectura de dispositivo en una colección.

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

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
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
## <a name="read-data"></a>Lectura de datos

Leamos el documento según su clave de partición e identificador con el método ReadDocumentAsync. Tenga en cuenta que las lecturas incluyen un valor PartitionKey (correspondiente al encabezado de solicitud `x-ms-documentdb-partitionkey` de la API de REST).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Actualización de datos

Ahora actualizaremos algunos datos con el método ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Eliminación de datos

Ahora eliminaremos un documento por clave de partición e identificador con el método DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Consulta de colecciones con particiones

Al consultar datos en las colecciones particionadas, Azure Cosmos DB enruta automáticamente la consulta a las particiones correspondientes a los valores de clave de partición especificados en el filtro (en caso de que los haya). Por ejemplo, esta consulta se enruta únicamente a la partición cuya clave es "XMS-0001".

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

## <a name="parallel-query-execution"></a>Ejecución de consultas en paralelo
Los SDK de SQL de Azure Cosmos DB de la versión 1.9.0 y posterior admiten opciones de ejecución de consultas en paralelo, que permiten realizar consultas de baja latencia en colecciones con particiones, incluso cuando tienen que acceder a un gran número de particiones. Por ejemplo, la siguiente consulta está configurada para ejecutarse en paralelo en particiones.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Puede administrar la ejecución de consultas en paralelo ajustando los parámetros siguientes:

* Al establecer `MaxDegreeOfParallelism`, puede controlar el grado de paralelismo; es decir, el número máximo de conexiones de red simultáneas en las particiones de la colección. Si lo establece en -1, el grado de paralelismo lo administra el SDK. Si el parámetro `MaxDegreeOfParallelism` no se especifica o está establecido en 0, que es el valor predeterminado, habrá una única conexión de red a las particiones de la colección.
* Al establecer `MaxBufferedItemCount`, puede compensar el uso de memoria por parte del cliente y la latencia en las consultas. Si se omite este parámetro o lo establece en -1, el número de elementos almacenados en búfer durante la ejecución de consultas en paralelo lo administrará el SDK.

Como se trata del mismo estado de la colección, una consulta en paralelo devolverá resultados en el mismo orden que la ejecución en serie. Al realizar una consulta entre particiones que incluye la ordenación (ORDER BY o TOP), el SDK de SQL emite la consulta en paralelo en las particiones y combina los resultados ordenados parcialmente en el lado del cliente para generar resultados ordenados globalmente.

## <a name="execute-stored-procedures"></a>Ejecución de procedimientos almacenados
Por último, puede ejecutar transacciones atómicas en relación con documentos con el mismo identificador de dispositivo, por ejemplo, si desea mantener los agregados o el estado más reciente de un dispositivo en un único documento. Para ello, agregue el código siguiente al proyecto.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

Eso es todo. Estos son los componentes principales de una aplicación Azure Cosmos DB que usa una clave de partición para escalar de manera eficaz la distribución de datos entre las distintas particiones.  

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en este tutorial en Azure Portal:

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre único del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha hecho lo siguiente: 

> [!div class="checklist"]
> * Se creó una cuenta de Azure Cosmos DB
> * Se creó una base de datos y una colección con una clave de partición
> * Se crearon documentos JSON
> * Se actualizó un documento
> * Se consultaron colecciones con particiones
> * Se ejecutó un procedimiento almacenado
> * Se eliminó un documento
> * Se eliminó una base de datos

Ahora puede pasar al tutorial siguiente e importar datos adicionales a su cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importación de datos a Azure Cosmos DB](import-data.md)
