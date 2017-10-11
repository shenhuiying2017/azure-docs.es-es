---
title: "Restauración de Azure SQL Data Warehouse (Azure Portal) | Microsoft Docs"
description: Tareas de Azure Portal para restaurar Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 09/21/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: f6bc8671410dc7015a8d2a4bea1ba11f9ae526c3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Restauración de Azure SQL Data Warehouse (Portal)
> [!div class="op_single_selector"]
> * [Información general][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
En este artículo, obtendrá información sobre cómo restaurar Azure SQL Data Warehouse mediante Azure Portal.

## <a name="before-you-begin"></a>Antes de empezar
**Compruebe la capacidad DTU**. Cada instancia de SQL Data Warehouse está hospedada en un servidor SQL Server (por ejemplo, myserver.database.windows.net) que tiene una cuota de unidad de rendimiento de datos (DTU) predeterminada. Antes de que pueda restaurar una instancia de SQL Data Warehouse, compruebe que su servidor SQL Server tiene suficientes cuotas de DTU restantes para la base de datos que va a restaurar. Para obtener información sobre cómo calcular la cuota de DTU o para solicitar más DTU, vea cómo [solicitar un cambio de cuota de DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Restauración de una base de datos activa o en pausa
Para restaurar una base de datos:

1. Inicie sesión en [Azure Portal][Azure portal].
2. En el panel izquierdo, seleccione **Examinar** y, después, seleccione **Servidores SQL Server**.

    ![Seleccione Examinar > Servidores SQL Server](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Busque el servidor y, a continuación, selecciónelo.

    ![Seleccione el servidor](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Busque la instancia de SQL Data Warehouse desde la que desea realizar la restauración y selecciónela.

    ![Seleccione la instancia de SQL Data Warehouse que desea restaurar](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En la parte superior de la hoja Data Warehouse, seleccione **Restaurar**.

    ![Seleccione Restaurar](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Especifique un nuevo **nombre de base de datos**.
7. Seleccione el último **punto de restauración**.

   No se olvide de elegir el punto de restauración más reciente. Dado que los puntos de restauración se muestran con la hora universal coordinada (UTC), la opción predeterminada puede no ser el último punto de restauración.

      ![Seleccione un punto de restauración](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Seleccione **Aceptar**.
9. El proceso de restauración de base de datos se iniciará, y puede usar **NOTIFICACIONES** para supervisar el proceso.

> [!NOTE]
> Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Configuración de la base de datos después de realizar la recuperación][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Restauración de una base de datos eliminada
Para restaurar una base de datos eliminada, consulte:

1. Inicie sesión en [Azure Portal][Azure portal].
2. En el panel izquierdo, seleccione **Examinar** y, después, seleccione **Servidores SQL Server**.

    ![Seleccione Examinar > Servidores SQL Server](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Busque el servidor y, a continuación, selecciónelo.

    ![Seleccione el servidor](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Desplácese hacia abajo hasta la sección **Operaciones** de la hoja del servidor.
5. Seleccione el icono **Bases de datos eliminadas**.

    ![Seleccione el icono Bases de datos eliminadas](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Seleccione la base de datos eliminada que desea restaurar.

    ![Selección de la base de datos que se va a restaurar](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Especifique un nuevo **nombre de base de datos**.

    ![Agregue un nombre para la base de datos](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Seleccione **Aceptar**.
9. El proceso de restauración de base de datos se iniciará, y puede usar **NOTIFICACIONES** para supervisar el proceso.

> [!NOTE]
> Para configurar la base de datos después de que haya finalizado la restauración, vea [Configuración de la base de datos después de realizar la recuperación][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Azure SQL Database, vea [Introducción a la continuidad empresarial con Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
