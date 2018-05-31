---
title: 'Azure Site Recovery: Configuración y prueba de la recuperación ante desastres en máquinas virtuales de Azure mediante Azure PowerShell | Microsoft Docs'
description: Aprenda a configurar la recuperación ante desastres en máquinas virtuales de Azure con Azure Site Recovery mediante Azure PowerShell.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: bsiva
ms.openlocfilehash: e12d19612e10885f97d4a8023967fb9ba2d1f8cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209872"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configuración de la recuperación ante desastres en máquinas virtuales de Azure mediante Azure PowerShell

En este artículo, aprenderá a configurar y probar la recuperación ante desastres en máquinas virtuales de Azure con Azure PowerShell. 

Aprenderá a:

> [!div class="checklist"]
> - Cree un almacén de Recovery Services.
> - Establecer el contexto de almacén de la sesión de PowerShell
> - Preparar el almacén para iniciar la replicación de máquinas virtuales de Azure
> - Crear asignaciones de red
> - Cree cuentas de almacenamiento en las que replicar máquinas virtuales.
> - Replicar máquinas virtuales de Azure en una región de recuperación para la recuperación ante desastres
> - Realice una conmutación por error de prueba, una validación y conmutación por error de prueba de limpieza.
> - Conmutar por error a la región de recuperación

> [!NOTE]
> La compatibilidad de PowerShell de Azure Site Recovery con el escenario "*Recuperación ante desastres en máquinas virtuales de Azure*" se encuentra actualmente en versión preliminar. Puede que no todas las funcionalidades del escenario disponibles a través del portal podrían estén disponibles a través de Azure PowerShell. Algunas de las funcionalidades del escenario que no se admiten actualmente a través de Azure PowerShell son:
> - La capacidad de replicar máquinas virtuales de Azure que usan discos administrados.
> - La capacidad de especificar que todos los discos de una máquina virtual deben replicarse sin tener que especificar explícitamente cada disco de la máquina virtual.  

## <a name="prerequisites"></a>requisitos previos

