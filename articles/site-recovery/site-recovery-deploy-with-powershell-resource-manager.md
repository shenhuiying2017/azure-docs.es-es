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
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: dbd562b73b0caecd0feb993bd6fb796dddb0438c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Replicación entre máquinas virtuales de Hyper-V local y Azure con PowerShell y Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell: administrador de recursos](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Portal clásico](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>Información general
Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, vea la [Información general sobre Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Puede funcionar con dos tipos de módulos: el módulo de perfil de Azure o el módulo de Azure Resource Manager.

Los cmdlets de PowerShell de Site Recovery que están disponibles con Azure PowerShell para Azure Resource Manager le permiten proteger y recuperar los servidores en Azure.

En este artículo se describe cómo usar Windows PowerShell, junto con Azure Resource Manager, para implementar Site Recovery con el fin de configurar y orquestar la protección de los servidores. El ejemplo que se usa en este artículo muestra cómo proteger, realizar conmutaciones por error y recuperar máquinas virtuales en un host de Hyper-V en Azure, mediante Azure PowerShell con Azure Resource Manager.

> [!NOTE]
> Los cmdlets de PowerShell de Site Recovery actualmente le permiten configurar lo siguiente: un sitio de Virtual Machine Manager en otro sitio, un sitio de Virtual Machine Manager en Azure y un sitio Hyper-V en Azure.
>
>

No es necesario ser un experto en PowerShell para leer este artículo, pero sí debe conocer los conceptos básicos, como módulos, cmdlets y sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Lea más sobre el [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Los asociados de Microsoft que forman parte del programa Proveedor de soluciones en la nube (CSP) pueden configurar y administrar la protección de los servidores de sus clientes en las suscripciones a CSP correspondientes de los clientes (suscripciones de inquilino).
>
>

## <a name="before-you-start"></a>Antes de comenzar
Asegúrese de que tiene preparados estos requisitos previos:

* Una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). También puede leer sobre los precios del [Administrador de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Para obtener información acerca de esta versión y cómo instalarla, consulte [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Los módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) y [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). Puede obtener las últimas versiones de estos módulos en la [Galería de PowerShell](https://www.powershellgallery.com/)

En este artículo se muestra cómo usar Azure PowerShell con Azure Resource Manager para configurar y administrar la protección de los servidores. El ejemplo utilizado en este artículo muestra cómo proteger una máquina virtual, que se ejecuta en un host de Hyper-V en Azure. Los siguientes requisitos previos son específicos para este ejemplo. Para consultar un conjunto más amplio de requisitos para los distintos escenarios de Site Recovery, vea la documentación correspondiente a cada escenario.

* Necesitará un host de Hyper-V que ejecute Windows Server 2012 R2 o Microsoft Hyper-V Server 2012 R2 que contenga una o varias máquinas virtuales.
* Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.
* Las máquinas virtuales que quiere proteger deben cumplir los [requisitos previos para las máquinas virtuales](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>Paso 1: Inicio de sesión en la cuenta de Azure
1. Abra una consola de PowerShell y ejecute este comando para iniciar sesión en la cuenta de Azure. El cmdlet abrirá una página web que le solicitará las credenciales de cuenta.

        Login-AzureRmAccount

    Como alternativa, también puede incluir las credenciales de cuenta como un parámetro en el cmdlet `Login-AzureRmAccount` utilizando el parámetro `-Credential`.

    Si usted es un asociado CSP que trabaja en nombre de un inquilino, especifique el cliente como inquilino usando su TenantID o su nombre de dominio principal de inquilino.

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. Una cuenta puede tener varias suscripciones, por lo que deberá asociar la suscripción que desee usar con la cuenta.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. Compruebe que su suscripción está registrada para usar los proveedores de Azure para Recovery Services y Site Recovery con los comandos siguientes:

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   Si **RegistrationState** está establecido en el valor **Registrado** en la salida de estos comandos, puede continuar con el paso 2. Si no es así, debe registrar al proveedor que falta en su suscripción.

   Para registrar el proveedor de Azure en Site Recovery y Servicios de recuperación, ejecute los comandos siguientes:

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   Compruebe que los proveedores se registraron correctamente mediante los comandos `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` y `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-recovery-services-vault"></a>Paso 2: Configuración del almacén de Servicios de recuperación
1. Cree un grupo de recursos Azure Resource Manager en el que podrá crear el almacén, o bien use un grupo de recursos existente. Puede crear un grupo de recursos nuevo mediante el siguiente comando:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    donde la variable $ResourceGroupName contiene el nombre del grupo de recursos que desea crear y la variable $Geo contiene la región de Azure en la que se va a crear el grupo de recursos (por ejemplo: "Sur de Brasil").

    Puede obtener una lista de grupos de recursos en la suscripción con el cmdlet `Get-AzureRmResourceGroup` .
2. Cree un nuevo almacén de Servicios de recuperación de Azure como sigue:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Puede recuperar una lista de los almacenes existentes con el cmdlet `Get-AzureRmRecoveryServicesVault`.

> [!NOTE]
> Si desea realizar operaciones en los almacenes de Site Recovery creados mediante el portal clásico o el módulo PowerShell de Administración de servicios de Azure, puede recuperar una lista de dichos almacenes con el cmdlet `Get-AzureRmSiteRecoveryVault`. Debe crear un nuevo almacén de Servicios de recuperación para todas las operaciones nuevas. Los almacenes de Site Recovery que ha creado anteriormente son compatibles, pero no tienen las últimas características.
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>Paso 3: Configuración del contexto de almacén de Servicios de recuperación
1. Establezca el contexto de almacén mediante la ejecución de los comandos siguientes:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Paso 4: Creación de un sitio Hyper-V y generación de una nueva clave de registro de almacén para el sitio.
1. Cree un nuevo sitio de Hyper-V como sigue:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Este cmdlet inicia un trabajo de Site Recovery para crear el sitio y devuelve un objeto de trabajo de Site Recovery. Espere a que el trabajo se complete y compruebe que se ha hecho correctamente.

    Puede recuperar el objeto de trabajo y, por tanto, comprobar el estado actual del trabajo mediante el cmdlet Get-AzureRmSiteRecoveryJob.
2. Genere y descargue una clave de registro para el sitio; para ello, proceda como sigue:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copie la clave descargada en el host de Hyper-V. Necesita la clave para registrar el host de Hyper-V en el sitio

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Paso 5: Instalación del proveedor de Azure Site Recovery y del agente de Servicios de recuperación de Azure en el host de Hyper-V
1. Descargue el instalador de la última versión del proveedor de [Microsoft](https://aka.ms/downloaddra).
2. Ejecute el instalador en el host de Hyper-V y al final de la instalación continúe con el paso de registro.
3. Cuando se le solicite, proporcione la clave de registro descargada del sitio y complete el registro del host de Hyper-V en el sitio.
4. Compruebe que el host de Hyper-V se ha registrado en el sitio mediante el comando siguiente:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Paso 6: Creación de una directiva de replicación y su asociación con el contenedor de protección
1. Cree una directiva de replicación como sigue:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Compruebe el trabajo devuelto para asegurarse de que la creación de la directiva de replicación se realiza correctamente.

   > [!IMPORTANT]
   > La cuenta de almacenamiento especificada debe estar en la misma región de Azure que el almacén de Servicios de recuperación y debe tener habilitada la replicación geográfica.
   >
   > * Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (clásico), la conmutación por error de las máquinas protegidas recupera la máquina en Azure IaaS (clásico).
   > * Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (Azure Resource Manager), la conmutación por error de las máquinas protegidas recupera la máquina en Azure IaaS (Azure Resource Manager).
   >
   >
2. Obtenga el contenedor de protección correspondiente al sitio; para ello, proceda como sigue:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Inicie la asociación del contenedor de protección con la directiva de replicación:

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

   Espere a que el trabajo de asociación se complete y asegúrese de que se ha hecho correctamente.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Paso 7: Habilitación de la protección para las máquinas virtuales
1. Obtenga la entidad de protección correspondiente a la máquina virtual que desea proteger:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Inicie la protección de la máquina virtual, como sigue:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > La cuenta de almacenamiento especificada debe estar en la misma región de Azure que el almacén de Servicios de recuperación y debe tener habilitada la replicación geográfica.
   >
   > * Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (clásico), la conmutación por error de las máquinas protegidas recupera la máquina en Azure IaaS (clásico).
   > * Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (Azure Resource Manager), la conmutación por error de las máquinas protegidas recupera la máquina en Azure IaaS (Azure Resource Manager).
   >
   > Si la máquina virtual que se protege tiene más de un disco conectado, especifique el disco del sistema operativo con el parámetro *OSDiskName* .
   >
   >
3. Espere a que las máquinas virtuales lleguen a un estado protegido después de la replicación inicial. Esta operación puede tardar unos minutos, en función de factores como el volumen de datos que se van a replicar y el ancho de banda del canal de subida disponible en Azure. Los valores State y StateDescription del trabajo se actualizan como se indica a continuación, cuando la máquina virtual alcanza un estado protegido.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Actualice las propiedades de recuperación, como el tamaño de rol de VM y la red de Azure para asociar a las tarjetas de interfaz de red de la máquina virtual tras la conmutación por error.

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
1. Ejecute un trabajo de conmutación por error de prueba, como sigue:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Compruebe que la prueba de la máquina virtual se crea en Azure. (El trabajo de conmutación por error de prueba está suspendido, después de crear la máquina virtual de prueba en Azure. El trabajo se completa limpiando los artefactos creados al reanudar el trabajo, como se muestra en el paso siguiente).
3. Ejecute la conmutación por error de prueba de la manera siguiente:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Pasos siguientes
[Más información](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre los cmdlets de PowerShell de Azure Site Recovery con Azure Resource Manager.
