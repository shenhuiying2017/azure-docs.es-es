---
title: "Implementación y administración de copia de seguridad de VM de Azure mediante PowerShell | Microsoft Docs"
description: "Obtenga información sobre cómo implementar y administrar Azure Backup mediante PowerShell."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/2/2017
ms.author: markgal;trinadhk;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f0f06adb8177ce2d17aa0b40666470279c04e22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>Uso de los cmdlets de AzureRM.Backup para realizar copias de seguridad de máquinas virtuales
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [Clásico](backup-azure-vms-classic-automation.md)
>
>

En este artículo se muestra cómo usar Azure PowerShell para realizar copias de seguridad de máquinas virtuales de Azure y recuperarlas. Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: Resource Manager y el clásico. Este artículo trata el uso del modelo de implementación clásico para hacer copia de seguridad de datos en un almacén de Backup. Si no ha creado un almacén de Backup en su suscripción, consulte el artículo en el que se explica el uso del modelo de Resource Manager, [Uso de los cmdlets AzureRM.RecoveryServices.Backup para realizar copias de seguridad de máquinas virtuales](backup-azure-vms-automation.md). Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

> [!IMPORTANT]
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> A partir del 15 de octubre de 2017, no podrá usar PowerShell para crear almacenes de Backup. **El 1 de noviembre de 2017**:
>- Todos los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>

## <a name="concepts"></a>Conceptos
En este artículo se ofrece información específica de los cmdlets de PowerShell que se usan para realizar copias de seguridad de máquinas virtuales. Para ver una introducción a la protección de máquinas virtuales de Azure, consulte [Planeación de la infraestructura de copia de seguridad de máquinas virtuales en Azure](backup-azure-vms-introduction.md).

> [!NOTE]
> Antes de comenzar, lea los [requisitos previos](backup-azure-vms-prepare.md) necesarios para utilizar Azure Backup y las [limitaciones](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) de la solución actual de copia de seguridad de máquina virtual.
>
>

Para usar PowerShell de forma eficaz, hay que conocer la jerarquía de objetos y el punto de partida.

