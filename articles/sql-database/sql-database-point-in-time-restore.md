---
title: "Restauración de una base de datos de Azure SQL Database a un momento dado anterior | Microsoft Docs"
description: "Restauración de una base de datos SQL de Azure a un momento dado anterior"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Restauración de una base de datos SQL de Azure a un momento dado anterior 

En este artículo explicativo se muestra cómo restaurar la base de datos a un momento dado anterior a partir de las [copias de seguridad automatizadas de SQL Database](sql-database-automated-backups.md). 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Restaurar a un momento dado anterior mediante Azure Portal

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery.md).
>

Seleccione una base de datos para restaurar en Azure Portal:

1. Abra el [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **Más servicios** > **Bases de datos SQL**.
3. Haga clic en la base de datos que desee restaurar.
4. En la parte superior de la página de la base de datos, seleccione **Restaurar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. En la página **Restaurar**, seleccione la fecha y hora (en hora UTC) para restaurar la base de datos y luego haga clic en **Aceptar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Después de hacer clic **Aceptar** en el paso anterior, haga clic en el icono de notificación situado en parte superior derecha de la página y haga clic en la notificación **Restaurando la SQL Database** para obtener detalles.
   
    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. Se abre la página de restauración de SQL Database con información sobre el estado de la restauración. Puede hacer clic en el elemento de línea para más detalles:
   
    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>Restauración a un momento dado anterior con PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>Restauración de su base de datos a un momento dado anterior como una base de datos única
1. Obtenga la base de datos que quiere restaurar con el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaure la base de datos a un momento específico con el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>Restauración de su base de datos a un momento dado en un grupo elástico
1. Obtenga la base de datos que quiere restaurar con el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaure la base de datos a un momento específico con el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>Pasos siguientes
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md)
* Para ver el punto de restauración más antiguo de las copias de seguridad de una base de datos generadas por el servicio, consulte [el artículo al respecto](sql-database-view-oldest-restore-point.md).
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


