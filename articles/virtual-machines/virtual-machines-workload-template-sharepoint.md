<properties
	pageTitle="Implementación de granjas de servidores SharePoint con plantillas del ARM | Microsoft Azure"
	description="Implemente fácilmente una granja de SharePoint de tres o nueve servidores con plantillas del Administrador de recursos y el Portal de vista previa de Azure, Azure PowerShell o la CLI de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Implementación de granjas de servidores SharePoint con plantillas del Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica. No puede crear este recurso con el modelo de implementación clásica.

Siga las instrucciones de este artículo para implementar una granja de servidores SharePoint Server 2013 de tres o nueve servidores mediante plantillas de administrador de recursos.

## Implementar una granja de tres servidores de SharePoint

En una granja de servidores de SharePoint Server 2013 básica, una plantilla de administrador de recursos crea tres máquinas virtuales en una nueva red virtual en tres subredes diferentes.

![](./media/virtual-machines-workload-template-sharepoint/three-server-sharepoint-farm.png)

Puede ejecutar la plantilla con el Portal de vista previa de Azure, Azure PowerShell o la CLI de Azure.

> [AZURE.NOTE]También puede crear esta configuración mediante el elemento [Granja de SharePoint 2013 sin alta disponibilidad](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) en Azure Marketplace del Portal de vista previa de Azure.

### Portal de vista previa de Azure

