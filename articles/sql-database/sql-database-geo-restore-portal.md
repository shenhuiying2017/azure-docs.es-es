---
title: "Restauración de Azure SQL Database a partir de una copia de seguridad automática (Azure Portal) | Microsoft Docs"
description: "Restaure una base de datos de Azure SQL a partir de una copia de seguridad automática (Azure Portal)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Restauración de una base de datos de Azure SQL a partir de una copia de seguridad automática usando el Azure Portal
> [!div class="op_single_selector"]
> * [Información general](sql-database-recovery-using-backups.md#geo-restore)
> * [Restauración geográfica: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

En este artículo se muestra cómo restaurar la base de datos a partir de una [copia de seguridad automática](sql-database-automated-backups.md) en un nuevo servidor mediante la [restauración geográfica](sql-database-recovery-using-backups.md#geo-restore) con el Azure Portal.

## <a name="select-a-database-to-restore"></a>Selección de la base de datos que se va a restaurar
Para restaurar una base de datos en el Azure Portal, siga los pasos indicados a continuación:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **+Nuevo** > **Bases de datos** > **SQL Database**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Seleccione **Copia de seguridad** como origen y, después, la copia de seguridad que desee restaurar. Especifique el nombre de la base de datos, el servidor en el que desee restaurar la base de datos y haga clic en **Crear**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Supervise el estado de la operación de restauración haciendo clic en el icono de notificación de la esquina superior derecha de la página.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md)
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


