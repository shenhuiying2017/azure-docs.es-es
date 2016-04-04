<properties
	pageTitle="Replicación de máquinas virtuales de Hyper-V en nubes de VMM con Azure Site Recovery y PowerShell (Administrador de recursos) | Microsoft Azure"
	description="Replicar máquinas virtuales de Hyper-V en nubes VMM con Azure Site Recovery y PowerShell"
	services="site-recovery"
	documentationCenter=""
	authors="Rajani-Janaki-Ram"
	manager="rochakm"
	editor="raynew"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="rajanaki"/>

# Replicación de máquinas virtuales de Hyper-V de nubes de VMM en Azure con PowerShell y Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal de Azure clásico](site-recovery-vmm-to-azure.md)
- [PowerShell: clásico](site-recovery-deploy-with-powershell.md)
- [PowerShell: administrador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md) 


## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md).

En este artículo se muestra cómo usar PowerShell para automatizar tareas comunes que debe realizar al configurar Azure Site Recovery para replicar máquinas virtuales Hyper-V en nubes de VMM de System Center en el almacenamiento de Azure.

El artículo incluye los requisitos previos para el escenario y muestra

- Configuración del almacén de Servicios de recuperación
- Instalación del proveedor de Azure Site Recovery en un servidor VMM 
- Registro del servidor en el almacén e incorporación de una cuenta de almacenamiento de Azure
- Instalación del agente de Servicios de recuperación de Azure en servidores host de Hyper-V
- Configuración de las opciones de protección para nubes VMM, que se aplicarán a todas las máquinas virtuales protegidas 
- Habilite la protección para esas máquinas virtuales. 
- Compruebe la conmutación por error para asegurarse de que todo funciona según lo esperado.

