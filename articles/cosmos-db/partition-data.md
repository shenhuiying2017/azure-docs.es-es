---
title: "Creación de particiones y escalado horizontal en Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo funciona la creación de particiones en Azure Cosmos DB, cómo configurar la creación de particiones y las claves de partición y cómo seleccionar la clave de partición correcta para su aplicación."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: 6f272136d535dddd9c8213293841ace203c042a1
ms.contentlocale: es-es
ms.lasthandoff: 09/08/2017

---

# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partición y escalado en Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) es un servicio de bases de datos multimodelo, con distribución global, diseñado para ayudarle a lograr un rendimiento rápido y predecible. Se escala sin problemas junto con la aplicación a medida que esta crece. Este artículo proporciona información general sobre cómo funcionan las particiones para todos los modelos de datos de Azure Cosmos DB. También describe cómo puede configurar los contenedores de Azure Cosmos DB para escalar de forma eficaz las aplicaciones.

Las particiones y las claves de partición también se analizan en este vídeo de Azure Friday con Scott Hanselman y Shireesh Thota, administrador de ingeniería principal de Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Creación de particiones en Azure Cosmos DB
Azure Cosmos DB permite almacenar y consultar datos sin esquemas con un tiempo de respuesta de milisegundos y a cualquier escala. Azure Cosmos DB proporciona contenedores para el almacenamiento de datos llamados *colecciones* (para el documento), *grafos* o *tablas*. Los contenedores son recursos lógicos y pueden abarcar uno o varios servidores o particiones físicos. Azure Cosmos DB determina el número de particiones en función del tamaño de almacenamiento y el procesamiento aprovisionado del contenedor. Todas las particiones de Azure Cosmos DB tienen una cantidad fija de almacenamiento con respaldo SSD asociado y se replican para ofrecer una alta disponibilidad. Azure Cosmos DB se encarga de administrar las particiones de principio a fin, por lo que no tendrá que escribir ningún código complejo ni administrar ninguna partición. Los contenedores de Azure Cosmos DB son ilimitados en términos de almacenamiento y rendimiento. 

![Partición de los recursos](./media/introduction/azure-cosmos-db-partitioning.png) 

La partición es transparente para la aplicación. Azure Cosmos DB admite lecturas y escrituras rápidas, consultas, lógica transaccional, niveles de coherencia y control de acceso muy detallado a través de métodos o API para un recurso de contenedor único. El servicio controla la distribución de datos por las distintas particiones, así como el enrutamiento de las solicitudes de consulta a la partición correcta. 

¿Cómo funciona la creación de particiones? Cada elemento debe tener una clave de partición y una clave de fila que lo identifiquen de forma única. La clave de partición ejerce de partición lógica para sus datos y proporciona a Azure Cosmos DB un límite natural para distribuir datos entre las particiones. En resumen, la creación de particiones funciona en Azure Cosmos DB de la siguiente manera:

* Usted aprovisiona un contenedor de Azure Cosmos DB con el rendimiento de las solicitudes/s `T`
* En un segundo plano, Azure Cosmos DB aprovisiona las particiones necesarias para atender las solicitudes/s `T`. Si `T` es superior al rendimiento máximo por partición `t`, Azure Cosmos DB aprovisiona las particiones `N` = `T/t`.
* Azure Cosmos DB asigna el espacio clave de los hash de clave de partición uniformemente en las particiones `N`. De este modo, cada partición (partición física) hospeda valores de clave de partición 1-N (particiones lógicas).
* Si una partición física `p` alcanza su límite de almacenamiento, Azure Cosmos DB divide sin ningún problema `p` en dos nuevas particiones, `p1` y `p2`. Distribuye los valores correspondientes a la mitad de las claves aproximadamente para cada una de las particiones. Esta operación de división es invisible para la aplicación.
* De forma similar, si el rendimiento que aprovisiona es superior al rendimiento `t*N`, Azure Cosmos DB divide una o varias de sus particiones para respaldar el rendimiento más alto.

La semántica para claves de partición es ligeramente diferente a fin de coincidir con la semántica de cada API, como se muestra en la siguiente tabla:

| API | Clave de partición | Clave de fila |
| --- | --- | --- |
| Azure Cosmos DB | Ruta de acceso de clave de partición personalizada | `id` fija | 
| MongoDB | Clave compartida personalizada  | `_id` fija | 
| Grafo | Propiedad de clave de partición personalizada | `id` fija | 
| Tabla | `PartitionKey` fija | `RowKey` fija | 

Azure Cosmos DB usa la creación de particiones basada en hash. Al escribir un elemento, Azure Cosmos DB aplica un algoritmo hash al valor de clave de partición y usa el resultado al que se ha aplicado un algoritmo hash para determinar en qué partición se va a almacenar el elemento. Azure Cosmos DB almacena todos los elementos con la misma clave de partición en la misma partición física. La elección de la clave de partición es una decisión importante que debe realizarse en el tiempo de diseño. Debe elegir un nombre de propiedad que tenga una amplia gama de valores e incluso patrones de acceso.

