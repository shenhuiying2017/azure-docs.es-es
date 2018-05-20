---
title: Aprovisionamiento del rendimiento de Azure Cosmos DB | Microsoft Docs
description: Aprenda a configurar el rendimiento aprovisionado para sus contenedores, colecciones, grafos y tablas de Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sngun
ms.openlocfilehash: 925167c6b4a7f173726ec094c2847a16ca3d0ef4
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Configuración y obtención del rendimiento para contenedores y la base de datos de Azure Cosmos DB

Puede establecer el rendimiento de un contenedor, o de un conjunto de contenedores, de Azure Cosmos DB desde Azure Portal o mediante los SDK del cliente. Si se aprovisiona el rendimiento de un conjunto de contenedores, todos ellos comparten el rendimiento aprovisionado. El aprovisionamiento del rendimiento en los contenedores individuales garantizará la reserva de rendimiento para ese contenedor específico. Por otra parte, el aprovisionamiento de rendimiento en una base de datos permite compartir dicho rendimiento entre todos los contenedores que pertenecen a dicha base de datos. En una base de datos de Azure Cosmos DB, puede tener un conjunto de contenedores que compartan tanto el rendimiento como los contenedores, que tienen rendimiento dedicado. 

Según el rendimiento aprovisionado, Azure Cosmos DB asigna las particiones físicas para hospedar el contenedor y divide y reequilibra los datos entre las particiones a medida que crece.

Al asignar RU/s en el nivel de contenedor individual, los contenedores se pueden crear como *fijos* o *ilimitados*. Los contenedores de tamaño fijo tienen un límite máximo de 10 GB y un rendimiento de 10 000 RU/s. Para crear un contenedor ilimitado, debe especificar un rendimiento mínimo de 1000 RU/s y una [clave de partición](partition-data.md). Como es posible que se tengan que dividir los datos entre varias particiones, es necesario elegir una clave de partición que tenga una cardinalidad alta (de cientos a millones de valores distintos). Al seleccionar una clave de partición con muchos valores distintos, se asegura de que Azure Cosmos DB pueda escalar el contenedor, la tabla, el grafo y las solicitudes de manera uniforme. 

Al asignar RU/s en un conjunto de contenedores, los contenedores que pertenecen a este conjunto se tratan como *ilimitados* y debe especificar una clave de partición.

![Unidades de solicitud de aprovisionamiento para contenedores individuales y conjuntos de contenedores](./media/request-units/provisioning_set_containers.png)

Este artículo le guiará por los pasos necesarios para configurar el rendimiento a diferentes niveles en una cuenta de Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Aprovisionamiento del rendimiento mediante Azure Portal

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Aprovisionamiento del rendimiento en un contenedor (colección/gráfico/tabla)

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).  
2. En la barra de navegación izquierda, seleccione **Todos los recursos** y busque su cuenta de Azure Cosmos DB.  
3. Puede configurar el rendimiento durante la creación de un contenedor (colección/grafico/tabla) o actualizar el rendimiento de un contenedor existente.  
4. Para asignar un rendimiento durante la creación de un contenedor, abra la hoja **Explorador de datos** y seleccione **Nueva colección** (Nuevo gráfico, Nueva tabla en el caso de otras API)  
5. Rellene el formulario de la hoja **Agregar colección**. Los campos de esta hoja se describen en la tabla siguiente:  

   |**Configuración**  |**Descripción**  |
   |---------|---------|
   |Id. de base de datos  |  Especifique un nombre único para identificar la base de datos. Una base de datos es un contenedor lógico de una o varias colecciones. Los nombres de base de datos tiene que tener entre 1 y 255 caracteres y no pueden contener /, \\; #, ?, o un espacio al final. |
   |Id. de colección  | Especifique un nombre único para identificar la colección. Los identificadores de las colecciones tienen los mismos requisitos de caracteres que los nombres de las bases de datos. |
   |Capacidad de almacenamiento   | Este valor representa la capacidad de almacenamiento de la base de datos. Al aprovisionar el rendimiento de una colección individual, la capacidad de almacenamiento puede ser **Fija (10 GB)** o **Sin límite**. Una capacidad de almacenamiento ilimitada requiere que se establezca una clave de partición para los datos.  |
   |Throughput   | Cada colección y base de datos pueden tener un rendimiento en unidades de solicitud por segundo.  En el caso de la capacidad de almacenamiento fija, el rendimiento mínimo es 400 unidades de solicitud por segundo (RU/s), mientras que en el de la capacidad de almacenamiento ilimitada, el mínimo se establece en 1000 RU/s.|

6. Después de especificar los valores de estos campos, seleccione **Aceptar** para guardar la configuración.  

   ![Establecer el rendimiento de una colección](./media/set-throughput/set-throughput-for-container.png)

