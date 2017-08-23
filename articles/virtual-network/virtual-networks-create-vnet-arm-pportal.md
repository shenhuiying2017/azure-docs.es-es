---
title: "Creación de una red virtual de Azure con varias subredes | Microsoft Docs"
description: "Obtenga información sobre cómo crear una red virtual con varias subredes en Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: a31f0524a6fa1de45498f340a27b863a3c627e04
ms.contentlocale: es-es
ms.lasthandoff: 08/01/2017

---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Creación de una red virtual con varias subredes

En este tutorial, aprenderá a crear una red virtual básica de Azure con subredes públicas y privadas independientes. Puede crear recursos de Azure, como máquinas virtuales, entornos de App Service, conjuntos de escalado de máquinas virtuales, Azure HDInsight y servicios en la nube en una subred. Los recursos de las redes virtuales se pueden comunicar entre sí y con los recursos de otras redes conectadas a una red virtual.

En las secciones siguientes se incluyen los pasos que se pueden seguir para crear una red virtual mediante [Azure Portal](#portal), la interfaz de línea de comandos de Azure ([CLI de Azure](#azure-cli)), [Azure PowerShell](#powershell) y una [plantilla de Azure Resource Manager](#resource-manager-template). El resultado es el mismo, independientemente de la herramienta que use para crear la red virtual. Haga clic en un vínculo de la herramienta para ir a esa sección del tutorial. Más información sobre todos los valores de [red virtual](virtual-network-manage-network.md) y [subred](virtual-network-manage-subnet.md).

En este artículo se indican los pasos para crear una red virtual mediante el modelo de implementación de Resource Manager, que es el modelo de implementación recomendado para crear nuevas redes virtuales. Si tiene que crear una red virtual (clásica), vea [Creación de una red virtual (clásica)](create-virtual-network-classic.md). Si no está familiarizado con los modelos de implementación de Azure, vea [Understand Azure deployment models (Descripción de los modelos de implementación de Azure)](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Azure Portal

1. En un explorador de Internet, vaya a [Azure Portal](https://portal.azure.com). Inicie sesión con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En el portal, haga clic en **+Nuevo** > **Redes** > **Red virtual**.
3. En la hoja **Crear red virtual**, escriba los valores siguientes y luego haga clic en **Crear**:

    |Configuración|Valor|
    |---|---|
    |Nombre|myVnet|
    |Espacio de direcciones|10.0.0.0/16|
    |Nombre de subred|Público|
    |Intervalo de direcciones de subred|10.0.0.0/24|
    |Grupos de recursos|Deje seleccionado **Crear nuevo** y después escriba **myResourceGroup**.|
    |Suscripción y ubicación|Seleccione su suscripción y ubicación.

    Si no está familiarizado con Azure, obtenga más información sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [ubicaciones](https://azure.microsoft.com/regions) (también denominadas *regiones*).
4. En el portal, solo se puede crear una subred cuando se crea una red virtual. En este tutorial, creará una segunda subred después de crear la red virtual. Más adelante podría crear recursos accesibles desde Internet en la subred **Pública**. También podría crear recursos que no fueran accesibles desde Internet en la subred **Privada**. Para crear la segunda subred, en el cuadro **Buscar recursos** de la parte superior de la página, escriba **myVnet**. En los resultados de la búsqueda, haga clic en **myVnet**. Si tiene varias redes virtuales con el mismo nombre en la suscripción, compruebe los grupos de recursos que se muestran debajo de cada red virtual. Asegúrese de hacer clic en el resultado de búsqueda **myVnet** que tenga el grupo de recursos **myResourceGroup**.
5. En la hoja **myVnet**, en **CONFIGURACIÓN**, haga clic en **Subredes**.
6. En la hoja **myVnet - Subredes**, haga clic en **+Subred**.
7. En la hoja **Agregar subred**, escriba **Privada** en **Nombre**. Para **Intervalo de direcciones**, escriba **10.0.1.0/24**.  Haga clic en **Aceptar**.
8. Revise las subredes en la hoja **myVnet - Subredes**. Puede ver las subredes **Pública** y **Privada** que ha creado.
9. **Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminar recursos](#delete-portal) de este artículo.

## <a name="azure-cli"></a>CLI de Azure

Los comandos de la CLI de Azure son los mismos, con independencia de que se ejecuten desde Windows, Linux o macOS. Pero existen diferencias de scripting entre los shells del sistema operativo. El script de los pasos siguientes se ejecuta en un shell de Bash. 

1. [Instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`. En lugar de instalar la CLI y sus requisitos previos, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Cloud Shell tiene la CLI de Azure preinstalada y configurada para su uso con la cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell (**> _**) de la parte superior del [portal](https://portal.azure.com) o simplemente haga clic en el botón *Pruébelo* en los pasos siguientes. 
2. Si ejecuta la CLI de forma local, inicie sesión en Azure con el comando `az login`. Si usa Cloud Shell, ya ha iniciado la sesión.
3. Revise el script siguiente y sus comentarios. En el explorador, copie el script y péguelo en la sesión de la CLI:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. Cuando el script termine de ejecutarse, revise las subredes de la red virtual. Copie el comando siguiente y péguelo en la sesión de la CLI:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli) de este artículo.

## <a name="powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. En una sesión de PowerShell, inicie sesión en Azure con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) mediante el comando `login-azurermaccount`.

3. Revise el script siguiente y sus comentarios. En el explorador, copie el script y péguelo en la sesión de PowerShell:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Para revisar las subredes de la red virtual, copie el comando siguiente y luego péguelo en la sesión de PowerShell:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-powershell) de este artículo.

## <a name="resource-manager-template"></a>Plantilla de Resource Manager

Puede implementar una red virtual con una plantilla de Azure Resource Manager. Para obtener más información sobre las plantillas, vea [¿Qué es Resource Manager?](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment) Para obtener acceso a la plantilla e información sobre sus parámetros, vea la plantilla [Crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Puede implementar la plantilla mediante el [portal](#template-portal), la [CLI de Azure](#template-cli) o [PowerShell](#template-powershell).

**Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de cualquiera de las subsecciones de [Eliminar recursos](#delete) de este artículo.

### <a name="template-portal"></a>Azure Portal

1. En el explorador, abra la [página de la plantilla](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Haga clic en botón **Deploy to Azure** (Implementar en Azure). Si aún no ha iniciado sesión en Azure, inicie sesión en la pantalla de inicio de sesión de Azure Portal que aparece.
3. Inicie sesión en el portal con su [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Escriba los siguientes valores para los parámetros:

    |Parámetro|Valor|
    |---|---|
    |Subscription|Seleccione su suscripción.|
    |Grupos de recursos|myResourceGroup|
    |Ubicación|Seleccionar una ubicación|
    |Nombre de red virtual|myVnet|
    |Prefijo de dirección de la red virtual|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privada|

5. Acepte los términos y condiciones y, después, haga clic en **Comprar** para implementar la red virtual.

### <a name="template-cli"></a>Azure CLI

1. [Instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`. En lugar de instalar la CLI y sus requisitos previos, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Cloud Shell tiene la CLI de Azure preinstalada y configurada para su uso con la cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell **> _** de la parte superior del [portal](https://portal.azure.com) o simplemente haga clic en el botón **Pruébelo** en los pasos siguientes. 
2. Si ejecuta la CLI de forma local, inicie sesión en Azure con el comando `az login`. Si usa Cloud Shell, ya ha iniciado la sesión.
3. Para crear un grupo de recursos para la red virtual, copie el siguiente comando y péguelo en la sesión de la CLI:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Puede implementar la plantilla mediante una de las siguientes opciones de parámetros:
    - **Valores de parámetro predeterminados**. Escriba el comando siguiente:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Valores de parámetro personalizados**. Descargue y modifique la plantilla antes de implementarla. También puede implementar la plantilla mediante el uso de parámetros en la línea de comandos o con un archivo de parámetros independiente. Para descargar la plantilla y los archivos de parámetros, haga clic en el botón **Explorar en GitHub** en la página de la plantilla [Crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). En GitHub, haga clic en el archivo **azuredeploy.parameters.json** o **azuredeploy.json**. Después, haga clic en el botón **Raw** para mostrar el archivo. En el explorador, copie el contenido del archivo. Guarde el contenido en un archivo en el equipo. Puede modificar los valores de los parámetros de la plantilla o implementar la plantilla con un archivo de parámetros independiente.  

    Para obtener más información sobre cómo implementar plantillas mediante estos métodos, escriba `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. En una sesión de PowerShell, para iniciar sesión con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), escriba `login-azurermaccount`.
3. Para crear un grupo de recursos para la red virtual, escriba el siguiente comando:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Puede implementar la plantilla mediante una de las siguientes opciones de parámetros:
    - **Valores de parámetro predeterminados**. Escriba el comando siguiente:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Valores de parámetro personalizados**. Descargue y modifique la plantilla antes de implementarla. También puede implementar la plantilla mediante el uso de parámetros en la línea de comandos o con un archivo de parámetros independiente. Para descargar la plantilla y los archivos de parámetros, haga clic en el botón **Explorar en GitHub** en la página de la plantilla [Crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). En GitHub, haga clic en el archivo **azuredeploy.parameters.json** o **azuredeploy.json**. Después, haga clic en el botón **Raw** para mostrar el archivo. En el explorador, copie el contenido del archivo. Guarde el contenido en un archivo en el equipo. Puede modificar los valores de los parámetros de la plantilla o implementar la plantilla con un archivo de parámetros independiente.  

    Para obtener más información sobre cómo implementar plantillas mediante estos métodos, escriba `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminación de recursos

Cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. En el cuadro de búsqueda del portal, escriba **myResourceGroup**. En los resultados de la búsqueda, haga clic en **myResourceGroup**.
2. En la hoja **myResourceGroup**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroup** y, luego, haga clic en **Eliminar**.

### <a name="delete-cli"></a>Azure CLI

En una sesión de la CLI, escriba el comando siguiente:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

En una sesión de PowerShell, escriba el comando siguiente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre todos los valores de red virtual y subred, vea [Administración de redes virtuales](virtual-network-manage-network.md#view-vnet) y [Manage virtual network subnets (Administración de subredes virtuales)](virtual-network-manage-subnet.md#create-subnet). Tiene varias opciones para el uso de redes virtuales y subredes en un entorno de producción para satisfacer requisitos diferentes.
- Para filtrar el tráfico de subred entrante y saliente, cree y aplique [grupos de seguridad de red](virtual-networks-nsg.md) a las subredes.
- Cree una máquina virtual [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y después conéctela a una red virtual existente.
- Para conectar dos redes virtuales en la misma ubicación de Azure, cree un [emparejamiento de red virtual](virtual-network-peering-overview.md) entre las redes virtuales.
- Conecte la red virtual a una red local mediante un circuito de [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

