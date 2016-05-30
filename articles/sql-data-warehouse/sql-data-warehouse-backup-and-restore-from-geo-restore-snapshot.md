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
   ms.date="04/20/2016"
   ms.author="sahajs;barbkess"/>

# Recuperación de una base de datos de una interrupción en Almacenamiento de datos SQL

La restauración geográfica ofrece la capacidad de restaurar una base de datos a partir de una copia de seguridad con redundancia geográfica. La base de datos restaurada se puede crear en cualquier servidor y en cualquier región de Azure. Dado que Georrestauración usa una copia de seguridad con redundancia geográfica como su fuente, se puede usar para recuperar una base de datos aunque esta sea inaccesible debido a una interrupción. Además de la recuperación de una interrupción, la restauración geográfica también se puede usar para otros escenarios como la migración o copia de una base de datos a un servidor o una región diferente.

## Cuándo iniciar la recuperación
La operación de recuperación requiere cambiar la cadena de conexión SQL en la recuperación y puede provocar la pérdida de datos permanente. Por lo tanto, debe realizarse solo cuando la duración de la interrupción pueda durar más que el RTO de su aplicación. Utilice los siguientes puntos de datos para reafirmar que la recuperación está garantizada:

- Error de conectividad permanente en la base de datos.
- El Portal de Azure muestra una alerta sobre una incidencia en una región con un gran impacto.

## Recuperación mediante la restauración geográfica
La recuperación de una base de datos crea una nueva base de datos desde la última copia de seguridad con redundancia geográfica. Es importante asegurarse de que el servidor en el que va a efectuar la restauración tenga suficiente capacidad DTU para la nueva base de datos. Consulte este blog para más información sobre [cómo ver y aumentar la cuota de DTU][].

### Portal de Azure
1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **+NUEVO** y después seleccione **Datos y almacenamiento** y, por último, **Almacenamiento de datos SQL**.
3. Seleccione **COPIA DE SEGURIDAD** como origen y después seleccione la copia de seguridad con redundancia geográfica que desea recuperar.
4. Especifique el resto de propiedades de la base de datos y haga clic en **Crear**.
5. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

### PowerShell

Para recuperar una base de datos, use el cmdlet [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Para usar Azure Powershell con Almacenamiento de datos SQL, se necesita instalar Azure PowerShell versión 1.0.3 o superior. Puede comprobar la versión ejecutando **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][]. Para más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

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
4. **Habilitar auditoría**: si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos.

La base de datos recuperada estará habilitada para TDE si la base de datos de origen está habilitada para TDE.

## Pasos siguientes
Para más información sobre las características de continuidad del negocio de las ediciones de Base de datos SQL de Azure, lea [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Cómo instalar y configurar Azure PowerShell]: ../powershell/powershell-install-configure.md
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Enumerar bases de datos recuperables]: https://msdn.microsoft.com/library/azure/dn800984.aspx
[Obtener base de datos recuperable]: https://msdn.microsoft.com/library/azure/dn800985.aspx
[Crear solicitud de recuperación de base de datos]: https://msdn.microsoft.com/library/azure/dn800986.aspx
[Estado de la operación de base de datos]: https://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->