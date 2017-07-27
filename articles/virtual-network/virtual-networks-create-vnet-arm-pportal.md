---
title: Crear una red virtual de Azure con subredes | Microsoft Docs
description: "Obtenga información sobre cómo crear una red virtual con varias subredes en Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: es-es
ms.lasthandoff: 06/01/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Creación de una red virtual con varias subredes

En este tutorial, aprenderá a crear una red virtual básica de Azure con subredes públicas y privadas independientes. A las subredes pueden conectarse recursos de Azure, como máquinas virtuales, entornos de Azure App Service, conjuntos de escalado de máquinas virtuales, Azure HDInsight y otros servicios en la nube. Los recursos que están conectados a redes virtuales puedan comunicarse entre sí a través de la red privada de Azure.

En las secciones siguientes se incluyen los pasos necesarios para implementar una red virtual mediante [Azure Portal](#portal), la interfaz de línea de comandos de Azure ([CLI de Azure](#azure-cli)), [Azure PowerShell](#powershell) y una [plantilla de Azure Resource Manager](#resource-manager-template). El resultado será el mismo, independientemente de la herramienta que use para implementar la red virtual. Haga clic en un vínculo de la herramienta para ir a esa sección del tutorial. Para obtener más información sobre toda la configuración de redes virtuales y subredes, vea [Administrar redes virtuales](virtual-network-manage-network.md) y [Administrar subredes virtuales](virtual-network-manage-subnet.md).

## <a name="portal"></a>Azure Portal

1. En un explorador de Internet, vaya a [Azure Portal](https://portal.azure.com). Inicie sesión con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En el portal, haga clic en **+Nuevo** > **Redes** > **Red virtual**.
3. En la hoja **Red virtual**, deje seleccionado **Administrador de recursos** en **Seleccionar un modelo de implementación** y haga clic en **Crear**.
4. En la hoja **Crear red virtual**, escriba los valores siguientes y luego haga clic en **Crear**:

    |Configuración|Valor|
    |---|---|
    |Nombre|MyVnet|
    |Espacio de direcciones|10.0.0.0/16|
    |Nombre de subred|Público|
    |Intervalo de direcciones de subred|10.0.0.0/24|
    |Grupos de recursos|Deje seleccionado **Crear nuevo** y después escriba **MyResourceGroup**.|
    |Suscripción y ubicación|Seleccione su suscripción y ubicación.

    Si no está familiarizado con Azure, obtenga más información sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [ubicaciones](https://azure.microsoft.com/regions) (también denominadas *regiones*).
6. En el portal, solo se puede crear una subred cuando se crea una red virtual. En este tutorial, creará una segunda subred después de crear la red virtual. Más adelante se podrían conectar recursos accesibles desde Internet a la subred **Pública**. También podría conectar recursos que no son accesibles desde Internet a una subred **Privada**. Para crear la segunda subred, escriba **MyVnet** en el cuadro **Buscar recursos** en la parte superior de la página. En los resultados de la búsqueda, haga clic en **MyVnet**. Si tiene varias redes virtuales con el mismo nombre en la suscripción, compruebe los grupos de recursos que se muestran debajo de cada red virtual. Asegúrese de hacer clic en el resultado de la búsqueda **MyVnet** que tenga el grupo de recursos **MyResourceGroup**.
7. En la hoja **MyVnet**, haga clic en **Subredes** en **CONFIGURACIÓN**.
8. En la hoja **MyVnet - Subredes**, haga clic en **+Subred**.
9. En la hoja **Agregar subred**, escriba **Privada** en **Nombre**. Para **Intervalo de direcciones**, escriba **10.0.1.0/24**.  Haga clic en **Aceptar**.
10. Revise las subredes en la hoja **MyVnet - Subredes**. Puede ver las subredes **Pública** y **Privada** que ha creado.
11. **Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminar recursos](#delete-portal) de este artículo.

## <a name="azure-cli"></a>CLI de Azure

Los comandos de la CLI de Azure son los mismos, con independencia de que se ejecuten desde Windows, Linux o macOS. Pero existen diferencias de scripting entre los shells del sistema operativo. Las instrucciones siguientes sirven para ejecutar un script de Bash que contiene comandos de la CLI de Azure:

1. En un explorador de Internet, abra [Azure Portal](https://portal.azure.com). Inicie sesión con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En la parte superior de la página del portal, a la derecha del cuadro **Buscar recursos**, haga clic en el icono **>_** para iniciar Azure Cloud Shell de Bash (versión preliminar). El panel de Cloud Shell aparece en la parte inferior del portal. Después de unos segundos, aparece el símbolo del sistema **username@Azure:~$**. Cloud Shell inicia automáticamente la sesión en Azure con las credenciales que usó para iniciar sesión en el portal.
3. En su explorador, copie el siguiente script:
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Cree un archivo de script y guárdelo. En el símbolo del sistema de Cloud Shell, escriba `nano myscript.sh --nonewlines`. El comando inicia el editor GNU nano con un archivo myscript.sh vacío. Coloque el cursor dentro de la ventana del editor, haga clic con el botón derecho y, después, haga clic en **Pegar**.  
5. Para guardar el archivo como myscript.sh, presione Ctrl+X, escriba **Y** y, después, presione la tecla Entrar. El almacenamiento de Cloud Shell no conserva los archivos guardados entre las sesiones. Si quiere conservar el script entre sesiones de Cloud Shell, configure el [almacenamiento persistente](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para Cloud Shell y guarde el archivo en almacenamiento persistente.
6. En el símbolo del sistema de Cloud Shell, para marcar el archivo como ejecutable, ejecute el comando `chmod +x myscript.sh`.
7. Escriba `./myscript.sh` para ejecutar el script.
8. Cuando el script termine de ejecutarse, revise las subredes de la red virtual, copie el siguiente comando y, después, péguelo en el panel Cloud Shell de Bash:
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Opcional**: para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminar recursos](#delete-cli) de este artículo.

## <a name="powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, vea [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar una sesión de PowerShell, vaya a **Inicio**, escriba **powershell** y después haga clic en **PowerShell**.
3. En la ventana de PowerShell, para iniciar sesión con su [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), escriba `login-azurermaccount`.
4. En su explorador, copie el siguiente script:
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Para ejecutar el script, haga clic con el botón derecho en la ventana de PowerShell.
6. Para revisar las subredes de la red virtual, copie el siguiente comando y, después, péguelo en la ventana de PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Opcional**: para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminar recursos](#delete-powershell) de este artículo.

## <a name="resource-manager-template"></a>Plantilla de Resource Manager

Puede implementar una red virtual con una plantilla de Azure Resource Manager. Para obtener más información sobre las plantillas, vea [¿Qué es Resource Manager?](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment) Para obtener acceso a la plantilla e información sobre sus parámetros, vea la plantilla [Crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Puede implementar la plantilla mediante el [portal](#template-portal), la [CLI de Azure](#template-cli) o [PowerShell](#template-powershell).

**Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de cualquiera de las subsecciones de [Eliminar recursos](#delete) de este artículo.

### <a name="template-portal"></a>Azure Portal

1. En el explorador, abra la [página de la plantilla](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Haga clic en botón **Deploy to Azure** (Implementar en Azure). Se abrirá la página de inicio de sesión de Azure Portal.
3. Inicie sesión en el portal con su [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Escriba los siguientes valores para los parámetros:

    |Parámetro|Valor|
    |---|---|
    |Subscription|Seleccione su suscripción.|
    |Grupos de recursos|MyResourceGroup|
    |Ubicación|Seleccionar una ubicación|
    |Nombre de red virtual|MyVnet|
    |Prefijo de dirección de la red virtual|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privada|

5. Acepte los términos y condiciones y, después, haga clic en **Comprar** para implementar la red virtual.

### <a name="template-cli"></a>Azure CLI

1. En el [portal](https://portal.azure.com), inicie sesión con su [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. A la derecha del cuadro **Buscar recursos**, haga clic en el icono **>_** para iniciar Azure Cloud Shell de Bash (versión preliminar). El panel de Cloud Shell aparece en la parte inferior del portal. Después de unos segundos, aparece el símbolo del sistema **username@Azure:~$**. Cloud Shell inicia automáticamente la sesión en Azure con las credenciales que usó para iniciar sesión en Azure Portal.
3. Para crear un grupo de recursos para la red virtual, escriba el siguiente comando:  `az group create --name MyResourceGroup --location eastus`
4. Puede implementar la plantilla mediante una de las siguientes opciones de parámetros:
    - **Valores de parámetro predeterminados**. Escriba el comando siguiente:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valores de parámetro personalizados**. Descargue y modifique la plantilla antes de implementarla. También puede implementar la plantilla mediante el uso de parámetros en la línea de comandos o con un archivo de parámetros independiente. Para descargar la plantilla y los archivos de parámetros, haga clic en el botón **Explorar en GitHub** en la página de la plantilla [Crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). En GitHub, haga clic en el archivo **azuredeploy.parameters.json** o **azuredeploy.json**. Después, haga clic en el botón **Raw** para mostrar el archivo. En el explorador, copie el contenido del archivo. Guarde el contenido en un archivo en el equipo. Puede modificar los valores de los parámetros de la plantilla o implementar la plantilla con un archivo de parámetros independiente.  

    Para obtener más información sobre cómo implementar plantillas mediante estos métodos, escriba `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, vea [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar una sesión de PowerShell, vaya a **Inicio**, escriba **powershell** y después haga clic en **PowerShell**.
3. En la ventana de PowerShell, para iniciar sesión con su [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), escriba `login-azurermaccount`.
4. Para crear un grupo de recursos para la red virtual, escriba el siguiente comando:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Puede implementar la plantilla mediante una de las siguientes opciones de parámetros:
    - **Valores de parámetro predeterminados**. Escriba el comando siguiente:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valores de parámetro personalizados**. Descargue y modifique la plantilla antes de implementarla. También puede implementar la plantilla mediante el uso de parámetros en la línea de comandos o con un archivo de parámetros independiente. Para descargar la plantilla y los archivos de parámetros, haga clic en el botón **Explorar en GitHub** en la página de la plantilla [Crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). En GitHub, haga clic en el archivo **azuredeploy.parameters.json** o **azuredeploy.json**. Después, haga clic en el botón **Raw** para mostrar el archivo. En el explorador, copie el contenido del archivo. Guarde el contenido en un archivo en el equipo. Puede modificar los valores de los parámetros de la plantilla o implementar la plantilla con un archivo de parámetros independiente.  

    Para obtener más información sobre cómo implementar plantillas mediante estos métodos, escriba `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminación de recursos
Cuando haya terminado este tutorial, es posible que quiera eliminar los recursos que ha usado, para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. En el cuadro de búsqueda del portal, escriba **MyResourceGroup**. En los resultados de la búsqueda, haga clic en **MyResourceGroup**.
2. En la hoja **MyResourceGroup**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, escriba **MyResourceGroup** en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y haga clic en **Eliminar**.

### <a name="delete-cli"></a>Azure CLI

En el símbolo del sistema de Cloud Shell, escriba el comando siguiente: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

En el símbolo del sistema de PowerShell, escriba el comando siguiente: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre toda la configuración de redes virtuales y subredes, vea [Administrar redes virtuales](virtual-network-manage-network.md#view-vnet) y [Administrar subredes virtuales](virtual-network-manage-subnet.md#create-subnet). Tiene varias opciones para el uso de redes virtuales y subredes en un entorno de producción para satisfacer requisitos diferentes.
- Cree y aplique [grupos de seguridad de red](virtual-networks-nsg.md) (NSG) a las subredes para filtrar el tráfico entrante y saliente de las subredes.
- Cree una [máquina virtual con Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o una [máquina virtual con Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y, después, conéctela a la red virtual.
- Conecte la red virtual a otra red virtual en la misma ubicación mediante el [emparejamiento de red virtual](virtual-network-peering-overview.md).
- Conecte la red virtual a una red local mediante una [red privada virtual de sitio a sitio](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un circuito [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

