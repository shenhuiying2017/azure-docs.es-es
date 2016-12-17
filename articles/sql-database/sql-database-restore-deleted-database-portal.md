---
title: "Restauración de una base de datos SQL de Azure eliminada (Azure Portal) | Microsoft Docs"
description: Restaure una base de datos SQL de Azure eliminada (Azure Portal).
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5a3f40ff8811c08d130c2a3e0a7d61aed9457d1


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure
> [!div class="op_single_selector"]
> * [Información general](sql-database-recovery-using-backups.md)
> * [**Restaurar bases de datos eliminadas: Portal**](sql-database-restore-deleted-database-portal.md)
> * [Restaurar bases de datos eliminadas: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## <a name="select-the-database-to-restore"></a>Selección de la base de datos que se va a restaurar
Para restaurar una base de datos eliminada en Azure Portal:

1. En [Azure Portal](https://portal.azure.com), haga clic en **Más servicios** > **Servidores SQL**.
2. Seleccione el servidor que contiene la base de datos que quiere restaurar.
3. Desplácese hacia abajo a la sección **Operaciones** de su hoja de servidor y seleccione **Bases de datos eliminadas**: ![Restauración de una base de datos de Azure SQL](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png).
4. Seleccione la base de datos que quiere restaurar.
5. Especifique un nombre de base de datos y haga clic en **Aceptar**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md)
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


