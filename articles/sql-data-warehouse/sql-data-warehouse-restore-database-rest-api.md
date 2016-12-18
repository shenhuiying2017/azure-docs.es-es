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
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed4442c5bd7a955e8d380bcb84cd62cd07eca05


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restauración de instancias de Almacenamiento de datos SQL de Azure (API de REST)
> [!div class="op_single_selector"]
> * [Información general][Información general]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante la API de REST.

## <a name="before-you-begin"></a>Antes de empezar
**Compruebe la capacidad DTU**. Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor SQL Server (p. ej., myserver.database.windows.net) que tiene una cuota de DTU predeterminada.  Antes de que pueda restaurar una instancia de Almacenamiento de datos SQL, compruebe que su servidor SQL Server tiene suficientes cuotas de DTU restantes para la base de datos en proceso de restauración. Para obtener más información sobre cómo calcular la unidad DTU necesaria o solicitar más DTU, consulte [Solicitar un cambio en la cuota de DTU][Solicitar un cambio en la cuota de DTU].

## <a name="restore-an-active-or-paused-database"></a>Restauración de una base de datos activa o en pausa
Para restaurar una base de datos:

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][Crear solicitud de restauración de base de datos] (Creación de solicitud de restauración de base de datos).
3. Realice el seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][Estado de operación de base de datos] (Estado de operación de base de datos).

> [!NOTE]
> Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Configuración de la base de datos después de realizar la recuperación][Configuración de la base de datos después de realizar la recuperación].
> 
> 

## <a name="restore-a-deleted-database"></a>Restauración de una base de datos eliminada
Para restaurar una base de datos eliminada, consulte:

1. Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][Lista de bases de datos eliminadas que se pueden restaurar] (Lista de bases de datos eliminadas que se pueden restaurar).
2. Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][Obtener base de datos eliminada que se puede restaurar](Obtención de la base de datos eliminada que se puede restaurar).
3. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][Crear solicitud de restauración de base de datos] (Creación de solicitud de restauración de base de datos).
4. Realice el seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][Estado de operación de base de datos] (Estado de operación de base de datos).

> [!NOTE]
> Para configurar la base de datos una vez finalizada la restauración, consulte [Configuración de la base de datos después de realizar la recuperación][Configuración de la base de datos después de realizar la recuperación].
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Azure SQL Database, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ../sql-database/sql-database-business-continuity.md
[Solicitar un cambio en la cuota de DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configuración de la base de datos después de realizar la recuperación]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Cómo instalar y configurar Azure PowerShell]: ./powershell-install-configure.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Crear solicitud de restauración de base de datos]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado de operación de base de datos]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtener base de datos eliminada que se puede restaurar]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista de bases de datos eliminadas que se pueden restaurar]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


