<properties
   pageTitle="Restauración de una instancia de Almacenamiento de datos SQL de Azure (API de REST) | Microsoft Azure"
   description="Tareas de la API de REST para restaurar una instancia de Almacenamiento de datos SQL de Azure."
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
   ms.date="06/10/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restauración de instancias de Almacenamiento de datos SQL de Azure (API de REST)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante la API de REST.

## Antes de empezar

**Compruebe la capacidad DTU**. Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor lógico de SQL Server. Este servidor lógico tiene un límite de capacidad expresado en DTU. Antes de poder restaurar una instancia de Almacenamiento de datos SQL, es importante asegurarse de que el servidor lógico de SQL Server que hospeda la base de datos tiene suficiente capacidad DTU para la base de datos que se está restaurando. Consulte este blog para obtener más información sobre [cómo ver y aumentar la cuota de DTU][].

## Restauración de una base de datos activa o en pausa

Para restaurar una base de datos:

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo las instrucciones de la guía de [finalización de una base de datos recuperada][].

## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada, consulte:

1.	Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][].
2.	Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][].
3.	Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
4.	Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo las instrucciones de la guía de [finalización de una base de datos recuperada][].


## Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Base de datos SQL de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ./sql-database-business-continuity.md
[finalización de una base de datos recuperada]: ./sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
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


<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0615_2016-->