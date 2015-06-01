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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Creación de un conjunto de disponibilidad con las plantillas del Administrador de recursos de Azure

Puede crear fácilmente un conjunto de disponibilidad para una máquina virtual con Azure PowerShell o xplat-cli y una plantilla del Administrador de recursos. Esta plantilla crea un conjunto de disponibilidad.
 
Antes de profundizar, asegúrese de tener Azure PowerShell y xplat-cli configurados y listos para usar.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## [Hacer algo] con una plantilla del Administrador de recursos mediante Azure PowerShell

Siga estos pasos para [hacer algo] mediante una plantilla del Administrador de recursos en el repositorio de plantillas de Github con Azure PowerShell.

### Paso 1: Descarga del archivo JSON

Designe una carpeta local como ubicación de los archivos de la plantilla JSON y créela (por ejemplo, C:\\Azure\\Templates[thing]).

Cambie el nombre de la carpeta y, a continuación, copie y ejecute estos comandos.

	$folderName="<folder name, such as C:\Azure\Templates[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Paso 2: (opcional) Visualización de los parámetros

Cuando se [hace algo] con una plantilla, debe especificar un conjunto de parámetros de configuración. Para ver los parámetros que se deben especificar para la plantilla en un archivo JSON local antes de ejecutar el comando para crear la máquina virtual, abra el archivo JSON en el editor de texto o la herramienta de su elección. Busque la sección "parameters" en la parte superior del archivo, que muestra el conjunto de parámetros que necesita la plantilla para configurar la máquina virtual. Esta es la sección **"parameters"** de la plantilla azuredeploy.json:

[Nota para los escritores: pegue la sección "parameters" de azuredeploy.json y dele formato de código.]

### Paso 3: Obtención de la [información necesaria para completar la plantilla].

[Nota para los escritores: sección opcional para recopilar valores de parámetros si es necesario.]

### Paso 4: [Realización de algo] con la plantilla.

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure, la carpeta para el archivo JSON guardado y, a continuación, ejecute estos comandos.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates[thing]>" 
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

[Nota para los escritores: pegue la visualización de PowerShell para los primeros parámetros solicitados y reemplace lo siguiente:]

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


## [Hacer algo] con una plantilla del Administrador de recursos mediante xplat-cli

Siga estos pasos para [hacer algo] mediante una plantilla del Administrador de recursos en el repositorio de plantillas de Github con comandos xplat-cli.

### Paso 1: Descarga del archivo JSON para la plantilla.

Designe una carpeta local como ubicación de los archivos de la plantilla JSON y créela (por ejemplo, C:\\Azure\\Templates[thing]).

Rellene el nombre de la carpeta y ejecute estos comandos.

[Comandos xplat para descargar el archivo de plantilla]

### Paso 2: (opcional) Visualización de los parámetros de plantilla.

Cuando se [hace algo] con una plantilla, debe especificar un conjunto de parámetros de configuración. Para ver los parámetros que se deben especificar para la plantilla en un archivo JSON local antes de ejecutar el comando para crear la máquina virtual, abra el archivo JSON en el editor de texto o la herramienta de su elección. Busque la sección "parameters" en la parte superior del archivo, que muestra el conjunto de parámetros que necesita la plantilla para configurar la máquina virtual. Esta es la sección **"parameters"** de la plantilla azuredeploy.json:

[Nota para los escritores: pegue la sección "parameters" de azuredeploy.json y dele formato de código.]

### Paso 3: Obtención de la [información necesaria para completar la plantilla].

[Nota para los escritores: sección opcional para recopilar valores de parámetros si es necesario.]

### Paso 4: [Realización de algo] con la plantilla.

Rellene [la información necesaria] y, a continuación, ejecute estos comandos.

[Comandos xplat para ejecutar el archivo de plantilla]

[explicación de cómo xplat ejecuta la plantilla]


Este es un ejemplo del conjunto de comandos xplat-cli para la plantilla.

[Ejemplo del comando xplat]

Verá algo parecido a lo siguiente.

[Nota para los escritores: pegue la visualización de xplat para los primeros parámetros solicitados]


Para quitar este grupo de recursos y todos sus recursos ([material del grupo de recursos]), utilice este comando.

[Comando xplat]




<!--HONumber=52-->
