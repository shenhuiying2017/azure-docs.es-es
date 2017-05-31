---
title: Aprovisionamiento del rendimiento de Azure Cosmos DB | Microsoft Docs
description: "Aprenda cómo establecer el rendimiento aprovisionado para la colección de Azure Cosmos DB."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5258ba0bc37442c983d91c5dd7435fd5fbefd56f
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="set-throughput-for-azure-cosmos-db-collections"></a>Establecimiento del rendimiento para las colecciones de Azure Cosmos DB

Puede establecer el rendimiento para las colecciones de Azure Cosmos DB en Azure Portal o mediante los SDK del cliente. 

En la tabla siguiente se enumeran los rendimientos disponibles para las colecciones:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Colección de partición única</strong></p></td>
            <td valign="top"><p><strong>Colección con particiones</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento mínimo</p></td>
            <td valign="top"><p>400 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>2 500 unidades de solicitud por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento máximo</p></td>
            <td valign="top"><p>10 000 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> Para establecer las colecciones con particiones en un valor de rendimiento comprendido entre 2500 RU/s y 10 000 RU/s, debe usar temporalmente Azure Portal. Esta función no está aún disponible en los SDK.

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para establecer el rendimiento mediante Azure Portal

1. Abra [Azure Portal](https://portal.azure.com) en una nueva ventana.
2. En la barra la izquierda, haga clic en **Azure Cosmos DB** o en **Más servicios**, en la parte inferior, desplácese a **Bases de datos** y, luego, haga clic en **Azure Cosmos DB**.
3. Seleccione la cuenta de Cosmos DB.
4. En la ventana nueva, en **colecciones**, haga clic en **Escala**, como se muestra en la captura de pantalla siguiente.
5. En la nueva ventana, seleccione la colección en la lista desplegable, cambie el valor de **Rendimiento** y haga clic en **Guardar**.

    ![Captura de pantalla que muestra cómo cambiar el rendimiento para una colección en Azure Portal yendo a su cuenta y haciendo clic en Escala](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>Para establecer el rendimiento mediante el SDK de .NET

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

400 RU/s es el rendimiento mínimo disponible en las recopilaciones de una sola partición de Cosmos DB (2500 RU/s es el valor mínimo para las colecciones particionadas). Las unidades de solicitud se establecen en intervalos de 100 RU/s pero el rendimiento no se puede establecer en 100 RU/s o en ningún valor inferior a 400 RU/s. Si está buscando un método rentable para desarrollar y probar Cosmos DB, puede usar gratuitamente el [Emulador de Azure Cosmos DB](documentdb-nosql-local-emulator.md), que puede implementar localmente sin costo alguno. 

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre el aprovisionamiento y cómo pasar a escala planetaria con Cosmos DB, consulte [Partición y escalado en Cosmos DB](documentdb-partition-data.md).

