<properties
	pageTitle="Plantilla ARM de servicios de dominio de Active Directory de alta disponibilidad | Microsoft Azure"
	description="Implemente fácilmente dos servidores que actúan como controladores de dominio de Dominio de Active Directory con una plantilla de administrador de recursos y el Portal de vista previa de Azure, Azure PowerShell o la CLI de Azure."
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
	ms.date="06/29/2015"
	ms.author="davidmu"/>


# Implementar un dominio de Servicios de Dominio de Active Directory de alta disponibilidad con una plantilla de Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de un recurso con el modelo de implementación del Administrador de recursos. No puede crear este recurso con el modelo de implementación clásica.

Siga las instrucciones de este artículo para implementar un dominio de Active Directory de alta disponibilidad mediante una plantilla de administrador de recursos. Esta plantilla crea dos máquinas virtuales en una nueva red virtual en la misma subred.

![](./media/virtual-machines-workload-template-ad-domain/two-server-ad.png)

Puede ejecutar la plantilla con el Portal de vista previa de Azure, Azure PowerShell o la CLI de Azure.

## Portal de vista previa de Azure

Para implementar esta carga de trabajo mediante una plantilla del Administrador de recursos y el portal de vista previa de Azure, haga clic [aquí](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-ad-domain/azure-portal-template.png)

1.	Para el panel **Plantilla**, haga clic en **Guardar**.
2.	Haga clic en **Parámetros**. En el panel **Parámetros**, escriba nuevos valores, seleccione de valores permitidos o acepte los valores predeterminados y, a continuación, haga clic en **Aceptar**.
3.	Si es necesario, haga clic en **Suscripción** y seleccione la suscripción de Azure correcta.
4.	Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente. Como alternativa, haga clic en **O crear nuevo** para crear uno nuevo para esta carga de trabajo.
5.	Si es necesario, haga clic en **Ubicación del grupo de recursos** y seleccione la ubicación correcta de Azure.
6.	Si es necesario, haga clic en **Condiciones legales** para revisar los términos y el contrato para usar la plantilla.
7.	Haga clic en **Crear**.

Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en generar la carga de trabajo. Una vez completada la ejecución de la plantilla, tendrá un nuevo dominio de Active Directory de dos servidores en el grupo de recursos nuevos o existentes.

## Azure PowerShell

Antes de comenzar, asegúrese de tener la versión correcta de Azure PowerShell instalada, de haber iniciado sesión y de haber cambiado al nuevo modo de Administrador de recursos. Para obtener detalles, haga clic [aquí](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates).

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos y una ubicación de centro de datos de Azure en el siguiente conjunto de comandos. Elimine todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aquí tiene un ejemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

A continuación, ejecute el bloque de comandos en el símbolo del sistema de Azure PowerShell.

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se le solicitará que proporcione los valores para una serie de parámetros. Cuando haya especificado todos los valores de parámetro, **New-AzureResourceGroupDeployment** crea y configura las máquinas virtuales.

Una vez completada la ejecución de la plantilla, tendrá una nueva configuración de dominio de Active Directory de dos servidores en el nuevo grupo de recursos.

## Azure CLI

Antes de comenzar, asegúrese de tener la versión correcta de la CLI de Azure instalada, de haber iniciado sesión y de haber cambiado al nuevo modo de Administrador de recursos. Para obtener detalles, haga clic [aquí](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

En primer lugar, cree un nuevo grupo de recursos. Use el siguiente comando y especifique el nombre del grupo y la ubicación del centro de datos de Azure en la que desea efectuar la implementación.

	azure group create <group name> <location>

A continuación, use el siguiente comando y especifique el nombre del nuevo grupo de recursos y el nombre de una implementación de Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json <group name> <deployment name>

Aquí tiene un ejemplo.

	azure group create adtestbed eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json adtestbed wldevtest

Al ejecutar el comando **azure group deployment create**, se le solicitará que proporcione los valores para una serie de parámetros. Cuando haya especificado todos los valores de parámetro, Azure creará y configurará las máquinas virtuales.

Una vez completada la ejecución de la plantilla, tendrá una nueva configuración de dominio de Servicios de Dominio de Active Directory de dos servidores en el nuevo grupo de recursos.


## Recursos adicionales

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](../resource-group-overview.md)

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->