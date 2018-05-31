---
title: Uso de la biblioteca de BulkExecutor en .NET para realizar operaciones en masa en Azure Cosmos DB | Microsoft Docs
description: Use la biblioteca de BulkExecutor en .NET de Azure Cosmos DB para importar y actualizar documentos en masa en las colecciones de Azure Cosmos DB.
keywords: Ejecutor en masa de .NET
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 608551090ce10e08ba517def644c72186a6f25e1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887249"
---
# <a name="using-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Uso de la biblioteca de BulkExecutor en .NET para realizar operaciones en masa en Azure Cosmos DB

En este tutorial se proporcionan instrucciones sobre cómo usar la biblioteca BulkExecutor en .NET de Azure Cosmos DB para importar y actualizar documentos en colecciones de Azure Cosmos DB. Para información sobre la biblioteca de BulkExecutor y cómo lo ayuda a aprovechar el almacenamiento y el rendimiento masivo, consulte el artículo de [información general sobre la biblioteca de BulkExecutor](bulk-executor-overview.md). Este tutorial lo guiará por una aplicación de .NET de ejemplo cuyas importaciones en bloque generaron documentos aleatoriamente en una colección de Azure Cosmos DB. Después de la importación, muestra cómo se pueden actualizar en masa los datos importados al especificar revisiones como operaciones que se deben realizar en campos de documentos específicos.

## <a name="prerequisites"></a>requisitos previos

* Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita Desarrollo de Azure durante la instalación de Visual Studio.

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar. 

