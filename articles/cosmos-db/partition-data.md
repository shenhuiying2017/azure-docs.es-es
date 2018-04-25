---
title: Creación de particiones y escalado horizontal en Azure Cosmos DB | Microsoft Docs
description: Obtenga información sobre cómo funciona la creación de particiones en Azure Cosmos DB, cómo configurar la creación de particiones y las claves de partición y cómo seleccionar la clave de partición correcta para su aplicación.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f4825d7393b4507b1cd512f3e33c5637fea8ba2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partición y escalado en Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) es un servicio de bases de datos multimodelo, con distribución global, diseñado para ayudarle a lograr un rendimiento rápido y predecible. Se escala sin problemas junto con la aplicación a medida que esta crece. Este artículo proporciona información general sobre cómo funcionan las particiones para todos los modelos de datos de Azure Cosmos DB. También describe cómo puede configurar los contenedores de Azure Cosmos DB para escalar de forma eficaz las aplicaciones.

En este vídeo se describe la creación de particiones y las claves de partición:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Creación de particiones en Azure Cosmos DB
Azure Cosmos DB permite almacenar y consultar datos sin esquemas con una latencia inferior a 10 milisegundos a cualquier escala. Azure Cosmos DB proporciona contenedores para el almacenamiento de datos llamados *colecciones* (para el documento), *grafos* o *tablas*. 

Los contenedores son recursos lógicos y pueden abarcar uno o varios servidores o particiones físicos. Azure Cosmos DB determina el número de particiones en función del tamaño de almacenamiento y la capacidad de proceso aprovisionada del contenedor. 

Una partición *física* es una cantidad fija de almacenamiento basado en SSD reservada. Todas las particiones físicas se replican para lograr una alta disponibilidad. Una o varias particiones físicas componen un contenedor. Azure Cosmos DB se encarga de administrar las particiones físicas de principio a fin, por lo que no tendrá que escribir ningún código complejo ni administrar ninguna partición. Los contenedores de Azure Cosmos DB son ilimitados en términos de almacenamiento y rendimiento. 

Una partición *lógica* es aquella dentro de una partición física que almacena todos los datos asociados a un valor de clave de partición única. Varias particiones lógicas pueden terminar en la misma partición física. En el siguiente diagrama, un único contenedor tiene tres particiones lógicas. Cada partición lógica almacena los datos para una clave de partición, LAX, AMS y MEL, respectivamente. Cada una de las particiones lógicas LAX, AMS y MEL no puede crecer más allá del límite máximo de las particiones lógicas de 10 GB. 

![Partición de los recursos](./media/introduction/azure-cosmos-db-partitioning.png) 

