<properties 
	pageTitle="Creación de una máquina virtual Windows con PowerShell y el Administrador de servicios de Azure" 
	description="Uso de Azure PowerShell para crear rápidamente una nueva máquina virtual Windows." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Creación de una máquina virtual Windows con PowerShell y el Administrador de servicios de Azure

Si ya ha instalado Azure PowerShell, debe tener la versión 0.8.0 o posterior de Azure PowerShell. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

	Get-Module azure | format-table version

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](install-configure-powershell.md) para instalar Azure PowerShell en un equipo local. A continuación, abra un símbolo del sistema de Azure PowerShell.

En primer lugar, debe iniciar sesión en Azure con este comando.

	Add-AzureAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Ahora, reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre de suscripción correcto y ejecute estos comandos.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

A continuación, necesitará una cuenta de almacenamiento. Puede mostrar la lista actual de cuentas de almacenamiento con este comando.

	Get-AzureStorageAccount | sort Label | Select Label

Si aún no tiene una, cree una nueva cuenta de almacenamiento. Debe elegir un nombre único que contenga solo letras minúsculas y números. Se puede comprobar que el nombre de cuenta de almacenamiento sea único con este comando.

	Test-AzureName -Storage <Proposed storage account name>

Si este comando devuelve "False", el nombre propuesto es único. Deberá especificar la ubicación de un centro de datos de Azure, al crear una cuenta de almacenamiento. Para obtener una lista de centros de datos de Azure, ejecute este comando.

	Get-AzureLocation | sort Name | Select Name

Ahora, cree y establezca la cuenta de almacenamiento con estos comandos. Rellene los nombres de la cuenta de almacenamiento y reemplace todo el contenido entre comillas, incluidos los caracteres < and >.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

A continuación, necesitará un servicio en la nube. Si no tiene un servicio en la nube existente, debe crear uno. Debe elegir un nombre único que contenga solo letras, números y guiones. El primer y el último carácter del campo deben ser una letra o un número.

Por ejemplo, podría asignarle el nombre TestCS-\* UniqueSequence \*, en el que *UniqueSequence* es una abreviatura de su organización. Por ejemplo, si el nombre de su organización es Tailspin Toys, podría asignar el nombre del servicio en la nube Tailspin TestCS.

Se puede comprobar la exclusividad del nombre con el siguiente comando de Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Si este comando devuelve "False", el nombre propuesto es único. Cree el servicio en la nube con estos comandos.

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

A continuación, copie el siguiente conjunto de comandos de PowerShell en un editor de texto como Bloc de notas.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

En el editor de texto, rellene el nombre de la máquina virtual, el nombre del servicio en la nube y la ubicación.

Por último, copie el conjunto de comandos en el Portapapeles y, a continuación, haga clic con el botón secundario en el símbolo del sistema del Azure PowerShell abierto. Esto emitirá el conjunto de comandos como una serie de comandos de PowerShell, le solicitará el nombre y la contraseña de la cuenta de administrador local y creará la máquina virtual de Azure. Este es un ejemplo de cómo se muestra la ejecución del conjunto de comandos.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	

## Recursos adicionales

[Creación de una máquina virtual Windows con el Administrador de recursos de Azure y PowerShell](virtual-machines-create-windows-powershell-rm.md)

[Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell](virtual-machines-create-windows-powershell-rm-template-simple.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](install-configure-powershell.md)

[Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=52-->
