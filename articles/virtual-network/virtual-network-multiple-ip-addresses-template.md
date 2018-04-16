---
title: "Varias direcciones IP para máquinas virtuales de Azure - Plantilla | Microsoft Azure"
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con una plantilla de Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Asignación de varias direcciones IP a máquinas virtuales con una plantilla de Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

En este artículo se describe cómo crear una máquina virtual con el modelo de implementación de Azure Resource Manager mediante una plantilla de Resource Manager. No se pueden asignar varias direcciones IP públicas y privadas a la misma NIC al implementar una máquina virtual a través del modelo de implementación clásica. Para información acerca de los modelos de implementación de Azure, lea el artículo [Understand deployment models](../resource-manager-deployment-model.md) (Descripción de los modelos de implementación).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Descripción de la plantilla

La implementación de una plantilla le permite crear rápida y coherentemente recursos de Azure con valores de configuración diferentes. Lea el artículo [Tutorial de la plantilla de Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) si no está familiarizado con las plantillas de Azure Resource Manager. La plantilla [Implementación de una máquina virtual con varias direcciones IP](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) se utiliza en este artículo.

<a name="resources"></a>La implementación de la plantilla crea los siguientes recursos:

|Recurso|Nombre|Descripción|
|---|---|---|
|Interfaz de red|*myNic1*|Las tres configuraciones IP que se describe en la sección de escenario de este artículo se crean y se asignan a esta NIC.|
|Recurso de direcciones IP públicas|Se crean dos: *myPublicIP* y *myPublicIP2*|Estos recursos son direcciones IP públicas estáticas asignadas y se asignan a las configuraciones IP *IPConfig-1* y *2 IPConfig* descritas en el escenario.|
|máquina virtual|*myVM1*|Una máquina virtual DS3 estándar.|
|Red virtual|*myVNet1*|Una red virtual con una subred con el nombre *mySubnet*.|
|Cuenta de almacenamiento|Único en la implementación|Una cuenta de almacenamiento.|

<a name="parameters"></a>Al implementar la plantilla, debe especificar los valores para los parámetros siguientes:

