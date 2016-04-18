<properties
   pageTitle="Recuperación de una base de datos de una interrupción en Almacenamiento de datos SQL | Microsoft Azure"
   description="Pasos para recuperar una base de datos de una interrupción en Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="sahajs;barbkess"/>

# Recuperación de una base de datos de una interrupción en Almacenamiento de datos SQL

La restauración geográfica ofrece la capacidad de restaurar una base de datos a partir de una copia de seguridad con redundancia geográfica para crear una base de datos nueva. La base de datos se puede crear en cualquier servidor y en cualquier región de Azure. Dado que Georrestauración usa una copia de seguridad con redundancia geográfica como su fuente, se puede usar para recuperar una base de datos aunque esta sea inaccesible debido a una interrupción. Además de la recuperación de una interrupción, la restauración geográfica también se puede usar para otros escenarios como la migración o copia de la base de datos a un servidor o región diferente.


## Cuándo iniciar la recuperación
La operación de recuperación requiere cambiar la cadena de conexión SQL en la recuperación y puede provocar la pérdida de datos permanente. Por lo tanto, debe realizarse solo cuando la duración de la interrupción pueda durar más que el RTO de su aplicación. Utilice los siguientes puntos de datos para reafirmar que la recuperación está garantizada:

- Error de conectividad permanente en la base de datos.
- El Portal de Azure muestra una alerta sobre una incidencia en una región con un gran impacto.


## Recuperación mediante la restauración geográfica
La recuperación de una base de datos crea una nueva base de datos desde la última copia de seguridad con redundancia geográfica. Es importante asegurarse de que el servidor en el que va a efectuar la recuperación tenga suficiente capacidad DTU para la nueva base de datos. Puede solicitar un aumento de esta cuota [contactando con el soporte técnico][].


### Portal de Azure
1. Inicie sesión en el [portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **+NUEVO** y después seleccione **Datos y almacenamiento** y, por último, **Almacenamiento de datos SQL**.
3. Seleccione **COPIA DE SEGURIDAD** como origen y después seleccione la copia de seguridad con redundancia geográfica que desea recuperar.
4. Especifique el resto de propiedades de la base de datos y haga clic en **Crear**.
5. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.


### PowerShell
Use Azure PowerShell para realizar la recuperación de la base de datos mediante programación. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Puede comprobar la versión ejecutando Get-Module -ListAvailable -Name Azure. Este artículo se basa en Microsoft Azure PowerShell versión 1.0.4.

Para recuperar una base de datos, utilice el cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
4. Obtenga la base de datos que desea recuperar.
5. Cree la solicitud de recuperación para la base de datos.
6. Compruebe el estado de la base de datos restaurada geográficamente.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

>[AZURE.NOTE] Tenga en cuenta que si el servidor es foo.database.windows.net, use "foo" como -ServerName en los cmdlets de PowerShell anteriores.

### API de REST
Use REST para realizar la recuperación de la base de datos mediante programación.

1. Obtenga la lista de bases de datos recuperables mediante la operación [Enumerar bases de datos recuperables][].
2. Obtenga la base de datos que desea recuperar mediante la operación [Obtener base de datos recuperable][].
3. Cree la solicitud de recuperación mediante la operación [Crear solicitud de recuperación de base de datos][].
4. Realice un seguimiento del estado de la recuperación mediante la operación [Estado de la operación de base de datos][].



## Configuración de la base de datos después de realizar la recuperación
Esta es una lista de comprobación que le ayudará a tener preparada la producción de la base de datos recuperada.

1. **Actualización de cadenas de conexión**: compruebe que las cadenas de conexión de las herramientas del cliente apuntan a la base de datos recién recuperada.
2. **Modificación de las reglas de firewall**: compruebe las reglas de firewall en el servidor de destino y asegúrese de que están habilitadas las conexiones desde los equipos cliente, o bien Azure al servidor y a la base de datos recién recuperada.
3. **Comprobación de los inicios de sesión del servidor y los usuarios de la base de datos**: compruebe si todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Vuelva a crear los inicios de sesión que falten y concédales los permisos adecuados en la base de datos recuperada. 
4. **Habilitación de Auditoría**: si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos.

La base de datos recuperada estará habilitada para TDE si la base de datos de origen está habilitada para TDE.


## Pasos siguientes
Para obtener información sobre las características de continuidad del negocio de otras ediciones de Base de datos SQL Azure, lea la [información general sobre la continuidad del negocio de Base de datos SQL de Azure][].


<!--Image references-->

<!--Article references-->
[información general sobre la continuidad del negocio de Base de datos SQL de Azure]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Enumerar bases de datos recuperables]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[Obtener base de datos recuperable]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[Crear solicitud de recuperación de base de datos]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[Estado de la operación de base de datos]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[portal de Azure]: https://portal.azure.com/
[contactando con el soporte técnico]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0406_2016-->