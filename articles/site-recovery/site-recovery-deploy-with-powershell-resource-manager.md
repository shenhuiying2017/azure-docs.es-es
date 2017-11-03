---
title: "Replicación de máquinas virtuales de Hyper-V con PowerShell y Azure Resource Manager | Microsoft Docs"
description: "Automatice la replicación de máquinas virtuales de Hyper-V en Azure con Azure Site Recovery mediante PowerShell y Azure Resource Manager."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: bsiva
ms.openlocfilehash: d93be3b958f85cd2892f92d84ed68996909a5d6b
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar la recuperación ante desastres en Azure para máquinas virtuales de Hyper-V mediante PowerShell y Azure Resource Manager

Gracias a [Azure Site Recovery](site-recovery-overview.md), puede mejorar su estrategia de continuidad de negocio y de recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales de Azure (VM), de máquinas virtuales locales y de servidores físicos.

En este artículo se describe la forma de usar Windows PowerShell con Azure Resource Manager para replicar las máquinas virtuales de Hyper-V en Azure. En el ejemplo que usaremos en este artículo se mostrará cómo replicar en Azure una máquina virtual que se ejecute en un host de Hyper-V.

## <a name="overview"></a>Información general

Azure PowerShell ofrece comandos cmdlet para administrar Azure con Windows PowerShell. Los cmdlets de PowerShell de Site Recovery que están disponibles con Azure PowerShell para Azure Resource Manager le permiten proteger y recuperar los servidores en Azure.

No es necesario ser un experto en PowerShell para leer este artículo, pero sí debe conocer conceptos básicos, como módulos, comandos cmdlet y sesiones. Le recomendamos que lea [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) y [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Usar Azure PowerShell con Azure Resource Manager).

> [!NOTE]
> Los asociados de Microsoft que forman parte del programa Proveedor de soluciones en la nube (CSP) pueden configurar y administrar la protección de los servidores de sus clientes en sus suscripciones a CSP correspondientes (suscripciones de inquilino).
>
>

## <a name="before-you-start"></a>Antes de comenzar
Asegúrese de que tiene preparados estos requisitos previos:

