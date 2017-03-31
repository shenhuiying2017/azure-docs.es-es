---
title: "Azure Portal: Administración de la retención de copias de seguridad de bases de datos a largo plazo | Microsoft Docs"
description: "Referencia rápida sobre cómo configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas de bases de datos de Azure SQL Database en un almacén de Azure Recovery Services mediante Azure Portal"
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
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fa4cf56c10f885a2fd4b62e08a369a51d1280ab3
ms.lasthandoff: 03/25/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>Configuración, administración y restauración desde la retención a largo plazo de copias de seguridad de bases de datos en un almacén de Azure Recovery Services mediante Azure Portal

En este tema, descubrirá cómo configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal. También puede llevar a cabo esta tarea con [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

Para más información sobre la retención de copias de seguridad a largo plazo, consulte [Retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md).

> [!TIP]
> Para ver un tutorial, consulte [Get Started with Backup and Restore for Data Protection and Recovery using the Azure portal](sql-database-get-started-backup-recovery-portal.md) (Introducción a la copia de seguridad y restauración para la protección y recuperación de datos mediante Azure Portal).
>

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Configuración de la retención a largo plazo en Azure Portal

1. Abra la hoja **SQL Server** del servidor.

    ![hoja sql server](./media/sql-database-get-started/sql-server-blade.png)

2. Haga clic en **Long-term backup retention** (Retención de copia de seguridad a largo plazo).

   ![vínculo de retención de copia de seguridad a largo plazo](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. En la hoja **Long-term backup retention** (Retención de copia de seguridad a largo plazo), revise y acepte los términos de versión preliminar (a no ser que ya lo haya hecho, o esta característica ya no esté en versión preliminar).

   ![aceptar los términos de versión preliminar](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Para configurar la retención de copia de seguridad a largo plazo para la base de datos, seleccione esa base de datos en la cuadrícula y luego haga clic en **Configurar** en la barra de herramientas.

   ![seleccionar base de datos para retención de copia de seguridad a largo plazo](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. En la hoja **Configurar**, haga clic en **Configurar los valores obligatorios** en **Recovery service vault** (Almacén de servicios de recuperación).

   ![vínculo para configurar almacén](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. En la hoja **Recovery services vault** (Almacén de servicios de recuperación), seleccione un almacén existente, si lo hay. Si no se encuentra ningún almacén de servicios de recuperación para su suscripción, haga clic para salir del flujo y crear uno.

   ![vínculo para crear nuevo almacén](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. En la hoja **Almacenes de Recovery Services**, haga clic en **Agregar**.

   ![vínculo para agregar nuevo almacén](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. En la hoja **Almacén de Recovery Services**, proporcione un nombre válido para el almacén de Recovery Services.

   ![nuevo nombre de almacén](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Seleccione la suscripción y el grupo de recursos y luego seleccione la ubicación del almacén. Cuando termine, haga clic en **Crear**.

   ![crear nuevo almacén](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > El almacén debe estar ubicado en la misma región que el servidor lógico de Azure SQL y debe usar el mismo grupo de recursos que el servidor lógico.
   >

10. Después de crear el nuevo almacén, ejecute los pasos necesarios para volver a la hoja **Almacén de Recovery Services**.

11. En la hoja **Almacén de Recovery Services**, haga clic en el almacén y luego en **Seleccionar**.

   ![seleccionar almacén existente](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. En la hoja **Configurar**, proporcione un nombre válido para la nueva directiva de retención, modifique la directiva de retención predeterminada según sea apropiado y luego haga clic en **Aceptar**.

   ![definir directiva de retención](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. En la hoja **Long-term backup retention** (Retención de copia de seguridad a largo plazo), haga clic en **Guardar** y luego en **Aceptar** para aplicar la directiva de retención de copia de seguridad a largo plazo a todas las bases de datos seleccionadas.

   ![definir directiva de retención](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Haga clic en **Guardar** para habilitar la retención de copia de seguridad a largo plazo mediante esta nueva directiva para el almacén de Azure Recovery Services que ha configurado.

   ![definir directiva de retención](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)


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
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery-portal.md).

## <a name="next-steps"></a>Pasos siguientes

- Para administrar las copias de seguridad de retención de copias de seguridad a largo plazo mediante PowerShell, consulte [Manage long-term backup retention using PowerShell](sql-database-manage-long-term-backup-retention-powershell.md) (Administración de la retención de copias de seguridad a largo plazo mediante PowerShell)
- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).
