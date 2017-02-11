---
title: "Visualización de las copias de seguridad en el almacén de Azure Recovery Services | Microsoft Docs"
description: "Referencia rápida sobre cómo ver las copias de seguridad en el almacén de Azure Recovery Services y el espacio que usan las copias de seguridad"
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>Visualización de la información sobre las copias de seguridad de base de datos con retención a largo plazo

En este tema de instrucciones, verá información sobre las copias de seguridad de base de datos con retención a largo plazo.

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Visualización de la información sobre la retención de copias de seguridad a largo plazo mediante Azure Portal 

1. Abra la hoja del almacén de Azure Recovery Services (vaya a **Todos los recursos** y selecciónela de la lista de recursos de su suscripción) para ver la cantidad de almacenamiento que usan las copias de seguridad de base de datos en el almacén.

   ![ver almacén de servicios de recuperación con copias de seguridad](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Abra la hoja **SQL Database** de la base de datos.

    ![hoja nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-blade.png)

3. En la barra de herramientas, haga clic en **Restaurar**.

    ![barra de herramientas restaurar](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. En la hoja Restaurar, haga clic en **A largo plazo**.

5. En Azure vault backups (Copias de seguridad de Azure Vault), haga clic en **Seleccionar una copia de seguridad** para ver las copias de seguridad de base de datos disponibles con retención a largo plazo.

    ![copias de seguridad en almacén](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Pasos siguientes

- Para configurar la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services, consulte [](sql-database-configure-long-term-retention.md)
- Para restaurar una base de datos de una copia de seguridad en retención a largo plazo, consulte el artículo sobre cómo [restaurar copias con retención a largo plazo](sql-database-restore-from-long-term-retention.md)
- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


