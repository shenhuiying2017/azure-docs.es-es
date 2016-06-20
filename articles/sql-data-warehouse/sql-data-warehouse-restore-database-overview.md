<properties
   pageTitle="Restauración de una base de datos en Almacenamiento de datos SQL de Azure (información general) | Microsoft Azure"
   description="Información general de las opciones de restauración de bases de datos para recuperar una base de datos en Almacenamiento de datos SQL de Azure."
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


# Restauración de una base de datos en Almacenamiento de datos SQL de Azure (información general)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-restore-database-overview.md)
- [Portal](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Describe las opciones para restaurar una base de datos en Almacenamiento de datos SQL de Azure. Entre estas se incluyen la restauración de un almacenamiento de datos activo y de un almacenamiento de datos eliminado. Los almacenamientos de datos activos y eliminados se restauran de las instantáneas automáticas creadas partir de todos los almacenamientos de datos.

## Escenarios de recuperación

**Recuperación de errores de infraestructura:** este escenario se refiere a la recuperación de problemas de infraestructura tales como, errores de disco, etc. Un cliente quiere garantizar la continuidad del negocio mediante una infraestructura de alta disponibilidad con tolerancia a errores.

**Recuperación de errores de usuario:** este escenario se refiere a la recuperación de datos dañados o eliminados de manera fortuita o involuntaria. Por si un usuario modificara o eliminara datos de manera fortuita o involuntaria, un cliente desea tener la opción de garantizar la continuidad del negocio mediante la restauración de la base de datos a un momento anterior en el tiempo.

## Directivas de instantánea

[AZURE.INCLUDE [Directiva de retención de copia de seguridad de Almacenamiento de datos SQL](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Capacidades de restauración de bases de datos

Veamos cómo Almacenamiento de datos SQL mejora la confiabilidad de la base de datos y permite la recuperación y la operación continua en los escenarios anteriormente mencionados.


### Redundancia de datos

Todos los datos de Almacenamiento de datos SQL se almacenan en Almacenamiento premium de Azure [con redundancia local (LRS)](../storage/storage-redundancy.md), y se mantienen 3 copias de los datos.

### Restauración de base de datos

La restauración de base de datos está diseñada para restablecer la base de datos a un punto anterior en el tiempo. El servicio Almacenamiento de datos SQL de Azure protege todas las bases de datos con instantáneas de almacenamiento automáticas cada 8 horas como mínimo y las conserva durante 7 días para ofrecerle un conjunto discreto de puntos de restauración. Las características de instantánea y restauración automáticas permiten proteger las bases de datos de daños o eliminaciones accidentales sin esfuerzos de administración. Para más información sobre la restauración de bases de datos, consulte [Restore a database in Azure SQL Data Warehouse (Portal)][] (Restauración de una base de datos en Almacenamiento de datos SQL de Azure (Portal)).

## Pasos siguientes
Para otras tareas de administración importantes, consulte [Información general de administración][].

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Información general de administración]: sql-data-warehouse-overview-management.md
[Restore a database in Azure SQL Data Warehouse (Portal)]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->