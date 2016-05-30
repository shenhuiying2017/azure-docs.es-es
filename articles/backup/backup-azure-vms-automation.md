<properties
   pageTitle="Implementación y administración de copia de seguridad para máquinas virtuales de ARM mediante PowerShell | Microsoft Azure"
   description="Uso de PowerShell para implementar y administrar copias de seguridad para máquinas virtuales de ARM en Azure"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# Implementación y administración de copia de seguridad para máquinas virtuales de ARM mediante PowerShell

> [AZURE.SELECTOR]
- [Implementación y administración de copia de seguridad de VM de Azure mediante PowerShell](backup-azure-vms-automation.md)
- [Deploy and manage backup for Azure VMs using PowerShell](backup-azure-vms-classic-automation.md) (Implementación y administración de copia de seguridad para máquinas virtuales de Azure mediante PowerShell)

En este artículo se muestra cómo usar Azure PowerShell para realizar la copia de seguridad y recuperación de una máquina virtual (VM) de Azure desde un almacén de Servicios de recuperación. Un almacén de Servicios de recuperación es un recurso de Azure Resource Manager (ARM) y se utiliza para proteger datos y recursos en los servicios Copia de seguridad de Azure y Azure Site Recovery. Use un almacén de Servicios de recuperación cuando trabaje en una implementación de ARM. Puede usar un almacén de Servicios de recuperación para proteger máquinas virtuales implementadas por el Administrador de servicios de Azure (ASM), así como máquinas virtuales de ARM.

>[AZURE.NOTE] Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). La información de este artículo es para su uso con las máquinas virtuales creadas con el modelo de Resource Manager.

Este artículo le guiará en el uso de PowerShell para proteger una máquina virtual y restaurar datos a partir de un punto de recuperación.

## Conceptos

Si no está familiarizado con el servicio de Copia de seguridad de Azure, puede obtener información general al respecto en [¿Qué es la Copia de seguridad de Azure?](backup-introduction-to-azure-backup.md) Antes de comenzar, asegúrese de que se tratan los aspectos fundamentales sobre los requisitos previos necesarios para trabajar con Copia de seguridad de Azure y las limitaciones de la solución actual de copia de seguridad de máquina virtual.

Para usar PowerShell de forma eficaz, es preciso conocer la jerarquía de objetos y desde dónde empezar.

![Jerarquía de objetos de Servicios de recuperación](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## Instalación y registro

Para empezar:

1. [Descargue la versión más reciente de PowerShell](https://github.com/Azure/azure-powershell/releases) (la versión mínima necesaria es 1.0.0).

2. Para buscar los cmdlets de PowerShell de Copia de seguridad de Azure disponibles, escriba el siguiente comando:

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


Las siguientes tareas se pueden automatizar con PowerShell:

- Creación de un almacén de Servicios de recuperación
- Copia de seguridad o protección de máquinas virtuales de Azure
- Desencadenamiento de un trabajo de copia de seguridad
- Supervisión de un trabajo de copia de seguridad
- Restauración de máquinas virtuales de Azure

## Creación de un almacén de Servicios de recuperación

> [AZURE.TIP] La primera vez que los clientes usen Copia de seguridad de Azure deberán registrar el proveedor de Azure Site Recovery que se va a usar con su suscripción. Para ello, ejecute el siguiente comando: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"

Puede crear un nuevo almacén de Servicios de recuperación con el cmdlet **New-AzureRmRecoveryServicesVault**. El almacén de Servicios de recuperación es un recurso de ARM, por lo que deberá colocarlo dentro de un grupo de recursos. En una consola de Azure PowerShell, ejecute los comandos siguientes:

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

Para ver una lista de todos los almacenes de Servicios de recuperación de una suscripción, use el cmdlet **Get-AzureRmRecoveryServicesVault**.

### Establecimiento de la redundancia de almacenamiento
Al crear un almacén de Servicios de recuperación, especifique el tipo de redundancia de almacenamiento que se usará: almacenamiento con redundancia local (LRS) o almacenamiento con redundancia geográfica (GRS). En el ejemplo siguiente se muestra que la opción BackupStorageRedundancy para testVault está establecida en GeoRedundant.

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] Muchos de los cmdlets de Copia de seguridad de Azure requieren el objeto de almacén de Servicios de recuperación como entrada. Por este motivo, es conveniente almacenar el objeto de almacén de Servicios de recuperación de copia de seguridad en una variable.


## Copias de seguridad de máquinas virtuales de Azure

Antes de habilitar la protección en una máquina virtual, debe establecer el contexto de almacén. El contexto se aplica a todos los cmdlets sucesivos.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Creación de una directiva de protección

Cuando se crea un nuevo almacén, viene con una directiva predeterminada. Esta directiva activa cada día a las 9:30 de la noche un trabajo de copia de seguridad. La instantánea de copia de seguridad se conserva durante 30 días. Puede utilizar la directiva predeterminada para proteger rápidamente la máquina virtual y modificar la directiva más adelante con detalles diferentes.

Para ver la lista disponible de las directivas del almacén, utilice el cmdlet Get-AzureRmRecoveryServicesBackupProtectionPolicy:

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] La zona horaria del campo BackupTime en PowerShell es UTC. Sin embargo, cuando el tiempo de copia de seguridad se muestra en el Portal de Azure, la hora se ajusta a la zona horaria local.

