<properties
   pageTitle="Implementación y administración de copias de seguridad para las máquinas virtuales implementadas por Resource Manager mediante PowerShell | Microsoft Azure"
   description="Use PowerShell para implementar y administrar copias de seguridad de Azure para máquinas virtuales implementadas según el modelo de Resource Manager."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="cfreeman"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/03/2016"
   ms.author="markgal; trinadhk"/>

# Implementación y administración de copias de seguridad para las máquinas virtuales implementadas según el modelo de Resource Manager mediante PowerShell

> [AZURE.SELECTOR]
- [Resource Manager](backup-azure-vms-automation.md)
- [Clásico](backup-azure-vms-classic-automation.md)

En este artículo se muestra cómo usar cmdlets de Azure PowerShell para realizar la copia de seguridad y recuperación de una máquina virtual (VM) de Azure desde un almacén de Servicios de recuperación. Un almacén de Servicios de recuperación es un recurso de Azure Resource Manager y se utiliza para proteger datos y recursos en los servicios Copia de seguridad de Azure y Azure Site Recovery. Puede usar un almacén de Servicios de recuperación para proteger máquinas virtuales implementadas según el modelo de Azure Service Manager (ASM), así como máquinas virtuales implementadas según el modelo de Azure Resource Manager.

>[AZURE.NOTE] Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). La información de este artículo es para su uso con las máquinas virtuales creadas con el modelo de Resource Manager.

Este artículo le guiará en el uso de PowerShell para proteger una máquina virtual y restaurar datos a partir de un punto de recuperación.

## Conceptos

Si no está familiarizado con el servicio de Copia de seguridad de Azure, puede obtener información general al respecto en [¿Qué es la Copia de seguridad de Azure?](backup-introduction-to-azure-backup.md) Antes de comenzar, asegúrese de que se tratan los aspectos fundamentales sobre los requisitos previos necesarios para trabajar con Copia de seguridad de Azure y las limitaciones de la solución actual de copia de seguridad de máquina virtual.

Para usar PowerShell de forma eficaz, es preciso conocer la jerarquía de objetos y desde dónde empezar.

