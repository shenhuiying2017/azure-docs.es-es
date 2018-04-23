---
title: Replicación de máquinas virtuales de Hyper-V en nubes de Virtual Machine Manager en un sitio secundario con PowerShell (Azure Resource Manager) | Microsoft Docs
description: Describe la replicación de máquinas virtuales de Hyper-V en nubes de Virtual Machine Manager en un sitio de Virtual Machine Manager secundario mediante PowerShell (Resource Manager)
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: 7c6af1b63d9e7904f5a397200c6950c62df08832
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Replicación de máquinas virtuales de Hyper-V en un sitio secundario con PowerShell (Resource Manager)

En este artículo se explica cómo automatizar los pasos necesarios para la replicación de máquinas virtuales de Hyper-V en nubes de System Center Virtual Machine Manager a una nube de Virtual Machine Manager en un sitio local secundario con [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>requisitos previos

- Revise la [arquitectura del escenario y sus componentes](hyper-v-vmm-architecture.md).
- Revise los [requisitos de compatibilidad](site-recovery-support-matrix-to-sec-site.md) de todos los componentes.
- Asegúrese de que los servidores de Virtual Machine Manager y los hosts de Hyper-V cumplan los [requisitos de compatibilidad](site-recovery-support-matrix-to-sec-site.md).
- Compruebe que las máquinas virtuales que desea replicar cumplen con la [compatibilidad con máquinas replicadas](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Preparar la asignación de red

La [asignación de red](hyper-v-vmm-network-mapping.md) funciona entre redes de máquinas virtuales de Virtual Machine Manager locales en nubes de origen y nubes de destino. La asignación hace lo siguiente:

- Conecta las máquinas virtuales a las redes de máquinas virtuales de destino después de la conmutación por error. 
- Ubica de manera óptima las máquinas virtuales de réplica en servidores de host Hyper-V de destino. 
- Si no configura la asignación de red, las máquinas virtuales de réplica no se conectarán a ninguna red de máquina virtual después de la conmutación por error.

Prepare Virtual Machine Manager como sigue:

* Asegúrese de que las [redes lógicas de Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) estén en los servidores de Virtual Machine Manager de origen y de destino:

    - La red lógica del servidor de origen se debe asociar con la nube de origen en la que se encuentran los hosts Hyper-V.
    - La red lógica del servidor de destino se debe asociar con la nube de destino.
* Asegúrese de que las [redes de VM](https://docs.microsoft.com/system-center/vmm/network-virtual) estén en los servidores de Virtual Machine Manager de origen y de destino. Las redes de máquina virtual se deben vincular a la red lógica de cada ubicación.
* Conecte máquinas virtuales en los host Hyper-V de origen a la red de máquina virtual de origen. 

## <a name="prepare-for-powershell"></a>Preparación para PowerShell

Asegúrese de que tiene Azure PowerShell listo para usar:

- Si ya usa PowerShell, actualice a la versión 0.8.10 o posterior. [Más información](/powershell/azureps-cmdlets-docs) sobre cómo configurar PowerShell.
- Una vez haya instalado y configurado PowerShell, revise los [cmdlets del servicio](/powershell/azure/overview).
- Para obtener más información sobre cómo usar los valores de parámetros, las entradas y las salidas de PowerShell, lea la guía de [introducción](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configuración de una suscripción
1. En PowerShell, inicie sesión en la cuenta de Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzureRmAccount #-Credential $Cred
2. Recupere una lista de sus suscripciones con los identificadores de suscripción. Tome nota del identificador de la suscripción en la que desea crear el almacén de Recovery Services. 

        Get-AzureRmSubscription
3. Establezca la suscripción para el almacén.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services
1. Cree un grupo de recursos de Azure Resource Manager si no dispone de uno.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Cree un almacén de Recovery Services. Guarde el objeto de almacén en una variable para usarlo más adelante. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Tras la creación, puede recuperar el objeto del almacén mediante el cmdlet Get-AzureRMRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Establecer el contexto de almacén
1. Recupere un almacén existente.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Establezca el contexto de almacén.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalación del proveedor de Site Recovery
1. En el equipo de Virtual Machine Manager, cree un directorio mediante la ejecución del comando siguiente:

       New-Item c:\ASR -type directory
2. Extraiga los archivos mediante el archivo de instalación del proveedor descargado.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale el proveedor y espere a que finalice la instalación.

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

4. Registre el servidor en el almacén.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Creación y asociación de una directiva de replicación
1. Cree una directiva de replicación (en este caso para Hyper-V 2012 R2) como se indica a continuación:

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
    > La nube de Virtual Machine Manager puede contener hosts de Hyper-V que ejecuten versiones distintas de Windows Server, pero la directiva de replicación es para una versión específica de un sistema operativo. Si tiene diferentes hosts que ejecutan diferentes sistemas operativos, cree directivas de replicación independientes para cada sistema. Por ejemplo, si tiene cinco hosts que ejecutan Windows Server 2012 y tres que ejecutan Windows Server 2012 R2, cree dos directivas de replicación; una para cada tipo de sistema operativo.

2. Obtenga el contenedor de protección principal (nube de Virtual Machine Manager principal) y el de recuperación (nube de Virtual Machine Manager de recuperación).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Recupere la directiva de replicación que creó, utilizando el nombre descriptivo.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Inicie la asociación del contenedor de protección (nube de Virtual Machine Manager) con la directiva de replicación.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Espere a que finalice el trabajo de asociación de directivas. Para comprobar si el trabajo ha terminado, use el siguiente fragmento de código de PowerShell:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Cuando haya finalizado el procesamiento, ejecute el siguiente comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para comprobar la finalización de la operación, siga los pasos descritos en [Supervisión de la actividad](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar asignación de red
1. Utilice este comando para recuperar los servidores del almacén actual. El comando almacena los servidores de Site Recovery en la variable de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Ejecute este comando para recuperar las redes para los servidores de origen y destino de Virtual Machine Manager.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > El servidor de Virtual Machine Manager de origen puede ser el primero o el segundo en la matriz de servidores. Compruebe los nombres de servidor de Virtual Machine Manager y recupere las redes correspondientes.


3. Este cmdlet crea una asignación entre la red principal y la de recuperación. Especifica la red principal como el primer elemento de $PrimaryNetworks. Especifica la red de recuperación como el primer elemento de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Habilitación de la protección de las máquinas virtuales
Una vez configurados correctamente los servidores, las nubes y las redes, habilite la protección de las máquinas virtuales en la nube.

1. Para habilitar la protección, ejecute el siguiente comando para recuperar el contenedor de protección:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenga la entidad de protección (máquina virtual) de la siguiente manera:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Habilite la replicación de la máquina virtual.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

Para probar la implementación, ejecute una conmutación por error de prueba para una única máquina virtual. También puede crear un plan de recuperación que contenga varias máquinas virtuales y ejecutar una conmutación por error de prueba del plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

1. Recupere la máquina virtual en la que conmutarán por error las máquinas virtuales.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2. Realización de una conmutación por error de prueba.

   Para una sola máquina virtual:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Para un plan de recuperación:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Para comprobar la finalización de la operación, siga los pasos descritos en [Supervisión de la actividad](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Ejecución de conmutaciones por error planeadas y no planeadas

1. Realización de una conmutación por error planeada.

   Para una sola máquina virtual:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Para un plan de recuperación:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Realización de una conmutación por error no planeada.

   Para una sola máquina virtual:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Para un plan de recuperación:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Supervisión de la actividad
Utilice los comandos siguientes para supervisar la actividad de conmutación por error. Espere a que el procesamiento finalice entre los trabajos.

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

[Más información](/powershell/module/azurerm.recoveryservices.backup/#recovery) sobre Site Recovery con los cmdlets de PowerShell de Resource Manager.
