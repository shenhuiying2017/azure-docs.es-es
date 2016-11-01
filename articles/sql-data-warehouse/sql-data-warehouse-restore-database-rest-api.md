<properties
   pageTitle="Restauración de una instancia de Almacenamiento de datos SQL de Azure (API de REST) | Microsoft Azure"
   description="Tareas de la API de REST para restaurar una instancia de Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/31/2016"
   ms.author="lakshmir;barbkess"/>


# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restauración de instancias de Almacenamiento de datos SQL de Azure (API de REST)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante la API de REST.

## <a name="before-you-begin"></a>Antes de empezar

**Compruebe la capacidad DTU**. Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor SQL Server (p. ej., myserver.database.windows.net) que tiene una cuota de DTU predeterminada.  Antes de que pueda restaurar una instancia de Almacenamiento de datos SQL, compruebe que su servidor SQL Server tiene suficientes cuotas de DTU restantes para la base de datos en proceso de restauración. Para más información sobre cómo calcular la unidad DTU necesaria o solicitar más DTU, consulte cómo [solicitar un cambio en la cuota de DTU][].

## <a name="restore-an-active-or-paused-database"></a>Restauración de una base de datos activa o en pausa

Para restaurar una base de datos:

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][] .
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][] .

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Configuración de la base de datos después de realizar la recuperación][].

## <a name="restore-a-deleted-database"></a>Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada, consulte:

1.  Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][] .
2.  Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][] .
3.  Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][] .
4.  Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][] .

>[AZURE.NOTE] Para configurar la base de datos una vez finalizada la restauración, consulte [Configuración de la base de datos después de realizar la recuperación][]. 


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Base de datos SQL de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ./sql-database-business-continuity.md
[Solicitar un cambio en la cuota de DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configuración de la base de datos después de realizar la recuperación]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
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



<!--HONumber=Oct16_HO2-->


