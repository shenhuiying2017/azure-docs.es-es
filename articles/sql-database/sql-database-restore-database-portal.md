---
title: "Azure Portal: Restauración de una instancia de Azure SQL Database | Microsoft Docs"
description: "Restauración de una instancia de Azure SQL Database (Azure Portal)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Restauración de una instancia de Azure SQL Database mediante Azure Portal

Los pasos siguientes muestran cómo restaurar una instancia de Azure SQL Database a un momento dado a partir de una base de datos eliminada y a partir de una copia de seguridad con redundancia geográfica.

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Restauración de una instancia de Azure SQL Database a un momento dado anterior 

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery-portal.md).
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


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>Restauración de una instancia de Azure SQL Database eliminada a partir de copias de seguridad
Para restaurar una base de datos eliminada en Azure Portal:

1. En [Azure Portal](https://portal.azure.com), haga clic en **Más servicios** > **Servidores SQL**.
2. Seleccione el servidor que contiene la base de datos que quiere restaurar.
3. Desplácese hacia abajo a la sección **Operaciones** de su hoja de servidor y seleccione **Bases de datos eliminadas**: ![Restauración de una base de datos de Azure SQL](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png).
4. Seleccione la base de datos que quiere restaurar.
5. Especifique un nombre de base de datos y haga clic en **Aceptar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Restauración de una instancia de Azure SQL Database a partir de una copia de seguridad con redundancia geográfica

Para restaurar geográficamente una base de datos en Azure Portal, siga los pasos indicados a continuación:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **+Nuevo** > **Bases de datos** > **SQL Database**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Seleccione **Copia de seguridad** como origen y, después, la copia de seguridad que desee restaurar. Especifique el nombre de la base de datos, el servidor en el que desee restaurar la base de datos y haga clic en **Crear**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Supervise el estado de la operación de restauración haciendo clic en el icono de notificación de la esquina superior derecha de la página.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md)
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md)