> [!NOTE]
> Se recomienda tener una clave de partición con muchos valores distintos (como mínimo varios centenares o miles).
>

Los contenedores de Azure Cosmos DB se pueden crear como *fijos* o *ilimitados*. Los contenedores de tamaño fijo tienen un límite máximo de 10 GB y un rendimiento de 10 000 RU/s. Algunas API permiten que la clave de partición se omita para los contenedores de tamaño fijo. Para crear un contenedor como ilimitado, debe especificar un rendimiento mínimo de 2500 RU/s.

## <a name="partitioning-and-provisioned-throughput"></a>Creación de particiones y procesamiento aprovisionado
Azure Cosmos DB se ha diseñado para ofrecer un rendimiento predecible. Al crear un contenedor, reserva capacidad de proceso en términos de *[unidades de solicitud](request-units.md) (RU) por segundo*. A cada solicitud se le asigna una carga de unidad de solicitud proporcional a la cantidad de recursos del sistema, como la CPU, la memoria y las E/S consumidas por la operación. Una lectura de un documento de 1 KB con coherencia de sesión consume 1 unidad de solicitud. Una lectura es 1 RU independientemente del número de elementos almacenados o del número de solicitudes que se ejecutan de manera simultánea. Los elementos más grandes exigen unidades de solicitud mayores en función del tamaño. Si se conoce el tamaño de las entidades y el número de lecturas que soportará la aplicación, se puede aprovisionar la cantidad exacta de procesamiento requerido para las necesidades de lectura de la aplicación. 

> [!NOTE]
> Para alcanzar el rendimiento total del contenedor, debe elegirse una clave de partición que permita distribuir uniformemente las solicitudes entre algunos valores de clave de partición definidos.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Uso de las API de Azure Cosmos DB
Puede usar Azure Portal o la CLI de Azure para crear contenedores y escalarlos en cualquier momento. En esta sección se muestra cómo crear contenedores y se especifica la definición de clave de partición y rendimiento en cada una de las API admitidas.

### <a name="azure-cosmos-db-api"></a>API de Azure Cosmos DB
En el ejemplo siguiente se muestra cómo crear un contenedor (colección) mediante la API de Azure Cosmos DB. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Puede leer un elemento (documento) mediante el método `GET` de la API de REST o mediante `ReadDocumentAsync` en uno de los SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>MongoDB API
Con la API de MongoDB, puede crear una colección particionada mediante la herramienta, el controlador o el SDK que prefiera. En este ejemplo, usamos el shell de Mongo para crear la colección.

En el shell de Mongo:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Resultados:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>Table API

Con Table API, especifique el rendimiento de las tablas en la configuración appSettings de su aplicación.

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

A continuación, cree una tabla mediante el SDK de Azure Table Storage. La clave de partición se crea implícitamente como el valor `PartitionKey`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

