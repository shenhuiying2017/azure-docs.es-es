<properties 
   pageTitle="Restaurar una base de datos de Azure SQL mediante la restauración a un momento dado con API de REST" 
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
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# Restaurar una base de datos de Azure SQL mediante la restauración a un momento dado con API de REST

> [AZURE.SELECTOR]
- [Restauración a un momento dado: portal](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [Restauración a un momento dado: PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)

## Información general

Esta guía muestra cómo restaurar una base de datos de SQL Azure mediante la restauración a un momento dado con API de REST. Se proporcionan vínculos a las operaciones más detalladas.

La restauración a un momento dado crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

Vea [Restaurar una base de datos de Azure SQL mediante la Restauración a un momento dado en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

## Cómo: Restaurar una base de datos de SQL Azure mediante API de REST.

1.	Obtener la base de datos que desee restaurar usando la operación [Obtener base de datos](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Crear solicitud de restauración de base de datos mediante la operación [Crear solicitud de restauración de base de datos](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Realizar un seguimiento de la solicitud de restauración mediante la operación [Estado de operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Pasos siguientes

Para obtener más información, consulte los temas siguientes: 

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauración a un momento dado de base de datos SQL de Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Referencia de la API de REST de administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
