---
title: "Creación de una instancia de Azure Virtual Network | Microsoft Docs"
description: Aprenda a crear una red virtual con varias subredes.
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Creación de una red virtual con varias subredes

En este tutorial, aprenderá a crear una instancia de Azure Virtual Network (red virtual) con subredes públicas y privadas independientes. A las subredes pueden conectarse varios recursos de Azure, como máquinas virtuales (VM), entornos de App Service, conjuntos de escalado de máquinas virtuales, HDInsight y Cloud Services. Los recursos conectados a redes virtuales pueden comunicarse entre sí a través de la red privada de Azure.

Las secciones siguientes incluyen pasos para implementar una red virtual mediante Azure [Portal](#portal), la [interfaz de la línea de comandos](#cli) (CLI) de Azure, Azure [PowerShell](#powershell) y una [plantilla](#template) de Azure Resource Manager. El resultado será el mismo, independientemente de la herramienta que elija para implementar la red virtual. Al hacer clic en el vínculo de cualquiera de las herramientas, se le dirigirá a la sección correspondiente del artículo. Para más información acerca de todos los ajustes de configuración de las redes virtuales y las subredes, lea los artículos [Administrar redes virtuales](virtual-network-manage-network.md) y [Administrar subredes](virtual-network-manage-subnet.md).

## <a name="portal"></a>Azure Portal

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En el portal, haga clic en **+Nuevo** > **Redes** > **Red virtual**.
3. En la hoja **Red virtual**, deje seleccionado *Resource Manager* en **Seleccionar un modelo de implementación** y haga clic en **Crear**.
4. En la hoja **Crear red virtual** que aparece, escriba los valores siguientes y haga clic en el botón **Crear**:

    |Configuración|Valor|
    |---|---|
    |Nombre|*MyVNet*|
    |Espacio de direcciones|*10.0.0.0/16*|
    |Nombre de subred|Público|
    |Intervalo de direcciones de subred|*10.0.0.0/24*|
    |Grupos de recursos|Deje seleccionado **Crear nuevo** y escriba *MyResourceGroup*.|
    |Suscripción y ubicación|Seleccione su suscripción y ubicación.

    Si no está familiarizado con Azure, consulte más información sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [ubicaciones](https://azure.microsoft.com/regions) (también denominadas regiones).
6. El portal solo permite crear una subred al crear una red virtual. En este tutorial, se crea una segunda subred una vez creada la red virtual. Más adelante podrá conectar recursos accesibles desde Internet a la subred *pública* y conectar recursos inaccesibles desde Internet a una subred privada. Para crear la segunda subred, escriba *MyVnet* en el cuadro *Buscar recursos* en la parte superior del portal. Haga clic en **MyVnet** cuando aparezca en los resultados de búsqueda. Si tiene varias redes virtuales en su suscripción con el mismo nombre, verá nombres de grupos de recursos enumerados bajo cada red virtual con el mismo nombre. Asegúrese de hacer clic en el resultado de MyVnet en el que figure *MyResourceGroup*.
7. En la hoja **MyVnet** que aparece, haga clic en **Subredes** en **CONFIGURACIÓN**.
8. En la hoja **MyVnet - Subredes**, haga clic en **+Subred**.
9. Escriba *Privada* en **Nombre**, *10.0.1.0/24* en **Intervalo de direcciones** en la hoja **Agregar subred** y, a continuación, haga clic en **Aceptar**.
10. Revise las subredes en la hoja **MyVnet - Subredes**. Verá las subredes **público** y **privada** que ha creado.
11. **Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminación de recursos](#delete-portal) de este artículo.

## <a name="cli"></a>CLI
Aunque los comandos de la CLI son los mismos independientemente de si se ejecutan los comandos mediante Windows, Linux o macOS, hay diferencias de script entre shells del sistema operativo. Las instrucciones siguientes sirven para ejecutar un script de Bash que contiene comandos de CLI:

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En la parte superior del portal, a la derecha de la barra *Buscar recursos*, haga clic en el icono **>_** para iniciar una instancia de Azure Cloud Shell de Bash (versión preliminar). El panel de Cloud Shell aparece en la parte inferior del portal y, después de unos segundos, presenta el símbolo del sistema **username@Azure:~ $**. Cloud Shell inicia automáticamente su sesión en Azure mediante las credenciales con las que se ha autenticado en el portal.
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
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Cree un archivo de script y guárdelo. Desde el símbolo del sistema de Cloud Shell, escriba `nano myscript.sh --nonewlines`. El comando inicia el editor GNU Nano con un archivo myscript.sh vacío. Coloque el mouse dentro de la ventana del editor, haga clic con el botón derecho y, a continuación, haga clic en **Pegar**. El almacenamiento de Cloud Shell no se mantiene entre distintas sesiones. Si prefiere conservar el script entre sesiones de Cloud Shell, configure el [almacenamiento persistente](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para Cloud Shell. 
5. En el teclado, mantenga presionadas las teclas **Ctrl+X**, escriba **Y** y, a continuación, presione la tecla **Entrar** para guardar el archivo como myscript.sh.
6. En el símbolo del sistema de Cloud Shell, marque el archivo como ejecutable con el comando `chmod +x myscript.sh`.
7. Escriba `./myscript.sh` para ejecutar el script.
8. Una vez completado el script, revise las subredes de la red virtual copiando y pegando el siguiente comando en la instancia de Cloud Shell de Bash:
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminación de recursos](#delete-cli) de este artículo.

## <a name="powershell"></a>PowerShell
1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de Azure PowerShell. Si no está familiarizado con Azure PowerShell, consulte el artículo [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell haciendo clic en el botón Inicio de Windows, luego escriba **Powershell** y, a continuación, en los resultados de búsqueda, haga clic en **PowerShell**.
3. En la ventana de PowerShell, escriba el comando `login-azurermaccount` para iniciar sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. En su explorador, copie el siguiente script:
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Para ejecutar el script, haga clic con el botón derecho en la ventana de PowerShell.
6. Revise las subredes de la red virtual copiando el siguiente comando y pegándolo en la ventana de PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de la sección [Eliminación de recursos](#delete-powershell) de este artículo.

## <a name="template"></a>Plantilla

Una red virtual puede implementarse con una plantilla de Azure Resource Manager. Para más información acerca de las plantillas, lea el artículo [¿Qué es Resource Manager?](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment) Para obtener acceso a la plantilla e información sobre sus parámetros, visite la página web de la [plantilla para crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Puede implementar la plantilla desde el [portal](#template-portal), la [CLI](#template-cli) o [PowerShell](#template-powershell).

**Opcional:** para eliminar los recursos creados en este tutorial, complete los pasos de cualquiera de las subsecciones de la sección [Eliminación de recursos](#delete) de este artículo.

### <a name="template-portal"></a>Portal

1. En el explorador, abra la [página web](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets) de la plantilla.
2. Haga clic en el botón **Implementar en Azure**, que abre la página de inicio de sesión de Azure Portal.
3. Inicie sesión en el portal con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Escriba los siguientes valores para los parámetros:

    |Parámetro|Valor|
    |---|---|
    |Subscription|Seleccione su suscripción.|
    |Grupos de recursos|MyResourceGroup|
    |Ubicación|Seleccione una ubicación.|
    |Nombre de red virtual|MyVnet|
    |Prefijo de dirección de la red virtual|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privada|

5. Acepte los términos y condiciones y, después, haga clic en **Comprar** para implementar la red virtual.

### <a name="template-cli"></a>CLI

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En la parte superior del portal, a la derecha de la barra *Buscar recursos*, haga clic en el icono **>_** para iniciar una instancia de Azure Cloud Shell de Bash (versión preliminar). El panel de Cloud Shell aparece en la parte inferior del portal y, después de unos segundos, presenta el símbolo del sistema **username@Azure:~ $**. Cloud Shell inicia automáticamente su sesión en Azure mediante las credenciales con las que se ha autenticado en el portal.
3. Cree un grupo de recursos para la red virtual mediante el siguiente comando:  `az group create --name MyResourceGroup --location eastus`
4. Puede implementar la plantilla con:
    - **Valores de parámetros predeterminados:** escriba el comando siguiente:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valores de parámetros personalizados:** descargue y modifique la plantilla antes de implementarla, implemente la plantilla con parámetros de la línea de comandos o implemente la plantilla mediante un archivo de parámetros independiente. Para descargar la plantilla y los archivos de parámetros, haga clic en el botón **Explorar en GitHub** de la página web de la [plantilla para crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). En GitHub, haga clic en el archivo **azuredeploy.parameters.json** o **azuredeploy.json** y, a continuación, haga clic en el botón **Raw** del archivo. Desde el explorador, copie el contenido y guárdelo en un archivo en su equipo. Modificar los valores de los parámetros de la plantilla o implemente la plantilla con el archivo de parámetros independiente.  

    Para más información sobre cómo implementar plantillas mediante estos métodos, escriba `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de Azure PowerShell. Si no está familiarizado con Azure PowerShell, consulte el artículo [Introducción a Azure PowerShell](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell haciendo clic en el botón Inicio de Windows, luego escriba **Powershell** y, a continuación, en los resultados de búsqueda, haga clic en **PowerShell**.
3. En la ventana de PowerShell, escriba el comando `login-azurermaccount` para iniciar sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Cree un grupo de recursos para la red virtual mediante el siguiente comando:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Puede implementar la plantilla con:
    - **Valores de parámetros predeterminados:** para ello, escriba el comando siguiente:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valores de parámetros personalizados:** para ello, puede descargar y modificar la plantilla antes de implementarla, implementar la plantilla con parámetros de la línea de comandos o implementar la plantilla mediante un archivo de parámetros independiente. Para descargar la plantilla y los archivos de parámetros, haga clic en el botón **Explorar en GitHub** de la página web de la [plantilla para crear una red virtual con dos subredes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). En GitHub, haga clic en el archivo **azuredeploy.parameters.json** o **azuredeploy.json** y, a continuación, haga clic en el botón **Raw** del archivo. Desde el explorador, copie el contenido y guárdelo en un archivo en su equipo. Modificar los valores de los parámetros de la plantilla o implemente la plantilla con el archivo de parámetros independiente.  

    Para más información sobre cómo implementar plantillas mediante estos métodos, escriba `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminación de recursos
Después de completar este tutorial, puede que desee eliminar el recurso para evitar incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos que contiene.

### <a name="delete-portal"></a>Portal

1. En el portal, comience a escribir *MyResourceGroup* en el cuadro *Buscar recursos* en la parte superior del portal. Haga clic en **MyResourceGroup** cuando aparezca en los resultados de búsqueda.
2. En la hoja MyResourceGroup que aparece, haga clic en el icono de eliminación dela parte superior de la hoja.
3. Para confirmar la eliminación, escriba *MyResourceGroup* en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y haga clic en **Eliminar**.

### <a name="delete-cli"></a>CLI

En el símbolo del sistema de Cloud Shell, escriba el comando siguiente: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

En el símbolo del sistema de PowerShell, escriba el comando siguiente: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de todos los ajustes de configuración de las redes virtuales y las subredes, lea los artículos [Administrar redes virtuales](virtual-network-manage-network.md#view-vnet) y [Administrar subredes](virtual-network-manage-subnet.md#create-subnet). Existen varias opciones que le permiten crear redes virtuales y subredes de producción para distintas necesidades.
- Filtre el tráfico entrante y saliente de las subredes mediante la creación de [grupos de seguridad de red](virtual-networks-nsg.md) para aplicarlos a las subredes.
- Cree una máquina virtual de [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y conéctela a la red virtual.
- Conecte la red virtual a otra red virtual en la misma ubicación mediante [emparejamiento de VNET](virtual-network-peering-overview.md).
- Conecte la red virtual a una red local mediante una [red privada virtual (VPN) de sitio a sitio](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un circuito [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

