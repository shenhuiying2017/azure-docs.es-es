---
title: Restauración de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (Portal de Azure) | Microsoft Docs
description: Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (Portal de Azure)
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
# Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Información general](sql-database-recovery-using-backups.md)
> * [Restauración geográfica: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la restauración geográfica con el Portal de Azure.

## Selección de la base de datos que se va a restaurar
Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1. Abra el [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **Nuevo** > **Datos y almacenamiento** > **Base de datos SQL**.
3. Seleccione **Copia de seguridad** como origen y, después, la copia de seguridad con redundancia geográfica que desea recuperar.
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/geo-restore.png)
4. Especifique el nombre de la base de datos, el servidor en el que desee restaurar la base de datos y haga clic en Crear:

## Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recover an Azure SQL database using automated database backups](sql-database-recovery-using-backups.md) (Recuperación de una base de datos SQL de Azure mediante copias de seguridad de base de datos automatizadas).
* Para conocer las opciones de recuperación más rápidas, consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md).
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->