---
title: "Azure Portal: Restauración de una base de datos de SQL Database a partir de una copia de seguridad con redundancia geográfica | Microsoft Docs"
description: "Restauración de una base de datos de Azure SQL Database en un nuevo servidor a partir de una copia de seguridad con redundancia geográfica mediante Azure Portal"
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
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>Restauración de una base de datos Azure SQL Database a partir de una copia de seguridad con redundancia geográfica con Azure Portal

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la restauración geográfica con Azure Portal. Esta tarea también puede realizarse [con PowerShell](sql-database-geo-restore-powershell.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Restauración de una base de datos Azure SQL Database a partir de una copia de seguridad con redundancia geográfica mediante Azure Portal

Para restaurar geográficamente una base de datos en Azure Portal, siga los pasos indicados a continuación:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **+Nuevo** > **Bases de datos** > **SQL Database**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Seleccione **Copia de seguridad** como origen y, después, la copia de seguridad que desee restaurar. Especifique el nombre de la base de datos, el servidor en el que desee restaurar la base de datos y haga clic en **Crear**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Supervise el estado de la operación de restauración haciendo clic en el icono de notificación de la esquina superior derecha de la página.


## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo usar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recuperación de una base de datos a partir de copias de seguridad iniciadas por un servicio](sql-database-recovery-using-backups.md).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md).