![Jerarquía de objetos](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

Los dos flujos más importantes son habilitar la protección para una máquina virtual y restaurar los datos a partir de un punto de recuperación. El objetivo de este artículo es ayudarlo a convertirse en un experto en el uso de los cmdlets de PowerShell para habilitar estos dos escenarios.

## <a name="setup-and-registration"></a>Instalación y registro
Para empezar:

1. [Descargue la versión de PowerShell más reciente](https://github.com/Azure/azure-powershell/releases) (la versión mínima necesaria es: 1.0.0)
2. Para buscar los cmdlets de PowerShell de Azure Backup disponibles, escriba el siguiente comando:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

Las siguientes tareas de instalación y registro se pueden automatizar con PowerShell:

* Creación de un almacén de copia de seguridad
* Registro de las VM en el servicio Azure Backup

### <a name="create-a-backup-vault"></a>Creación de un almacén de copia de seguridad
> [!WARNING]
> La primera vez que los clientes usen Azure Backup deben registrar el proveedor de Azure Backup que se va a usar con su suscripción. Para ello, ejecute el siguiente comando: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
>
>

Puede crear un nuevo almacén de copia de seguridad mediante el cmdlet **New-AzureRmBackupVault** . El almacén de copia de seguridad es un recurso ARM, por lo que necesita colocarlo dentro de un grupo de recursos. En una consola de Azure PowerShell, ejecute los comandos siguientes:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Para obtener una lista de todos los almacenes de copia de seguridad de una suscripción dada, use el cmdlet **Get-AzureRmBackupVault** .

> [!NOTE]
> Es conveniente almacenar el objeto de almacén de copia de seguridad en una variable. Dicho objeto de almacén se necesita como entrada en muchos de los cmdlets de Azure Backup.
>
>

### <a name="registering-the-vms"></a>Registro de las máquinas virtuales
El primer paso para configurar la copia de seguridad con Azure Backup es registrar un equipo o una máquina virtual en un almacén de Azure Backup. El cmdlet **Register-AzureRmBackupContainer** toma la información de entrada de una máquina virtual IaaS de Azure y la registra en el almacén especificado. La operación de registro asocia la máquina virtual de Azure con el almacén de copia de seguridad y realiza el seguimiento de la máquina virtual a través del ciclo de vida de la copia de seguridad.

Al registrar una máquina virtual en el servicio Azure Backup, se crea un objeto contenedor de nivel superior. Normalmente, un contenedor contiene varios elementos de los que se puede realizar una copia de seguridad, pero en el caso de las máquinas virtuales, solo habrá un elemento de copia de seguridad para el contenedor.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Copias de seguridad de máquinas virtuales de Azure
### <a name="create-a-protection-policy"></a>Creación de una directiva de protección
Para empezar a realizar copias de seguridad de las máquinas virtuales no es obligatorio crear una nueva directiva de protección. El almacén incluye una "directiva predeterminada" que se puede usar para habilitar rápidamente la protección y que se puede editar posteriormente con los detalles adecuados. Para obtener una lista de las directivas disponibles en el almacén, use el cmdlet **Get-AzureRmBackupProtectionPolicy** :

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> La zona horaria del campo BackupTime en PowerShell es UTC. Sin embargo, cuando el tiempo de copia de seguridad se muestra en el Portal de Azure, la zona horaria estará alineada al sistema local, junto con la diferencia horaria con UTC.
>
>

Una directiva de copia de seguridad está asociada con al menos una directiva de retención. La directiva de retención define el tiempo que los puntos de recuperación se conservan en Azure Backup. El cmdlet **New-AzureRmBackupRetentionPolicy** crea objetos de PowerShell que contienen información de la directiva de retención. Estos objetos de directiva de retención se usan como entradas al cmdlet *New-AzureRmBackupProtectionPolicy*, o bien directamente con *Enable-AzureRmBackupProtection*.

Una directiva de copia de seguridad define cuándo y con qué frecuencia se realiza la copia de seguridad de un elemento. El cmdlet **New-AzureRmBackupProtectionPolicy** crea un objeto de PowerShell que contiene información de la directiva de copia de seguridad. La directiva de copia de seguridad se usa como entrada al cmdlet *Enable-AzureRmBackupProtection* .

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>Habilitar protección
La habilitación de la protección implica dos objetos: el elemento y la directiva, y ambos deben pertenecer al mismo almacén. Una vez que la directiva está asociada con el elemento, el flujo de trabajo de copia de seguridad se activará en la programación definida.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>Copia de seguridad inicial
La programación de copia de seguridad se encargará de realizar la copia inicial completa del elemento y la copia incremental en las copias de seguridad posteriores. Sin embargo, si quiere forzar que la copia de seguridad inicial se realice en un momento determinado o incluso inmediatamente, use el cmdlet **Backup-AzureRmBackupItem** :

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> La zona horaria de los campos de StartTime y EndTime mostrados en PowerShell es UTC. Sin embargo, cuando se muestra información similar en el Portal de Azure, la zona horaria estará alineada al reloj del sistema local.
>
>

### <a name="monitoring-a-backup-job"></a>Supervisión de trabajos de copia de seguridad
La mayor parte de las operaciones de ejecución prolongada en Azure Backup se modelan como un trabajo. Esto facilita el seguimiento del progreso sin tener que mantener el Portal de Azure abierto constantemente.

Para obtener el estado más reciente de un trabajo en curso, use el cmdlet **Get-AzureRmBackupJob** .

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

En lugar de sondear si finalizaron estos trabajos (lo que supone un código adicional innecesario), es más fácil usar el cmdlet **Wait-AzureRmBackupJob** . Si se usa en un script, el cmdlet pausará la ejecución hasta que el trabajo se complete o se alcance el valor de tiempo de espera especificado.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Restauración de máquinas virtuales de Azure
Para restaurar los datos de una copia de seguridad, es preciso identificar el elemento del que se realizó la copia de seguridad y el punto de recuperación que contiene los datos de un momento específico. Esta información se le proporciona al cmdlet Restore-AzureRmBackupItem para iniciar una restauración de los datos del almacén a la cuenta del cliente.

### <a name="select-the-vm"></a>Selección de la máquina virtual
Para obtener el objeto de PowerShell que identifica el elemento de copia de seguridad adecuada, es preciso que empiece en el contenedor del almacén y avance hacia abajo por la jerarquía de objetos. Para seleccionar el contenedor que representa la máquina virtual, use el cmdlet **Get-AzureRmBackupContainer** y canalícelo al cmdlet **Get-AzureRmBackupItem**.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>Elección de un punto de recuperación
Ahora puede enumerar todos los puntos de recuperación del elemento de copia de seguridad con el cmdlet **Get-AzureRmBackupRecoveryPoint** y elegir el punto de recuperación para realizar la restauración. Normalmente los usuarios eligen el punto *AppConsistent* más reciente de la lista.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

La variable ```$rp``` es una matriz de puntos de recuperación para el elemento de copia de seguridad seleccionado, ordenados en orden inverso de tiempo: el punto de recuperación más reciente se encuentra en el índice 0. Use la indexación de matrices de PowerShell estándar para seleccionar el punto de recuperación. Por ejemplo: ```$rp[0]``` seleccionará el último punto de recuperación.

### <a name="restoring-disks"></a>Restauración de discos
Hay una diferencia clave entre las operaciones de restauración realizadas a través del Portal de Azure y las realizadas a través de Azure PowerShell. Con PowerShell, la operación de restauración se detiene en la restauración de los discos y la información de configuración desde el punto de recuperación. No crea máquinas virtuales.

> [!WARNING]
> Restore-AzureRmBackupItem no crea una VM. Solo restaura los discos en la cuenta de almacenamiento especificada. Este no es el mismo comportamiento que observará en el Portal de Azure.
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Los detalles de la operación de restauración se pueden obtener con el cmdlet **Get-AzureRmBackupJobDetails** cuando finalice el trabajo de restauración. La propiedad *ErrorDetails* tendrá la información necesaria para volver a compilar la máquina virtual.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>Compilación de la máquina virtual
La compilación de la máquina virtual a partir de los discos restaurados puede realizarse con los cmdlets anteriores de PowerShell para Azure Service Management, las nuevas plantillas de Azure Resource Manager o incluso con el Portal de Azure. En un ejemplo rápido, mostraremos cómo conseguirlo mediante los cmdlets de Azure Service Management.

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Para obtener más información sobre cómo compilar una máquina virtual a partir de los discos restaurados, consulte la información relativa a los cmdlets siguientes:

* [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>Ejemplos de código
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1. Obtener el estado de finalización de las subtareas de trabajo
Para realizar el seguimiento del estado de finalización de las subtareas individuales, puede usar el cmdlet **Get-AzureRmBackupJobDetails** :

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2. Crear un informe diario o semanal de los trabajos de copia de seguridad
Normalmente, los administradores desean saber qué trabajos de copia de seguridad se ejecutaron en las últimas 24 horas y cuál es su estado. Además, la cantidad de datos transferidos ofrece a los administradores una manera de calcular el uso mensual de los datos. El script siguiente extrae los datos sin procesar desde el servicio Azure Backup y muestra la información en la consola de PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Si quiere agregar funcionalidades gráficas a esta salida del informe, lea la información de la publicación del blog de TechNet [Charting with PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>Pasos siguientes
Si prefiere usar PowerShell para interactuar con los recursos de Azure, consulte el artículo de PowerShell sobre cómo proteger Windows Server llamado [Implementación y administración de copias de seguridad en Azure para Windows Server](backup-client-automation-classic.md). También hay un artículo de PowerShell sobre cómo administrar las copias de seguridad DPM: [Implementación y administración de copias de seguridad en Azure para servidores de Data Protection Manager (DPM) con PowerShell](backup-dpm-automation-classic.md). Estos dos artículos tienen una versión para las implementaciones de Resource Manager, así como las implementaciones clásicas.
