<properties
	pageTitle="Crear una máquina virtual con una plantilla | Microsoft Azure"
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
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.


Puede crear fácilmente una nueva máquina virtual de Azure basada en Windows mediante una plantilla del Administrador de recursos con Azure PowerShell. Esta plantilla crea una sola máquina virtual que ejecuta Windows en una nueva red virtual con una sola subred en un nuevo grupo de recursos.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Creación de una máquina virtual Windows

Siga estos pasos para crear una máquina virtual Windows mediante una plantilla del Administrador de recursos en el repositorio de plantillas de Github con Azure PowerShell.

En los siguientes comandos, rellene un nombre que desee usar para la implementación, un nombre para el nuevo grupo de recursos y la ubicación en la que se debe crear el recurso.


		$deployName="<deployment name>"
		$RGName="<resource group name>"
		$locName="<Azure location, such as West US>"
		$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
		New-AzureRmResourceGroup –Name $RGName –Location $locName
		New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Al ejecutar el comando **New-AzureRmResourceGroupDeployment**, se le solicitará que proporcione los valores de los parámetros en la sección "parameters" del archivo JSON. Cuando haya especificado todos los valores de parámetros, el comando crea el grupo de recursos y la máquina virtual.

Puede ver algo así:


		cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
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
											Name                   Type                       Value
											===============        =========================  ==========
											newStorageAccountName  String                     newsaacct
											adminUsername          String                     WinAdmin1
											adminPassword          SecureString
											dnsNameForPublicIP     String                     contoso
											windowsOSVersion       String                     2012-R2-Datacenter
		Outputs           :


## Pasos siguientes

Aprenda a administrar la máquina virtual que acaba de crear. Para ello, consulte [Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0114_2016-->