Puede recuperar una sola entidad mediante el siguiente fragmento de código:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Para más información, consulte [Desarrollo con Table API](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>API Graph

Con la API Graph, debe usar Azure Portal o la CLI de Azure para crear contenedores. De forma alternativa, como Azure Cosmos DB tiene varios modelos, puede usar uno de los otros modelos para crear y escalar el contenedor de grafos.

Puede leer cualquier vértice o borde mediante el id. y la clave de partición en Gremlin. Por ejemplo, para un grafo con la región ("EE. UU.") como clave de partición y "Seattle" como clave de fila, puede encontrar un vértice mediante la siguiente sintaxis:

```
g.V(['USA', 'Seattle'])
```

Puede hacer referencia a un borde mediante la clave de partición y la clave de fila.

```
g.E(['USA', 'I5'])
```

Para más información, consulte [Compatibilidad con Azure Cosmos DB](gremlin-support.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Diseño para la creación de particiones
Para escalar de forma eficaz con Azure Cosmos DB, debe elegir una buena clave de partición al crear el contenedor. Existen dos consideraciones principales cuando se trata de elegir una clave de partición:

* El **límite para la consulta y las transacciones**. La elección de una clave de partición debe equilibrar la necesidad de permitir el uso de transacciones frente a la necesidad de distribuir las entidades en varias claves de partición para garantizar que la solución sea escalable. Por una parte, se puede establecer la misma clave de partición para todos los elementos, pero esta opción puede limitar la escalabilidad de su solución. Por otra parte, puede asignar una clave de partición única para cada elemento. Esta opción es muy escalable, pero no le permitirá utilizar transacciones entre documentos mediante los procedimientos y desencadenadores almacenados. Una clave de partición idónea le permite usar consultas eficaces y tiene suficiente cardinalidad para asegurar que la solución es escalable. 
* **Sin cuellos de botella de rendimiento y almacenamiento**. Es importante elegir una propiedad que permita la distribución de las escrituras en diversos valores definidos. Las solicitudes dirigidas a la misma clave de partición no pueden superar el rendimiento de una sola partición y se limitan. Por tanto, es importante elegir una clave de partición que no dé como resultado "zonas activas" dentro de la aplicación. Puesto que todos los datos de una clave de una sola partición deben almacenarse en una partición, también se recomienda evitar claves de partición que tengan grandes volúmenes de datos para el mismo valor. 

Echemos un vistazo a algunos escenarios en el mundo real, así como a las buenas claves de partición de cada uno de ellos:
* Si implementa un back-end de perfil de usuario, el identificador de usuario es una buena elección para la clave de partición.
* Si almacena datos IoT, como el estado del dispositivo, un identificador de dispositivo es una buena elección para la clave de partición.
* Si usa Azure Cosmos DB para registrar datos de serie temporal, el nombre de host o el identificador de proceso son una buena elección para la clave de partición.
* Si tiene una arquitectura multiempresa, el identificador del inquilino es una buena elección para la clave de partición.

En algunos casos de uso, como IoT y los perfiles de usuario, la clave de partición podría ser igual que su identificador (clave de documento). En otros, como en los datos de serie temporal, la clave de partición será diferente del identificador.

### <a name="partitioning-and-loggingtime-series-data"></a>Creación de particiones, registro y datos de serie temporal
Uno de los casos de uso más comunes de Azure Cosmos DB es el registro y la telemetría. Es importante elegir una buena clave de partición, ya que es posible que necesite leer y escribir grandes volúmenes de datos. La opción depende de las tasas de lectura y escritura y de los tipos de consultas que espera ejecutar. Estas son algunas sugerencias sobre cómo elegir una buena clave de partición:

* Si el caso de uso implica una pequeña tasa de escrituras que se acumulan durante un largo período de tiempo, y necesita consultar por intervalos de marcas de tiempo y otros filtros, use un resumen de la marca de tiempo. Por ejemplo, un buen enfoque es usar la fecha como una clave de partición. Esto le permite consultar todos los datos para una fecha desde una sola partición. 
* Si la carga de trabajo tiene muchas operaciones de escritura, lo que es más común, debe usar una clave de partición que no se base en la marca de tiempo. Con este enfoque, Cosmos DB puede distribuir escrituras uniformemente en varias particiones. En este caso, un nombre de host, identificador de proceso, identificador de actividad u otra propiedad con cardinalidad elevada, es una buena elección. 
* Hay otro enfoque híbrido en el que tiene varios contenedores, uno para cada día o mes, y la clave de partición es una propiedad pormenorizada como nombre de host. Este enfoque tiene la ventaja de que se pueden establecer distintos niveles de rendimiento en función de la ventana de tiempo. Por ejemplo, el contenedor para el mes actual se aprovisiona con un mayor rendimiento ya que sirve lecturas y escrituras. Los meses anteriores se aprovisionan con menor rendimiento ya que solo sirven lecturas.

### <a name="partitioning-and-multitenancy"></a>Particiones y aplicaciones multiinquilino
Si implementa una aplicación multiinquilino mediante Azure Cosmos DB, hay dos patrones populares: una clave de partición por inquilino y un contenedor por inquilino. Estas son las ventajas y desventajas de cada uno:

* **Una clave de partición por inquilino**. En este modelo, los inquilinos se colocan en un solo contenedor. Por su parte, las consultas e inserciones de elementos dentro de un único inquilino pueden realizarse en una sola partición. También se puede implementar la lógica transaccional en todos los elementos de un inquilino. Puesto que varios inquilinos comparten un contenedor, se pueden ahorrar costos de almacenamiento y rendimiento mediante la agrupación de los recursos para los inquilinos en un solo contenedor, en lugar de aprovisionar capacidad de aumento adicional para cada inquilino. El inconveniente es que no es posible aislar el procesamiento por inquilino. Así, los incrementos de rendimiento y procesamiento se aplican a todo el contenedor, frente a los incrementos destinados a los inquilinos.
* **Un contenedor por inquilino**. En este modelo, cada inquilino tiene su propio contenedor y puede reservar un rendimiento por inquilino. Con los nuevos precios de aprovisionamiento de Azure Cosmos DB, este modelo es más rentable para las aplicaciones multiinquilino con un número pequeño de inquilinos.

También puede usar un enfoque de combinación o niveles que coloca los inquilinos pequeños y migra los inquilinos más grandes a su propio contenedor.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, hemos proporcionado información general sobre los conceptos y procedimientos recomendados para crear particiones con cualquier API de Azure Cosmos DB. 

* Información sobre el [procesamiento aprovisionado en Azure Cosmos DB](request-units.md).
* Información sobre la [distribución global en Azure Cosmos DB](distribute-data-globally.md).




