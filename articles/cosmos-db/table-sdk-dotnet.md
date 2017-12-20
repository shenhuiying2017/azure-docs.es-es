---
title: SDK y recursos de .NET para Table API de Azure CosmosDB | Microsoft Docs
description: "Obtenga toda la información acerca de Table API de Azure Cosmos DB, incluidas las fechas de lanzamiento, las fechas de retirada y los cambios realizados en cada versión."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2017
ms.author: mimig
ms.openlocfilehash: 02bb5d23ee9468ab1f74396877cdcd6bdd8b8fba
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Table API .NET de Azure Cosmos DB: descarga y notas de la versión
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Descarga del SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentación de la API**|[Documentación de referencia de API de .NET](https://aka.ms/acdbtableapiref)|
|**Guía de inicio rápido**|[Azure Cosmos DB: compilación de una aplicación con .NET y Table API](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: desarrollo con Table API en .NET](tutorial-develop-table-dotnet.md)|
|**Plataforma admitida actualmente**|[Microsoft .NET 4.5 Framework](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Si ha creado una cuenta de Table API durante la versión preliminar, cree una [nueva cuenta de Table API](create-table-dotnet.md#create-a-database-account) para trabajar con los SDK de Table API disponibles con carácter general.
>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versión de disponibilidad general

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* Versión preliminar inicial

## <a name="release-and-retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notifica la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

El paquete de versión preliminar [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) está en desuso y se ha sustituido por el paquete [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). El SDK de WindowsAzure.Storage PremiumTable se retirará el 15 de noviembre de 2018, momento en el cual las solicitudes al SDK retirado no se permitirán.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechaza cualquier solicitud realizada a Azure Cosmos DB mediante un SDK retirado.
<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.0.0](#1.0.0) |15 de noviembre de 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 de noviembre de 2017 |--- |

## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

al intentar usar el paquete de NuGet Microsoft.Azure.CosmosDB.Table, tiene dos opciones para solucionar el problema:

* Usar la consola de administración de paquetes para instalar el paquete Microsoft.Azure.CosmosDB.Table y sus dependencias. Para ello, escriba lo siguiente en la consola de administración de paquetes de la solución. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Usar su herramienta preferida de administración de paquetes de Nuget para instalar el paquete de Nuget Microsoft.Azure.Storage.Common antes de instalar Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>P+F

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre Table API de Azure Cosmos DB, consulte [Introducción a Table API de Azure Cosmos DB](table-introduction.md). 
