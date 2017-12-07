---
title: "Importación de datos de Cassandra en Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo usar el comando Copy de CQL para copiar datos de Cassandra en Azure Cosmos DB."
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 21168d0862cfdaaaced60fa80a2dc04859f49550
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: importación de datos de Cassandra

Este tutorial proporciona instrucciones sobre cómo importar datos de Cassandra en Azure Cosmos DB mediante el comando COPY de lenguaje de consulta de Cassandra (CQL). 

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Recuperación de la cadena de conexión
> * Importación de datos mediante el comando COPY de cqlsh
> * Importación mediante el conector Spark 

# <a name="prerequisites"></a>Requisitos previos

* Instale [Apache Cassandra](http://cassandra.apache.org/download/) y asegúrese de forma específica de que *cqlsh* está presente.
* Aumente el rendimiento: la duración de la migración de datos depende de la cantidad de rendimiento aprovisionado para las tablas. Asegúrese de aumentar el rendimiento para migraciones de datos más grandes. Después de haber completado la migración, reduzca el rendimiento para ahorrar costos. Para más información sobre cómo aumentar el rendimiento en [Azure Portal](https://portal.azure.com), consulte [Configuración del rendimiento para contenedores de Azure Cosmos DB](set-throughput.md).
* Habilite SSL: Azure Cosmos DB tiene estrictos estándares y requisitos de seguridad. No olvide habilitar SSL al interactuar con la cuenta. Al usar CQL con SSH, tiene una opción para proporcionar información de SSL. 

## <a name="find-your-connection-string"></a>Buscar la cadena de conexión

1. En [Azure Portal](https://portal.azure.com), en el extremo izquierdo, haga clic en **Azure Cosmos DB**.

2. En el panel **Suscripciones**, seleccione el nombre de cuenta.

3. Haga clic en **Cadena de conexión**. El panel derecho contiene toda la información que necesita para conectarse correctamente a la cuenta.

    ![Página Cadena de conexión](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Usar COPY de cqlsh

Para importar datos de Cassandra en Azure Cosmos DB para su uso con la API Cassandra, utilice la siguiente guía:

1. Inicie sesión en cqhsh mediante la información sobre la conexión del portal.
2. Use el [comando COPY de CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) para copiar datos locales en el punto de conexión de API Apache Cassandra. Asegúrese de que el origen y el destino están en el mismo centro de datos para minimizar los problemas de latencia.

### <a name="guide-for-moving-data-with-cqlsh"></a>Guía para mover datos con cqlsh

1. Cree previamente la tabla y escálela:
    * De forma predeterminada, Azure Cosmos DB aprovisiona una nueva tabla de API Cassandra con 1000 unidades de solicitud por segundo (RU/s) (la creación basada en CQL se aprovisiona con 400 RU/s). Antes de iniciar la migración mediante cqlsh, cree previamente todas las tablas a partir de [Azure Portal](https://portal.azure.com) o cqlsh. 

    * En [Azure Portal](https://portal.azure.com), aumente el rendimiento de las tablas del rendimiento predeterminado (400 o 1000 RU/s) a 10 000 RU/s mientras dura la migración. Gracias al mayor rendimiento, puede evitar la limitación y realizar la migración en menos tiempo. Con la facturación por horas en Azure Cosmos DB, puede reducir el rendimiento inmediatamente después de la migración para ahorrar costes.

2. Determine el cargo de la unidad de solicitud de una operación. Puede hacerlo mediante el SDK de la API Cassandra de Azure Cosmos DB que elija. En este ejemplo se muestra la versión de .NET de la obtención de cargos de la unidad de solicitud. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Determine la latencia del equipo en el servicio de Azure Cosmos DB. Si está en un centro de datos de Azure, la latencia debe ser un número en milisegundos bajo de un solo digito. Si está fuera del centro de datos de Azure, puede usar psping o azurespeed.com para obtener la latencia aproximada desde su ubicación.   

4. Calcule los valores adecuados de aquellos parámetros (NUMPROCESS, INGESTRATE, MAXBATCHSIZE o MINBATCHSIZE) que proporcionen un buen rendimiento. 

5. Ejecute el comando de migración final. La ejecución de este comando supone que ha iniciado cqlsh mediante la información sobre la cadena de conexión.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Usar Spark para importar datos

Para los datos que residen en un clúster existente en Azure Virtual Machines, la importación de datos mediante Spark también es una opción posible. Esto requiere que Spark se configure como intermediario para una ingesta única o regular. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información sobre cómo completar las siguientes tareas:

> [!div class="checklist"]
> * Recuperación de la cadena de conexión
> * Importación de datos mediante el comando copy de cql
> * Importación mediante el conector Spark 

Ahora puede continuar con la sección de conceptos para obtener más información sobre Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Niveles de coherencia de datos optimizables en Azure Cosmos DB](../cosmos-db/consistency-levels.md)
