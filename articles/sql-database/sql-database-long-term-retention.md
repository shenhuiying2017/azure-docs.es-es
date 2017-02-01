---
title: "Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo | Microsoft Docs"
description: "Obtenga información acerca de cómo Azure SQL Database admite almacenar copias de seguridad durante diez años como máximo."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 8b13faf1f6cdac355cc4d22b825cc2362a50e8f9


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo
Muchas aplicaciones tienen finalidades normativas, de cumplimiento u otras de carácter empresarial que requieren que se conserven las copias de seguridad de las bases de datos completas automáticas más allá de los entre 7 y 35 días proporcionados por las [copias de seguridad automáticas](sql-database-automated-backups.md) de SQL Database.

La función **Long-Term Backup Retention** (Retención a largo plazo de copias de seguridad) le permite almacenar las copias de seguridad de Azure SQL Database en un almacén de Azure Recovery Services hasta 10 años. Puede almacenar hasta 1000 bases de datos por almacén. Puede seleccionar cualquier copia de seguridad del almacén para restaurarlo como una base de datos nueva.

> [!NOTE]
> Puede habilitar hasta 200 bases de datos por almacén durante 24 horas. Por lo tanto, se recomienda que use un almacén independiente para cada servidor para minimizar el impacto de este límite. 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>¿Cómo funciona la retención a largo plazo de SQL Database?

La retención a largo plazo de copias de seguridad permite asociar un servidor de Azure SQL Database a un almacén de Azure Recovery Services. 

* El almacén debe crearse en la misma suscripción de Azure que creó el servidor SQL Server y en la misma región geográfica y el grupo de recursos. 
* A continuación, configure una directiva de retención para cualquier base de datos. Esta directiva tiene como consecuencia que se copien las copias de seguridad completas semanales de la base de datos en el almacén de Recovery Services y que se conserven durante el periodo especificado de retención (hasta 10 años). 
* A continuación, puede restaurar desde cualquiera de estas copias de seguridad a una base de datos nueva en cualquier servidor de la suscripción. La copia se realiza mediante Azure Storage desde las copias de seguridad existentes y no afecta al rendimiento de la base de datos existente.

## <a name="how-do-i-enable-long-term-retention"></a>¿Cómo se habilita la retención a largo plazo?

Para configurar la retención a largo plazo de copias de seguridad de una base de datos, es preciso:

1. crear un almacén de Azure Recovery Services en la misma región, suscripción y grupo de recursos que el servidor de SQL Database, 
2. registrar el servidor en el almacén.
3. crear una directiva de protección de Azure Recovery Services,
4. aplicar la directiva de protección a las bases de datos que requieren la retención a largo plazo de copias de seguridad.

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>¿Cómo se restaura una base de datos almacenada con la función de retención a largo plazo?

Para recuperar desde una copia de seguridad de retención a largo plazo, es preciso:

1. incluir en una lista el almacén donde se almacena la copia de seguridad,
2. incluir en una lista el contenedor que se asigna a su servidor lógico,
3. incluir en una lista el origen de datos en el almacén que se asigna a la base de datos,
4. incluir en una lista de los puntos de restauración disponibles para restaurar,
5. restaurar desde el punto de recuperación en el servidor objetivo en su suscripción.

## <a name="how-much-does-long-term-retention-cost"></a>¿Cuánto cuesta la retención a largo plazo?

