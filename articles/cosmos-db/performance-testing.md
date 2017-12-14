---
title: Pruebas de escala y rendimiento de Azure Cosmos DB | Microsoft Docs
description: "Más información sobre cómo realizar pruebas de escala y de rendimiento con Azure Cosmos DB"
keywords: pruebas de rendimiento
services: cosmos-db
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: f72a75f2750d4838566815bece5c2221359ece29
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Pruebas de escala y rendimiento con Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Las pruebas de escala y rendimiento representan un paso clave en el desarrollo de aplicaciones. Para muchas aplicaciones, el nivel de base de datos tiene un impacto significativo en el rendimiento y la escalabilidad general. Por tanto, es un componente esencial de las pruebas de rendimiento. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) está diseñado específicamente para el escalado elástico y un rendimiento predecible. Estas funcionalidades hacen que resulte una opción ideal para las aplicaciones que necesitan un nivel de base de datos con un alto rendimiento. 

Este artículo es una referencia para los desarrolladores que implementan los conjuntos de pruebas de rendimiento para las cargas de trabajo de Azure Cosmos DB. También se puede utilizar para evaluar Azure Cosmos DB para escenarios de aplicaciones de alto rendimiento. Se centra principalmente en las pruebas de rendimiento aislado de la base de datos, pero también incluye prácticas recomendadas para las aplicaciones de producción.

Después de leer este artículo, podrá responder a las preguntas siguientes: 

* ¿Dónde puedo encontrar una aplicación cliente de .NET de ejemplo para pruebas de rendimiento de Azure Cosmos DB? 
* ¿Cómo se pueden alcanzar niveles de alto rendimiento con Azure Cosmos DB desde mi aplicación cliente?

Para empezar a trabajar con código, descargue el proyecto del [ejemplo de pruebas de rendimiento de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> El objetivo de esta aplicación es demostrar cómo obtener el mejor rendimiento de Azure Cosmos DB con un pequeño número de equipos cliente. El objetivo del ejemplo no es alcanzar la capacidad de rendimiento máxima de Azure Cosmos DB (que se puede escalar sin límites).
> 
> 

Si busca opciones de configuración de cliente para mejorar el rendimiento de Azure Cosmos DB, consulte [Sugerencias de rendimiento para Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Ejecute la aplicación de pruebas de rendimiento
La forma más rápida de empezar es compilar y ejecutar este ejemplo de .NET, tal como se describe en los pasos siguientes. También puede revisar el código fuente e implementar configuraciones similares en sus propias aplicaciones cliente.

**Paso 1:** descargue el proyecto del [ejemplo de pruebas de rendimiento de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) o bifurque el repositorio de GitHub.

**Paso 2:** modifique la configuración de EndpointUrl, AuthorizationKey, CollectionThroughput y DocumentTemplate (opcional) en el archivo App.config.

> [!NOTE]
> Antes de aprovisionar colecciones con un alto rendimiento, consulte la [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/) para estimar los costos por colección. Azure Cosmos DB realiza una facturación independiente por horas por el almacenamiento y el rendimiento. Puede ahorrar costos si elimina o reduce el rendimiento de las colecciones de Azure Cosmos DB después de realizar las pruebas.
> 
> 

**Paso 3:** compile y ejecute la aplicación de consola en la línea de comandos. El resultado debe ser parecido a lo siguiente:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Paso 4 (si es necesario):** el rendimiento notificado (RU/s) de la herramienta debe ser igual o mayor que el rendimiento de aprovisionamiento de la colección. Si no es así, puede alcanzar el límite si aumenta el valor de DegreeOfParallelism en incrementos pequeños. Si el rendimiento de la aplicación cliente se estanca, inicie varias instancias de la aplicación en equipos cliente adicionales. Si necesita ayuda con este paso, escriba un correo electrónico a askcosmosdb@microsoft.com o rellene una incidencia de soporte técnico desde [Azure Portal](https://portal.azure.com).

Una vez que se ejecute la aplicación, puede probar diferentes [directivas de indexación](indexing-policies.md) y [niveles de coherencia](consistency-levels.md) para conocer su repercusión en el rendimiento y la latencia. También puede revisar el código fuente e implementar configuraciones similares a sus propios conjuntos de pruebas o aplicaciones de producción.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, vimos cómo puede realizar pruebas de rendimiento y escala con Azure Cosmos DB mediante una aplicación de consola .NET. Para más información, consulte los siguientes artículos.

* [Ejemplo de pruebas de rendimiento de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opciones de configuración de cliente para mejorar el rendimiento de Azure Cosmos DB](performance-tips.md)
* [Creación de particiones en el servidor en Azure Cosmos DB](partition-data.md)


