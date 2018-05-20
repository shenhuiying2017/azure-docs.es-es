---
title: Recursos, API y SDK para los procesadores de fuente de cambios .NET para Azure Cosmos DB | Microsoft Docs
description: Aprenda todo lo necesario sobre el SDK y la API para los procesadores de fuente de cambios como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios realizados de una versión a otra del SDK para los procesadores de fuente de cambios de .NET.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kfile
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/19/2018
ms.author: maquaran
ms.openlocfilehash: 6ae2ae9cdf018652b5ca81efc014c0c6ccb2e813
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK para los procesadores de fuente de cambios de .NET: descarga y notas de la versión
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

|   |   |
|---|---|
|**Descarga del SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentación de la API**|[Documentación de referencia de la API de biblioteca de procesadores de fuente de cambios](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Primeros pasos**|[Primeros pasos con el SDL para los procesadores de fuente de cambios de .NET](change-feed.md)|
|**Plataforma admitida actualmente**| [Microsoft .NET 4.5 Framework](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notas de la versión

### <a name="stable-builds"></a>Compilaciones estables

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correcciones de la estimación de trabajo pendiente.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Mejoras de estabilidad.
* Compatibilidad con puntos de control manuales.
* Compatible con el [SDK de .NET para SQL](sql-api-sdk-dotnet.md), versiones 1.21 y posteriores.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Agrega compatibilidad con .NET Standard 2.0. El paquete admite ahora los monikers de plataforma `netstandard2.0` y `net451`.
* Compatible con el [SDK de .NET para SQL](sql-api-sdk-dotnet.md), versiones 1.17.0 y posteriores.
* Compatible con el [SDK de .NET Core para SQL](sql-api-sdk-dotnet-core.md), versiones 1.5.1 y posteriores.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Corrige un problema con el cálculo de la estimación de trabajo restante cuando la fuente de cambios estaba vacía o no había trabajo pendiente.
* Compatible con el [SDK de .NET para SQL](sql-api-sdk-dotnet.md), versiones 1.13.2 y posteriores.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Agregue un método para obtener una estimación de trabajo restante que se va a procesar en la fuente de cambio.
* Compatible con el [SDK de .NET para SQL](sql-api-sdk-dotnet.md), versiones 1.13.2 y posteriores.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA
* Compatible con el [SDK de .NET para SQL](sql-api-sdk-dotnet.md), versiones 1.14.1 y anteriores.

### <a name="pre-release-builds"></a>Compilaciones de versión preliminar

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Nueva API v2:
  * Patrón de generador para la construcción flexible del procesador: la clase ChangeFeedProcessorBuilder.
    * Puede tomar cualquier combinación de parámetros.
    * Puede tomar la instancia DocumentClient para supervisar o conceder colecciones (no disponible en v1).
  * IChangeFeedObserver.ProcessChangesAsync ahora toma CancellationToken.
  * IRemainingWorkEstimator: el estimador de trabajo restante se puede usar de forma independiente del procesador.
  * Nuevos puntos de extensibilidad:
    * IParitionLoadBalancingStrategy: para el equilibrio de carga personalizado de particiones entre instancias del procesador.
    * ILease, ILeaseManager: para la administración personalizada de concesiones.
    * IPartitionProcessor: para cambios de procesamiento personalizados en una partición.
* Logging: usa la biblioteca [LibLog](https://github.com/damianh/LibLog).
* Compatible 100 % con la API v1.
* Compatible con el [SDK de .NET para SQL](sql-api-sdk-dotnet.md), versiones 1.21.1 y superior.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechazará cualquier solicitud realizada a Cosmos DB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.3.2](#1.3.2) |18 de abril de 2018 |--- |
| [1.3.1](#1.3.1) |13 de marzo de 2018 |--- |
| [1.2.0](#1.2.0) |31 de octubre de 2017 |--- |
| [1.1.1](#1.1.1) |29 de agosto de 2017 |--- |
| [1.1.0](#1.1.0) |13 de agosto de 2017 |--- |
| [1.0.0](#1.0.0) |7 de julio de 2017 |--- |


## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

