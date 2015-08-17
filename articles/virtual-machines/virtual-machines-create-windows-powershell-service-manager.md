<properties
	pageTitle="Cree y administre una máquina virtual Windows en Administración de servicios con Azure."
	description="Use Azure PowerShell para crear una nueva máquina virtual basada en Windows rápidamente en la administración de servicios y realizar funciones de administración."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="kathydav"/>

# Creación y administración de una máquina virtual basada en Windows en Administración de servicios mediante Azure PowerShell

En este artículo se describe cómo crear y administrar máquinas virtuales Azure basadas en Windows en Administración de servicios mediante Azure PowerShell.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Configuración de Azure PowerShell

Si ya ha instalado Azure PowerShell, debe tener la versión 0.8.0 o posterior de Azure PowerShell. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell:

	Get-Module azure | format-table version

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md) para instalar Azure PowerShell en un equipo local. A continuación, abra un símbolo del sistema de Azure PowerShell.

En primer lugar, debe iniciar sesión en Azure mediante este comando:

	Add-AzureAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando:

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Ahora, reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre de suscripción correcto y ejecute estos comandos:

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## de una máquina virtual

Primero, necesitará una cuenta de almacenamiento. Puede mostrar la lista actual de cuentas de almacenamiento con este comando:

	Get-AzureStorageAccount | sort Label | Select Label

Si aún no tiene una, cree una nueva cuenta de almacenamiento. Debe elegir un nombre único que contenga solo letras minúsculas y números. Se puede comprobar que el nombre de cuenta de almacenamiento sea único con este comando:

	Test-AzureName -Storage <Proposed Storage account name>

Si este comando devuelve "False", el nombre propuesto es único.

Deberá especificar la ubicación de un centro de datos de Azure, al crear una cuenta de almacenamiento. Para obtener una lista de centros de datos de Azure, ejecute este comando:

	Get-AzureLocation | sort Name | Select Name

Ahora, cree y establezca la cuenta de almacenamiento mediante los comandos siguientes. Rellene los nombres de la cuenta de almacenamiento y reemplace todo el contenido entre comillas, incluidos los caracteres < and >.

	$stAccount="<chosen Storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

A continuación, necesitará un servicio en la nube. Si no tiene un servicio en la nube existente, debe crear uno. Debe elegir un nombre único que contenga solo letras, números y guiones. El primer y el último carácter del campo deben ser una letra o un número.

Por ejemplo, podría asignarle el nombre TestCS-*UniqueSequence*, en el que *UniqueSequence* es una abreviatura de su organización. Por ejemplo, si el nombre de su organización es Tailspin Toys, podría asignar el nombre del servicio en la nube Tailspin TestCS.

Se puede comprobar la exclusividad del nombre con este comando de Azure PowerShell:

	Test-AzureName -Service <Proposed cloud service name>

Si este comando devuelve "False", el nombre propuesto es único. Cree el servicio en la nube mediante los siguientes comandos:

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

A continuación, copie este conjunto de comandos de Azure PowerShell en un editor de texto, como Bloc de notas:

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

En el editor de texto, rellene el nombre de la máquina virtual, el nombre del servicio en la nube y la ubicación.

Por último, copie el conjunto de comandos en el Portapapeles y, a continuación, haga clic con el botón secundario en el símbolo del sistema del Azure PowerShell abierto. Esto emitirá el conjunto de comandos como una serie de comandos de Azure PowerShell, le solicitará el nombre y la contraseña de la cuenta de administrador local y creará la máquina virtual de Azure. Este es un ejemplo de cómo se muestra la ejecución del conjunto de comandos:

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	VERBOSE: 3:01:17 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Completed Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:23 PM - Completed Operation: Get-AzureVMImage
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
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

## Visualización de información acerca de una máquina virtual
Es una tarea básica que utilizará a menudo. Utilícelo para obtener información acerca de una máquina virtual, realizar tareas en una máquina virtual y obtener el resultado para almacenarlo en una variable.

Para obtener información acerca de la máquina virtual, ejecute este comando y reemplace todo el contenido de las comillas, incluidos los caracteres < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para almacenar la salida en una variable $vm, ejecute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Inicio de sesión en una máquina virtual Windows

Ejecute estos comandos:

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]Puede obtener el nombre de la máquina virtual y del servicio de nube en la presentación del comando **Get-AzureVM**.

## Detención de una máquina virtual

Ejecute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilice el parámetro **StayProvisioned** para mantener la IP virtual (VIP) del servicio de nube en caso de que sea la última máquina virtual en ese servicio de nube. Si usa este parámetro, se le facturará por la máquina virtual.

## Inicio de una máquina virtual

Ejecute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Acoplamiento de un disco de datos
Esta tarea requiere unos pocos pasos. En primer lugar, use el cmdlet **Add-AzureDataDisk** para agregar el disco al objeto $vm. A continuación, use el cmdlet Update-AzureVM para actualizar la configuración de la máquina virtual.

También tendrá que decidir si desea adjuntar un disco nuevo o uno que contenga los datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta en el mismo comando.

Para adjuntar un disco nuevo, ejecute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB <disk size> -DiskLabel "<label name>" -LUN <LUN number> -VM $vm | Update-AzureVM

Para adjuntar un disco de datos existente, ejecute este comando:

    Add-AzureDataDisk -Import -DiskName "<existing disk name>" -LUN <LUN number> | Update-AzureVM

Para adjuntar discos de datos desde un archivo .vhd existente en el almacenamiento de blobs, ejecute este comando:

    $diskLoc="https://mystorage.blob.core.windows.net/mycontainer/" + "<existing disk name>" + ".vhd"
	Add-AzureDataDisk -ImportFrom -MediaLocation  $diskLoc -DiskLabel "<label name>" -LUN <LUN number> | Update-AzureVM

## Recursos adicionales

[Creación de una máquina virtual Windows con el Administrador de recursos y Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

[Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=August15_HO6-->