---
title: Restauración de una base de datos SQL de Azure a un momento dado anterior (Portal de Azure) | Microsoft Docs
description: Restauración de una base de datos SQL de Azure a un momento dado anterior.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/17/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure
> [!div class="op_single_selector"]
> * [Información general](sql-database-recovery-using-backups.md)
> * [Restauración a un momento: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior mediante PowerShell a partir de las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md) mediante el Portal de Azure.

## Selección de una base de datos para restaurarla a un momento dado anterior
Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1. Abra el [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** > **Bases de datos SQL**.
3. Vaya a la base de datos que quiera restaurar y selecciónela.
4. En la parte superior de la hoja de la base de datos, seleccione **Restaurar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Especifique el nombre de base de datos y un momento dado y haga clic en Aceptar:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->