Para implementar esta carga de trabajo mediante una plantilla del Administrador de recursos y el Portal de vista previa de Azure, haga clic [aquí](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Haga clic en **Parámetros**. En el panel **Parámetros**, escriba nuevos valores, seleccione de valores permitidos o acepte los valores predeterminados y, a continuación, haga clic en **Aceptar**.
2.	Si es necesario, haga clic en **Suscripción** y seleccione la suscripción de Azure correcta.
3.	Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente. Como alternativa, haga clic en **O crear nuevo** para crear uno nuevo para esta carga de trabajo.
4.	Si es necesario, haga clic en **Ubicación del grupo de recursos** y seleccione la ubicación correcta de Azure.
6.	Haga clic en **Condiciones legales** para revisar los términos y el contrato para usar la plantilla y después haga clic en **Comprar**.
7.	Haga clic en **Crear**.

Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en generar la carga de trabajo. Cuando haya finalizado, tendrá una nueva granja SharePoint de tres servidores en el grupo de recursos nuevo o existente.

### Azure PowerShell

> [AZURE.NOTE]Este artículo contiene comandos para la versión preliminar de Azure PowerShell 1.0. Para ejecutar estos comandos en Azure PowerShell 0.9.8 y versiones anteriores, reemplace **New-AzureRMResourceGroup** por **New-AzureResourceGroup**, reemplace **New-AzureRMResourceGroupDeployment** por **New-AzureResourceGroupDeployment** y agregue el comando **Switch-AzureMode AzureResourceManager** antes del comando **New-AzureResourceGroup**. Para obtener más información, consulte la [versión de vista previa de Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos y una ubicación de centro de datos de Azure en el siguiente conjunto de comandos. Elimine todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aquí tiene un ejemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

A continuación, ejecute el bloque de comandos en el símbolo del sistema de Azure PowerShell.

Al ejecutar el comando **New-AzureRMResourceGroupDeployment**, se le pedirá que proporcione los valores de una serie de parámetros. Una vez especificados todos los valores de parámetro, **New-AzureRmResourceGroupDeployment** crea y configura las máquinas virtuales.

Cuando haya finalizado la ejecución de la plantilla, tendrá una nueva granja SharePoint de tres servidores en el grupo de recursos nuevo.

### Azure CLI

Antes de comenzar, asegúrese de tener la versión correcta de la CLI de Azure instalada, de haber iniciado sesión y de haber cambiado al nuevo modo de Administrador de recursos. Para obtener detalles, haga clic [aquí](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

En primer lugar, cree un nuevo grupo de recursos. Use el siguiente comando y especifique el nombre del grupo y la ubicación del centro de datos de Azure en la que desea efectuar la implementación.

	azure group create <group name> <location>

A continuación, use el siguiente comando y especifique el nombre del nuevo grupo de recursos y el nombre de una implementación de Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json <group name> <deployment name>

Aquí tiene un ejemplo.

	azure group create sp3serverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json sp3serverfarm spdevtest

Al ejecutar el comando **azure group deployment create**, se le solicitará que proporcione los valores para una serie de parámetros. Cuando haya especificado todos los valores de parámetro, Azure creará y configurará las máquinas virtuales.

Ahora tiene una nueva granja de SharePoint de tres servidores en el nuevo grupo de recursos.

## Implementar una granja de nueve servidores de SharePoint

En una granja de servidores de SharePoint Server 2013 de alta disponibilidad, una plantilla de administrador de recursos crea nueve máquinas virtuales en una nueva red virtual en cuatro subredes diferentes.

![](./media/virtual-machines-workload-template-sharepoint/nine-server-sharepoint-farm.png)

> [AZURE.NOTE]También puede crear esta configuración mediante el elemento [Granja de alta disponibilidad de SharePoint 2013](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) en Azure Marketplace del Portal de vista previa de Azure.

### Portal de vista previa de Azure

Para implementar esta carga de trabajo mediante una plantilla del Administrador de recursos y el Portal de vista previa de Azure, haga clic [aquí](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-server-farm-ha%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Haga clic en **Parámetros**. En el panel **Parámetros**, escriba nuevos valores, seleccione de valores permitidos o acepte los valores predeterminados y, a continuación, haga clic en **Aceptar**.
2.	Si es necesario, haga clic en **Suscripción** y seleccione la suscripción de Azure correcta.
3.	Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente. Como alternativa, haga clic en **O crear nuevo** para crear uno nuevo para esta carga de trabajo.
4.	Si es necesario, haga clic en **Ubicación del grupo de recursos** y seleccione la ubicación correcta de Azure.
5.	Haga clic en **Condiciones legales** para revisar los términos y el contrato para usar la plantilla y después haga clic en **Comprar**.
6.	Haga clic en **Crear**.

Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en generar la carga de trabajo. Cuando haya finalizado, tendrá una nueva granja SharePoint de nueve servidores en el grupo de recursos nuevo o existente.

### Azure PowerShell

> [AZURE.NOTE]Este artículo contiene comandos para la versión preliminar de Azure PowerShell 1.0. Para ejecutar estos comandos en Azure PowerShell 0.9.8 y versiones anteriores, reemplace **New-AzureRMResourceGroup** por **New-AzureResourceGroup**, reemplace **New-AzureRMResourceGroupDeployment** por **New-AzureResourceGroupDeployment** y agregue el comando **Switch-AzureMode AzureResourceManager** antes del comando **New-AzureResourceGroup**. Para obtener más información, consulte la [versión de vista previa de Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos y una ubicación de centro de datos de Azure en el siguiente conjunto de comandos. Elimine todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aquí tiene un ejemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

A continuación, ejecute el bloque de comandos en el símbolo del sistema de Azure PowerShell.

Al ejecutar el comando **New-AzureRMResourceGroupDeployment**, se le pedirá que proporcione los valores de una serie de parámetros. Una vez especificados todos los valores de parámetro, **New-AzureRmResourceGroupDeployment** crea y configura las máquinas virtuales.

Cuando haya finalizado la ejecución de la plantilla, tendrá una nueva granja SharePoint de nueve servidores en el grupo de recursos nuevo.

### Azure CLI

Antes de comenzar, asegúrese de tener la versión correcta de la CLI de Azure instalada, de haber iniciado sesión y de haber cambiado al nuevo modo de Administrador de recursos. Para obtener detalles, haga clic [aquí](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

En primer lugar, cree un nuevo grupo de recursos. Use el siguiente comando y especifique el nombre del grupo y la ubicación del centro de datos de Azure en la que desea efectuar la implementación.

	azure group create <group name> <location>

A continuación, use el siguiente comando y especifique el nombre del nuevo grupo de recursos y el nombre de una implementación de Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json <group name> <deployment name>

Aquí tiene un ejemplo.

	azure group create sphaserverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json sphaserverfarm spdevtest

Al ejecutar el comando **azure group deployment create**, se le solicitará que proporcione los valores para una serie de parámetros. Cuando haya especificado todos los valores de parámetro, Azure creará y configurará las máquinas virtuales.

Cuando haya finalizado la ejecución de la plantilla, tendrá una nueva granja SharePoint Server 2013 de nueve servidores en el grupo de recursos nuevo.


## Recursos adicionales

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Proveedores de proceso, red y almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](../resource-group-overview.md)

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=AcomDC_1125_2015-->