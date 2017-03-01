---
title: "Azure Portal: Restauración de una base de datos de Azure SQL Database a un momento dado anterior | Microsoft Docs"
description: "Restauración de una base de datos de Azure SQL Database a un momento dado anterior con Azure Portal"
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
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restauración de una base de datos de Azure SQL Database a un momento dado anterior con Azure Portal

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior a partir de las [copias de seguridad automatizadas de SQL Database](sql-database-automated-backups.md). mediante PowerShell. Esta tarea también puede realizarse [mediante PowerShell](sql-database-point-in-time-restore-powershell.md).

## <a name="restore-to-a-previous-point-in-time"></a>Restauración a un momento dado anterior 

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

## <a name="next-steps"></a>Pasos siguientes
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md)
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)


