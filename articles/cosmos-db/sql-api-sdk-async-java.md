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
ms.date: 05/18/2018
ms.author: sngun
ms.openlocfilehash: 9dae401bc007b78d8ee3c6993735650e3b26b9d1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359533"
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

<tr><td>**Introducción**</td><td>[Introducción al SDK de Java asincrónico](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Código de ejemplo**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Consejos de rendimiento**</td><td>[Léame de GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Tiempo de ejecución mínimo admitido**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Se agregó compatibilidad con la directiva de índice único.
* Se agregó compatibilidad para limitar la respuesta del tamaño del token de continuación en opciones de fuente.
* Se agregó compatibilidad para división de particiones en la consulta entre particiones.
* Se ha corregido un error en la serialización de marca de tiempo de Json ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Se ha corregido un error en la serialización de enumeración.
* Se ha corregido un error en los documentos de administración de tamaño de 2 MB ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Se ha actualizado la dependencia com.fasterxml.jackson.core:jackson-databind a la versión 2.9.5 debido a un error ([jackson-databind: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Se ha actualizado la dependencia rxjava-extras a la versión 0.8.0.17 debido a un error ([rxjava-extras: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Se ha actualizado la descripción de metadatos en el archivo pom para que esté alineado con el resto de la documentación.
* Se ha mejorado la sintaxis ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github 40 #](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

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
| [1.0.2](#1.0.2) |18 de mayo de 2018|--- |
| [1.0.1](#1.0.1) |20 de abril de 2018|--- |
| [1.0.0](#1.0.0) |27 de febrero de 2018|--- |

## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