7. Para actualizar el rendimiento de un contenedor existente, expanda la base de datos y el contenedor y, después, haga clic en **Configuración**. En la ventana nueva, escriba el nuevo valor del rendimiento y, después, seleccione **Guardar**.  

   ![Actualizar el rendimiento de una colección](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Aprovisionamiento del rendimiento de un conjunto de contenedores o a nivel de base de datos

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).  
2. En la barra de navegación izquierda, seleccione **Todos los recursos** y busque su cuenta de Azure Cosmos DB.  
3. Puede configurar el rendimiento durante la creación de una base de datos o actualizar el rendimiento de una base de datos existente.  
4. Para asignar el rendimiento durante la creación de una base de datos, abra la hoja **Explorador de datos** y seleccione **Nueva base de datos**  
5. Rellene el valor **Id. de base de datos**, marque la opción **Provision throughput** (Aprovisionar rendimiento) y configura el valor de rendimiento. Una base de datos se puede aprovisionar con un valor de rendimiento mínimo de 50 000 RU/s.  

   ![Establecer el aprovisionamiento con la opción de base de datos nueva](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Para actualizar el rendimiento de una base de datos existente, expanda la base de datos y el contenedor y, después, haga clic en **Escala**. En la ventana nueva, escriba el nuevo valor del rendimiento y, después, seleccione **Guardar**.  

   ![Actualizar el rendimiento de una base de datos](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Aprovisionamiento del rendimiento tanto de un conjunto de contenedores como de un contenedor individual en una base de datos

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).  
2. En la barra de navegación izquierda, seleccione **Todos los recursos** y busque su cuenta de Azure Cosmos DB.  
3. Cree una base de datos y asígnele el rendimiento. Abra la hoja **Explorador de datos** y seleccione **Nueva base de datos**  
4. Rellene el valor **Id. de base de datos**, marque la opción **Provision throughput** (Aprovisionar rendimiento) y configura el valor de rendimiento. Una base de datos se puede aprovisionar con un valor de rendimiento mínimo de 50 000 RU/s.  

   ![Establecer el aprovisionamiento con la opción de base de datos nueva](./media/set-throughput/set-throughput-with-new-database-option.png)

5. A continuación, cree una colección en la base de datos que creó en el paso anterior. Para ello, haga clic con el botón derecho en la base de datos y seleccione **Nueva colección**.  

6. En la hoja **Agregar colección**, escriba el nombre de la colección y una clave de partición. Opcionalmente, puede aprovisionar el rendimiento de un contenedor concreto si elige no asignar un valor de rendimiento, el rendimiento asignado a la base de datos se comparte con la colección.  

   ![Opcionalmente, establezca el rendimiento del contenedor](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Consideraciones que deben tenerse en cuenta al aprovisionar el rendimiento

A continuación encontrará algunas consideraciones que le ayudarán a decidir una estrategia de reserva de rendimiento.

Considere la posibilidad de aprovisionar el rendimiento a nivel de base de datos (es decir, para el conjunto de contenedores) en los casos siguientes:

* Si tiene doce, o más, contenedores que puedan compartir el rendimiento entre ellos.  

* Al migrar desde una base de datos de inquilino único diseñada para ejecutarse en máquinas virtuales hospedadas por IaaS o en entornos locales (por ejemplo, NoSQL o bases de datos relacionales) a Azure Cosmos DB y tiene muchos contenedores.  

* Si desea tener en cuenta picos no planeados en las cargas de trabajo mediante el uso de un rendimiento agrupado a nivel de base de datos.  

* En lugar de establecer el rendimiento de un contenedor individual, le interesa obtener el rendimiento agregado en un conjunto de contenedores de la base de datos.

Considere la posibilidad de aprovisionar el rendimiento en un contenedor individual en los casos siguientes:

* Si tiene pocos contenedores de Azure Cosmos DB.  

* Si desea obtener el rendimiento garantizado en un contenedor determinado con el respaldo de SLA.

## <a name="throughput-ranges"></a>Rangos de rendimiento

En la tabla siguiente se enumeran los rendimientos disponibles para los contenedores:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contenedor de una única partición</strong></p></td>
            <td valign="top"><p><strong>Contenedor con particiones</strong></p></td>
            <td valign="top"><p><strong>Conjunto de contenedores</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento mínimo</p></td>
            <td valign="top"><p>400 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>1000 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>50000 unidades de solicitud por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento máximo</p></td>
            <td valign="top"><p>10 000 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Configuración del rendimiento mediante SQL API para .NET

Este es un fragmento de código para crear un contenedor con 3000 unidades de solicitud por segundo para un contenedor individual mediante el SDK de .NET de la API de SQL:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Este es un fragmento de código para aprovisionar 100 000 unidades de solicitud por segundo en un conjunto de contenedores mediante el SDK de .NET de la API de SQL:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB funciona con un modelo de reserva para el rendimiento. Es decir, se le cobrará por la cantidad de rendimiento *reservada*, independientemente de la que *use* activamente. A medida que los patrones de uso, datos y carga de la aplicación cambian, puede escalar y reducir verticalmente de forma sencilla la cantidad de unidades de solicitud reservadas mediante los SDK o con [Azure Portal](https://portal.azure.com).

Cada contenedor, o conjunto de contenedores, está asignado a un recurso `Offer` de Azure Cosmos DB que contiene metadatos sobre el rendimiento aprovisionado. Puede cambiar el rendimiento asignado buscando el recurso de oferta correspondiente para un contenedor y, a continuación, actualizándolo con el nuevo valor de rendimiento. A continuación se muestra un fragmento de código para cambiar el rendimiento del contenedor a 5000 unidades de solicitud por segundo mediante el SDK de .NET:

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

No se producirá ningún cambio en la disponibilidad del contenedor, o el conjunto de contenedores, cuando cambie el rendimiento. Por lo general, el nuevo rendimiento reservado es efectivo en cuestión de segundos después de su aplicación.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Configuración del rendimiento mediante SQL API para Java

El siguiente fragmento de código recupera el rendimiento actual y lo cambia a 500 RU/s. Para obtener un ejemplo de código completo, vea el archivo [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) en GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Obtención de rendimiento mediante el comando GetLastRequestStatistics de la API de MongoDB

La API de MongoDB admite un comando personalizado, *getLastRequestStatistics*, para recuperar las cargas de solicitudes de las operaciones especificadas.

Por ejemplo, en el shell de Mongo, ejecute la operación en la que desea comprobar la carga de solicitudes.
```
> db.sample.find()
```

Después, ejecute el comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Un método para calcular la cantidad de rendimiento reservado que necesita la aplicación es registrar la carga de unidades de solicitud asociadas a la ejecución de las operaciones típicas, frente a un elemento representativo que usa la aplicación y, después, calcular el número de operaciones que prevé realizar cada segundo.

> [!NOTE]
> Si dispone de tipos de elemento que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, registre el cargo de unidad de solicitud de operación aplicable asociado a cada *tipo* de elemento típico.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Obtención de rendimiento mediante el uso de las métricas de portal de API de MongoDB

La manera más sencilla de obtener una buena estimación de los cargos por la unidad de solicitud de la base de datos de API de MongoDB es usar las métricas de [Azure Portal](https://portal.azure.com). Con los grafos *Número de solicitudes* y *Cargo de solicitud*, puede obtener una estimación de cuántas unidades de solicitud está consumiendo cada operación y cuántas unidades de solicitud consumen entre sí.

![Métricas del portal de API de MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Superación de los límites de rendimiento reservados en la API de MongoDB
Las aplicaciones que superan el rendimiento aprovisionado para un contenedor o un conjunto de contenedores tendrán velocidad limitada hasta que la velocidad de consumo caiga por debajo de la velocidad del rendimiento aprovisionado. Cuando se produce una limitación de la velocidad, el back-end finalizará la solicitud con un código de error `16500`: `Too Many Requests`. De forma predeterminada, la API de MongoDB volverá a intentarlo automáticamente hasta 10 veces antes de devolver un código de error `Too Many Requests`. Si recibe numerosos códigos de error `Too Many Requests`, puede plantearse agregar una lógica de reintento en las rutinas de control de error de la aplicación o [mejorar el rendimiento aprovisionado para el contenedor](set-throughput.md).

## <a name="throughput-faq"></a>Preguntas más frecuentes sobre el rendimiento

**¿Se puede configurar el rendimiento a menos de 400 RU/s?**

400 RU/s es el rendimiento mínimo disponible en los contenedores de una sola partición de Cosmos DB (1000 RU/s es el valor mínimo para los contenedores con particiones). Las unidades de solicitud se establecen en intervalos de 100 RU/s pero el rendimiento no se puede establecer en 100 RU/s o en ningún valor inferior a 400 RU/s. Si está buscando un método rentable para desarrollar y probar Cosmos DB, puede usar gratuitamente el [Emulador de Azure Cosmos DB](local-emulator.md), que puede implementar localmente sin costo alguno. 

**¿Cómo se configura el rendimiento mediante la API de MongoDB?**

No hay ninguna extensión de API de MongoDB para configurar el rendimiento. La recomendación es utilizar SQL API, como se muestra en [Configuración del rendimiento mediante SQL API para .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Pasos siguientes

* Para aprender a calcular el rendimiento y las unidades de solicitud, consulte [Unidades de solicitud y cálculo de rendimiento en Azure Cosmos DB](request-units.md)

* Para aprender más sobre el aprovisionamiento y cómo pasar a escala planetaria con Cosmos DB, consulte [Partición y escalado en Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