|Nombre|Descripción|
|---|---|
|adminUsername|Nombre de usuario de administrador. El nombre de usuario debe cumplir con los [requisitos de nombre de usuario de Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Contraseña de administrador: la contraseña de administrador debe cumplir con los [requisitos de contraseña de Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Nombre DNS para PublicIPAddressName1. El nombre DNS se resolverá como una de las direcciones IP públicas asignadas a la máquina virtual. El nombre debe ser único dentro de la región de Azure (ubicación) en la que crea la máquina virtual.|
|dnsLabelPrefix1|Nombre DNS para PublicIPAddressName2. El nombre DNS se resolverá como una de las direcciones IP públicas asignadas a la máquina virtual. El nombre debe ser único dentro de la región de Azure (ubicación) en la que crea la máquina virtual.|
|OSVersion|La versión de Windows o Linux para la máquina virtual. El sistema operativo es una imagen totalmente revisada de la versión de Windows o Linux determinada seleccionada.|
|imagePublisher|El publicador de la imagen de Windows o Linux para la máquina virtual seleccionada.|
|imageOffer|La imagen de Windows o Linux para la máquina virtual seleccionada.|

Cada uno de los recursos implementados por la plantilla se configura con varias configuraciones predeterminadas. Puede ver estos valores a través de cualquiera de los métodos siguientes:

- **Ver la plantilla en GitHub:** si está familiarizado con las plantillas, puede ver la configuración dentro de la [plantilla](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Ver la configuración después de implementar:** si no está familiarizado con las plantillas, puede implementar la plantilla mediante pasos de una de las secciones siguientes y, a continuación, ver la configuración después de la implementación.

Puede usar Azure Portal, PowerShell o la interfaz de la línea de comandos (CLI) de Azure para implementar la plantilla. Todos los métodos generan el mismo resultado. Para implementar la plantilla, complete los pasos en una de las siguientes secciones:

## <a name="deploy-using-the-azure-portal"></a>Implementación mediante Azure Portal

Para implementar la plantilla mediante Azure Portal, complete los siguientes pasos:

1. Modifique la plantilla, si lo desea. La plantilla implementa los recursos y la configuración que aparece en la sección [recursos](#resources) de este artículo. Para obtener más información sobre las plantillas y cómo crearlas, lea el artículo [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Implemente la plantilla con uno de los métodos siguientes:
    - **Seleccionar la plantilla en el portal:** complete los pasos descritos en el artículo [Implementación de recursos desde plantilla personalizada](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template). Elija la plantilla preexistente denominada *101-vm-varios-ipconfig*.
    - **Directamente:** haga clic en el botón siguiente para abrir la plantilla directamente en el portal:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Independientemente del método que elija, deberá suministrar valores para los [parámetros](#parameters) enumerados anteriormente en este artículo. Una vez que se implemente la máquina virtual, conéctese a ella y agregue las direcciones IP privadas al sistema operativo que implementó completando los pasos en la sección [Incorporación de direcciones IP a un sistema operativo de la máquina virtual](#os-config) de este artículo. No agregue las direcciones IP públicas al sistema operativo.

## <a name="deploy-using-powershell"></a>Implementación mediante PowerShell

Para implementar la plantilla mediante PowerShell, complete los siguientes pasos:

1. Implemente la plantilla siguiendo los pasos descritos en el artículo [Implementación de una plantilla con PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md). El artículo describe varias opciones para la implementación de una plantilla. Si decide implementar con `-TemplateUri parameter`, e identificador URI de esta plantilla es *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Si decide implementar con el parámetro `-TemplateFile`, copie el contenido del [archivo de plantilla](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) desde GitHub en un nuevo archivo en su equipo. Modifique el contenido de la plantilla, si lo desea. La plantilla implementa los recursos y la configuración que aparece en la sección [recursos](#resources) de este artículo. Para más información sobre las plantillas y cómo crearlas, lea el artículo [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Independientemente de la opción que elija para la implementación de la plantilla, debe proporcionar valores para los valores de parámetro que se enumeran en la sección [parámetros](#parameters) de este artículo. Si decide proporcionar parámetros mediante un archivo de parámetros, copie el contenido del [archivo de parámetros](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) de GitHub en un nuevo archivo en el equipo. Modifique los valores en el archivo. Use el archivo que creó como valor para el parámetro `-TemplateParameterFile`.

    Para determinar los valores válidos para los parámetros de OSVersion, ImagePublisher e imageOffer, complete los pasos del artículo sobre [navegación y selección de imágenes de la máquina virtual de Windows](../virtual-machines/windows/cli-ps-findimage.md).

    >[!TIP]
    >Si no está seguro de si dnslabelprefix está disponible, escriba el comando `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` para averiguarlo. Si está disponible, el comando devolverá `True`.

2. Una vez que se implemente la máquina virtual, conéctese a ella y agregue las direcciones IP privadas al sistema operativo que implementó completando los pasos en la sección [Incorporación de direcciones IP a un sistema operativo de la máquina virtual](#os-config) de este artículo. No agregue las direcciones IP públicas al sistema operativo.

## <a name="deploy-using-the-azure-cli"></a>Implementación mediante la CLI de Azure

Para implementar la plantilla mediante la CLI de Azure 1.0, complete los siguientes pasos:

1. Implemente la plantilla siguiendo los pasos descritos en el artículo [Implementación de una plantilla con la CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md). El artículo describe varias opciones para la implementación de una plantilla. Si decide implementar con `--template-uri` (-f), e identificador URI de esta plantilla es *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Si decide implementar con el parámetro `--template-file` (-f), copie el contenido del [archivo de plantilla](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) desde GitHub en un nuevo archivo en su equipo. Modifique el contenido de la plantilla, si lo desea. La plantilla implementa los recursos y la configuración que aparece en la sección [recursos](#resources) de este artículo. Para más información sobre las plantillas y cómo crearlas, lea el artículo [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Independientemente de la opción que elija para la implementación de la plantilla, debe proporcionar valores para los valores de parámetro que se enumeran en la sección [parámetros](#parameters) de este artículo. Si decide proporcionar parámetros mediante un archivo de parámetros, copie el contenido del [archivo de parámetros](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) de GitHub en un nuevo archivo en el equipo. Modifique los valores en el archivo. Use el archivo que creó como valor para el parámetro `--parameters-file` (-e).

    Para determinar los valores válidos para los parámetros de OSVersion, ImagePublisher e imageOffer, complete los pasos del artículo sobre [navegación y selección de imágenes de la máquina virtual de Windows](../virtual-machines/windows/cli-ps-findimage.md).

2. Una vez que se implemente la máquina virtual, conéctese a ella y agregue las direcciones IP privadas al sistema operativo que implementó completando los pasos en la sección [Incorporación de direcciones IP a un sistema operativo de la máquina virtual](#os-config) de este artículo. No agregue las direcciones IP públicas al sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
