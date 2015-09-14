<properties 
   pageTitle="Recuperación de una base de datos SQL tras un desastre"
	description="Obtenga información acerca de cómo recuperar una base de datos tras un fallo o error en el centro de datos regional con las capacidades de replicación geográfica y restauración geográfica de bases de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="elfisher"
	manager="jeffreyg"
	editor="monicar"/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="07/14/2015"
	ms.author="elfish"/>

# Recuperación de una base de datos SQL de Azure tras una interrupción

Las bases de datos SQL de Azure ofrecen varias capacidades de recuperación tras interrupciones:

- Replicación geográfica activa [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Replicación geográfica estándar [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Restauración geográfica [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

Para obtener información acerca de la preparación para los desastres y sobre cuándo se debe recuperar la base de datos, visite la página [Diseño para la continuidad del negocio](sql-database-business-continuity-design.md).

##Cuándo iniciar la recuperación 

La operación de recuperación repercute en la aplicación. Este proceso requiere cambiar la cadena de conexión SQL y puede provocar la pérdida de datos permanente. Por lo tanto debe realizarse solo cuando la duración de la interrupción pueda durar más que el RTO de su aplicación. Cuando se implementa la aplicación en producción, deberá supervisar con frecuencia el estado de la aplicación. Para ello, use los siguientes puntos de datos para garantizar la recuperación:

1. Error de conectividad permanente de la capa de aplicación en la base de datos.
2. El Portal de Azure muestra una alerta sobre una incidencia en una región con un gran impacto.

## Conmutación por error a la base de datos secundaria de replicación geográfica
> [AZURE.NOTE]Debe configurar la [replicación geográfica estándar](https://msdn.microsoft.com/library/azure/dn758204.aspx) o la [replicación geográfica activa](https://msdn.microsoft.com/library/azure/dn741339.aspx) con una base de datos secundaria que se usará para la conmutación por error. La replicación geográfica estándar solo está disponible para las bases de datos Standard y Premium.

Si se produce una interrupción en la base de datos principal, podrá conmutar por error a una base de datos secundaria para restaurar la disponibilidad. Para ello, deberá forzar la finalización de la relación de copia continua. Para obtener una descripción completa de cómo finalizar las relaciones de copia continua, diríjase [aquí](https://msdn.microsoft.com/library/azure/dn741323.aspx).



###Portal de Azure
1. Inicie sesión en el [Portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la base de datos y selecciónela. 
4. En la parte inferior de la hoja de la base de datos, seleccione el **Mapa de replicación geográfica**.
4. En **Bases de datos secundarias**, haga clic con el botón secundario en la fila con el nombre de la base de datos que desea recuperar y seleccione **Detener**.

Una vez finalizada la relación de copia continua, podrá configurar la base de datos recuperada para el uso siguiendo la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md).
###PowerShell
Use PowerShell para realizar la recuperación de la base de datos mediante programación.

Para finalizar la relación de la base de datos secundaria, use el cmdlet [AzureSqlDatabaseCopy Stop](https://msdn.microsoft.com/library/dn720223).
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
Una vez finalizada la relación de copia continua, podrá configurar la base de datos recuperada para el uso siguiendo la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md).
###API de REST 
Use REST para realizar la recuperación de la base de datos mediante programación.

1. Obtenga la copia continua de la base de datos mediante la operación [Obtener copia de la base de datos](https://msdn.microsoft.com/library/azure/dn509570.aspx).
2. Detenga la copia continua de la base de datos mediante la operación [Detener copia de la base de datos](https://msdn.microsoft.com/library/azure/dn509573.aspx). Use el nombre del servidor secundario y el nombre de la base de datos en el URI de la solicitud de Detener copia de la base de datos.

 Una vez finalizada la relación de copia continua, podrá configurar la base de datos recuperada para el uso siguiendo la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md).
## Recuperación mediante la restauración geográfica

En el caso de una interrupción en una base de datos, es posible recuperar la base de datos desde la última copia de seguridad con redundancia geográfica mediante la restauración geográfica.

> [AZURE.NOTE]Al recuperar una base de datos se crea una nueva base de datos. Es importante asegurarse de que el servidor en el que va a efectuar la recuperación tenga suficiente capacidad DTU para la nueva base de datos. Puede solicitar un aumento de esta cuota [contactando con el soporte técnico](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal de Azure
1. Inicie sesión en el [Portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **NUEVO** y, a continuación, seleccione **Datos y almacenamiento**. Luego seleccione **Base de datos SQL**.
2. Seleccione **COPIA DE SEGURIDAD** como origen y, a continuación, seleccione la copia de seguridad con redundancia geográfica que desea recuperar.
3. Especifique el resto de propiedades de la base de datos y, a continuación, haga clic en **Crear**.
4. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**, en el lado izquierdo de la pantalla.

Una vez recuperada la base de datos, podrá configurarla para el uso. Para ello siga los pasos descritos en la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md).
###PowerShell 
Use PowerShell para realizar la recuperación de la base de datos mediante programación.

Para iniciar una solicitud de restauración geográfica, use el cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx). Para ver un tutorial detallado, vea el [vídeo de procedimientos](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

Una vez recuperada la base de datos, podrá configurarla para el uso. Para ello siga los pasos descritos en la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md).
###API de REST 

Use REST para realizar la recuperación de la base de datos mediante programación.

1.	Obtenga la lista de bases de datos recuperables mediante la operación [Enumerar bases de datos recuperables](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenga la base de datos que desea recuperar mediante la operación [Obtener base de datos recuperable](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Cree la solicitud de recuperación mediante la operación [Crear solicitud de recuperación de base de datos](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Realice un seguimiento del estado de la recuperación mediante la operación [Estado de la operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Una vez recuperada la base de datos, podrá configurarla para el uso. Para ello siga los pasos descritos en la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md).
 

<!---HONumber=September15_HO1-->