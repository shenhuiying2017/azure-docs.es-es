---
title: "Restauración una base de datos desde una copia de seguridad en el almacén de Azure Recovery Services | Microsoft Docs"
description: "Referencia rápida sobre cómo restaurar una base de datos de una copia de seguridad en el almacén de Azure Recovery Services y el espacio que usan las copias de seguridad"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restauración de una base de datos de una copia de seguridad con retención a largo plazo

En este tema de instrucciones, aprenderá a restaurar una base de datos de una copia de seguridad con retención a largo plazo.

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Restauración de la retención de copias de seguridad a largo plazo mediante Azure Portal

1. En la hoja **Azure vault backups** (Copias de seguridad de Azure Vault), haga clic en la copia de seguridad que quiere restaurar y luego haga clic en **Seleccionar**.

    ![seleccionar copia de seguridad en almacén](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. En el cuadro de texto **Nombre de la base de datos**, proporcione el nombre de la base de datos restaurada.

    ![nombre de nueva base de datos](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Haga clic en **Aceptar** para restaurar la base de datos de la copia de seguridad del almacén en la nueva base de datos.

4. En la barra de herramientas, haga clic en el icono de notificación para ver el estado del trabajo de restauración.

    ![progreso de trabajo de restauración del almacén](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Cuando finalice el trabajo de restauración, abra la hoja **Bases de datos SQL** para ver la base de datos recién restaurada.

    ![base de datos restaurada desde almacén](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Pasos siguientes

- Para configurar la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services, consulte [Configuración de la retención de copia de seguridad a largo plazo](sql-database-configure-long-term-retention.md).
- Para ver las copias de seguridad en el almacén de Recovery Services, vea [Visualización de copias de seguridad con retención a largo plazo](sql-database-view-backups-in-vault.md).
- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