Si tiene problemas al configurar este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [clásico y Administrador de recursos](../resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación del Administrador de recursos.

## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:

### Requisitos previos de Azure

- Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Si no tiene una, comience con un [cuenta gratuita](https://azure.microsoft.com/free). También puede leer sobre los precios del [Administrador de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Si prueba la replicación en un escenario de suscripción de CSP, necesitará una suscripción de CSP. Más información sobre el programa CSP en [inscripción en el programa CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Necesitará una cuenta de almacenamiento de Azure v2 (ARM) para almacenar los datos replicados en Azure. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción o a la suscripción de CSP. Para más información sobre la configuración del almacenamiento de Azure, consulte [Introducción al almacenamiento de Microsoft Azure](../storage/storage-introduction.md) como referencia.
- Tendrá que asegurarse de que las máquinas virtuales que quiere proteger cumplen los [requisitos previos de máquina virtual de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Actualmente, solo las operaciones de nivel de máquina virtual son posibles a través de Powershell. Pronto se incluirá compatibilidad con operaciones de nivel de plan de recuperación. Por ahora, está limitado a la realización de conmutaciones por error solo en un nivel de granularidad de 'máquina virtual protegida' y no en un nivel de plan de recuperación.

### Requisitos previos de VMM
- Necesitará un servidor VMM que se ejecute en System Center 2012 R2.
- Cualquier servidor VMM que contiene máquinas virtuales que desea proteger debe estar ejecutando el proveedor de Azure Site Recovery, que se instala durante la implementación de Azure Site Recovery.
- Necesitará al menos una nube en el servidor VMM que desea proteger. La nube debe contener:
	- Uno o más grupos de hosts de VMM
	- Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts
	- Una o más máquinas virtuales en el servidor de Hyper-V de origen.
- Más información acerca de cómo configurar las nubes de VMM:
	- Obtenga más información acerca de las nubes privadas de VMM en [Novedades de la nube privada con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) y en [VMM 2012 y las nubes](http://go.microsoft.com/fwlink/?LinkId=324956).
	- Obtenga información acerca de cómo [configurar el tejido de la nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric).
	- Una vez configurados los elementos del tejido de nube, aprenda a crear nubes privadas en [Creación de una nube privada en VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) y [Tutorial: Creación de nubes privadas con System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### Requisitos previos de Hyper-V

- Los servidores host de Hyper-V deben estar ejecutando al menos Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.
- Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. Para 
- Para obtener instrucciones, consulte [Configuración del agente de réplicas de Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Cualquier servidor o clúster del host de Hyper-V para el que desee administrar la protección debe incluirse en una nube de VMM.

### Requisitos previos de asignación de redes
Al proteger las máquinas virtuales en Azure la asignación de redes distribuye las redes de máquinas virtuales en las redes de Azure de destino y del servidor VMM de origen para habilitar lo siguiente:

- Todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, independientemente del plan de recuperación en el que se encuentran.
- Si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.
- Si no configura la asignación de redes, solo las máquinas virtuales que conmutan por error en el mismo plan de recuperación podrán conectarse entre sí después de la conmutación por error en Azure.

Si desea implementar la asignación de redes, necesitará lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen deben estar conectadas a una red de máquina virtual. Esa red debe estar vinculada a una red lógica asociada con la nube.
- Una red de Azure a la que puedan conectarse máquinas virtuales replicadas después de la conmutación por error. Seleccionará esta red en el momento de la conmutación por error. La red debe estar en la misma región que su suscripción de Azure Site Recovery.

Más información sobre asignación de redes en

- [Configuración de redes lógicas en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Configuración de redes de máquina virtual y puertas de enlace en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Configuración y supervisión de redes virtuales en Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###Requisitos previos de PowerShell
Asegúrese de que tiene Azure PowerShell listo para usar. Si ya usa PowerShell, necesitará actualizar a la versión 0.8.10 o posterior. Para más información sobre cómo configurar PowerShell, consulte la [Guía para instalar y configurar Azure PowerShell](../powershell-install-configure.md). Una vez que haya configurado PowerShell, puede ver todos los cmdlets disponibles para el servicio [aquí](https://msdn.microsoft.com/library/dn850420.aspx).

Para ver sugerencias que puedan ayudarle a usar los cmdlets, como, por ejemplo, cómo se controlan normalmente los valores de parámetro, las entradas y las salidas en Azure PowerShell, consulte [Guía para empezar a trabajar con cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Paso 1: Establecimiento de la suscripción 

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


## Paso 2: Creación de un almacén de Servicios de recuperación

1. Si todavía no tiene una, debe crear también un grupo de recursos de ARM.

		New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Cree un almacén de Servicios de recuperación y guarde el objeto de almacén de ASR creado en una variable (se utilizará más adelante). También puede recuperar el objeto de almacén de ASR tras la creación con el cmdlet Get-AzureRMRecoveryServicesVault:-

		$vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## Paso 3: Generación de una clave de registro de almacén

Generación de una clave de registro en el almacén. Después de descargar el proveedor de Azure Site Recovery y de instalarlo en el servidor VMM, usará esta clave para registrar el servidor VMM en el almacén.

1.	Obtenga el archivo de configuración de almacén y establezca el contexto:
	

		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault vaultname -Path #VaultSettingFilePath
	
	
2.	Establezca el contexto de almacén ejecutando los comandos siguientes:
	
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath

## Paso 4: Instalación del proveedor de Azure Site Recovery

1.	En el equipo VMM, cree un directorio ejecutando el comando siguiente:
	
		New-Item c:\ASR -type directory
		
2.	Extraiga los archivos mediante el proveedor descargado ejecutando comando siguiente
	
		pushd C:\ASR\
		.\AzureSiteRecoveryProvider.exe /x:. /q

	
3.	Instale el proveedor mediante los siguientes comandos:
	
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
	
4.	Registre el servidor en el almacén con el siguiente comando:
	
		$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
		pushd $BinPath
		$encryptionFilePath = "C:\temp".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

	
## Paso 5: Creación de una cuenta de almacenamiento de Azure

1. Si no tiene una cuenta de almacenamiento de Azure, cree una cuenta habilitada para replicación geográfica ejecutando el comando siguiente:

		$StorageAccountName = "teststorageacc1"	#StorageAccountname
		$StorageAccountGeo  = "Southeast Asia" 	
		$ResourceGroupName =  “myRG” 			#ResourceGroupName 
		$RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Tenga en cuenta que la cuenta de almacenamiento debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.

## Paso 6: Instalación del agente de los Servicios de recuperación de Azure

1. Descargue el agente de Servicios de recuperación de Azure en [http:/aka.ms/latestmarsagent](http:/aka.ms/latestmarsagent "http:/aka.ms/latestmarsagent") e instálelo en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.

2. Ejecute el siguiente comando en todos los hosts de VMM:

	marsagentinstaller.exe /q /nu

## Paso 7: Configuración de la protección de la nube

1.	Cree una directiva de replicación en Azure ejecutando el comando siguiente:

	
		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points 

		$policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.	Obtenga un contenedor de protección ejecutando los comandos siguientes:
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
  
3.	Obtenga los detalles de la directiva para una variable con el trabajo que se creó y mencionando el nombre descriptivo de la directiva:

		$policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.	Inicie la asociación del contenedor de protección con la directiva de replicación:

		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.	Cuando haya finalizado el trabajo, ejecute el siguiente comando:
   
		$job = Get-AzureRmSiteRecoveryJob -Job $associationJob
   		if($job -eq $null -or $job.StateDescription -ne "Completed")
   		 {
        $isJobLeftForProcessing = $true;
    	}

6.	Cuando haya finalizado el procesamiento, ejecute el siguiente comando:

		if($isJobLeftForProcessing)
    	{
    	Start-Sleep -Seconds 60
    	}
        }While($isJobLeftForProcessing)

Para comprobar la finalización de la operación, siga los pasos en [Supervisión de la actividad](#monitor).

## Paso 8: Configuración de la asignación de red

Antes de comenzar la asignación de red, compruebe que las máquinas virtuales en el servidor de VMM de origen están conectadas a una red de VM. Además, cree una o varias redes virtuales de Azure.

Más información sobre cómo crear una red virtual con Azure Resource Manager y PowerShell en [Creación de una red virtual con una conexión VPN de sitio a sitio mediante Azure Resource Manager y PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell/)

Tenga en cuenta que se pueden asignar varias redes de máquinas virtuales a una sola red de Azure. Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

1. El primer comando obtiene los servidores para el almacén actual de Azure Site Recovery. El comando almacena los servidores de Microsoft Azure Site Recovery en la variable de matriz $Servers.

		$Servers = Get-AzureRmSiteRecoveryServer

2. El segundo comando obtiene la red de recuperación del sitio para el primer servidor de la matriz $Servers. El comando almacena las redes en la variable $Networks.


    	$Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. El tercer comando obtiene redes virtuales de Azure y luego almacena ese valor en la variable $AzureVmNetworks.
   
		$AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. El cmdlet final crea una asignación entre la red principal y la red de la máquina virtual de Azure. El cmdlet especifica la red principal como el primer elemento de $Networks. El cmdlet especifica una red de máquina virtual como primer elemento de $AzureVmNetworks.

		New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## Paso 9: Habilitación de la protección para las máquinas virtuales

Una vez configurados correctamente los servidores, las nubes y las redes, puede habilitar la protección para las máquinas virtuales en la nube.

 Tenga en cuenta lo siguiente:

 - Las máquinas virtuales de deben cumplir los requisitos de Azure. Consúltelos en [Requisitos previos y compatibilidad](../site-recovery-best-practices) en la Guía de planeación.

 - Para habilitar la protección, deben establecerse las propiedades del sistema operativo y el disco del sistema operativo para la máquina virtual. Al crear una máquina virtual en VMM con una plantilla de máquina virtual puede establecer la propiedad. También puede establecer estas propiedades para máquinas virtuales existentes en las pestañas **General** y **Configuración del hardware** de las propiedades de la máquina virtual. Si no ve estas propiedades en VMM, podrá configurarlas en el portal de Azure Site Recovery.


  1. Para habilitar la protección, ejecute el siguiente comando para obtener el contenedor de protección:
	
			$ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
	
  2. Obtenga una entidad de protección (VM) ejecutando los comandos siguientes:
	
	 		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
		
  3. Habilite DR para VM ejecutando el comando siguiente:

			$jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## Prueba de la implementación

Para probar la implementación, puede realizar una prueba de conmutación por error para una sola máquina virtual, o bien, crear un plan de recuperación que incluya varias máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula el mecanismo de conmutación por error y recuperación en una red aislada. Observe lo siguiente:

- Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.
- Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.

Para comprobar la finalización de la operación, siga los pasos en [Supervisión de la actividad](#monitor).


### Ejecución de una conmutación por error de prueba

1.	Inicie la conmutación por error de prueba ejecutando el siguiente comando:
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### Ejecución de una conmutación por error planeada

1. Inicie la conmutación por error planeada ejecutando el comando siguiente:
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### Ejecución de una conmutación por error no planeada

1. Inicie la conmutación por error planeada ejecutando el comando siguiente:
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

	
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



## Pasos siguientes

[Más información](https://msdn.microsoft.com/library/dn850420.aspx) sobre cmdlets de PowerShell de Azure Site Recovery</a>.

<!---HONumber=AcomDC_0323_2016-->