* También puede [probar gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sin una suscripción de Azure, de forma gratuita y sin compromiso. O bien, puede usar el [emulador de Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) con el identificador URI `https://localhost:8081`. La clave principal se proporciona en [Authenticating requests](local-emulator.md#authenticating-requests) (Autenticación de solicitudes).

* Cree una cuenta de API de SQL de Azure Cosmos DB mediante los pasos descritos en la sección de [creación de una cuenta de base de datos](create-sql-api-dotnet.md#create-a-database-account) del artículo de inicio rápido de .NET. 

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a pasar a trabajar con el código mediante la descarga de algunas aplicaciones de .NET de ejemplo de GitHub. Estas aplicaciones realizan operaciones en masa con los datos de Azure Cosmos DB. Para clonar las aplicaciones, abra un símbolo del sistema, vaya al directorio donde quiere copiarlas y ejecute el siguiente comando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

El repositorio clonado contiene dos ejemplos, "BulkImportSample" y "BulkUpdateSample". Puede abrir cualquiera de las aplicaciones de ejemplo, actualizar las cadenas de conexión en un archivo App.config con las cadenas de conexión de la cuenta de Azure Cosmos DB, compilar la solución y ejecutarla. 

La aplicación “BulkImportSample” genera documentos aleatorios y los importa en bloque en Azure Cosmos DB. La aplicación “BulkUpdateSample” realiza actualizaciones en masa de los documentos importados al especificar revisiones como operaciones que se deben realizar en campos de documentos específicos. En las secciones siguientes, se revisará el código en cada una de estas aplicaciones de ejemplo.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importación de datos en masa a Azure Cosmos DB

1. Navegue hasta la carpeta "BulkImportSample" y abra el archivo "BulkImportSample.sln".  

2. Las cadenas de conexión de Azure Cosmos DB se recuperan del archivo App.config como se muestra en el código siguiente:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   El importador en bloque crea una base de datos y una colección con el nombre de la base de datos, el nombre de la colección y los valores de la capacidad de proceso especificados en el archivo App.config. 

3. A continuación, se inicializa el objeto DocumentClient con el modo de conexión TCP directo:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. El objeto BulkExecutor se inicializa con un valor alto de reintentos para el tiempo de espera y las solicitudes limitadas. Además, se establecen en 0 para pasar el control de congestión de BulkExecutor durante su vigencia.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. La aplicación llama a la API BulkImportAsync. La biblioteca de .NET ofrece dos sobrecargas de la API de importación en bloque; una que acepta una lista de documentos JSON serializados y otra que acepta una lista de documentos POCO deserializados. Para obtener información sobre las definiciones de cada uno de estos métodos sobrecargados, consulte la [documentación de la API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **El método BulkImportAsync acepta los siguientes parámetros:**
   
   |**Parámetro**  |**Descripción** |
   |---------|---------|
   |enableUpsert    |   Una marca para habilitar upsert en los documentos. Si ya existe un documento con un identificador determinado, este se actualiza. De forma predeterminada, se establece en false.      |
   |disableAutomaticIdGeneration    |    Una marca para deshabilitar la generación automática de identificador. De forma predeterminada, se establece en true.     |
   |maxConcurrencyPerPartitionKeyRange    | El grado máximo de simultaneidad por intervalo con clave de partición; si se establece en null, la biblioteca usará el valor predeterminado de 20. |
   |maxInMemorySortingBatchSize     |  El número máximo de documentos que se extraen desde el enumerador de documento que se pasa a la llamada API en cada fase.  Para la fase de ordenación de preprocesamiento en memoria para la importación en bloque, establecer el valor null dará lugar a que la biblioteca use el valor predeterminado de min(documents.count, 1000000).       |
   |cancellationToken    |    El token de cancelación para cerrar correctamente la importación en bloque.     |

   **Definición del objeto de respuesta de importación en bloque** El resultado de la llamada API de importación en bloque contiene los siguientes atributos:

   |**Parámetro**  |**Descripción**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  El número total de documentos importados correctamente aparte de los documentos proporcionados a la llamada API de importación en bloque.       |
   |TotalRequestUnitsConsumed (double)   |   Total de unidades de solicitud (RU) consumidas por la llamada API de importación en bloque.      |
   |TotalTimeTaken (TimeSpan)    |   El tiempo total que tarda la llamada API de importación en bloque en completar la ejecución.      |
   |BadInputDocuments (List<object>)   |     La lista de documentos con formato incorrecto no importados correctamente en la llamada API de importación en bloque. El usuario debe corregir los documentos devueltos y reintentar la importación. Los documentos con formato incorrecto incluyen documentos cuyo valor de identificador no es una cadena (null o cualquier otro tipo de datos se consideran no válidos).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Actualización de datos en masa en Azure Cosmos DB

Puede actualizar los documentos existentes con el uso de la API BulkUpdateAsync. En este ejemplo, se establece el nuevo valor en el campo de nombre y se elimina el campo de descripción de los documentos existentes. Para consultar el conjunto completo de operaciones admitidas de actualización de campos, vea la [documentación de la API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Navegue hasta la carpeta "BulkUpdateSample" y abra el archivo "BulkUpdateSample.sln".  

2. Define los elementos de actualización junto con las operaciones de actualización de campos correspondientes. En este ejemplo, usará SetUpdateOperation para actualizar el campo de nombre y UnsetUpdateOperation para eliminar el campo de descripción de todos los documentos. También puede realizar otras operaciones, como incrementar un campo de documento con un valor determinado, insertar valores específicos en un campo de matriz o quitar un valor concreto de un campo de matriz. Para obtener información sobre los distintos métodos proporcionados por la API de actualización en masa, vea la [documentación de la API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. La aplicación llama a la API BulkUpdateAsync. Para obtener información sobre la definición del método BulkUpdateAsync, vea la [documentación de la API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **El método BulkUpdateAsync acepta los siguientes parámetros:**

   |**Parámetro**  |**Descripción** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   El grado máximo de simultaneidad por intervalo con clave de partición; si se establece en null, la biblioteca usará el valor predeterminado de 20.   |
   |maxInMemorySortingBatchSize    |    El número máximo de elementos de actualización extraídos del enumerador de elementos de actualización pasados a la llamada API en cada fase para la fase de ordenación de preprocesamiento en memoria para actualizaciones en masa; la configuración de null dará lugar a que la biblioteca use el valor predeterminado de min(updateItems.count, 1000000).     |
   | cancellationToken|El token de cancelación para cerrar correctamente la actualización en masa. |

   **Definición del objeto de respuesta de actualización en masa** El resultado de la llamada API de actualización en masa contiene los siguientes atributos:

   |**Parámetro**  |**Descripción** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   El número total de documentos actualizados correctamente aparte de los documentos proporcionados a la llamada API de actualización en masa.      |
   |TotalRequestUnitsConsumed (double)   |    Total de unidades de solicitud (RU) consumidas por la llamada API de actualización en masa.    |
   |TotalTimeTaken (TimeSpan)   | El tiempo total que tarda la llamada API de actualización en masa en completar la ejecución. |
    
## <a name="performance-tips"></a>Consejos de rendimiento 

Tenga en cuenta los siguientes puntos para mejorar el rendimiento al utilizar la biblioteca de BulkExecutor:

* Para obtener el mejor rendimiento, ejecute la aplicación desde una máquina virtual de Azure en la misma región que la región de escritura de la cuenta de Cosmos DB.  

* Se recomienda crear instancias de un único objeto BulkExecutor en toda la aplicación dentro de una sola máquina virtual que corresponde a una colección específica de Cosmos DB.  

* Esto se debe a que una única ejecución de API de operaciones en masa consume un gran fragmento de E/S de red y de CPU del equipo cliente. Esto sucede al generar varias tareas internamente y al evitar la creación de varias tareas simultáneas dentro de su proceso de aplicación, donde cada una ejecuta llamadas API de operaciones en masa. Si una única llamada API de operaciones en masa en una única máquina virtual no puede consumir la capacidad de proceso de toda la colección (si la capacidad de proceso de la colección es superior a 1 millón RU/s), es preferible crear máquinas virtuales independientes para ejecutar llamadas API de operaciones en masa simultáneamente.  

* Asegúrese de que se invoque InitializeAsync() después de crear la instancia de un objeto BulkExecutor para capturar la asignación de particiones de la colección de Cosmos DB de destino.  

* En el archivo App.Config de la aplicación, asegúrese de que **gcServer** está habilitado para mejorar el rendimiento.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* La biblioteca emite seguimientos que se pueden recopilar en un archivo de registro o en la consola. Para habilitar ambas opciones, agregue lo siguiente al archivo App.Config de la aplicación.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
```

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre el paquete Nuget y las notas de la versión de la biblioteca de BulkExecutor en .NET, vea la [documentación sobre el SDK de BulkExecutor](sql-api-sdk-bulk-executor-dot-net.md). 
