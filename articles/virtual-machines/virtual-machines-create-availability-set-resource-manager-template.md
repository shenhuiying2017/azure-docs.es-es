<properties
	pageTitle="Creación de un conjunto de disponibilidad con las plantillas del Administrador de recursos de Azure"
	description="Describe cómo utilizar la plantilla del conjunto de disponibilidad e incluye la sintaxis de la plantilla"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="kathydav"/>

# Creación de un conjunto de disponibilidad con las plantillas del Administrador de recursos de Azure

Puede crear fácilmente un conjunto de disponibilidad para una máquina virtual con Azure PowerShell o la línea de comandos (CLI) de Azure y una plantilla del Administrador de recursos. Esta plantilla crea un conjunto de disponibilidad.

Antes de comenzar, asegúrese de tener Azure, PowerShell y CLI de Azure configurados y listos para usar.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## Creación de un conjunto de disponibilidad con una plantilla del Administrador de recursos

Siga estos pasos para crear una máquina virtual mediante una plantilla del Administrador de recursos en el repositorio de plantillas de Github con Azure PowerShell.

### Paso 1: Descarga del archivo JSON

Designe una carpeta local como ubicación de los archivos de la plantilla JSON y créela (por ejemplo, C:\\Azure\\Templates\\availability).

Cambie el nombre de la carpeta y, a continuación, copie y ejecute estos comandos.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

### Paso 2: Recopilación de los detalles de los parámetros obligatorios

Cuando se utiliza una plantilla, necesitará para proporcionar detalles como la ubicación y el nombre del conjunto. Para averiguar qué parámetros son necesarios para una plantilla, realice una de las acciones siguientes:

- Revise la lista de parámetros [aquí](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/).
- Abra el archivo JSON en un editor de texto o la herramienta de su elección. Busque la sección "parameters" en la parte superior del archivo, que muestra el conjunto de parámetros que necesita la plantilla para configurar la máquina virtual.

Recopile la información necesaria para tenerla preparada para escribirla. Al ejecutar el comando para implementar la plantilla, se le pedirá la información.

### Paso 3: Creación del conjunto de disponibilidad

Las secciones siguientes muestran cómo utilizar Azure PowerShell o CLI de Azure para hacerlo.

### Uso de Azure PowerShell

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure, la carpeta para el archivo JSON guardado y, a continuación, ejecute estos comandos.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se le solicitará que proporcione los valores de parámetros en la sección **"parameters"** del archivo JSON. Una vez hecho esto, el comando crea el grupo de recursos y el conjunto de disponibilidad.

Este es un ejemplo del conjunto de comandos PowerShell para la plantilla.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Verá algo parecido a lo siguiente.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Para quitar este grupo de recursos y todos sus recursos (cuenta de almacenamiento, máquina virtual y red virtual), utilice este comando.

	Remove-AzureResourceGroup –Name "<resource group name>"


## Uso de CLI de Azure

Siga estos pasos para crear el conjunto de disponibilidad mediante una plantilla del Administrador de recursos en el repositorio de Github de plantillas con los comandos CLI de Azure.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json

<!---HONumber=August15_HO6-->