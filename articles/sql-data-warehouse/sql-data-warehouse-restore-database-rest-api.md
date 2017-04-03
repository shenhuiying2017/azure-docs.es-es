---
title: "Restauración de Azure SQL Data Warehouse (API de REST) | Microsoft Docs"
description: Tareas de la API de REST para restaurar una instancia de Almacenamiento de datos SQL de Azure.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: 7bd71144cd2c96fcfb6636ca8d24fc354f86584d
ms.lasthandoff: 12/06/2016


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restauración de instancias de Almacenamiento de datos SQL de Azure (API de REST)
> [!div class="op_single_selector"]
> * [Información general][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante la API de REST.

## <a name="before-you-begin"></a>Antes de empezar
**Compruebe la capacidad DTU**. Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor SQL Server (p. ej., myserver.database.windows.net) que tiene una cuota de DTU predeterminada.  Antes de que pueda restaurar una instancia de Almacenamiento de datos SQL, compruebe que su servidor SQL Server tiene suficientes cuotas de DTU restantes para la base de datos en proceso de restauración. Para obtener más información sobre cómo calcular la unidad DTU necesaria o solicitar más DTU, consulte cómo [solicitar un cambio en la cuota de DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Restauración de una base de datos activa o en pausa
Para restaurar una base de datos:

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][Create database restore request].
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][Database operation status].

> [!NOTE]
> Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Configuración de la base de datos después de realizar la recuperación][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Restauración de una base de datos eliminada
Para restaurar una base de datos eliminada, consulte:

1. Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][List restorable dropped databases].
2. Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][Get restorable dropped database].
3. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][Create database restore request].
4. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][Database operation status].

> [!NOTE]
> Para configurar la base de datos una vez finalizada la restauración, consulte [Configuración de la base de datos después de realizar la recuperación][Configure your database after recovery].
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de SQL Database de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