Cuando un contenedor cumple los [requisitos previos de la creación de particiones](#prerequisites), esta creación es completamente transparente para la aplicación. Azure Cosmos DB admite lecturas y escrituras rápidas, consultas, lógica transaccional, niveles de coherencia y control de acceso muy detallado a través de métodos o API para un recurso de contenedor único. El servicio controla la distribución de los datos en las distintas particiones físicas y lógicas, así como el enrutamiento de las solicitudes de consulta a la partición correcta. 

## <a name="how-does-partitioning-work"></a>¿Cómo funciona la creación de particiones?

¿Cómo funciona la creación de particiones? Cada elemento debe tener una *clave de partición* y una *clave de fila* que lo identifiquen de forma única. La clave de partición ejerce de partición lógica para los datos y proporciona a Azure Cosmos DB un límite natural para distribuir datos entre las particiones físicas. Los datos de una única partición lógica deben residir dentro de una misma partición física, pero la administración de particiones físicas está administrada por Azure Cosmos DB. 

En resumen, la creación de particiones funciona en Azure Cosmos DB de la siguiente manera:

* Va a aprovisionar un contenedor de Azure Cosmos DB con una capacidad de proceso de **T** RU/s (solicitudes por segundo).
* En un segundo plano, Azure Cosmos DB aprovisiona las particiones necesarias para atender **T** solicitudes por segundo. Si **T** es superior al rendimiento máximo por partición **t**,Azure Cosmos DB aprovisiona **N = T/t** particiones.
* Azure Cosmos DB asigna el espacio de claves de los hash de claves de partición uniformemente en las **N** particiones. De este modo, cada partición (partición física) hospeda **1/N** valores de clave de partición (particiones lógicas).
* Si una partición física **p** alcanza su límite de almacenamiento, Azure Cosmos DB divide **p** en dos nuevas particiones, **p1** y **p2**. Distribuye los valores correspondientes a la mitad de las claves, aproximadamente, a cada una de las nuevas particiones. Esta operación de división es completamente invisible para la aplicación. Si una partición física alcanza su límite de almacenamiento y todos los datos en la partición física pertenecen a la misma clave de partición lógica, no tiene lugar la operación de división. Esto se debe a que todos los datos de una clave de partición lógica única deben residir en la misma partición física. En este caso se debe emplear una estrategia de claves de partición diferente.
* Si el rendimiento que aprovisiona es mayor que **t*N**, Azure Cosmos DB divide una o varias de sus particiones para respaldar el rendimiento más alto.

La semántica para claves de partición es ligeramente diferente a fin de coincidir con la semántica de cada API, como se muestra en la siguiente tabla:

| API | Clave de partición | Clave de fila |
| --- | --- | --- |
| SQL | Ruta de acceso de clave de partición personalizada | `id` fija | 
| MongoDB | Clave compartida personalizada  | `_id` fija | 
| Gremlin | Propiedad de clave de partición personalizada | `id` fija | 
| Tabla | `PartitionKey` fija | `RowKey` fija | 

Azure Cosmos DB usa la creación de particiones basada en hash. Al escribir un elemento, Azure Cosmos DB aplica un algoritmo hash al valor de clave de partición y usa el resultado al que se ha aplicado un algoritmo hash para determinar en qué partición se va a almacenar el elemento. Azure Cosmos DB almacena todos los elementos con la misma clave de partición en la misma partición física. La elección de la clave de partición es una decisión importante que debe realizarse en el tiempo de diseño. Debe elegir un nombre de propiedad que tenga una amplia gama de valores e incluso patrones de acceso. Si una partición física alcanza su límite de almacenamiento y los datos de la partición tienen la misma clave de partición, Azure Cosmos DB devuelve el mensaje que indica que *la clave de partición alcanzó el tamaño máximo de 10 GB* y la partición no se divide; por tanto, elegir una clave de partición es una decisión muy importante.

> [!NOTE]
> Se recomienda tener una clave de partición con un gran número de valores distintos (por ejemplo, centenares o miles). Le permite distribuir la carga de trabajo uniformemente entre estos valores. Una clave de partición idónea es la que aparece con frecuencia como filtro en sus consultas y que tiene suficiente cardinalidad para asegurar que la solución sea escalable.
>

Los contenedores de Azure Cosmos DB se pueden crear como *fijos* o *ilimitados* en Azure Portal. Los contenedores de tamaño fijo tienen un límite máximo de 10 GB y un rendimiento de 10 000 RU/s. Para crear un contenedor como ilimitado, debe especificar una clave de partición y una capacidad de proceso mínima de 1000 RU/s. 

Es una buena idea comprobar cómo se distribuyen los datos en las particiones. Para comprobar esto en el portal, vaya a la cuenta de Azure Cosmos DB, haga clic en **Métrica**, en la sección **Supervisión** y luego haga clic en la pestaña **Almacenamiento** para ver cómo se distribuyen los datos en las diferentes particiones físicas.

![Partición de los recursos](./media/partition-data/partitionkey-example.png)

La imagen de la izquierda anterior muestra el resultado de una clave de partición incorrecta y la imagen de la derecha anterior muestra el resultado cuando se elige una clave de partición correcta. En la imagen de la izquierda, puede ver que los datos no se distribuyen uniformemente en las particiones. Debe procurar elegir una clave de partición que distribuya los datos como se ve en la imagen de la derecha.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Requisitos previos para la creación de particiones

Para que las particiones físicas se dividan automáticamente en **p1** y **p2** tal y como se describe en [Cómo funciona la creación de particiones](#how-does-partitioning-work), el contenedor debe crearse con un rendimiento de 1000 RU/s o más, y se debe proporcionar una clave de partición. Al crear un contenedor (por ejemplo, una colección, un grafo o una tabla) en Azure Portal, seleccione la opción **Ilimitada** para la capacidad de almacenamiento a fin de aprovechar las ventajas del escalado ilimitado. 

Si ha creado un contenedor en Azure Portal o mediante programación y la capacidad de proceso inicial era de 1000 RU/s o más, y ha proporcionado una clave de partición, puede aprovechar el escalado ilimitado sin realizar cambios en su contenedor. Esto incluye los contenedores de tamaño **Fijo**, siempre que el contenedor inicial se haya creado con una capacidad de proceso de al menos 1000 RU/s y se haya especificado una clave de partición.

Si ha creado un contenedor de tamaño **Fijo** sin ninguna clave de partición o con una capacidad de proceso menor que 1000 RU/s, el contenedor no se podrá escalar automáticamente tal y como se describe en este artículo. Para migrar los datos de un contenedor como este a un contenedor ilimitado (uno con al menos 1000 RU/s y una clave de partición), debe usar la [Herramienta de migración de datos](import-data.md) o la [biblioteca de fuentes de cambios](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Creación de particiones y procesamiento aprovisionado
Azure Cosmos DB se ha diseñado para ofrecer un rendimiento predecible. Al crear un contenedor, reserva la capacidad de proceso en términos de *[unidades de solicitud](request-units.md) (RU) por segundo*. Cada solicitud realiza una carga de unidad de solicitud proporcional a la cantidad de recursos del sistema, como la CPU, la memoria y la E/S consumida por la operación. Una lectura de un documento de 1 KB con coherencia de sesión consume 1 unidad de solicitud. Una lectura es 1 RU independientemente del número de elementos almacenados o del número de solicitudes que se ejecutan de manera simultánea. Los elementos más grandes exigen unidades de solicitud mayores en función del tamaño. Si se conoce el tamaño de las entidades y el número de lecturas que debe admitir la aplicación, se puede aprovisionar la capacidad de proceso exacta requerida para las necesidades de la aplicación. 

> [!NOTE]
> Para utilizar toda la capacidad de proceso aprovisionada de un contenedor, hay que elegir una clave de partición que permita distribuir uniformemente las solicitudes entre todos los valores de clave de partición definidos.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Uso de las API de Azure Cosmos DB
Puede usar Azure Portal o la CLI de Azure para crear contenedores y escalarlos en cualquier momento. En esta sección se muestra cómo crear contenedores y cómo especificar la capacidad de proceso aprovisionada y la clave de partición que utiliza cada API.


### <a name="sql-api"></a>API DE SQL
En el ejemplo siguiente se muestra cómo crear un contenedor (una colección) mediante SQL API. 

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

Para más información, consulte [Creación de particiones en Azure Cosmos DB con SQL API](sql-api-partition-data.md).

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

Para crear una tabla con Table API, utilice el método `CreateIfNotExists`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

La capacidad de proceso aprovisionada se establece como un argumento de `CreateIfNotExists`. La clave de partición se crea implícitamente como el valor `PartitionKey`. 

Puede recuperar una sola entidad con el código siguiente:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Para más información, consulte [Desarrollo con Table API](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>API de Gremlin

Con la API de Gremlin, puede usar Azure Portal o la CLI de Azure para crear un contenedor que representa un grafo. Como Azure Cosmos DB tiene varios modelos, también puede usar una de las otras API para crear y escalar el contenedor de grafos.

Puede leer cualquier vértice o borde mediante el id. y la clave de partición en Gremlin. Por ejemplo, para un grafo con la región ("EE. UU.") como clave de partición y "Seattle" como clave de fila, puede encontrar un vértice mediante la siguiente sintaxis:

```
g.V(['USA', 'Seattle'])
```

Puede hacer referencia a un borde mediante la clave de partición y la clave de fila.

```
g.E(['USA', 'I5'])
```

Para más información, consulte [Using a partitioned graph in Azure Cosmos DB](graph-partitioning.md) (Uso de un grafo con particiones en Azure Cosmos DB).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Diseño para escala
Para escalar de forma eficaz con Azure Cosmos DB, debe elegir una buena clave de partición al crear el contenedor. Existen dos consideraciones principales cuando se trata de elegir una buena clave de partición:

* **Límites y transacciones de consultas**. La elección de una clave de partición debe equilibrar la necesidad de usar transacciones frente al requisito de distribuir las entidades en varias claves de partición para garantizar que la solución sea escalable. Por una parte, se puede establecer la misma clave de partición para todos los elementos, pero esta opción puede limitar la escalabilidad de su solución. Por otra parte, puede asignar una clave de partición única para cada elemento. Esta opción es muy escalable, pero no le permitirá utilizar transacciones entre documentos mediante los procedimientos y desencadenadores almacenados. Una clave de partición idónea le permite usar consultas eficaces y tiene suficiente cardinalidad para asegurar que la solución es escalable. 
* **Sin cuellos de botella de rendimiento y almacenamiento**. Es importante elegir una propiedad que permita la distribución de las escrituras en diversos valores definidos. Las solicitudes a la misma clave de partición no pueden exceder la capacidad de proceso aprovisionada asignada a una partición, y tendrán una velocidad limitada. Por tanto, es importante elegir una clave de partición que no dé como resultado "zonas activas" dentro de la aplicación. Puesto que todos los datos de una clave de una sola partición deben almacenarse en una partición, también se recomienda evitar claves de partición que tengan grandes volúmenes de datos para el mismo valor. 

Echemos un vistazo a algunos escenarios en el mundo real, así como a las buenas claves de partición de cada uno de ellos:
* Si implementa un back-end de perfil de usuario, el *identificador de usuario* es una buena elección como clave de partición.
* Si almacena datos de IoT, como el estado del dispositivo, un *identificador de dispositivo* es una buena elección como clave de partición.
* Si usa Azure Cosmos DB para registrar datos de series temporales, el *nombre de host* o el *identificador de proceso* son una buena elección como clave de partición.
* Si tiene una arquitectura multiempresa, el *identificador del inquilino* es una buena elección como clave de partición.

En algunos casos de uso, como IoT y los perfiles de usuario, la clave de partición podría ser igual que su *identificador* (clave de documento). En otros, como en los datos de series temporales, la clave de partición será diferente del *identificador*.

### <a name="partitioning-and-loggingtime-series-data"></a>Creación de particiones, registro y datos de serie temporal
Uno de los casos de uso más comunes de Azure Cosmos DB es el registro y la telemetría. Es importante elegir una buena clave de partición en este escenario, ya que es posible que necesite leer y escribir grandes volúmenes de datos. La elección de una clave de partición depende de las tasas de lectura y escritura y de los tipos de consultas que espera ejecutar. Estas son algunas sugerencias sobre cómo elegir una buena clave de partición:

* Si su caso implica una pequeña tasa de escrituras que se acumulan durante un largo período de tiempo, y necesita consultar por intervalos de marcas de tiempo con otros filtros, use un resumen de la marca de tiempo. Por ejemplo, un buen enfoque es usar la fecha como una clave de partición. Esto le permite consultar todos los datos para una fecha dada desde una sola partición. 
* Si la carga de trabajo tiene muchas escrituras, lo que es muy común en este escenario, utilice una clave de partición que no esté basada en la marca de tiempo. Como tal, Azure Cosmos DB puede distribuir y escalar escrituras uniformemente entre varias particiones. En este caso, el *nombre de host*, el *identificador de proceso*, el *identificador de actividad* u otra propiedad con cardinalidad elevada, son una buena elección. 
* Otro enfoque es un enfoque híbrido, en el que tiene varios contenedores, uno para cada día o mes, y la clave de partición es una propiedad pormenorizada como *nombre de host*. Este enfoque tiene la ventaja de que puede establecer un rendimiento diferente para cada contenedor en función de la franja de tiempo, la escala y las necesidades de rendimiento. Por ejemplo, un contenedor para el mes actual se puede aprovisionar con un mayor rendimiento ya que sirve lecturas y escrituras. Los meses anteriores se pueden aprovisionar con menor rendimiento ya que solo atienden lecturas.

### <a name="partitioning-and-multitenancy"></a>Particiones y aplicaciones multiinquilino
Si implementa una aplicación multiinquilino mediante Azure Cosmos DB, hay dos diseños populares que se deben considerar: *una clave de partición por inquilino* y *un contenedor por inquilino*. Estas son las ventajas y desventajas de cada uno:

* **Una clave de partición por inquilino**. En este modelo, los inquilinos se colocan en un solo contenedor. Las consultas e inserciones de un único inquilino pueden realizarse en una sola partición. También se puede implementar la lógica transaccional en todos los elementos que pertenecen a un inquilino. Puesto que varios inquilinos comparten un contenedor, se puede utilizar mejor el almacenamiento y el rendimiento aprovisionado mediante la agrupación de los recursos para los inquilinos en un solo contenedor, en lugar de aprovisionar para cada inquilino. El inconveniente es que no es posible aislar el procesamiento por inquilino. El aumento del rendimiento para garantizarlo se aplicará a todo el contenedor con todos los inquilinos frente a los aumentos previstos para un inquilino individual.
* **Un contenedor por inquilino**. En este modelo, cada inquilino tiene su propio contenedor y puede reservar una capacidad de proceso garantizada por inquilino. Este modelo es más rentable para las aplicaciones multiinquilino con un número pequeño de inquilinos.

También puede usar un enfoque de híbrido que coloca juntos los inquilinos pequeños y aísla los inquilinos grandes en su propio contenedor.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, hemos proporcionado información general sobre los conceptos y procedimientos recomendados para la creación de particiones y el escalado con Azure Cosmos DB. 

* Información sobre el [procesamiento aprovisionado en Azure Cosmos DB](request-units.md).
* Información sobre la [distribución global en Azure Cosmos DB](distribute-data-globally.md).



