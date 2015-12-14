<properties
	pageTitle="Administrar VM del Administrador de recursos de Azure | Microsoft Azure"
	description="Administración de máquinas virtuales con el Administrador de recursos de Azure, con plantillas y con PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [PowerShell - Windows](virtual-machines-deploy-rmtemplates-powershell.md)
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

El uso de plantillas de Azure PowerShell y del Administrador de recursos te proporciona mucha eficacia y flexibilidad al administrar los recursos de Microsoft Azure. Puedes usar las tareas de este artículo para crear y administrar recursos de máquinas virtuales.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-tutorial-classic-portal.md).

Estas tareas usan una plantilla del Administrador de recursos y PowerShell:

- [de una máquina virtual](#windowsvm)
- [Creación de una máquina virtual con un disco especializado](#customvm)
- [Creación de varias máquinas virtuales en una red virtual con un equilibrador de carga externo](#multivm)

Estas tareas solo usan PowerShell:

- [Eliminación de un grupo de recursos](#removerg)
- [Visualización de información acerca de una máquina virtual](#displayvm)
- [Inicio de una máquina virtual](#start)
- [Detención de una máquina virtual](#stop)
- [Reinicio de una máquina virtual](#restart)
- [Eliminación de una máquina virtual](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Plantillas y grupos de recursos del Administrador de recursos y de Azure

En algunas de las tareas de este artículo se muestra cómo usar las plantillas del Administrador de recursos de Azure y PowerShell para implementar y administrar automáticamente las máquinas virtuales de Azure.

La mayoría de las aplicaciones que se ejecutan en Microsoft Azure están creadas a partir de una combinación de diferentes tipos de recursos en la nube, como una o más máquinas virtuales y cuentas de almacenamiento, una base de datos SQL o una red virtual. Las plantillas del Administrador de recursos de Azure te permiten administrar los distintos recursos conjuntamente mediante una descripción de JSON de los recursos y los parámetros de configuración e implementación asociados.

Después de definir una plantilla de recursos basada en JSON, la puedes usar con un comando de PowerShell para implementar los recursos definidos en Azure. Puede ejecutar estos comandos de forma independiente en el shell de comandos de PowerShell, o bien puede integrarlos con un script que contenga una lógica de automatización adicional.

Los recursos que crea con las plantillas del Administrador de recursos de Azure se implementan en un *grupo de recursos de Azure* nuevo o ya existente. Un grupo de recursos te permite administrar varios recursos implementados conjuntamente como un grupo lógico, para que puedas administrar el ciclo de vida general del grupo o de la aplicación.

Si está interesado en la creación de plantillas, vea [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).

### Crear un grupo de recursos

Si todavía no tienes un grupo de recursos, necesitarás uno para las tareas que crean un recurso.

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del nuevo grupo de recursos, y la *ubicación de Azure* por la ubicación del centro de datos de Azure donde quiera que se ubique el recurso, y ejecútelo:

	New-AzureRmResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>TAREA: Creación de una máquina virtual

Esta tarea usa una plantilla de la galería de plantillas. Para obtener más información sobre la plantilla, vea [Implementación de una máquina virtual de Windows sencilla en el oeste de EE. UU.](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

En el comando siguiente, reemplace el *nombre de la implementación* por el nombre que quiera usar para la implementación, y el *nombre del grupo de recursos* por el nombre del grupo de recursos existente, y ejecútelo:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Este es un ejemplo:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Se le pedirá que proporcione los valores de los parámetros en la sección **parameters** del archivo JSON:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

Entonces, se devuelve algo parecido a lo siguiente:

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
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>TAREA: Creación de una máquina virtual con un disco especializado

Esta tarea usa una plantilla de la galería de plantillas. Para obtener más información sobre la plantilla, vea [Creación de una máquina virtual desde un disco VHD especializado](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/).

En el comando siguiente, reemplace el *nombre de la implementación* por el nombre que quiera usar para la implementación, y el *nombre del grupo de recursos* por el nombre del grupo de recursos existente, y ejecútelo:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Este es un ejemplo:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Se le pedirá que proporcione los valores de los parámetros en la sección **parameters** del archivo JSON:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]El ejemplo anterior usa un archivo vhd que existe en la cuenta de almacenamiento saacct. El nombre del disco se proporcionó a la plantilla como un parámetro.

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>TAREA: Creación de varias máquinas virtuales en una red virtual con un equilibrador de carga externo

Esta tarea usa una plantilla de la galería de plantillas. Para obtener más información sobre la plantilla, vea [Creación de una máquina virtual desde un disco VHD especializado](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

En el comando siguiente, reemplace el *nombre de la implementación* por el nombre que quiera usar para la implementación, y el *nombre del grupo de recursos* por el nombre del grupo de recursos existente, y ejecútelo:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

Se le pedirá que proporcione los valores de los parámetros en la sección **parameters** del archivo JSON:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>TAREA: Eliminación de un grupo de recursos

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que quiera quitar, y ejecútelo:

	Remove-AzureRmResourceGroup  -Name "resource group name"

> [AZURE.NOTE]Puede usar el parámetro **–Force** para omitir la solicitud de confirmación.

Se te pedirá una confirmación si no usaste el parámetro -Force:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>TAREA: Visualización de información acerca de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Entonces, se devuelve algo parecido a lo siguiente:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>TAREA: Inicio de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Entonces, se devuelve algo parecido a lo siguiente:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>TAREA: Detención de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Se te pedirá una confirmación:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Entonces, se devuelve algo parecido a lo siguiente:

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>TAREA: Reinicio de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Entonces, se devuelve algo parecido a lo siguiente:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>TAREA: Eliminación de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]Puede usar el parámetro **–Force** para omitir la solicitud de confirmación.

Se te pedirá una confirmación si no usaste el parámetro -Force:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Entonces, se devuelve algo parecido a lo siguiente:

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## Recursos adicionales
[Plantillas de inicio rápido de Azure](http://azure.microsoft.com/documentation/templates/) y [Marcos de la aplicación](virtual-machines-app-frameworks.md)

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](resource-group-overview.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=AcomDC_1203_2015-->