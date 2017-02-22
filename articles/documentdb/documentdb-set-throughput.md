---
title: Aprovisionamiento del rendimiento de Azure DocumentDB | Microsoft Docs
description: "Aprenda cómo establecer el rendimiento aprovisionado para la colección de DocumentDB."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: c65c4021f5d0beda8005abdd375ac51729acf0a8
ms.openlocfilehash: 8ff246ab7d70bd00ce989d3049e016d692722d18


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Establecimiento del rendimiento para las colecciones de Azure DocumentDB

Puede establecer el rendimiento para las colecciones de DocumentDB en Azure Portal o mediante los SDK del cliente. 

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
2. En la barra la izquierda, haga clic en **NoSQL (DocumentDB)** o en **Más servicios**, en la parte inferior, desplácese a **Bases de datos** y seleccione **NoSQL (DocumentDB)**.
3. Seleccione su cuenta de DocumentDB.
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

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre el aprovisionamiento y cómo pasar a escala planetaria con DocumentDB, consulte [Partición y escalado en Azure DocumentDB](documentdb-partition-data.md).


<!--HONumber=Feb17_HO2-->


