---
title: SDK y API para .NET Core de DocumentDB | Microsoft Docs
description: "Aprenda todo lo necesario sobre el SDK y la API para .NET Core como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios realizados de una versión a otra del SDK para .NET Core de DocumentDB."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/22/2017
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: e37416268802b849f6be350a8d7e0e6b284e830b
ms.openlocfilehash: a2972c7c7fead727e6147212b211d102faf01a29
ms.lasthandoff: 02/22/2017


---
# <a name="documentdb-apis-and-sdks"></a>SDK y API de DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-net-core-api-and-sdk"></a>SDK y API para .NET Core de DocumentDB
<table>

<tr><td>**Descarga del SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de API de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Ejemplos**</td><td>[Ejemplos de código de .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Primeros pasos**</td><td>[Introducción al SDK de .NET Core de DocumentDB](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Tutorial de la aplicación web**</td><td>[Desarrollo de aplicaciones web con DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Plataforma admitida actualmente**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

El SDK de .NET Core para DocumentDB .NET Core SDK incluye paridad de características con la última versión del [SDK de .NET para DocumentDB](documentdb-sdk-dotnet.md).

> [!NOTE] 
> El SDK de .NET Core para DocumentDB aún no es compatible con aplicaciones de la Plataforma universal de Windows (UWP). Si quiere que el SDK de .NET Core que admita aplicaciones de UWP, envíe un correo electrónico a [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.0)

* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG). Consulte [Compatibilidad con agregación](documentdb-sql-query.md#Aggregates).
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

El SDK de .NET Core para DocumentDB le permite crear aplicaciones [ASP.NET Core](https://www.asp.net/core) y [.NET Core](https://www.microsoft.com/net/core#windows) rápidas y para varias plataformas: Windows, Mac y Linux. La versión más reciente del SDK de .NET Core para DocumentDB es totalmente compatible con [Xamarin](https://www.xamarin.com) y se usará para compilar aplicaciones que tienen como destino iOS, Android y Mono (Linux).  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

El SDK de .NET Core Preview para DocumentDB le permite crear aplicaciones [ASP.NET Core](https://www.asp.net/core) y [.NET Core](https://www.microsoft.com/net/core#windows) rápidas y para varias plataformas: Windows, Mac y Linux.

Incluye paridad de características con la última versión del [SDK de .NET para DocumentDB](documentdb-sdk-dotnet.md) y admite lo siguiente:
* Todos los [modos de conexión](documentdb-performance-tips.md#networking): modo de puerta de enlace, TCP directo y HTTPS directo. 
* Todos los [niveles de coherencia](documentdb-consistency-levels.md): fuerte, sesión, obsolescencia limitada y ocasional.
* [Colecciones con particiones](documentdb-partition-data.md). 
* [Cuentas de base de datos de varias regiones y replicación geográfica](documentdb-distribute-data-globally.md).

Si tiene preguntas relacionadas con este SDK, publíquelas en los foros de [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) o presente un problema en el [repositorio de GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.1.0](#1.1.0) |16 de febrero de 2017 |--- |
| [1.0.0](#1.0.0) |21 de diciembre de 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 de noviembre de 2016 |31 de diciembre de 2016 |

## <a name="see-also"></a>Otras referencias
Para más información sobre DocumentDB, vea la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 