* Una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). También puede leer sobre los precios del [Administrador de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Para obtener información acerca de esta versión y cómo instalarla, consulte [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Los módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) y [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). Puede obtener las últimas versiones de estos módulos en la [Galería de PowerShell](https://www.powershellgallery.com/)

Además, en el ejemplo específico que se describe en este artículo verá que necesita cumplir los siguientes requisitos previos:

* Un host de Hyper-V que ejecute Windows Server 2012 R2 o Microsoft Hyper-V Server 2012 R2 que contenga una o varias máquinas virtuales. Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.
* Las máquinas virtuales que quiera replicar deben cumplir con [estos requisitos previos](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>Paso 1: Inicio de sesión en la cuenta de Azure

1. Abra una consola de PowerShell y ejecute este comando para iniciar sesión en la cuenta de Azure. El cmdlet abrirá una página web que le solicitará las credenciales de la cuenta: **Login-AzureRmAccount**.
    - Como alternativa, puede incluir sus credenciales de cuenta como un parámetro en el cmdlet **Login-AzureRmAccount** mediante el parámetro **-Credential**.
    - Si usted es un asociado CSP que trabaja en nombre de un inquilino, especifique el cliente como inquilino usando su TenantID o su nombre de dominio principal de inquilino. Por ejemplo: **Login-AzureRmAccount -Tenant "fabrikam.com"**
2. Ya que una cuenta puede tener varias suscripciones, le recomendamos que asocie la suscripción que quiera usar a esa cuenta:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Compruebe que la suscripción esté registrada para usar los proveedores de Azure para Recovery Services y Site Recovery mediante los comandos siguientes:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices``Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Si **RegistrationState** está establecido en el valor **Registrado** en la salida de los comandos, puede continuar con el paso 2. Si no es así, debe registrar al proveedor que falta en su suscripción mediante estos comandos:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery``Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Compruebe que los proveedores se registraron correctamente mediante los siguientes comandos:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Paso 2: configurar el almacén

1. Cree un grupo de recursos de Azure Resource Manager en el que pueda crear el almacén, o bien use un grupo de recursos existente. Cree un nuevo grupo de recursos tal y como se indica a continuación. La variable $ResourceGroupName contiene el nombre del grupo de recursos que quiere crear y la variable $Geo contiene la región de Azure en la que se va a crear el grupo de recursos (por ejemplo: "Sur de Brasil").

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. Para obtener una lista de grupos de recursos de la suscripción, use el cmdlet **Get-AzureRmResourceGroup**.
2. Cree un nuevo almacén de Servicios de recuperación de Azure como sigue:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Puede recuperar una lista de los almacenes existentes mediante el cmdlet **AzureRmRecoveryServicesVault Get**.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Paso 3: Configuración del contexto de almacén de Servicios de recuperación

Establezca el contexto de almacén de la manera siguiente:

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Paso 4: crear un sitio de Hyper-V

1. Cree un nuevo sitio de Hyper-V como sigue:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Este cmdlet inicia un trabajo de Site Recovery para crear el sitio y devuelve un objeto de trabajo de Site Recovery. Espere a que el trabajo se complete y compruebe que se ha hecho correctamente.
3. Para recuperar el objeto de trabajo y comprobar el estado actual del trabajo, use el **cmdlet Get-AzureRmSiteRecoveryJob**.
4. Genere y descargue una clave de registro para el sitio; para ello, proceda como sigue:

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Copie la clave descargada en el host de Hyper-V. Necesita la clave para registrar el host de Hyper-V en el sitio

## <a name="step-5-install-the-provider-and-agent"></a>Paso 5: instalar el proveedor y el agente

1. Descargue el instalador de la última versión del proveedor de [Microsoft](https://aka.ms/downloaddra).
2. Ejecute el instalador en el host de Hyper-V.
3. Al final de la instalación, vaya al paso que describe el registro.
4. Cuando se le solicite, proporcione la clave descargada y complete el registro del host de Hyper-V.
5. Compruebe que el host de Hyper-V se registró en el sitio tal como se indica a continuación:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Paso 6: crear una directiva de replicación

Antes de comenzar, recuerde que la cuenta de almacenamiento especificada debe estar en la misma región de Azure que el almacén y que debe tener habilitada la replicación geográfica.

1. Cree una directiva de replicación como sigue:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Compruebe el trabajo devuelto para asegurarse de que la creación de la directiva de replicación se realiza correctamente.

3. Obtenga el contenedor de protección correspondiente al sitio; para ello, proceda como sigue:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Asocie el contenedor de protección con la directiva de replicación tal como se indica:

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

4. Espere a que finalice el trabajo de asociación.

## <a name="step-7-enable-vm-protection"></a>Paso 7: habilitar la protección de máquina virtual

1. Obtenga la entidad de protección correspondiente a la máquina virtual que desea proteger, tal como se indica a continuación:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Proteja la máquina virtual. Si la máquina virtual que se protege tiene más de un disco conectado, especifique el disco del sistema operativo con el parámetro *OSDiskName* .

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Espere a que las máquinas virtuales lleguen a un estado protegido después de la replicación inicial. Esta operación puede tardar unos minutos en función de factores, como el volumen de datos que se van a replicar y el ancho de banda del canal de subida disponible en Azure. Cuando se alcanza un estado protegido, los valores State y StateDescription del trabajo se actualizan tal como se indica a continuación: 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Actualice las propiedades de recuperación (como el tamaño de rol de la máquina virtual) y la red de Azure a la cual se asociarán el adaptador de red de la máquina virtual tras la conmutación por error.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Paso 8: Ejecución de una conmutación por error de prueba
1. Ejecute la conmutación por error de prueba de la manera siguiente:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Compruebe que la prueba de la máquina virtual se crea en Azure. El trabajo de conmutación por error de prueba se suspende después de crear la máquina virtual de prueba en Azure.
3. Para limpiar y completar la conmutación por error de prueba, ejecute:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Pasos siguientes
[Más información](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre Azure Site Recovery con los cmdlets de PowerShell de Azure Resource Manager.
