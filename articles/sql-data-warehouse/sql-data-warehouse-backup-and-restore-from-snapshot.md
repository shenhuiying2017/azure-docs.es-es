<properties
   pageTitle="Recuperación de una base de datos de un error de usuario en Almacenamiento de datos SQL | Microsoft Azure"
   description="Pasos para recuperar una base de datos de un error de usuario en Almacenamiento de datos SQL"
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
   ms.date="04/30/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Recuperación de una base de datos de un error de usuario en Almacenamiento de datos SQL

Almacenamiento de datos SQL ofrece dos funcionalidades básicas de recuperación de errores de usuario que provocan daños en los datos o su eliminación de forma involuntaria:

- Restauración de una base de datos activa
- Restauración de una base de datos eliminada

Ambas capacidades restauran a una base de datos nueva del mismo servidor. Es importante asegurarse de que el servidor en el que va a efectuar la restauración tenga suficiente capacidad DTU para la nueva base de datos. Consulte este blog para obtener más información sobre [cómo ver y aumentar la cuota de DTU][].

## Recuperación de una base de datos activa
El servicio Almacenamiento de datos SQL de Azure protege todas las bases de datos activas con instantáneas de base de datos cada ocho horas como mínimo y las conserva durante siete días para ofrecerle un conjunto discreto de puntos de restauración. Las instantáneas de base de datos también se crean al pausar o quitar la base de datos y se conservan durante siete días. En caso de errores de usuario que provocan la modificación no intencionada de los datos, puede restaurar la base de datos a cualquiera de los puntos de restauración dentro del período de retención.

### Portal de Azure

Para restaurar mediante el Portal de Azure, use estos pasos:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la base de datos y selecciónela.
4. En la parte superior de la hoja de la base de datos, haga clic en **Restaurar**.
5. Especifique un nuevo **Nombre de base de datos**, seleccione un **Punto de restauración** y después haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

### PowerShell

Use Azure PowerShell para realizar una restauración de una base de datos mediante programación con el cmdlet [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Para usar Azure Powershell con Almacenamiento de datos SQL, se necesita instalar Azure PowerShell versión 1.0.3 o superior. Puede comprobar la versión ejecutando **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][]. Para obtener más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
4. Enumere los puntos de restauración de la base de datos.
5. Elija el punto de restauración deseado mediante RestorePointCreationDate.
6. Restaure la base de datos al punto de restauración deseado.
7. Compruebe que la base de datos restaurada está en línea.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Para el servidor foo.database.windows.net, use "foo" como -ServerName en los cmdlets de PowerShell anteriores.

### API de REST
Use REST para realizar la restauración de la base de datos mediante programación.

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalize a recovered database][] (Finalización de una base de datos recuperada).

## Recuperar una base de datos eliminada
Almacenamiento de datos SQL de Azure toma una instantánea de la base de datos antes de eliminarla y la conserva durante siete días. En caso de que se elimine una base de datos por error, puede restaurarla al momento en que se eliminó.

### Portal de Azure

Para restaurar una base de datos eliminada mediante el Portal de Azure, siga estos pasos indicados a continuación:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Servidores SQL**.
3. Vaya al servidor y selecciónelo.
4. Desplácese hacia abajo hasta la opción Operaciones de la hoja del servidor y haga clic en el icono **Bases de datos eliminadas**.
5. Seleccione la base de datos eliminada que desee restaurar.
5. Especifique un nuevo **nombre de base de datos** y haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.


### PowerShell
Para restaurar una base de datos eliminada, use el cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos eliminada que se va a restaurar.
4. Obtenga la base de datos eliminada específica.
5. Restaure la base de datos eliminada.
6. Compruebe que la base de datos restaurada está en línea.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Para el servidor foo.database.windows.net, use "foo" como -ServerName en los cmdlets de PowerShell anteriores.

### API de REST
Use REST para realizar la restauración de la base de datos mediante programación.

1.	Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][].
2.	Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][].
3.	Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
4.	Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalize a recovered database][] (Finalización de una base de datos recuperada).

## Pasos siguientes
Para obtener información sobre las características de continuidad del negocio de las ediciones de Base de datos SQL de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[Cómo instalar y configurar Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Crear solicitud de restauración de base de datos]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado de operación de base de datos]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtener base de datos eliminada que se puede restaurar]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista de bases de datos eliminadas que se pueden restaurar]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0504_2016-->