---
title: Aprovisionamiento del rendimiento de Azure Cosmos DB | Microsoft Docs
description: Aprenda a configurar el rendimiento aprovisionado para sus contenedores, colecciones, grafos y tablas de Azure Cosmos DB.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Configuración del rendimiento para contenedores de Azure Cosmos DB

Puede configurar el rendimiento de los contenedores de Azure Cosmos DB en Azure Portal o mediante los SDK del cliente. 

En la tabla siguiente se enumeran los rendimientos disponibles para los contenedores:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contenedor de una única partición</strong></p></td>
            <td valign="top"><p><strong>Contenedor con particiones</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento mínimo</p></td>
            <td valign="top"><p>400 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>2 500 unidades de solicitud por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento máximo</p></td>
            <td valign="top"><p>10 000 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>Sin límite</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para establecer el rendimiento mediante Azure Portal

1. Abra [Azure Portal](https://portal.azure.com) en una nueva ventana.
2. En la barra la izquierda, haga clic en **Azure Cosmos DB** o en **Más servicios**, en la parte inferior, desplácese a **Bases de datos** y, luego, haga clic en **Azure Cosmos DB**.
3. Seleccione la cuenta de Cosmos DB.
4. En la nueva ventana, haga clic en **Explorador de datos (versión preliminar)** en el menú de navegación.
5. En la nueva ventana, expanda la base de datos y el contenedor y, a continuación, haga clic en **Escala y configuración**.
6. En la nueva ventana, escriba el nuevo valor de rendimiento en el cuadro **Rendimiento** y luego haga clic en **Guardar**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>Configuración del rendimiento mediante la API de DocumentDB para .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Preguntas más frecuentes sobre el rendimiento

**¿Se puede configurar el rendimiento a menos de 400 RU/s?**

400 RU/s es el rendimiento mínimo disponible en las recopilaciones de una sola partición de Cosmos DB (2500 RU/s es el valor mínimo para las colecciones particionadas). Las unidades de solicitud se establecen en intervalos de 100 RU/s pero el rendimiento no se puede establecer en 100 RU/s o en ningún valor inferior a 400 RU/s. Si está buscando un método rentable para desarrollar y probar Cosmos DB, puede usar gratuitamente el [Emulador de Azure Cosmos DB](local-emulator.md), que puede implementar localmente sin costo alguno. 

**¿Cómo se configura el rendimiento mediante la API de MongoDB?**

No hay ninguna extensión de API de MongoDB para configurar el rendimiento. La recomendación es utilizar la API de DocumentDB, como se muestra en [Configuración del rendimiento mediante la API de DocumentDB para .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre el aprovisionamiento y cómo pasar a escala planetaria con Cosmos DB, consulte [Partición y escalado en Cosmos DB](partition-data.md).
