---
title: "Creación de una red virtual de Azure (clásica) con varias subredes | Microsoft Docs"
description: "Obtenga información sobre cómo crear una red virtual (clásica) con varias subredes en Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 099e3c259f0b63e4376847727eb8e185aeb37380
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Creación de una red virtual (clásica) con varias subredes

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artículo trata del modelo de implementación clásico. Microsoft recomienda crear las redes virtuales más nuevas a través del modelo de implementación de [Resource Manager](virtual-networks-create-vnet-arm-pportal.md).

En este tutorial, aprenderá a crear una red virtual (clásica) básica de Azure con subredes públicas y privadas independientes. Puede crear recursos de Azure, como máquinas virtuales y servicios en la nube en una subred. Los recursos creados en las redes virtuales (clásicas) se pueden comunicar entre sí y con los recursos de otras redes conectadas a una red virtual.

Más información sobre todos los valores de [red virtual](virtual-network-manage-network.md) y [subred](virtual-network-manage-subnet.md).

> [!WARNING]
> Azure elimina inmediatamente las redes virtuales (clásicas) cuando se [deshabilita una suscripción](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Las redes virtuales (clásicas) se eliminan independientemente de si hay recursos en la red virtual. Si más adelante vuelve a habilitar la suscripción, debe volver a crear los recursos que existían en la red virtual.

Puede crear una red virtual (clásica) mediante [Azure Portal](#portal), la [interfaz de la línea de comandos de Azure (CLI) 1.0](#azure-cli) o [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. En un explorador de Internet, vaya a [Azure Portal](https://portal.azure.com). Inicie sesión con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Haga clic en **+Nuevo** en el portal.
3. Escriba *Red virtual* en la casilla **Buscar en el Marketplace** en la parte superior de la hoja **Nuevo** que aparece.  Haga clic en **Red virtual** cuando aparezca en los resultados de la búsqueda.
4. Seleccione **Clásica** en la casilla **Seleccionar un modelo de implementación** en la hoja **Virtual Network** que aparece y, luego, haga clic en **Crear**. 
5. Especifique los siguientes valores en la hoja **Crear red virtual (clásica)** y, luego, haga clic en **Crear**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVnet|
    |Espacio de direcciones|10.0.0.0/16|
    |Nombre de subred|Público|
    |Intervalo de direcciones de subred|10.0.0.0/24|
    |Grupos de recursos|Deje seleccionado **Crear nuevo** y después escriba **myResourceGroup**.|
    |Suscripción y ubicación|Seleccione su suscripción y ubicación.

    Si no está familiarizado con Azure, obtenga más información sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [ubicaciones](https://azure.microsoft.com/regions) (también denominadas *regiones*).
4. En el portal, solo se puede crear una subred cuando se crea una red virtual. En este tutorial, creará una segunda subred después de crear la red virtual. Más adelante podría crear recursos accesibles desde Internet en la subred **Pública**. También podría crear recursos que no fueran accesibles desde Internet en la subred **Privada**. Para crear la segunda subred, escriba **myVnet** en el cuadro **Buscar recursos** en la parte superior de la página. Haga clic en **myVnet** cuando aparezca en los resultados de búsqueda.
5. Haga clic en **Subredes** (en la sección **CONFIGURACIÓN**) en la hoja **Crear red virtual (clásica)** que aparece.
6. Haga clic en **+Agregar** en la hoja **myVnet: subredes** que aparece.
7. Especifique **Privado** para el **Nombre** en la hoja **Agregar subred**. Especifique **10.0.1.0/24** para **Intervalo de direcciones**.  Haga clic en **OK**.
8. En la hoja **myVnet: subredes**, puede ver las subredes **Pública** y **Privada** que ha creado.
9. **Opcional**: cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso:
    - Haga clic en **Introducción** en la hoja **myVnet**.
    - Haga clic en el icono **Eliminar** en la hoja **myVnet**.
    - Para confirmar la eliminación, haga clic en **Sí** en el cuadro **Eliminar red virtual**.

## <a name="azure-cli"></a>CLI de Azure

1. Puede [instalar y configurar la CLI de Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o usar la CLI dentro de Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Para obtener ayuda con los comandos de la CLI, escriba `azure <command> --help`. 
2. En una sesión de la CLI, inicie sesión en Azure con el comando siguiente. Si hace clic en **Pruébelo** en el cuadro siguiente, se abre Cloud Shell. Puede iniciar sesión en la suscripción de Azure sin especificar el siguiente comando:

    ```azurecli-interactive
    azure login
    ```

3. Para asegurarse de que la CLI está en el modo de administración de servicios, especifique el siguiente comando:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Cree una red virtual con una subred privada:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Cree una subred pública dentro de una red virtual:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Revise la red virtual y las subredes:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcional**: cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Aunque no puede especificar un grupo de recursos para crear una red virtual (clásica) en el uso de la CLI, Azure crear la red virtual en un grupo de recursos con el nombre *Redes predeterminadas*.

## <a name="powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [Azure](https://www.powershellgallery.com/packages/Azure) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell.
3. En PowerShell, inicie sesión en Azure especificando el comando `Add-AzureAccount`.
4. Cambiar la siguiente ruta de acceso y nombre de archivo, según corresponda y, luego, exporte el archivo de configuración de red existente:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Para crear una red virtual con subredes públicas y privadas, utilice cualquier editor de texto para agregar el elemento **VirtualNetworkSite** que sigue al archivo de configuración de red.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Revise todo el [esquema del archivo de configuración de red](https://msdn.microsoft.com/library/azure/jj157100.aspx) completo.

6. Importe el archivo de configuración de red:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importar un archivo de configuración de red modificada puede producir cambios en las redes virtuales (clásicas) existentes en la suscripción. Asegúrese de agregar solo la red virtual anterior y que no cambia o quita ninguna red virtual existente de la suscripción. 

7. Revise la red virtual y las subredes:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcional**: cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso. Para eliminar la red virtual, complete los pasos del 4 al 6 de nuevo. Esta vez quitando el elemento **VirtualNetworkSite** agregado en el paso 5.
 
> [!NOTE]
> Aunque no puede especificar un grupo de recursos para crear una red virtual (clásica) en el uso de PowerShell, Azure crear la red virtual en un grupo de recursos con el nombre *Redes predeterminadas*.

---

## <a name="next-steps"></a>pasos siguientes

- Para más información sobre todos los valores de red virtual y subred, vea [Administración de redes virtuales](virtual-network-manage-network.md) y [Manage virtual network subnets (Administración de subredes virtuales)](virtual-network-manage-subnet.md). Tiene varias opciones para el uso de redes virtuales y subredes en un entorno de producción para satisfacer requisitos diferentes.
- Para filtrar el tráfico de subred entrante y saliente, cree y aplique [grupos de seguridad de red](virtual-networks-nsg.md) a las subredes.
- Cree una máquina virtual [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y después conéctela a una red virtual existente.
- Para conectar dos redes virtuales en la misma ubicación de Azure, cree un [emparejamiento de red virtual](create-peering-different-deployment-models.md) entre las redes virtuales. Puede emparejar una red virtual (Resource Manager) en una red virtual (clásica), pero no puede crear un emparejamiento entre dos redes virtuales (clásicas).
- Conecte la red virtual a una red local mediante un circuito de [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
