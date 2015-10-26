<properties
	pageTitle="Proteger las máquinas virtuales de un sitio VMM en otro con PowerShell y el Administrador de recursos de Microsoft Azure"
	description="Protección automática entre dos sitios de VMM locales y Azure con PowerShell y el Administrador de recursos de Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  Sitio VMM a sitio VMM con PowerShell y el Administrador de recursos de Azure


## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Puede funcionar con dos tipos de módulos: el módulo de perfil de Azure o el módulo de Administrador de recursos de Azure (ARM).

En este artículo se describe cómo usar Windows PowerShell ® junto con ARM para implementar Azure Site Recovery para configurar y orquestar la protección de máquinas virtuales entre dos sitios VMM. Las máquinas virtuales que se ejecutan en servidores host Hyper-V que se encuentran en nubes privadas de VMM en un sitio primario se replicarán y conmutarán por error a un sitio secundario de VMM mediante Réplica de Hyper-V.

No es necesario ser un experto en PowerShell para leer este artículo, pero en él se da por hecho que conoce los conceptos básicos, como módulos, cmdlets y sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx). - Obtenga más información acerca de [Uso de Azure PowerShell con el Administrador de recursos de Azure](powershell-azure-resource-manager.md).

Este artículo incluye los requisitos previos del escenario y muestra cómo configurar Azure PowerShell para funcionar con Site Recovery, crear un almacén de Site Recovery, instalar el proveedor de Azure Site Recovery en los servidores VMM y registrarlos servidores en el almacén, configurar los valores de la protección para las nubes VMM que se aplicarán a todas las máquinas virtuales protegidas y, a continuación, habilitar la protección de dichas máquinas virtuales. Terminará comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.


## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:

- Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](pricing/free-trial/). También puede leer sobre los precios del [Administrador de Azure Site Recovery](http://azure.microsoft.com/pricing/details/site-recovery/).
- Necesitará un servidor de VMM en los sitios principales y secundarios que se ejecutan en System Center 2012 R2.
- Cada servidor VMM debe tener al menos una nube que contenga:
	- Uno o más grupos de hosts de VMM
	- Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts.
	- Una o más máquinas virtuales en el servidor de Hyper-V de origen.
	- Más información acerca de cómo configurar las nubes de VMM:

		- [Novedades de la nube privada con System Center 2012 R2 VMM](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) y en [VMM 2012 y las nubes](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
		- [Configuración del tejido de nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [Creación de una nube privada en VMM](https://technet.microsoft.com/library/jj860425.aspx) y [Tutorial: Creación de nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
- Uno o más servidores Hyper-V deben estar ejecutando al menos Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes. El servidor o el clúster debe incluirse en una nube VMM.
- Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. Para obtener instrucciones, consulte [Configuración del agente de réplicas de Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

	- Necesitará Azure PowerShell. Asegúrese de que está ejecutando la versión 0.9.6 o posterior de Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md). 
	- Al instalar Azure PowerShell también se instala una consola personalizada. Puede ejecutar los comandos de PowerShell de esta consola o de cualquier otro programa de host, como Windows PowerShell ISE.

## Paso 1: Configurar PowerShell


1. Abra una consola de PowerShell y ejecute este comando para cambiar al módulo ARM:

    `Switch-AzureMode AzureResourceManager`

3. Ahora ejecute este comando para agregar su cuenta de Azure a la sesión de PowerShell. El cmdlet pide las credenciales de inicio de sesión de su cuenta.

    `Add-AzureAccount`

	Tenga en cuenta que si usted es un socio CSP que trabaja en nombre de un inquilino, necesitará especificar al cliente como inquilino al agregar la cuenta de Azure:

    `Add-AzureAccount-Tenant "customer"`

5. Una cuenta puede tener varias suscripciones, por lo que deberá asociar la suscripción que desee usar con la cuenta.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. Si usa los cmdlets de Site Recovery por primera vez en la suscripción, deberá registrar el proveedor de Azure para Site Recovery:

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## Paso 2: Configurar el almacén de Site Recovery

1. Si actualmente todavía no dispone de un almacén de Site Recovery necesitará crear uno mediante la ejecución del cmdlet [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx):

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## Paso 3: Generación de una clave de registro de almacén

1. Ejecute el cmdlet [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) para obtener una clave de registro del almacén. Esta clave se necesita para registrar servidores VMM en el almacén:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## Paso 4: Instalación del proveedor de Azure Site Recovery

1. Descargue el archivo de instalación del proveedor de la página de Inicio rápido en el almacén de Site Recovery.
2. En cada servidor VMM, cree una carpeta con este comando:

    `pushd C:\ASR`

3. Ejecute este comando para extraer los archivos del proveedor descargado:

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. Ejecute lo siguiente para instalar el proveedor:

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. Espere a que la instalación finalice y, a continuación, registre el servidor en el almacén:

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## Paso 5: Configurar el contexto del almacén

1. Ejecute el cmdlet Get-AzureSiteRecoveryVault para asegurarse de que todos los comandos se ejecutan en el almacén específico:

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. Descargue la configuración del almacén:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. Para asegurarse de que los cmdlets se ejecutan en el almacén, ejecute:

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## Paso 3: Configuración de la protección de la nube

Una vez registrado el servidor VMM en el almacén puede configurar los valores de protección de la nube que se aplicarán a todas las máquinas virtuales de hosts de Hyper-V ubicadas en nubes en el servidor VMM registrado.

1. Cree un contenedor en el almacén para las nubes principales y secundarias:

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. Configure la configuración de protección para aplicar a las nubes:

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. Inicie un trabajo para asociar los contenedores de la nube con la configuración de protección de la nube:

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## Paso 4: Habilitar la protección de máquina virtual

Habilite la protección de máquinas virtuales en las nubes de VMM:

1. Obtenga la máquina virtual que desee proteger:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. Habilite la protección de la máquina virtual:

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## Paso 5: Ejecución de una conmutación por error de prueba

1.	Seleccione la máquina virtual que desee conmutar por error:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. Ejecute un trabajo de conmutación por error de prueba:

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. Compruebe que la máquina virtual en la que se ha realizado la conmutación por error aparece en el sitio secundario y complete la conmutación por error:

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## Pasos siguientes

Para efectuar preguntas y comentarios sobre este escenario, visite el [foro de Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)

<!---HONumber=Oct15_HO3-->