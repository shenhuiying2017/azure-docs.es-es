---
title: "Replicación de máquinas virtuales de Hyper-V en VMM en un sitio secundario con PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "En este artículo se describe cómo implementar Azure Site Recovery para organizar la replicación, la conmutación por error y la recuperación de máquinas virtuales Hyper-V de nubes VMM en un sitio VMM secundario mediante PowerShell (Resource Manager)."
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: sutalasi
ms.openlocfilehash: c978c2e31e775f56824d765491f6d7b73648b8ae
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Replicación de máquinas virtuales Hyper-V de nubes VMM en un sitio VMM secundario mediante PowerShell (Resource Manager)

En este artículo se muestra cómo usar PowerShell para automatizar las tareas comunes que debe realizar al configurar Azure Site Recovery para replicar máquinas virtuales Hyper-V de nubes VMM de System Center a nubes VMM de System Center de un sitio secundario.



## <a name="on-premises-prerequisites"></a>Requisitos previos locales
Esto es lo que necesita tener en los sitios locales principal y secundario para implementar este escenario:

| **Requisitos previos** | **Detalles** |
| --- | --- |
| **VMM** |Se recomienda implementar un servidor VMM en el sitio principal y otro del mismo tipo en el secundario.<br/><br/> También puede llevar a cabo una replicación entre nubes en un solo servidor VMM. Para ello, necesitará, al menos, dos nubes configuradas en el servidor VMM.<br/><br/> Los servidores VMM deben estar ejecutando, como mínimo, System Center 2012 SP1 con las actualizaciones más recientes.<br/><br/> Cada servidor VMM debe tener una o más nubes configuradas y todas las nubes deben tener definido el perfil de capacidad de Hyper-V. <br/><br/>Las nubes deben incluir uno o más grupos de hosts de VMM. Los servidores VMM deben tener acceso a Internet. |
| **Hyper-V** |Los servidores Hyper-V deben estar ejecutando, como mínimo, Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> Un servidor de Hyper-V debe contener una o varias máquinas virtuales.<br/><br/>  Los servidores host de Hyper-V deben estar ubicados en grupos host de las nubes de VMM principal y secundaria.<br/><br/> Si está ejecutando Hyper-V en un clúster en Windows Server 2012 R2, debe instalar la [actualización 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si va a ejecutar Hyper-V en un clúster de Windows Server 2012, tenga en cuenta que el agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. [Más información](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Proveedor** |Durante la implementación de Site Recovery, se instala el proveedor de Azure Site Recovery en los servidores VMM. El proveedor se comunica con Site Recovery mediante HTTPS 443 para organizar la replicación. La replicación de datos se produce entre los servidores Hyper-V principal y secundario mediante la conexión LAN o VPN.<br/><br/> El proveedor que se ejecuta en el servidor VMM necesita acceso a estas direcciones URL: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net.<br/><br/> Además, hay que permitir tanto la comunicación del firewall entre los servidores VMM y los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) como el protocolo HTTPS (443). |

### <a name="network-mapping-prerequisites"></a>Requisitos previos de asignación de redes
La asignación de red se realiza entre las redes de máquinas virtuales VMM que se encuentran en los servidores VMM principal y secundario con el fin de realizar lo siguiente:

* Colocar óptimamente las máquinas virtuales de réplica en los hosts de Hyper-V secundarios tras la conmutación por error.
* Conectar las máquinas virtuales de réplica a las redes VM adecuadas.
* Si no configura la asignación de red, las máquinas virtuales de réplica no se conectarán a ninguna red tras la conmutación por error.
* Si quiere configurar la asignación de red durante la implementación de Site Recovery, esto es lo que debe hacer:

  * Asegúrese de que las máquinas virtuales del servidor host de Hyper-V de origen estén conectadas a una red de máquinas virtuales de VMM. Esa red debe estar vinculada a una red lógica asociada con la nube.
  * Compruebe que la nube secundaria que se va a utilizar para la recuperación tenga configurada una red de VM correspondiente. Dicha red de máquina virtual debe estar vinculada a una red lógica asociada con la nube secundaria.

Puede encontrar información sobre cómo configurar redes VMM en los siguientes artículos:

* [Configuración de redes lógicas en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Configuración de redes de máquina virtual y puertas de enlace en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)


### <a name="powershell-prerequisites"></a>Requisitos previos de PowerShell
Asegúrese de que tiene Azure PowerShell listo para usar. Si ya usa PowerShell, necesitará actualizar a la versión 0.8.10 o posterior. Para obtener más información sobre cómo configurar PowerShell, lea la [guía para instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs). Una vez que haya configurado PowerShell, puede ver todos los cmdlets disponibles para el servicio [aquí](/powershell/azure/overview).

Para ver sugerencias que puedan ayudarlo a usar los cmdlets —por ejemplo, cómo se controlan normalmente los valores de parámetro, las entradas y las salidas en Azure PowerShell—, lea la [guía de introducción a los cmdlets de Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Paso 1: Establecimiento de la suscripción
1. Desde Azure PowerShell, inicie sesión en la cuenta de Azure mediante los siguientes cmdlets

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Obtenga una lista de las suscripciones. También mostrará los identificadores de suscripción de cada una de las suscripciones. Anotación del identificador de suscripción de la suscripción en la que desea crear el almacén de Servicios de recuperación    

        Get-AzureRmSubscription
3. Establecimiento de la suscripción en la que se va a crear el almacén de Servicios de recuperación mediante la mención del identificador de suscripción

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Paso 2: Creación de un almacén de Servicios de recuperación
1. Cree un grupo de recursos de Azure Resource Manager si todavía no lo ha hecho.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Cree un almacén de Recovery Services y guarde el objeto de almacén de Recovery Services en una variable (se utilizará más adelante). Tras la creación, puede recuperar el objeto de almacén mediante el cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Paso 3: Configuración del contexto de almacén de Servicios de recuperación
1. Si ya ha creado un almacén, ejecute el siguiente comando para obtenerlo.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Establezca el contexto de almacén mediante la ejecución del comando siguiente.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Paso 4: Instalación del proveedor de Azure Site Recovery
1. En el equipo VMM, cree un directorio ejecutando el comando siguiente:

       New-Item c:\ASR -type directory
2. Extraiga los archivos mediante el proveedor descargado ejecutando comando siguiente

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale el proveedor mediante los siguientes comandos:

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   Espere hasta que la instalación se complete.
4. Registre el servidor en el almacén con el siguiente comando:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>Paso 5: Creación y asociación de una directiva de replicación
1. Cree una directiva de replicación en Hyper-V 2012 R2 ejecutando el comando siguiente:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > La nube VMM puede contener hosts Hyper-V que ejecuten versiones distintas de Windows Server (tal y como se mencionó en los requisitos previos de Hyper-V), pero la directiva de replicación es específica de la versión del sistema operativo. Si tiene diferentes hosts que ejecutan diferentes versiones del sistema operativo, cree directivas de replicación independientes para cada tipo de versión del sistema operativo. Por ejemplo, si tiene cinco hosts que se ejecutan en Windows Server 2012 y tres que se ejecutan en Windows Server 2012 R2, cree dos directivas de replicación; una para cada tipo de versión del sistema operativo.

1. Obtenga el contenedor de protección principal (nube VMM principal) y el de recuperación (nube VMM de recuperación) ejecutando los siguientes comandos:

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Recupere la directiva que creó en el paso 1 usando su nombre descriptivo.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Inicie la asociación del contenedor de protección (nube VMM) con la directiva de replicación:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Espere a que finalice el trabajo de asociación de directivas. Puede comprobar si se ha completado el trabajo ejecutando el siguiente fragmento de PowerShell.

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   Cuando haya finalizado el procesamiento, ejecute el siguiente comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para comprobar la finalización de la operación, siga los pasos en [Supervisión de la actividad](#monitor).

## <a name="step-6-configure-network-mapping"></a>Paso 6: Configuración de la asignación de red
1. El primer comando obtiene los servidores para el almacén actual de Azure Site Recovery. El comando almacena los servidores de Microsoft Azure Site Recovery en la variable de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Con los siguientes comandos se obtiene la red de Site Recovery de los servidores VMM de origen y de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > El servidor VMM de origen puede ser el primero o el segundo en la matriz de servidores. Compruebe los nombres de los servidores VMM y obtenga las redes de forma apropiada.


1. El cmdlet final crea una asignación entre la red principal y la de recuperación. El cmdlet especifica la red principal como primer elemento de $PrimaryNetworks y la de recuperación como primer elemento de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>Paso 7: Configuración de la asignación de almacenamiento
1. El comando siguiente introduce la lista de clasificaciones de almacenamiento en la variable $storageclassifications.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. Los siguientes comandos introducen la clasificación de origen en la variable $SourceClassification y la de destino en $TargetClassification.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > Las clasificaciones de origen y de destino pueden ser cualquier elemento de la matriz. Consulte el resultado del siguiente comando para calcular el índice de clasificaciones de origen y destino de la matriz $storageclassifications.

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object -Property FriendlyName, Id | Format-Table


1. El siguiente cmdlet crea una asignación entre la clasificación de origen y la de destino.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>Paso 8: Habilitación de la protección para las máquinas virtuales
Una vez configurados correctamente los servidores, las nubes y las redes, puede habilitar la protección para las máquinas virtuales en la nube.

1. Para habilitar la protección, ejecute el siguiente comando para obtener el contenedor de protección:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenga una entidad de protección (VM) ejecutando los comandos siguientes:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Habilite la replicación de la máquina virtual ejecutando el comando siguiente:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>Prueba de la implementación
Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

> [!NOTE]
> Puede crear un plan de recuperación para su aplicación en el Portal de Azure.
>
>

Para comprobar la finalización de la operación, siga los pasos en [Supervisión de la actividad](#monitor).

### <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba
1. Ejecute los siguientes cmdlets para obtener la red de máquina virtual en la que quiere probar la conmutación por error a sus máquinas virtuales.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Realice una conmutación por error de prueba de una máquina virtual ejecutando lo siguiente:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. Realice una conmutación por error de prueba de un plan de recuperación ejecutando lo siguiente:

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>Ejecución de una conmutación por error planeada
1. Realice una conmutación por error planeada de una máquina virtual ejecutando lo siguiente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. Realice una conmutación por error planeada de un plan de recuperación ejecutando lo siguiente:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Ejecución de una conmutación por error no planeada
1. Realice una conmutación por error no planeada de una máquina virtual ejecutando lo siguiente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Realice una conmutación por error no planeada de un plan de recuperación ejecutando lo siguiente:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a> Supervisión de la actividad
Utilice los comandos siguientes para supervisar la actividad. Tenga en cuenta que debe esperar entre los trabajos para que el procesamiento finalice.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Pasos siguientes
[Más información](/powershell/module/azurerm.recoveryservices.backup/#recovery) sobre los cmdlets de PowerShell de Azure Site Recovery con Azure Resource Manager.
