---
title: API, SDK y recursos de .NET Core para SQL de Azure Cosmos DB | Microsoft Docs
description: "Aprenda todo lo necesario sobre el SDK y la API de .NET Core para SQL como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios realizados de una versión a otra del SDK para .NET Core de Azure Cosmos DB."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/17/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8e3e0e8868c05188d9d6cb26fe6c2bd2891c17d
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>SDK de .NET Core para la API de SQL de Azure Cosmos DB: notas de la versión y recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Fuente de cambios de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**Descarga del SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de API de .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Muestras**</td><td>[Ejemplos de código de .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Introducción**</td><td>[Introducción al SDK para .NET Core de Azure Cosmos DB](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Tutorial de la aplicación web**</td><td>[Desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Plataforma admitida actualmente**</td><td>[.NET Standard 1.6 y .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

El SDK de .NET Core para Azure Cosmos DB incluye paridad de características con la última versión del [SDK de .NET para Azure Cosmos DB](sql-api-sdk-dotnet.md).

> [!NOTE] 
> El SDK de .NET Core para Azure Cosmos DB aún no es compatible con aplicaciones de la Plataforma universal de Windows (UWP). Si quiere que el SDK de .NET Core que admita aplicaciones de UWP, envíe un correo electrónico a [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Agrega la capacidad de especificar índices únicos para los documentos mediante el uso de la propiedad UniqueKeyPolicy en DocumentCollection.
 * Se ha corregido un error en el cual la configuración personalizada de JsonSerializer no se estaba respetando para algunas consultas y para la ejecución de procedimientos almacenados.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Cambio de personalización de marca de Azure DocumentDB a Azure Cosmos DB en la documentación de referencia de API, la información de metadatos de los ensamblados y el paquete NuGet. 
 * Exposición de la información de diagnóstico y la latencia de la respuesta de las solicitudes enviadas con el modo de conexión directa. Los nombres de propiedad son RequestDiagnosticsString y RequestLatency en la clase ResourceResponse.
 * Esta versión del SDK requiere la versión más reciente del emulador de Azure Cosmos DB disponible para su descarga desde https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Se han agregado varias mejoras y correcciones que aumentan la confiabilidad.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Cambios internos relacionados con la compatibilidad con [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Se agregó compatibilidad para PartitionKeyRangeId como un FeedOption para definir el ámbito de los resultados de la consulta en un valor de intervalo de claves de partición específico. 
* Se agregó compatibilidad para StartTime como un ChangeFeedOption para empezar la búsqueda de los cambios después de ese momento. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Se corrigió un problema en la clase JsonSerializable que podría provocar una excepción de desbordamiento de pila.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Se agregó compatibilidad con la especificación de JsonSerializerSettings personalizado al crear una instancia de [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Compatibilidad con .NET Standard 1.5 como una de las siguientes plataformas de destino.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Se corrigió un problema que afectaba a las máquinas x64 que no admiten instrucciones SSE4 y que producía una excepción SEHException al ejecutar consultas de Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Se agregó compatibilidad con un nuevo nivel de coherencia denominado ConsistentPrefix.
*   Se agregó compatibilidad con métricas de consulta para particiones individuales.
*   Se agregó compatibilidad para limitar el tamaño del token de continuación para las consultas.
*   Se agregó compatibilidad para un seguimiento más detallado de las solicitudes con error.
*   Se hicieron algunas mejoras de rendimiento en el SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Se ha corregido un problema que omitía el valor PartitionKey proporcionado en FeedOptions para consultas de funciones agregadas.
* Se ha corregido un problema en el control transparente de administración de particiones durante la ejecución entre particiones de la consulta Order By.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Se ha corregido un problema que provocaba interbloqueos en algunas de las API asincrónicas cuando se empleaban en el contexto de ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correcciones para aumentar la resistencia de los SDK frente a la conmutación automática por error en determinadas condiciones.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Corrección de un problema que, en ocasiones, provoca una excepción WebException: No se puede resolver el nombre remoto.
* Se ha agregado compatibilidad para leer un documento escrito directamente mediante la adición de nuevas sobrecargas a la API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Se agregó compatibilidad con consultas de agregación para LINQ (COUNT, MIN, MAX, SUM y AVG).
* Se corrigió un problema de pérdida de memoria para el objeto ConnectionPolicy causado por el uso del controlador de eventos.
* Se corrigió un problema por el que UpsertAttachmentAsync no funcionaba cuando se usaba el valor ETag.
* Se corrigió un problema por el que la continuación de la consulta order-by en la partición cruzada no funcionaba cuando se ordenaba por un campo de cadena.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG). Consulte [Compatibilidad con agregación](sql-api-sql-query.md#Aggregates).
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

El SDK de .NET Core para Azure Cosmos DB le permite crear aplicaciones [ASP.NET Core](https://www.asp.net/core) y [.NET Core](https://www.microsoft.com/net/core#windows) rápidas y multiplataforma: Windows, Mac y Linux. La versión más reciente del SDK de .NET Core para Azure Cosmos DB es totalmente compatible con [Xamarin](https://www.xamarin.com) y se usará para compilar aplicaciones que tienen como destino iOS, Android y Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

El SDK de .NET Core para Azure Cosmos DB en versión preliminar le permite crear aplicaciones [ASP.NET Core](https://www.asp.net/core) y [.NET Core](https://www.microsoft.com/net/core#windows) rápidas y multiplataforma: Windows, Mac y Linux.

Incluye paridad de características con la última versión del [SDK de .NET para Azure Cosmos DB](sql-api-sdk-dotnet.md) y admite lo siguiente:
* Todos los [modos de conexión](performance-tips.md#networking): modo de puerta de enlace, TCP directo y HTTPS directo. 
* Todos los [niveles de coherencia](consistency-levels.md): fuerte, sesión, obsolescencia limitada y ocasional.
* [Colecciones con particiones](partition-data.md). 
* [Cuentas de base de datos de varias regiones y replicación geográfica](distribute-data-globally.md).

Si tiene preguntas relacionadas con este SDK, publíquelas en los foros de [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) o presente un problema en el [repositorio de GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.7.1](#1.7.1) |16 de noviembre de 2017 |--- |
| [1.7.0](#1.7.0) |10 de noviembre de 2017 |--- |
| [1.6.0](#1.6.0) |17 de octubre de 2017 |--- |
| [1.5.1](#1.5.1) |02 de octubre de 2017 |--- |
| [1.5.0](#1.5.0) |10 de agosto de 2017 |--- | 
| [1.4.1](#1.4.1) |7 de agosto de 2017 |--- |
| [1.4.0](#1.4.0) |2 de agosto de 2017 |--- |
| [1.3.2](#1.3.2) |12 de junio de 2017 |--- |
| [1.3.1](#1.3.1) |23 de mayo de 2017 |--- |
| [1.3.0](#1.3.0) |10 de mayo de 2017 |--- |
| [1.2.2](#1.2.2) |19 de abril de 2017 |--- |
| [1.2.1](#1.2.1) |29 de marzo de 2017 |--- |
| [1.2.0](#1.2.0) |25 de marzo de 2017 |--- |
| [1.1.2](#1.1.2) |20 de marzo de 2017 |--- |
| [1.1.1](#1.1.1) |14 de marzo de 2017 |--- |
| [1.1.0](#1.1.0) |16 de febrero de 2017 |--- |
| [1.0.0](#1.0.0) |21 de diciembre de 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 de noviembre de 2016 |31 de diciembre de 2016 |

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

