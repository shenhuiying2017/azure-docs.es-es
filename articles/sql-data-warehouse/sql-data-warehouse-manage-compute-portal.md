---
title: "Administración de la potencia de proceso en Azure SQL Data Warehouse (Azure Portal) | Microsoft Docs"
description: Tareas del Portal de Azure para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 63888d5dd103b585cf18e4787d3e779810163e3d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Administración de la potencia de proceso en Azure SQL Data Warehouse (Azure Portal)
> [!div class="op_single_selector"]
> * [Información general](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>


## <a name="scale-compute-power"></a>Escalado de la potencia de proceso
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar los recursos de proceso:

1. Abra [Azure Portal][Azure portal], abra la base de datos y haga clic en **Escalar**.

    ![Haga clic en Escala][1]
2. En la hoja Escalar, mueva el control deslizante izquierdo o derecho para cambiar el valor de DWU.

    ![Mueva el control deslizante][2]
3. Haga clic en **Save**. Aparece un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Haga clic en Guardar][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausa del proceso
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos:

1. Abra [Azure Portal][Azure portal] y la base de datos. Tenga en cuenta que el estado sea **En línea**.

    ![Estado En línea][6]
2. Para suspender los recursos de proceso y memoria, haga clic en **Pausar**. Aparece un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Confirme la pausa][7]
3. Mientras SQL Data Warehouse está iniciando la base de datos, el estado será **En pausa**.
4. Cuando el estado sea **En pausa**, se realizará la operación de pausa y ya no se le cobrará por DWU.

    ![Estado de pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Reanudación del proceso
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para reanudar una base de datos:

1. Abra [Azure Portal][Azure portal] y la base de datos. Tenga en cuenta que el estado sea **En pausa**.

    ![Base de datos de pausa][4]
2. Para reanudar la base de datos, haga clic en **Iniciar**. Aparece un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Confirme la reanudación][5]
3. Mientras SQL Data Warehouse está iniciando la base de datos, el estado será "Reanudando".
4. Cuando el estado sea **En línea**, la base de datos estará lista.

    ![Estado En línea][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Para más información, consulte [Introducción a la administración][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
