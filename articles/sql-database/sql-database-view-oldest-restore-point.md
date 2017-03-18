---
title: "Visualización del punto de restauración más antiguo de las copias de seguridad generadas por el servicio de una base de datos SQL de Azure | Microsoft Docs"
description: "Referencia rápida sobre cómo ver eñ punto de restauración más antiguo de las copias de seguridad generadas por el servicio de una base de datos"
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
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238
ms.lasthandoff: 12/09/2016


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>Visualización del punto de restauración más antiguo de las copias de seguridad generadas por el servicio de una base de datos SQL de Azure

En este tema, aprenderá a ver el punto de restauración más antiguo de las copias de seguridad generadas por el servicio de una base de datos SQL de Azure.

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>Visualización del punto de restauración más antiguo mediante Azure Portal

1. Abra la hoja **SQL Database** de la base de datos.

    ![hoja nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-blade.png)

2. En la barra de herramientas, haga clic en **Restaurar**.

    ![barra de herramientas restaurar](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. En la hoja Restaurar, revise el punto de restauración más antiguo.

    ![punto de restauración más antiguo](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Pasos siguientes

- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).