La retención a largo plazo de una base de datos Azure SQL Database se cobra según las [ tarifas de precios de servicios de copias de seguridad de Azure](https://azure.microsoft.com/pricing/details/backup/).

Una vez que el servidor de Azure SQL Database esté registrado en el almacén, se le cobra por el almacenamiento total utilizado por las copias de seguridad semanales guardadas en el almacén.

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>Configuración de la retención a largo plazo en Azure Portal

En la hoja del servidor de Azure SQL Database, puede configurar la retención a largo plazo y, si es necesario, crear un almacén de Azure Recovery Services.

- Para configurar la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services, consulte [Configuración de la retención de copia de seguridad a largo plazo](sql-database-configure-long-term-retention.md).
- Para recuperar una base de datos de una copia de seguridad en retención a largo plazo, consulte el artículo sobre cómo [recuperar una copia de seguridad en retención a largo plazo](sql-database-restore-from-long-term-retention.md)
- Para ver las copias de seguridad en el almacén de Recovery Services, vea [Visualización de copias de seguridad con retención a largo plazo](sql-database-view-backups-in-vault.md).

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="configuring-long-term-retention-using-powershell"></a>Configuración de la retención a largo plazo con PowerShell

Use los pasos siguientes para configurar la retención a largo plazo mediante PowerShell.
1. Cree un almacén de Recovery Services
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. Registre el servidor de Azure SQL Database en el almacén para que las bases de datos del servidor puedan tener copias de seguridad almacenadas a largo plazo.
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. Crear una directiva de retención para almacenar las copias de seguridad.
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. Habilitar la retención a largo plazo para la base de datos SQL Database que desea que las copias de seguridad guardadas en el almacén.
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. Hacer una lista del servidor asociado al almacén. Cada servidor está asociado a un contenedor específico en el almacén. Puede hacer una lista de los servidores registrados ejecutando los siguientes comandos.
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. Hacer una lista de las bases de datos que cuentan con una directiva de retención en el contenedor. Cada base de datos tiene un elemento de la copia de seguridad asociada en el contenedor correspondiente. El nombre del elemento de la copia de seguridad se deriva del nombre de base de datos.
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>Restauración de una copia de seguridad de retención a largo plazo

Siga estos pasos para restaurar una base de datos desde una copia de seguridad en el almacén de Azure Recovery Service:

1. Buscar el contenedor de Recovery Service asociado a SQL Server.
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. Buscar el elemento de la copia de seguridad asociado a una base de datos.
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. Buscar la copia de seguridad desde el que desea realizar la restauración.
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. Restaurar desde el punto de recuperación en una base de datos nueva Azure SQL Database.
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>Inhabilitación de la retención a largo plazo

Recovery Service controla automáticamente la limpieza de las copias de seguridad según la directiva de retención proporcionada. 

* Para detener el envío de copias de seguridad de una base de datos específica al almacén, elimine la directiva de retención de esa base de datos.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Las copias de seguridad que ya están en el almacén no resultan afectadas. Recovery Service los elimina automáticamente cuando termina el período de retención.
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>Eliminación de copias de seguridad desde el almacén de Azure Recovery Services

Para eliminar las copias de seguridad manualmente del almacén.

1. Identificar el contenedor en el almacén para "myserver"
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. Identificar el elemento de la copia de seguridad que desea eliminar.
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. Eliminar los elementos de la copia de seguridad (todas las copias de seguridad de la base de datos "mydb")
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. Eliminar el contenedor asociado a "myserver"
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>Preguntas frecuentes relativas a la retención a largo plazo:

1. P: ¿puedo eliminar manualmente las copias de seguridad específicas en el almacén?
   R.: no en este momento, el almacén limpiará automáticamente las copias de seguridad cuando haya terminado el periodo de retención.
2. P: ¿puedo registrar mi servidor para guardar las copias de seguridad en más de un almacén?
   R.: no, actualmente solo puede guardar copias de seguridad en un almacén a la vez.
3. P: ¿puedo tener un almacén y un servidor en suscripciones distintas?
   R: no, actualmente el almacén y el servidor deben estar en la misma suscripción y el grupo de recursos.
4. P: ¿puedo usar un almacén que he creado en una región distinta a la de mi servidor?
   R: no, el almacén y el servidor deben estar en la misma región para minimizar el tiempo de copia y evitar los cargos de tráfico.
5. P.: ¿cuántas bases de datos puedo guardar en un almacén?
   R: actualmente solo se admiten hasta 1000 bases de datos por almacén. 
6. P: ¿Cuántos almacenes puedo crear por suscripción? A: se pueden crear 25 almacenes como máximo por suscripción.
7. P: ¿cuántas bases de datos puedo configurar diariamente por almacén? R: solo puede configurar 200 bases de datos diariamente por almacén.
8. P.: ¿la retención a largo plazo funciona con grupos elásticos?
   R: Sí. Cualquier base de datos del grupo puede configurarse con la directiva de retención.
9. P: ¿puedo elegir el momento en que se crea la copia de seguridad?
   R.: no, la base de datos SQL Database controla la programación de copias de seguridad con el fin de minimizar el impacto del rendimiento en las bases de datos.
10. P: he habilitado TDE en mi base de datos. ¿Puedo usar TDE con el almacén? R: sí, es compatible con TDE. Puede restaurar la base de datos desde el almacén incluso si la base de datos original ya no existe.
11. P: ¿Qué ocurre con las copias de seguridad en el almacén si se suspende mi suscripción? R.: si la suscripción se suspende, se conservan las bases de datos y las copias de seguridad existentes, pero las copias de seguridad nuevas no se copian en el almacén. Después haber reactivado la suscripción, el servicio retoma el copiado de copias de seguridad en el almacén. El almacén estará accesible para las operaciones de restauración mediante el uso de copias de seguridad que se copiaron allí antes de que se suspendiera la suscripción. 
12. P.: ¿puedo obtener acceso a los archivos de copias de seguridad de la base de datos SQL Database para descargar o restaurar a SQL Server?
   R.: actualmente, no.
13. P.: ¿es posible tener varias programaciones (diaria, semanal, mensual, anual) dentro de una directiva de retención de SQL.
   R: no, en este momento esto solo está disponible para las copias de seguridad de máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes
Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para descubrir otras soluciones de continuidad empresarial de Azure SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).




<!--HONumber=Dec16_HO2-->


