<properties
   pageTitle="Restauración de una base de datos en Almacenamiento de datos SQL de Azure (API de REST) | Microsoft Azure"
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
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restauración de una base de datos en Almacenamiento de datos SQL de Azure (API de REST)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-restore-database-overview.md)
- [Portal](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Tareas de API de REST para restaurar una base de datos activa, eliminada o inaccesible en Almacenamiento de datos SQL de Azure.

Tareas de este tema:

- Restauración de una base de datos activa
- Restauración de una base de datos eliminada
- Restauración de una base de datos inaccesible desde otra región geográfica de Azure

## Antes de empezar

**Compruebe su capacidad de DTU de Base de datos SQL.** Como Almacenamiento de datos SQL se restaura a una nueva base de datos en su servidor SQL lógico, es importante asegurarse de que el servidor SQL al que va a restaurar tiene suficiente capacidad de DTU para la nueva base de datos. Consulte este blog para más información sobre [cómo ver y aumentar la cuota de DTU][].

## Restauración de una base de datos activa

Para restaurar una base de datos:

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].

## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada

1.	Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][].
2.	Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][].
3.	Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
4.	Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].

## Restauración dese una región geográfica de Azure

Para realizar una restauración geográfica:

1. Obtenga la lista de bases de datos recuperables mediante la operación [Enumerar bases de datos recuperables].
2. Obtenga la base de datos que desea recuperar mediante la operación [Obtener base de datos recuperable].
3. Cree la solicitud de recuperación mediante la operación [Crear solicitud de recuperación de base de datos].
4. Realice un seguimiento del estado de la recuperación mediante la operación [Estado de la operación de base de datos][].

### Configuración de la base de datos después de realizar una restauración geográfica
Esta es una lista de comprobación que le ayudará a tener preparada la producción de la base de datos recuperada.

1. **Actualización de cadenas de conexión**: compruebe que las cadenas de conexión de las herramientas del cliente apuntan a la base de datos recién recuperada.
2. **Modificación de las reglas de firewall**: compruebe las reglas de firewall en el servidor de destino y asegúrese de que están habilitadas las conexiones desde los equipos cliente, o bien Azure al servidor y a la base de datos recién recuperada.
3. **Comprobación de los inicios de sesión del servidor y los usuarios de la base de datos**: compruebe si todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Vuelva a crear los inicios de sesión que falten y concédales los permisos adecuados en la base de datos recuperada. 
4. **Habilitar auditoría**: si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos.

La base de datos recuperada estará habilitada para TDE si la base de datos de origen está habilitada para TDE.


## Pasos siguientes
Para más información sobre las características de continuidad del negocio de las ediciones de Base de datos SQL de Azure, lea [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: sql-database-business-continuity.md
[Finalización de una base de datos SQL de Azure recuperada]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Crear solicitud de restauración de base de datos]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado de la operación de base de datos]: https://msdn.microsoft.com/library/azure/dn720371.aspx
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