![Jerarquía de objetos de Servicios de recuperación](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para ver la referencia del cmdlet de PowerShell AzureRmRecoveryServicesBackup, consulte [Azure Backup - Recovery Services Cmdlets](https://msdn.microsoft.com/library/mt723320.aspx) (Copia de seguridad de Azure: cmdlets de Servicios de recuperación) en la biblioteca de Azure. Para ver la referencia del cmdlet de PowerShell AzureRmRecoveryServicesVault, consulte [Azure Recovery Service Cmdlets](https://msdn.microsoft.com/library/mt643905.aspx) (Cmdlets del Servicio de recuperación de Azure).


## Instalación y registro

Para empezar:

1. [Descargue la versión más reciente de PowerShell](https://github.com/Azure/azure-powershell/releases) (la versión mínima necesaria es 1.4.0).

2. Para buscar los cmdlets de PowerShell de Copia de seguridad de Azure disponibles, escriba el siguiente comando:

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


Las siguientes tareas se pueden automatizar con PowerShell:

- Creación de un almacén de Servicios de recuperación
- Copia de seguridad o protección de máquinas virtuales de Azure
- Desencadenamiento de un trabajo de copia de seguridad
- Supervisión de un trabajo de copia de seguridad
- Restauración de máquinas virtuales de Azure

## Creación de un almacén de Servicios de recuperación

Los siguientes pasos le guiarán por el proceso de creación de un almacén de Servicios de recuperación. Un almacén de Servicios de recuperación no es lo mismo que un almacén de copia de seguridad.

1. Si utiliza Copia de seguridad de Azure por primera vez, debe utilizar el cmdlet **[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)** para registrar el proveedor de Servicios de recuperación de Azure con su suscripción.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. El almacén de Servicios de recuperación es un recurso de Resource Manager, por lo que deberá colocarlo dentro de un grupo de recursos. Puede usar un grupo de recursos existente o crear uno nuevo con el cmdlet **[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)**. Al crear un nuevo grupo de recursos, especifique su nombre y su ubicación.

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Utilice el cmdlet **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)** para crear el nuevo almacén. Asegúrese de especificar para el almacén la misma ubicación del grupo de recursos.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especifique el tipo de redundancia de almacenamiento que se usará: [almacenamiento con redundancia local (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) o [almacenamiento con redundancia geográfica (GRS)](../storage/storage-redundancy.md#geo-redundant-storage). En el ejemplo siguiente se muestra que la opción -BackupStorageRedundancy para testVault está establecida en GeoRedundant.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

    > [AZURE.TIP] Muchos de los cmdlets de Copia de seguridad de Azure requieren el objeto de almacén de Servicios de recuperación como entrada. Por este motivo, es conveniente almacenar el objeto de almacén de Servicios de recuperación de copia de seguridad en una variable.

## Visualización de los almacenes de una suscripción
Utilice el cmdlet **[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)** para ver una lista de todos los almacenes de la suscripción actual. Puede utilizar este comando para comprobar que se haya creado un nuevo almacén o a fin de ver qué almacenes están disponibles en la suscripción.

Ejecute el comando Get-AzureRmRecoveryServicesVault, y se mostrarán todos los almacenes de la suscripción.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## Copias de seguridad de máquinas virtuales de Azure
Ahora que ha creado un almacén de Servicios de recuperación, podrá usarlo para proteger una máquina virtual. Sin embargo, antes de aplicar la protección, debe establecer el contexto del almacén y comprobar la directiva de protección. El contexto del almacén define el tipo de los datos protegidos en el almacén. La directiva de protección consiste en la programación que indica cuándo se debe ejecutar el trabajo de copia de seguridad y cuánto tiempo se conserva cada instantánea de copia de seguridad.

Antes de habilitar la protección en una máquina virtual, debe establecer el contexto de almacén. El contexto se aplica a todos los cmdlets sucesivos.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Creación de una directiva de protección

Cuando se crea un nuevo almacén, viene con una directiva predeterminada. Esta directiva activa un trabajo de copia de seguridad cada día a la hora indicada. De acuerdo con la directiva predeterminada, la instantánea de copia de seguridad se conserva durante 30 días. Puede utilizar la directiva predeterminada para proteger rápidamente la máquina virtual y modificar la directiva más adelante con detalles diferentes.

Utilice **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)** para ver la lista de directivas disponibles en el almacén:

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] La zona horaria del campo BackupTime en PowerShell es UTC. Sin embargo, cuando el tiempo de copia de seguridad se muestra en el Portal de Azure, la hora se ajusta a la zona horaria local.

Una directiva de protección de copia de seguridad está asociada con al menos una directiva de retención. La directiva de retención define el tiempo que los puntos de recuperación se conservan en Copia de seguridad de Azure. Use **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** para ver la directiva de retención predeterminada. Del mismo modo, puede usar **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** para obtener la directiva de programación predeterminada. Los objetos de directiva de retención y programación se usan como entradas para el cmdlet **New-AzureRmRecoveryServicesBackupProtectionPolicy**.

Una directiva de protección de copia de seguridad define cuándo y con qué frecuencia se realiza la copia de seguridad de un elemento. El cmdlet New-AzureRmRecoveryServicesBackupProtectionPolicy crea un objeto de PowerShell que contiene información de la directiva de copia de seguridad. La directiva de copia de seguridad se usa como entrada al cmdlet Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### Habilitar protección

En la habilitación de la protección intervienen dos objetos: el elemento y la directiva. Ambos objetos son necesarios para habilitar la protección en el almacén. Después de que la directiva se haya asociado con el almacén, el flujo de trabajo de copia de seguridad se desencadena a la hora definida en la programación de la directiva.

Procedimiento para habilitar la protección de máquinas virtuales de ARM sin cifrar

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para habilitar la protección de máquinas virtuales cifradas mediante BEK y KEK, debe conceder permisos para que el servicio Copia de seguridad de Microsoft Azure lea las claves y los secretos del almacén de claves.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'KeyVaultName' -ResourceGroupName 'RGNameOfKeyVault' -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para máquinas virtuales basadas en ASM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### Modificación de una directiva de protección

Para modificar la directiva, modifique el objeto BackupSchedulePolicyObject o BackupRetentionPolicy y modifique la directiva mediante Set-AzureRmRecoveryServicesBackupProtectionPolicy.

En el ejemplo siguiente se cambia el recuento de retención a 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Ejecución de una copia de seguridad inicial

La programación de copia de seguridad desencadena una copia de seguridad completa en la copia de seguridad inicial del elemento. En sucesivas copias de seguridad, la copia de seguridad es una copia incremental. Si quiere forzar que la copia de seguridad tenga lugar a una determinada hora o incluso inmediatamente, use el cmdlet **[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)**:

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: La zona horaria de los campos StartTime y EndTime en PowerShell es UTC. Sin embargo, cuando la hora se muestra en el Portal de Azure, esta se ajusta a la zona horaria local.

## Supervisión de trabajos de copia de seguridad

La mayor parte de las operaciones de ejecución prolongada en Copia de seguridad de Azure se modelan como un trabajo. Esto facilita el seguimiento del progreso sin tener que mantener el Portal de Azure abierto constantemente.

Para obtener el estado más reciente de un trabajo en curso, use el cmdlet Get-AzureRmRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

En lugar de sondear si finalizaron estos trabajos (lo que supone un código adicional innecesario), use el cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)**. Este cmdlet detiene la ejecución hasta que el trabajo se complete o se alcance el valor de tiempo de espera especificado.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Restauración de máquinas virtuales de Azure

Hay una diferencia clave entre restaurar una máquina virtual mediante el Portal de Azure y hacerlo con PowerShell. Con PowerShell, la operación de restauración se completa una vez que se creen los discos y la información de configuración a partir del punto de recuperación. La operación de restauración no crea una máquina virtual. Se proporcionan las instrucciones para crear la máquina virtual a partir de discos. Sin embargo, para restaurar completamente una máquina virtual, tendrá que seguir estos procedimientos:

- Selección de la máquina virtual
- Elección de un punto de recuperación
- Restauración de los discos
- Creación de la máquina virtual a partir de los discos almacenados

En el siguiente gráfico se muestra la jerarquía de objetos desde RecoveryServicesVault hasta BackupRecoveryPoint.

![Jerarquía de objetos de Servicios de recuperación donde se muestra BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar los datos de una copia de seguridad, identifique el elemento del que se realizó la copia de seguridad y el punto de recuperación que contiene los datos de un momento específico. Después, utilice el cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** para restaurar los datos del almacén a la cuenta del cliente.

### Selección de la máquina virtual

Para obtener el objeto de PowerShell que identifica el elemento de copia de seguridad correcto, comience en el contenedor del almacén y avance hacia abajo por la jerarquía de objetos. Para seleccionar el contenedor que representa la máquina virtual, use el cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** y canalícelo al cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Elección de un punto de recuperación

Utilice el cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)** para enumerar todos los puntos de recuperación del elemento de copia de seguridad. Después, elija el punto de recuperación que se debe restaurar. Si no está seguro de qué punto de recuperación debe usar, se recomienda elegir el punto RecoveryPointType = AppConsistent más reciente de la lista.

En el siguiente script, la variable **$rp** es una matriz de puntos de recuperación para el elemento de copia de seguridad seleccionado. La matriz se ordena en orden inverso de tiempo con el punto de recuperación más reciente en el índice 0. Use la indexación de matrices de PowerShell estándar para seleccionar el punto de recuperación. Por ejemplo: $rp[0] seleccionará el último punto de recuperación.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### Restauración de los discos

Utilice el cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** con el fin de restaurar los datos y la configuración para un elemento de copia de seguridad a un punto de recuperación. Una vez que haya identificado un punto de recuperación, úselo como el valor del parámetro **-RecoveryPoint**. En el código de ejemplo anterior, se eligió **$rp[0]** como el punto de recuperación que se iba a usar. En el código de ejemplo siguiente, se especifica **$rp[0]** como el punto de recuperación que se va a restaurar en el disco.

Para restaurar los discos y la información de configuración

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Una vez que se haya completado el trabajo de recuperación, use el cmdlet **[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)** para obtener los detalles de la operación de restauración. La propiedad JobDetails tiene la información necesaria para recompilar la máquina virtual.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

Una vez que restaure los discos, vaya a la siguiente sección para obtener información sobre la creación de la máquina virtual.

### Creación de una máquina virtual a partir de discos restaurados

Tras haber restaurado los discos, siga estos pasos para crear y configurar la máquina virtual a partir de ellos.

1. Realice una consulta destinada a las propiedades de los discos restaurados para obtener los detalles del trabajo.

    ```
    PS C:\> $properties = $details.properties
    PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
    PS C:\> $containerName = $properties["Config Blob Container Name"]
    PS C:\> $blobName = $properties["Config Blob Name"]
    ```

2. Establezca el contexto del almacenamiento de Azure y restaure el archivo de configuración JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Utilice el archivo de configuración JSON para crear la configuración de la máquina virtual.

    ```
  PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```

4. Conecte el disco del sistema operativo y los discos de datos.

      Para las máquinas virtuales sin cifrar,

       ```
       PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -CreateOption “Attach”
       PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
       }
       ```
      Las máquinas virtuales cifradas, debe especificar la [información del almacén de claves](https://msdn.microsoft.com/library/dn868052.aspx) para poder conectar discos.
      
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -DiskEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007" -DiskEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -KeyEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007" -KeyEncryptionKeyVaultId "subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -CreateOption "Attach" -Windows
      PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
       }
      ```
      
5. Ajuste la configuración de la red.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Cree la máquina virtual.

    ```
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## Pasos siguientes

Si prefiere usar PowerShell para interactuar con los recursos de Azure, consulte el artículo de PowerShell sobre cómo proteger Windows Server llamado [Implementación y administración de copias de seguridad en Azure para Windows Server](./backup-client-automation.md). También hay un artículo de PowerShell sobre cómo administrar las copias de seguridad DPM: [Implementación y administración de copias de seguridad en Azure para servidores de Data Protection Manager (DPM) con PowerShell](./backup-dpm-automation.md). Estos dos artículos tienen una versión para las implementaciones de Resource Manager, así como las implementaciones clásicas.

<!---HONumber=AcomDC_0817_2016-->