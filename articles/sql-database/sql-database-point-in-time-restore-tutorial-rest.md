<properties 
   pageTitle="Restaurar una base de datos SQL de Azure mediante la restauración a un momento dado con API de REST" 
   description="Restauración a un momento dado, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, API de REST" 
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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# Restaurar una base de datos SQL de Azure mediante la restauración a un momento dado con API de REST

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## Información general

Esta guía muestra cómo restaurar una base de datos SQL de Azure mediante la restauración a un momento dado con API de REST. Se proporcionan vínculos a las operaciones más detalladas.

La restauración a un momento dado crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [servicio de soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

Consulte [Restaurar una base de datos SQL de Azure mediante la Restauración a un momento dado en el portal de Azure](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Cómo: Restaurar una base de datos SQL de Azure mediante API de REST

1.	Obtenga la base de datos que desee restaurar mediante la operación [Obtener base de datos](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Cree la solicitud de restauración con la operación [Crear solicitud de restauración de base de datos](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Realice el seguimiento de la solicitud de restauración con la operación [Estado de operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database Point in Time Restore](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/) (Restauración a un momento dado de Base de datos SQL de Azure, blog)

[Referencia de la API de REST de administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=July15_HO4-->