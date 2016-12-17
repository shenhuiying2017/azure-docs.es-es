---
title: "Restauración de Azure SQL Database a un momento dado anterior (Azure Portal) | Microsoft Docs"
description: Restaure Azure SQL Database a un momento dado anterior.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restauración de Azure SQL Database a un momento dado anterior con Azure Portal
> [!div class="op_single_selector"]
> * [Información general](sql-database-recovery-using-backups.md)
> * [Restauración a un momento dado: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior mediante PowerShell a partir de las [copias de seguridad automatizadas de SQL Database](sql-database-automated-backups.md) mediante Azure Portal.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restauración de una Base de datos SQL a un momento dado anterior
Seleccione una base de datos para restaurar en Azure Portal:

1. Abra el [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **Más servicios** > **Bases de datos SQL**.
3. Haga clic en la base de datos que desee restaurar.
4. En la parte superior de la página de la base de datos, seleccione **Restaurar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. En la página **Restaurar**, seleccione la fecha y hora (en hora UTC) para restaurar la base de datos y luego haga clic en **Aceptar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Supervisión de la operación de restauración
1. Después de hacer clic **Aceptar** en el paso anterior, haga clic en el icono de notificación situado en parte superior derecha de la página y haga clic en la notificación **Restaurando la SQL Database** para obtener detalles.
   
    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. Se abre la página de restauración de SQL Database con información sobre el estado de la restauración. Puede hacer clic en el elemento de línea para más detalles:
   
    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md)
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


