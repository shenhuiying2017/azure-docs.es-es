---
title: 'Azure Cosmos DB: API, SDK y recursos de Java asincrónico para SQL | Microsoft Docs'
description: Obtenga toda la información sobre la API y el SDK de Java asincrónico para SQL, incluidas la fechas de lanzamiento, fechas de retirada y cambios realizados entre las versiones del SDK de Java asincrónico para SQL de Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/20/2018
ms.author: sngun
ms.openlocfilehash: 25a84c42430c76d296e12d3f83040fa18febdcb1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK de Java asincrónico para la API de SQL de Azure Cosmos DB: notas de la versión y recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Fuente de cambios de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor: .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor: Java](sql-api-sdk-bulk-executor-java.md)

El SDK de Java asincrónico de API de SQL es distinto al SDK de Java de API de SQL al proporcionar a las operaciones asincrónicas compatibilidad con la [biblioteca de Netty](http://netty.io/). El [SDK de Java de API de SQL](sql-api-sdk-java.md) no admite las operaciones asincrónicas. 

<table>

<tr><td>**Descarga del SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de API](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Contribuya al SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Primeros pasos**</td><td>[Introducción al SDK de Java asincrónico](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Código de ejemplo**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Consejos de rendimiento**</td><td>[Léame de GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Tiempo de ejecución mínimo admitido**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Se agregó compatibilidad con la contrapresión en las consultas.
* Se agregó compatibilidad con el identificador de intervalo de claves de partición en la consulta.
* Corrección para permitir un token de mayor continuación en el encabezado de solicitud (corrección de errores GitHub n.º 24).
* Se actualizó la dependencia de Netty a 4.1.22.Final para garantizar que la JVM se apaga después de que finaliza el subproceso principal.
* Corrección para evitar pasar el token de sesión cuando se leen los recursos maestros.
* Se agregaron más ejemplos.
* Se agregaron más escenarios de pruebas comparativas.
* Se corrigieron los archivos de encabezado de Java para la generación correcta de documentos de Java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* El SDK de disponibilidad general con compatibilidad total para E/S sin bloqueo mediante la [biblioteca de Netty](http://netty.io/) en el modo de puerta de enlace. 

## <a name="release-and-retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se han agregado nuevas características, funcionalidades y optimizaciones al SDK actual. Por lo tanto, se recomienda actualizar siempre a la última versión del SDK lo antes posible.

El servicio rechazará cualquier solicitud realizada a Cosmos DB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.0.1](#1.0.1) |20 de abril de 2018|--- |
| [1.0.0](#1.0.0) |27 de febrero de 2018|--- |

## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

