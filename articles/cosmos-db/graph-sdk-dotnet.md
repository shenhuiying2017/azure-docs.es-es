---
title: SDK y recursos de .NET para la API Graph de Azure CosmosDB | Microsoft Docs
description: "Infórmese acerca de la API Graph de Azure CosmosDB, incluidas las fechas de lanzamiento, las fechas de retirada y los cambios realizados en cada versión."
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 28f926d8d1f4c6006a348ba9c8289b885aef7641
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>API Graph .NET de Azure Cosmos DB: descarga y notas de la versión

|   |   |
|---|---|
|**Descarga del SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Documentación de la API**|[Documentación de referencia de API de .NET](https://aka.ms/acdbgraphapiref)|
|**Guía de inicio rápido**|[Azure Cosmos DB: Creación de una aplicación de gráficos con .NET y la API Graph](create-graph-dotnet.md)|
|**Tutorial**|[Azure CosmosDB: creación de un contenedor con la API Graph](tutorial-develop-graph-dotnet.md)|
|**Plataforma admitida actualmente**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Notas de la versión

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>Corrección de errores
* Corrección para cargar `appsettings.json` (`netstandard1.6`) opcionalmente

#### <a name="whats-new"></a>Novedades
* Cambio de Microsoft.Azure.Graphs a la plataforma de destino AnyCPU.
* Eliminación del ensamblado Mono del paquete de manifiesto `net461`.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>Novedades
* Se ha agregado compatibilidad con `.netstandard 1.6`
  * Requiere `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Se ha agregado un nuevo analizador `gremlin-groovy` para reemplazar el existente. Este analizador admite un subconjunto de sintaxis `gremlin-groovy` de Tinkerpop e incluye:
  * Mejora del rendimiento de análisis, que lo duplica.
  * Se han resuelto varios problemas relacionados con el escape de caracteres en cadenas, la administración incorrecta de valores literales y otras irregularidades del analizador anterior.
* Se han agregado optimizaciones para los cruces seguros con predicados de perímetro.
  *  En los saltos de cruce seguro con filtros se debería experimentar esta mejora, por ejemplo: `g.V('1').outE().has('name', 'marko').inV()`.
* Se han agregado optimizaciones para cruces seguros con el paso `limit()`.

#### <a name="breaking-changes"></a>Cambios importantes
* Se ha eliminado la compatibilidad con .NET Framework 4.5.1

* El nuevo analizador se alinea con la gramática `gremlin-groovy`. Como resultado, algunas expresiones que funcionaban anteriormente resultan ambiguas en el nuevo analizador. Nota:
  * `in` y `as` son palabras clave reservadas en `gremlin-groovy`, así que estos pasos se deben calificar con `.in()` o `.as()` para evitar errores de sintaxis. Por ejemplo: `g.V().repeat(in()).times(2)` -> _produce un error de sintaxis_  
 `g.V().repeat(__.in()).times(2)` -> _se realizó correctamente_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* Versión preliminar inicial.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechazará cualquier solicitud realizada a Azure Cosmos DB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |17 de octubre de 2017 |--- |
| [0.3.0-preview](#0.3.0-preview) |2 de octubre de 2017 |--- |
| [0.2.4-preview](#0.2.4-preview) |4 de agosto de 2017 |--- |
| [0.2.2-preview](#0.2.2-preview) |23 de junio de 2017 |--- |
| [0.2.1-preview](#0.2.2-preview) |8 de junio de 2017 |--- |
| [0.2.0-preview](#0.2.2-preview) |10 de mayo de 2017 |--- |
| [0.1.0-preview](#0.1.0-preview) |8 de mayo de 2017 |--- |

## <a name="see-also"></a>Otras referencias
Para más información acerca de la API Graph de Azure Cosmos DB, consulte [Introducción a Azure Cosmos DB: API Graph](graph-introduction.md). 
