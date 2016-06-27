<properties 
   pageTitle="Recuperación de errores de usuario en Base de datos SQL" 
   description="Obtenga información acerca de cómo recuperarse de errores de los usuarios, datos dañados accidentalmente o una base de datos eliminada con la característica Restauración a un momento dado (PITR) de Base de datos SQL de Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/10/2016"
   ms.author="carlrab"/>

# Recuperar una base de datos SQL de Azure de un error de usuario

Base de datos SQL de Azure ofrece dos capacidades básicas para recuperase de los errores de usuario o de la modificación no intencionada de los datos.

- [Restauración a un momento dado](sql-database-point-in-time-restore.md) 
- [Restaurar la base de datos eliminada](sql-database-point-in-time-restore.md#restoring-a-recently-deleted-database)


Base de datos SQL de Azure siempre se restaura en una base de datos nueva. Estas capacidades de restauración se ofrecen para todas las bases de datos de los niveles Basic, Standard y Premium.

##Restauración a un momento dado

En caso de un error de usuario o de modificación no intencionada de los datos, se puede usar Restauración a un momento dado para restaurar la base de datos a cualquier punto dado del período de retención de la base de datos.

Las bases de datos de la versión Basic tienen 7 días de retención, las de la versión Standard disponen de 14 días de retención y las de la versión Premium tienen 35 días de retención. Para obtener más información acerca de la retención de la base de datos, vea [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md).

Para llevar a cabo un punto de restauración a un momento dado, consulte:

- [Point in Time Restore with the Azure Portal](sql-database-point-in-time-restore-portal.md) (Restauración a un momento dado con el Portal de Azure)
- [Point in Time Restore with PowerShell](sql-database-point-in-time-restore-powershell.md) (Restauración a un momento dado con PowerShell)
- [Point in Time Restore with REST API (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restauración a un momento dado con la API de REST (createmode=PointInTimeRestore)) 


## Restauración de una base de datos eliminada

En caso de que se elimine una base de datos, Base de datos SQL de Azure le permite restaurar la base de datos eliminada en el momento en que se eliminó. Base de datos SQL de Azure almacena la copia de seguridad de la base de datos eliminada durante el período de retención de la base de datos.

El período de retención de una base de datos eliminada lo determinan el nivel de servicio de la base de datos mientras esta existe, o bien el número de días en que existe la base de datos, el menor de estos dos valores. Para obtener más información sobre la retención de la base de datos, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

Para restaurar una base de datos eliminada, consulte:

- [Restore a deleted database with the Azure Portal](sql-database-restore-deleted-database-portal.md) (Restauración de una base de datos eliminada con el Portal de Azure)
- [Restore a deleted database with PowerShell](sql-database-restore-deleted-database-powershell.md) (Restauración de una base de datos eliminada con PowerShell)
- [Restore a deleted database with REST API (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restauración de una base de datos eliminada con la API de REST (createmode=Restore))


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->