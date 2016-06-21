<properties
   pageTitle="Restauración de bases de datos en Almacenamiento de datos SQL de Azure (Portal de Azure) | Microsoft Azure"
   description="Tareas de API de REST para restaurar una base de datos activa, eliminada o inaccesible en Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="elfish;barbkess;sonyama;kevin"/>

# Copia de seguridad y restauración de una base de datos en Almacenamiento de datos SQL de Azure (API de REST)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-manage-database-restore.md)
- [Portal](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Tareas de PowerShell para restaurar una base de datos en Almacenamiento de datos SQL.

Tareas de este tema:

- Restauración de una base de datos activa
- Restauración de una base de datos eliminada

## Antes de empezar

**Compruebe su capacidad de DTU de Base de datos SQL.** Como Almacenamiento de datos SQL se restaura a una nueva base de datos en su servidor SQL lógico, es importante asegurarse de que el servidor SQL al que va a restaurar tiene suficiente capacidad de DTU para la nueva base de datos. Consulte este blog para más información sobre [cómo ver y aumentar la cuota de DTU][].

## Restauración de una base de datos activa

Para restaurar una base de datos:

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Cuando finalice la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].

## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada

1.	Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][].
2.	Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][].
3.	Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
4.	Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Cuando finalice la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].


## Pasos siguientes
Para más información sobre las características de continuidad del negocio de las ediciones de Base de datos SQL de Azure, lea [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: sql-database-business-continuity.md
[Finalización de una base de datos SQL de Azure recuperada]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Crear solicitud de restauración de base de datos]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado de operación de base de datos]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtener base de datos eliminada que se puede restaurar]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista de bases de datos eliminadas que se pueden restaurar]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->