Una directiva de protección de copia de seguridad está asociada con al menos una directiva de retención. La directiva de retención define el tiempo que los puntos de recuperación se conservan en Copia de seguridad de Azure. Use Get-AzureRmRecoveryServicesBackupRetentionPolicyObject para ver la directiva de retención predeterminada. Igualmente, puede obtener la directiva de programación predeterminada mediante Get-AzureRmRecoveryServicesBackupSchedulePolicyObject. Los objetos de directiva de retención y programación se usan como entradas al cmdlet New-AzureRmRecoveryServicesBackupProtectionPolicy.

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

En la habilitación de la protección intervienen dos objetos: el elemento y la directiva. Ambos objetos son necesarios para habilitar la protección en el almacén. Después de que la directiva se ha asociado con el elemento, el flujo de trabajo de copia de seguridad se desencadena a la hora definida en la programación de la directiva.

```
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
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Ejecución de una copia de seguridad inicial

La programación de copia de seguridad desencadena una copia de seguridad completa en la copia de seguridad inicial del elemento. En sucesivas copias de seguridad, la copia de seguridad es una copia incremental. Si desea forzar que la copia de seguridad tenga lugar a una determinada hora o incluso inmediatamente, use el cmdlet Backup-AzureRmRecoveryServicesBackupItem:

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: La zona horaria de los campos StartTime y EndTime en PowerShell es UTC. Sin embargo, cuando la hora se muestra en el Portal de Azure, esta se ajusta a la zona horaria local.

## Supervisión de trabajos de copia de seguridad

La mayor parte de las operaciones de ejecución prolongada en Copia de seguridad de Azure se modelan como un trabajo. Esto facilita el seguimiento del progreso sin tener que mantener el Portal de Azure abierto constantemente.

Para obtener el estado más reciente de un trabajo en curso, use el cmdlet Get-AzureRMRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

En lugar de sondear si finalizaron estos trabajos (lo que supone un código adicional innecesario), es más fácil usar el cmdlet Wait-AzureRmRecoveryServicesBackupJob. Si se usa en un script, el cmdlet pausará la ejecución hasta que el trabajo se complete o se alcance el valor de tiempo de espera especificado.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Restauración de máquinas virtuales de Azure

Para restaurar los datos de una copia de seguridad, es preciso identificar el elemento del que se realizó la copia de seguridad y el punto de recuperación que contiene los datos de un momento dado. Esta información se proporciona al cmdlet Restore-AzureRMRecoveryServicesBackupItem para iniciar una restauración de los datos desde el almacén hasta la cuenta del cliente.

### Selección de la máquina virtual

Para obtener el objeto de PowerShell que identifica el elemento de copia de seguridad correcto, comience en el contenedor del almacén y avance hacia abajo por la jerarquía de objetos. Para seleccionar el contenedor que representa la VM, use el cmdlet Get-AzureRmRecoveryServicesBackupContainer y canalícelo al cmdlet Get-AzureRmRecoveryServicesBackupItem.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Elección de un punto de recuperación

Ahora puede mostrar todos los puntos de recuperación del elemento de copia de seguridad con el cmdlet Get-AzureRMRecoveryServicesBackupRecoveryPoint y elegir el punto de recuperación que quiere restaurar. Normalmente los usuarios eligen el punto AppConsistent más reciente de la lista.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

La variable $rp es una matriz de puntos de recuperación para el elemento de copia de seguridad seleccionado, en orden inverso de tiempo: el punto de recuperación más reciente se encuentra en el índice 0. Use la indexación de matrices de PowerShell estándar para seleccionar el punto de recuperación. Por ejemplo: $rp[0] seleccionará el último punto de recuperación.

### Restauración de discos

Hay una diferencia clave entre las operaciones de restauración realizadas a través del Portal de Azure y las realizadas a través de Azure PowerShell. Con PowerShell, la operación de restauración se detiene en la restauración de los discos y la información de configuración desde el punto de recuperación. No crea máquinas virtuales.

> [AZURE.WARNING] El cmdlet Restore-AzureRMRecoveryServicesBackupItem no crea una máquina virtual, solo restaura los discos en la cuenta de almacenamiento especificada. Este proceso es diferente de lo que ocurre en el Portal de Azure.

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Se pueden obtener los detalles de la operación de restauración mediante el cmdlet Get-AzureRmRecoveryServicesBackupJobDetails una vez que el trabajo de restauración ha finalizado. La propiedad JobDetails tendrá la información necesaria para recompilar la máquina virtual.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## Registro de Windows Server o DPM en un almacén de Servicios de recuperación

Después de crear el almacén de Servicios de recuperación, descargue el agente más reciente y las credenciales de almacén y guárdelas en una ubicación adecuada como C:\\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

En Windows Server o el servidor de DPM, ejecute el cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar la máquina con el almacén.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->