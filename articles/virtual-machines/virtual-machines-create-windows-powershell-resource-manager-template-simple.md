<properties
	pageTitle="Crear una máquina virtual con la plantilla ARM | Microsoft Azure"
	description="Use una plantilla del Administrador de recursos y Azure PowerShell para crear una nueva máquina virtual Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de un recurso con el modelo de implementación del Administrador de recursos. No puede crear este recurso con el modelo de implementación clásica.

Puede crear fácilmente una nueva máquina virtual de Azure basada en Windows mediante una plantilla del Administrador de recursos con Azure PowerShell. Esta plantilla crea una sola máquina virtual que ejecuta Windows en una nueva red virtual con una sola subred en un nuevo grupo de recursos.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

Antes de profundizar, asegúrese de tener Azure y PowerShell configurados y listos para usar.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Creación de una máquina virtual Windows

Siga estos pasos para crear una máquina virtual Windows mediante una plantilla del Administrador de recursos en el repositorio de plantillas de Github con Azure PowerShell.

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos de Azure y una ubicación de centro de datos de Azure y, a continuación, ejecute estos comandos.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se le solicitará que proporcione los valores de parámetros en la sección "parameters" del archivo JSON. Cuando haya especificado todos los valores de parámetros, el comando crea el grupo de recursos y la máquina virtual.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Verá algo parecido a lo siguiente:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Ahora dispone de una nueva máquina virtual Windows denominada MyWindowsVM en el nuevo grupo de recursos.

## Recursos adicionales

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](resource-group-overview.md)

[Creación de una máquina virtual Windows con el Administrador de recursos de Azure y PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Creación de una máquina virtual Windows con PowerShell y el Administrador de servicios de Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->