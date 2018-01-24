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
ms.devlang: 
ms.topic: article
ms.date: 10/30/2017
ms.author: arramac
ms.openlocfilehash: d1968e9fea0fb08edfdbf9e09acca9c4af00b048
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Compatibilidad con la fuente de cambios en Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) es una base de datos replicada globalmente, rápida y flexible, ideal para aplicaciones IoT, juegos, comerciales y de registro operativo. Un modelo de diseño común en estas aplicaciones es usar los cambios en los datos para iniciar acciones adicionales. Estas acciones adicionales podrían ser cualquiera de las siguientes: 

* Desencadenamiento de una notificación o una llamada a una API cuando se inserta o modifica un documento.
* Procesamiento del flujo para IoT o análisis.
* Movimiento de datos adicionales mediante la sincronización con una memoria caché, el motor de búsqueda o el almacén de datos, o archivar los datos en un almacenamiento en frío.

La **compatibilidad con la fuente de cambios**  en Azure Cosmos DB le permite crear soluciones eficientes y escalables para cada uno de estos patrones, como se muestra en la imagen siguiente:

![Uso de la fuente de cambios de Azure Cosmos DB para aumentar la eficacia de los escenarios de informática orientada a eventos y análisis en tiempo real](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> La compatibilidad con la fuente de cambios se proporciona para todos los contenedores y modelos de datos de Azure Cosmos DB. Sin embargo, la fuente de cambios se lee mediante el cliente de SQL y serializa los elementos en formato JSON. Debido al formato JSON, los clientes de MongoDB experimentarán una discrepancia entre los documentos con formato BSON y la fuente de cambios con formato JSON. 

## <a name="how-does-change-feed-work"></a>¿Cómo funciona la fuente de cambios?

La compatibilidad con la fuente de cambios en Azure Cosmos DB se proporciona al observar si hay algún cambio en una colección de Azure Cosmos DB. A continuación, muestra la lista ordenada de los documentos que han cambiado en el orden en el que se modificaron. Los cambios se conservan y se pueden procesar de manera asincrónica. El resultado puede distribuirse entre uno o varios clientes para procesarse en paralelo. 

Puede leer la fuente de cambios de tres maneras diferentes, como se describe más adelante en este artículo:

1.  [Uso de Azure Functions](#azure-functions)
2.  [Uso del SDK de Azure Cosmos DB](#rest-apis)
3.  [Uso de la biblioteca de procesador de fuente de cambios de Azure Cosmos DB](#change-feed-processor)

La fuente de cambios está disponible para cada intervalo de claves de partición dentro de la colección de documentos y, por tanto, se puede distribuir entre uno o varios consumidores para el procesamiento en paralelo, tal y como se muestra en la imagen siguiente.

![Procesamiento distribuido de la fuente de cambios de Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Detalles adicionales:
* La fuente de cambios está habilitada de forma predeterminada para todas las cuentas.
* Puede usar el [procesamiento aprovisionado](request-units.md) en su región de escritura o en cualquier [región de lectura](distribute-data-globally.md) para leer la fuente de cambios, igual que con cualquier otra operación de Azure Cosmos DB.
* La fuente de cambios incluye inserciones y operaciones de actualización realizadas en los documentos dentro de la colección. Puede capturar eliminaciones estableciendo un indicador "eliminación temporal" dentro de los documentos en lugar de eliminaciones. Como alternativa, puede establecer un período finito de caducidad para los documentos mediante la [funcionalidad TTL](time-to-live.md), por ejemplo, 24 horas, y usar el valor de esa propiedad para capturar las eliminaciones. Con esta solución, tiene que procesar los cambios dentro de un intervalo de tiempo más corto que el período de expiración de TTL.
* Cada cambio realizado en un documento aparece exactamente una vez en la fuente de cambios y los clientes administran su lógica de puntos de comprobación. La biblioteca de procesadores de fuentes de cambio proporciona semántica de puntos de comprobación automáticos y "al menos una vez".
* Solo el cambio más reciente en un documento determinado se incluye en el registro de cambios. Puede que los cambios intermedios no estén disponibles.
* La fuente de cambios está clasificada por orden de modificación dentro de cada valor de clave de partición. No hay ningún orden garantizado entre valores de clave de partición.
* Los cambios se pueden sincronizar desde cualquier punto en el tiempo, es decir, no hay ningún período fijo de retención de datos en el que los cambios estén disponibles.
* Los cambios están disponibles en fragmentos de intervalos de claves de partición. Esta funcionalidad permite que los cambios de colecciones grandes se procesen en paralelo por medio de varios consumidores/servidores.
* Las aplicaciones pueden solicitar varias fuentes de cambios a la vez en la misma colección.

## <a name="use-cases-and-scenarios"></a>Casos de uso y escenarios

La fuente de cambios permite procesar con eficacia grandes conjuntos de datos con un elevado volumen de escrituras, y ofrece una alternativa a la consulta de conjuntos de datos enteros para identificar lo que ha cambiado. 

Por ejemplo, con una fuente de cambios, puede realizar las siguientes tareas de manera eficaz:

* Actualizar una caché, un índice de búsqueda o un almacenamiento de datos con los datos almacenados en Azure Cosmos DB.
* Implementar archivado y niveles de datos de aplicación, es decir, almacenar "datos activos" en Azure Cosmos DB y "jubilar datos inactivos" en [Azure Blob Storage](../storage/common/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar análisis por lotes sobre los datos mediante [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Realizar migraciones con cero tiempo de inactividad a otra cuenta de Azure Cosmos DB con otro esquema de partición.
* Implementar [canalizaciones Lambda en Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB proporciona una solución de base de datos escalable que puede hacer frente tanto a la ingesta como a la consulta, e implementar arquitecturas Lambda con un bajo TCO. 
* Recibir y almacenar datos de eventos de dispositivos, sensores, infraestructura y aplicaciones, y luego procesarlos en tiempo real con [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) o [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

La imagen siguiente muestra cómo las canalizaciones lambda que tanto ingieren como consultan Azure Cosmos DB pueden usar la compatibilidad con fuentes de cambios: 

![Canalización Lambda basada en Azure Cosmos DB para ingesta y consulta](./media/change-feed/lambda.png)

Además, en las aplicaciones web y móviles [sin servidor](http://azure.com/serverless), puede realizar el seguimiento de eventos, como cambios en el perfil del cliente, las preferencias o la ubicación para desencadenar determinadas acciones como enviar notificaciones push a sus dispositivos mediante [Azure Functions](#azure-functions). Si usa Azure Cosmos DB para compilar un juego, puede usar la fuente de cambios para, por ejemplo, implementar marcadores en tiempo real basados en las puntuaciones de los juegos completados.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Uso de Azure Functions 

Si usa Azure Functions, la manera más sencilla de conectarse a una fuente de cambios de Azure Cosmos DB es agregar un desencadenador de Azure Cosmos DB a la aplicación Azure Functions. Cuando cree un desencadenador de Azure Cosmos DB en una aplicación de Azure Functions, seleccione la colección de Azure Cosmos DB a la que conectarse, y la función se desencadena siempre que se realiza un cambio en la colección. 

Los desencadenadores pueden crearse en el portal de Azure Functions, en el portal de Azure Cosmos DB o mediante programación. Para más información, vea [Azure Cosmos DB: informática de base de datos sin servidor con Azure Functions](serverless-computing-database.md).

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>Uso del SDK

El [SDK de SQL](sql-api-sdk-dotnet.md) para Azure Cosmos DB le otorga la capacidad de leer y administrar una fuente de cambios. Pero con la eficacia, también vienen una gran cantidad de responsabilidades, demasiadas. Si desea administrar puntos de control, tratar con números de secuencia de documentos y tener un control específico sobre las claves de las particiones, el SDK puede ser la solución correcta.

En esta sección se explica cómo usar el SDK de SQL para trabajar con una fuente de cambios.

1. Empiece por leer los siguientes recursos desde appconfig. Las instrucciones sobre cómo recuperar la clave de autorización y puntos de conexión están disponibles en [Actualización de la cadena de conexión](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Cree el cliente como se indica a continuación:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Obtenga los intervalos de claves de partición:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Llame a ExecuteNextAsync para cada intervalo de claves de partición:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

Si tiene varios lectores, puede usar **ChangeFeedOptions** para distribuir la carga de lectura en subprocesos o clientes distintos.

Y eso es todo, con estas pocas líneas de código puede empezar a leer la fuente de cambios. Puede obtener el código completo que se usa en este artículo desde el [repositorio de GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

En el código del paso 4 anterior, el fragmento **ResponseContinuation** de la última línea tiene el último número de secuencia lógica (LSN) del documento, que se usará la próxima vez que lea documentos nuevos tras este número de secuencia. Mediante el uso del valor **StartTime** de **ChangeFeedOption** puede ampliar la red para obtener los documentos. Por lo tanto, si el valor de **ResponseContinuation** es null, pero **StartTime** vuelve atrás en el tiempo, se le devolverán todos los documentos que cambiaron desde el momento que indica **StartTime**. Pero, si **ResponseContinuation** tiene un valor, el sistema le ayudará a obtener todos los documentos desde ese LSN.

Por lo tanto, la matriz de puntos de control solo mantiene el LSN para cada partición. Pero si no desea ocuparse de las particiones, los puntos de control, LSN, las horas de inicio, etc., la opción más sencilla consiste en utilizar la biblioteca de procesadores de fuente de cambios.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Uso de la biblioteca de procesadores de fuente de cambios 

La [biblioteca de procesadores de fuente de cambios de Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) puede ayudarle a distribuir fácilmente el procesamiento de eventos entre varios consumidores. Esta biblioteca simplifica la lectura de los cambios a través de las particiones y varios subprocesos que trabajan en paralelo.

La ventaja principal de la biblioteca de procesadores de fuente de cambios es que no tiene que administrar cada partición y token de continuación, ni que sondear manualmente cada colección.

La biblioteca de procesadores de fuente de cambios simplifica la lectura de los cambios a través de las particiones y varios subprocesos que trabajan en paralelo.  Administra automáticamente la lectura de los cambios a través de las particiones que usan un mecanismo de concesión. Como puede ver en la imagen siguiente, si inicia dos clientes que usan la biblioteca de procesadores de fuente de cambios, dividen el trabajo entre ellos. A medida que continúe aumentando los clientes, siguen dividiéndose el trabajo entre ellos.

![Procesamiento distribuido de la fuente de cambios de Azure Cosmos DB](./media/change-feed/change-feed-output.png)

El cliente de la izquierda se inició en primer lugar y comenzó la supervisión de todas las particiones; a continuación, se inició el segundo cliente y el primero dejó ir a algunas de las concesiones para el segundo cliente. Como puede ver, esta es la forma "agradable" de distribuir el trabajo entre distintas máquinas y clientes.

Tenga en cuenta que, si tiene dos funciones de Azure sin servidor supervisando la misma colección y usando la misma concesión, las dos funciones pueden obtener documentos diferentes, en función del modo en que la biblioteca de procesadores decida procesar las particiones.

### <a name="understanding-the-change-feed-processor-library"></a>Información de la biblioteca de procesadores de fuente de cambios

Hay cuatro componentes principales en la implementación del procesador de fuente de cambios: la colección supervisada, la colección de concesión, el host de procesador y los consumidores. 

> [!WARNING]
> Crear una colección implica precios, debido a que reserva rendimiento para que la aplicación se comunique con Azure Cosmos DB. Para más detalles, visite la [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

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

### <a name="working-with-the-change-feed-processor-library"></a>Trabajo con la biblioteca de procesadores de fuente de cambios

Antes de instalar el paquete NuGet del procesador de fuente de cambios, instale primero: 

* Microsoft.Azure.DocumentDB, versión 1.13.1 o posterior 
* Newtonsoft.Json, versión 9.0.1 o posterior

A continuación, instale el [paquete Nuget Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e inclúyalo como una referencia.

Para implementar la biblioteca de procesadores de fuente de cambios, tiene que hacer lo siguiente:

1. Implemente un objeto **DocumentFeedObserver**, que implementa **IChangeFeedObserver**.

2. Implemente un objeto **DocumentFeedObserverFactory**, que implementa **IChangeFeedObserverFactory**.

3. En el método **CreateObserver** de **DocumentFeedObserverFacory**, cree una instancia del objeto **ChangeFeedObserver** que creó en el paso 1 y devuélvala.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Cree una instancia de **DocumentObserverFactory**.

5. Cree una instancia de un **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Registre la instancia de **DocumentFeedObserverFactory** con el host.

El código de los pasos del 4 al 6 es: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

¡Ya está! Después de estos pasos, los documentos empezarán a entrar en el método **DocumentFeedObserver ProcessChangesAsync**.

## <a name="next-steps"></a>pasos siguientes

Para más información acerca Azure Cosmos DB con Azure Functions, consulte [Azure Cosmos DB: informática de base de datos sin servidor con Azure Functions](serverless-computing-database.md).

Para más información sobre cómo utilizar la biblioteca de procesadores de fuente de cambios, use los siguientes recursos:

* [Página de información](sql-api-sdk-dotnet-changefeed.md) 
* [Paquete NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Código de ejemplo que muestra los pasos del 1 al 6 anterior](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Ejemplos adicionales en GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Para más información sobre cómo utilizar la fuente de cambios mediante el SDK, use los siguientes recursos:

* [Página de información de SDK](sql-api-sdk-dotnet.md)
