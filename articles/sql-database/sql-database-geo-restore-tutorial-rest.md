<properties 
   pageTitle="Recuperar una base de datos SQL de Azure mediante la restauración geográfica con API de REST" 
   description="Restauración geográfica, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, API de REST" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal"/>

# Recuperar una base de datos SQL de Azure mediante la restauración geográfica con API de REST

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)

## Información general

Esta guía muestra cómo recuperar una base de datos SQL de Azure mediante API de REST. Se proporcionan vínculos a las operaciones más detalladas. La restauración geográfica es la principal protección de recuperación ante desastres incluida para todos los niveles de servicio de bases de datos Basic, Standard y Premium de Azure SQL.

## Restricciones y seguridad

Consulte [Recuperar una base de datos SQL de Azure mediante la restauración geográfica en el portal de Azure](sql-database-geo-restore-tutorial-management-portal.md).

## Restaurar una base de datos SQL de Azure mediante API de REST

1.	Obtenga la lista de bases de datos recuperables mediante la operación [Enumerar bases de datos recuperables](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenga la base de datos que desea recuperar mediante la operación [Obtener base de datos recuperable](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Cree la solicitud de recuperación mediante la operación [Crear solicitud de recuperación de base de datos](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Realice un seguimiento del estado de la recuperación mediante la operación [Estado de la operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Restaurar una base de datos SQL de Azure mediante la Restauración a un momento dado en el portal de Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Restaurar una base de datos SQL de Azure eliminada en el portal de Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database Geo-Restore](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/) (Restauración geográfica de base de datos SQL de Azure, blog)

[Referencia de la API de REST de administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=August15_HO6-->