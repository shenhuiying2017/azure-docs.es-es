<properties
	pageTitle="SQL Server AlwaysOn con una plantilla del Administrador de recursos de Azure | Microsoft Azure"
	description="Implemente fácilmente cinco servidores que admitan SQL Server AlwaysOn con una plantilla del Administrador de recursos y el Portal de vista previa de Azure, Azure PowerShell o la CLI de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Implementar SQL Server AlwaysOn con una plantilla de Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica. No puede crear este recurso con el modelo de implementación clásica.

Siga las instrucciones de esta sección para implementar SQL Server AlwaysOn mediante una plantilla del Administrador de recursos de Azure. Esta plantilla crea cinco máquinas virtuales en una nueva red virtual en dos subredes diferentes.

![](./media/virtual-machines-workload-template-sql-alwayson/five-server-sqlao.png)

Puede ejecutar la plantilla con el Portal de vista previa de Azure, Azure PowerShell o la CLI de Azure.

## Portal de vista previa de Azure

Para implementar esta carga de trabajo mediante una plantilla del Administrador de recursos de Azure y el Portal de vista previa de Azure, haga clic [aquí](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsql-server-2014-alwayson-dsc%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sql-alwayson/azure-portal-template.png)

1.	Para el panel **Plantilla**, haga clic en **Guardar**.
2.	Haga clic en **Parámetros**. En el panel **Parámetros**, escriba nuevos valores, seleccione de valores permitidos o acepte los valores predeterminados y, a continuación, haga clic en **Aceptar**.
3.	Si es necesario, haga clic en **Suscripción** y seleccione la suscripción de Azure correcta.
4.	Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente. Como alternativa, haga clic en **O crear nuevo** para crear uno nuevo para esta carga de trabajo.
5.	Si es necesario, haga clic en **Ubicación del grupo de recursos** y seleccione la ubicación correcta de Azure.
6.	Si es necesario, haga clic en **Condiciones legales** para revisar los términos y el contrato para usar la plantilla.
7.	Haga clic en **Crear**.

Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en generar la carga de trabajo. Una vez completada la ejecución de la plantilla, tendrá una nueva configuración de SQL Server AlwaysOn de cinco servidores en el grupo de recursos nuevo o existente.

## Azure PowerShell

Antes de comenzar, asegúrese de tener la versión correcta de Azure PowerShell instalada, de haber iniciado sesión y de haber cambiado al nuevo modo de Administrador de recursos. Para obtener detalles, haga clic [aquí](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates).

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos y una ubicación de centro de datos de Azure en el siguiente conjunto de comandos. Elimine todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aquí tiene un ejemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

A continuación, ejecute el bloque de comandos en el símbolo del sistema de Azure PowerShell.

Al ejecutar el comando **New-AzureRmResourceGroupDeployment**, se le pedirá que proporcione los valores de una serie de parámetros. Una vez especificados todos los valores de parámetro, **New-AzureRmResourceGroupDeployment** crea y configura las máquinas virtuales.

Una vez completada la ejecución de la plantilla, tendrá una nueva configuración de SQL Server AlwaysOn de cinco servidores en el nuevo grupo de recursos.

## Azure CLI

Antes de comenzar, asegúrese de tener la versión correcta de la CLI de Azure instalada, de haber iniciado sesión y de haber cambiado al nuevo modo de Administrador de recursos. Para obtener detalles, haga clic [aquí](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

En primer lugar, cree un nuevo grupo de recursos. Use el siguiente comando y especifique el nombre del grupo y la ubicación del centro de datos de Azure en la que desea efectuar la implementación.

	azure group create <group name> <location>

A continuación, use el siguiente comando y especifique el nombre del nuevo grupo de recursos y el nombre de una implementación de Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json <group name> <deployment name>

Aquí tiene un ejemplo.

	azure group create sqlao eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json sqlao sqldevtest

Al ejecutar el comando **azure group deployment create**, se le solicitará que proporcione los valores para una serie de parámetros. Cuando haya especificado todos los valores de parámetro, Azure creará y configurará las máquinas virtuales.

Una vez completada la ejecución de la plantilla, tendrá una nueva configuración de SQL Server AlwaysOn de cinco servidores en el nuevo grupo de recursos.


## Recursos adicionales

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Proveedores de proceso, red y almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](../resource-group-overview.md)

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Oct15_HO3-->