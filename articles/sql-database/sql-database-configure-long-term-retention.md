---
title: "Configuración de la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services | Microsoft Docs"
description: "Referencia rápida sobre cómo configurar la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services"
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
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configuración de la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services 

En este tema, aprenderá a configurar la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services.

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

14. Cuando haya habilitado la retención de copia de seguridad a largo plazo, abra la hoja **Almacén de Recovery Services** (vaya a **Todos los recursos** y selecciónela en la lista de recursos de su suscripción).

   ![ver el almacén de servicios de recuperación](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Pasos siguientes

- Para recuperar una base de datos de una copia de seguridad en retención a largo plazo, consulte el artículo sobre cómo [recuperar una copia de seguridad en retención a largo plazo](sql-database-restore-from-long-term-retention.md)
- Para ver las copias de seguridad en el almacén de Recovery Services, vea [Visualización de copias de seguridad con retención a largo plazo](sql-database-view-backups-in-vault.md).
- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