Antes de comenzar:
- Asegúrese de entender la [arquitectura y los componentes del escenario](azure-to-azure-architecture.md).
- Revise los [requisitos de compatibilidad](azure-to-azure-support-matrix.md) de todos los componentes.
- Tiene la versión 5.7.0 o posterior del módulo AzureRm PowerShell. Si necesita instalar o actualizar Azure PowerShell, siga la guía [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicio de sesión en la suscripción de Microsoft Azure

Inicio de sesión en su suscripción de Azure con el cmdlet Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Seleccione su suscripción a Azure. Use el cmdlet Get-AzureRmSubscription para obtener la lista de suscripciones de Azure a las que tiene acceso. Seleccione la suscripción de Azure con la que quiere trabajar mediante el cmdlet Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obtención de los detalles de las máquinas virtuales que se van a replicar

En el ejemplo de este artículo, una máquina virtual de la región Este de EE. UU. se replicará y recuperará en la región Oeste de EE. UU. 2. La máquina virtual que se replica es una máquina virtual con un disco del sistema operativo y un único disco de datos. El nombre de la máquina virtual usada en el ejemplo es AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenga los detalles de los discos de la máquina virtual. Estos detalles se usarán posteriormente al iniciar la replicación de la máquina virtual.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Cree el grupo de recursos en el que se creará el almacén de Recovery Services. 

> [!IMPORTANT]
> * El almacén de Recovery Services, y las máquinas virtuales que se van a proteger, deben estar en diferentes ubicaciones de Azure.
> * El grupo de recursos del almacén de Recovery Services, y las máquinas virtuales que se van a proteger, deben estar en diferentes ubicaciones de Azure.
> * El almacén de Recovery Services, y el grupo de recursos al que pertenece, pueden estar en la misma ubicación de Azure.
 
En el ejemplo de este artículo, la máquina virtual que se protege se encuentra en la región Este de EE. UU. La región de recuperación seleccionada para la recuperación ante desastres es la región Oeste de EE. UU. 2. El almacén de Recovery Services, y el grupo de recursos del almacén, están ambos en la región de recuperación (Oeste de EE. UU. 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```
   
Cree un almacén de Recovery Services. En el ejemplo siguiente se crea un almacén de Recovery Services llamado a2aDemoRecoveryVault en la región Oeste de EE. UU. 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 
## <a name="set-the-vault-context"></a>Establecer el contexto de almacén

> [!TIP]
> El módulo PowerShell de Azure Site Recovery (módulo AzureRm.RecoveryServices.SiteRecovery) viene con alias fáciles de usar para la mayoría de los cmdlets. Los cmdlets del módulo adoptan la forma *\<Operación>-**AzureRmRecoveryServicesAsr**\<Objeto>* y tienen alias equivalentes que adoptan la forma *\<Operación>-**ASR**\<Objeto>*. En este artículo se usan alias de cmdlet para facilitar la lectura.

Establezca el contexto de almacén que se usará en la sesión de PowerShell. Para ello, descargue el archivo de configuración del almacén e impórtelo en la sesión de PowerShell. 

Una vez establecido, las operaciones posteriores de Azure Site Recovery de la sesión de PowerShell se realizan en el contexto de almacén seleccionado. 

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Preparación del almacén para iniciar la replicación de máquinas virtuales de Azure

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Creación de un objeto de tejido de Site Recovery para representar la región primaria (origen)

El objeto de tejido en el almacén representa una región de Azure. El objeto de tejido principal es el objeto de tejido creado para representar la región de Azure a la que pertenecen las máquinas virtuales que se van a proteger. En el ejemplo de este artículo, la máquina virtual que se protege se encuentra en la región Este de EE. UU.

> [!NOTE]
> Las operaciones de Azure Site Recovery se ejecutan de manera asincrónica. Cuando se inicia una operación, se envía un trabajo de Azure Site Recovery y se devuelve un objeto de seguimiento de trabajo. Use el objeto de seguimiento de trabajos para obtener el estado más reciente del trabajo (Get-ASRJob) y para supervisar el estado de la operación.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Si las máquinas virtuales procedentes de varias regiones de Azure se van a proteger en el mismo almacén, cree un objeto de tejido para cada región de Azure de origen.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Creación de un objeto de tejido de Site Recovery para representar la región de recuperación

El objeto de tejido de recuperación representa la ubicación de Azure de recuperación. Las máquinas virtuales se replicarán y recuperarán (en caso de una conmutación por error) en la región de recuperación representada por el tejido de recuperación. La región de Azure de recuperación usada en este ejemplo es Oeste de EE. UU. 2

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Creación de un contenedor de protección de Site Recovery en el tejido principal

El contenedor de protección es un contenedor que se usa para agrupar los elementos replicados en un tejido.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Creación de un contenedor de protección de Site Recovery en el tejido de recuperación

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. Creación de una directiva de replicación

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Creación de una asignación de contenedor de protección entre el contenedor de protección principal y el de recuperación

Una asignación de contenedor de protección asigna el contenedor de protección principal con un contenedor de protección de recuperación y una directiva de replicación. Cree una asignación por cada directiva de replicación que vaya a usar para replicar máquinas virtuales entre un par de contenedores de protección.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Creación de una asignación de contenedor de protección para la conmutación por recuperación (replicación inversa después de una conmutación por error)

Después de una conmutación por error, cuando esté listo para devolver la máquina virtual conmutada por error a la región de Azure original, realizará la conmutación por recuperación. Para ello, la máquina virtual conmutada por error se replica a la inversa desde la región de conmutación por error a la región original. En la replicación inversa, los roles de la región original y la región de recuperación se intercambian. La región original se convierte ahora en la nueva región de recuperación y la que originalmente era la región de recuperación se convierte ahora en la región primaria. La asignación de contenedor de protección para la replicación inversa representa los roles intercambiados de las regiones original y de recuperación.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy 
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Creación de cuentas de almacenamiento en caché y cuentas de almacenamiento de destino

Una cuenta de almacenamiento en caché es una cuenta de almacenamiento estándar en la misma región de Azure que la máquina virtual que se replica. La cuenta de almacenamiento en caché se usa para almacenar temporalmente los cambios de replicación antes de que estos se muevan a la región de Azure de recuperación. Puede elegir (aunque no es necesario) especificar diferentes cuentas de almacenamiento en caché para los distintos discos de una máquina virtual.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

En máquinas virtuales que no usan discos administrados, la cuenta de almacenamiento de destino es la cuenta de almacenamiento de la región de recuperación en la que se replican los discos de la máquina virtual. La cuenta de almacenamiento de destino puede ser una cuenta de almacenamiento estándar o una cuenta de almacenamiento premium. Seleccione el tipo de cuenta de almacenamiento necesario según la velocidad de cambio de los datos (velocidad de escritura de E/S) de los discos y los límites de actividad admitidos de Azure Site Recovery para el tipo de almacenamiento.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Creación de asignaciones de red

Una asignación de red asigna redes virtuales de la región primaria a las redes virtuales de la región de recuperación. La asignación de red especifica la red virtual de Azure en la región de recuperación a la que debe conmutar por error una máquina virtual de la red virtual principal. Una red virtual de Azure se puede asignar a una sola red virtual de Azure en una región de recuperación.

- Creación de una red virtual de Azure en la región de recuperación a la que se conmuta por error

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Recuperación de la red virtual principal (la red virtual a la que está conectada la máquina virtual)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to
    
    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Creación de la asignación de red entre la red virtual principal y la red virtual de recuperación
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
    
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    
   ```
- Creación de la asignación de red para la dirección inversa (conmutación por recuperación)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
        
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replicación de máquinas virtuales de Azure

Replique la máquina virtual de Azure.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Una vez que la operación de replicación inicial se realiza correctamente, se replican los datos de la máquina virtual en la región de recuperación. 

El proceso de replicación comienza con la propagación inicial de una copia de los discos de replicación de la máquina virtual en la región de recuperación. Esta fase se conoce como fase de replicación inicial. 

Una vez completada la replicación inicial, la replicación pasa a la fase de sincronización diferencial. En este momento, la máquina virtual está protegida y puede realizarse en ella una operación de conmutación por error de prueba. El estado de replicación del elemento replicado que representa la máquina virtual pasa al estado "Protegido" una vez finalizada la replicación inicial.

Para supervisar el estado y el mantenimiento de la replicación en la máquina virtual, puede obtener los detalles correspondientes al elemento protegido de la replicación.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Realizar una conmutación por error de prueba, una validación y conmutación por error de prueba de limpieza

Una vez que la replicación de la máquina virtual ha alcanzado un estado protegido, se puede realizar una operación de conmutación por error de prueba en la máquina virtual (en el elemento protegido de replicación de la máquina virtual).

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Realice una conmutación por error de prueba.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Espere a que finalice la operación de conmutación por error de prueba.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
Una vez que el trabajo de conmutación por error de prueba se completa correctamente, puede conectarse a la máquina virtual conmutada por error de prueba y validar esta operación.

Después de que la prueba ha finalizado en la máquina virtual conmutada por error de prueba, limpie la copia de prueba mediante una operación de conmutación por error de prueba de limpieza. Esta operación elimina la copia de prueba de la máquina virtual que se creó con la conmutación por error de prueba.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Conmutación por error a Azure

Conmute por error la máquina virtual a un punto de recuperación concreto.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Una vez conmutada por error correctamente, puede confirmar la operación de conmutación por error.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

Después de una conmutación por error, cuando esté listo para volver a la región original, inicie la replicación inversa del elemento protegido de replicación mediante el cmdlet Update-AzureRmRecoveryServicesAsrProtectionDirection.

## <a name="next-steps"></a>Pasos siguientes
Consulte la [referencia de PowerShell de Azure Site Recovery](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) para obtener información sobre cómo realizar otras tareas, como la creación de planes de recuperación y la prueba de la conmutación por error de los planes de recuperación a través de PowerShell.
