<properties 
   pageTitle="Restaurar una base de datos SQL de Azure eliminada con la API de REST" 
   description="Base de datos SQL de Microsoft Azure, restaurar base de datos eliminada, recuperar base de datos eliminada, API de REST" 
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

# Restaurar una base de datos SQL de Azure eliminada con la API de REST

> [AZURE.SELECTOR]
- [Restaurar la base de datos eliminada: portal](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [Restaurar base de datos eliminada: PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-powershell/)

## Información general

Esta guía muestra cómo restaurar una base de datos SQL de Azure eliminada con API de REST. Se proporcionan vínculos a las operaciones más detalladas.

Al restaurar una base de datos de SQL Azure eliminada, se crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

Vea [Restaurar una base de datos de SQL Azure eliminada en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/).

## Cómo: Restaurar una base de datos SQL de Azure eliminada mediante API de REST

1.	Obtenga una lista de todas las bases de datos eliminadas mediante la operación [Lista de bases de datos eliminadas que se pueden restaurar](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Obtenga los detalles de la base de datos eliminada que desea restaurar mediante la operación [Obtener base de datos eliminada que se puede restaurar](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Inicie la restauración usando la operación [Crear solicitud de restauración de base de datos](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Realice un seguimiento del estado de la restauración mediante la operación [Estado de operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Referencia de la API de REST de administración de servicios](http://msdn.microsoft.com/library/azure/ee460799.aspx)
<!--HONumber=47-->
