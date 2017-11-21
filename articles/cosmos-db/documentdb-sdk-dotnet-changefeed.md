---
title: Recursos y SDK para los procesadores de fuente de cambios .NET para Azure DocumentDB | Microsoft Docs
description: "Aprenda todo lo necesario sobre el SDK y la API para los procesadores de fuente de cambios como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios realizados de una versión a otra del SDK para los procesadores de fuente de cambios .NET de DocumentDB."
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: maquaran
ms.openlocfilehash: 8c5a081105d37b8f1c78e90d9ccb8d945099b621
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>SDK para los procesadores de fuente de cambios .NET de DocumentDB: descarga y notas de la versión
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Fuente de cambios de .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

|   |   |
|---|---|
|**Descarga del SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentación de la API**|[Documentación de referencia de la API de biblioteca de procesadores de fuente de cambios](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introducción**|[Primeros pasos con el SDL para los procesadores de fuente de cambios .NET de DocumentDB](change-feed.md)|
|**Plataforma admitida actualmente**| [Microsoft .NET 4.5 Framework](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Agrega compatibilidad con .NET Standard 2.0. El paquete admite ahora los monikers de plataforma `netstandard2.0` y `net451`.
* Compatible con el [SDK para .NET de DocumentDB](documentdb-sdk-dotnet.md), versiones 1.17.0 y superiores.
* Compatible con el [SDK para .NET Core de DocumentDB](documentdb-sdk-dotnet-core.md), versiones 1.5.1 y superiores.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Corrige un problema con el cálculo de la estimación de trabajo restante cuando la fuente de cambios estaba vacía o no había trabajo pendiente.
* Compatible con el [SDK para .NET de DocumentDB](documentdb-sdk-dotnet.md), versiones 1.13.2 y superiores.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Agregue un método para obtener una estimación de trabajo restante que se va a procesar en la fuente de cambio.
* Compatible con el [SDK para .NET de DocumentDB](documentdb-sdk-dotnet.md), versiones 1.13.2 y superiores.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA
* Compatible con el [SDK para .NET de DocumentDB](documentdb-sdk-dotnet.md), versiones 1.14.1 e inferiores.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechazará cualquier solicitud realizada a Cosmos DB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.2.0](#1.2.0) |31 de octubre de 2017 |--- |
| [1.1.1](#1.1.1) |29 de agosto de 2017 |--- |
| [1.1.0](#1.1.0) |13 de agosto de 2017 |--- |
| [1.0.0](#1.0.0) |7 de julio de 2017 |--- |


## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte también
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

