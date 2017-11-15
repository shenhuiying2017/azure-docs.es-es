---
title: "Creación de un emparejamiento de red virtual de Azure: modelos de implementación diferentes: misma suscripción | Microsoft Docs"
description: "Obtenga información sobre cómo crear un emparejamiento de redes virtuales entre redes virtuales creadas mediante diferentes modelos de implementación de Azure que existen en la misma suscripción de Azure."
services: virtual-network
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: ebe489b6e0993dad42950acdafac48e662da7f77
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Creación de un emparejamiento de red virtual: distintos modelos de implementación, la misma suscripción 

En este tutorial aprenderá a crear un emparejamiento de redes virtuales entre dos redes virtuales creadas mediante diferentes modelos de implementación. Hay redes virtuales en la misma suscripción en la misma suscripción. Emparejar dos redes virtuales permite que los recursos en distintas redes virtuales se comuniquen entre sí con el mismo ancho de banda y latencia que tendrían los recursos si estuvieran en la misma red virtual. Obtenga más información sobre el [Emparejamiento de redes virtuales](virtual-network-peering-overview.md). 

Los pasos para crear un emparejamiento de redes virtuales cambian en función de si las redes virtuales se encuentran en la misma suscripción o en suscripciones diferentes, y en función del [modelo de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con el que se han creado las redes virtuales. Para más información sobre cómo crear un emparejamiento de redes virtuales en otros escenarios, haga clic en el escenario en la tabla siguiente:

|Modelo de implementación de Azure  | Suscripción de Azure  |
|--------- |---------|
|[Ambas mediante Resource Manager](virtual-network-create-peering.md) |Iguales|
|[Ambas mediante Resource Manager](create-peering-different-subscriptions.md) |Diferentes|
|[Una mediante Resource Manager y la otra clásica](create-peering-different-deployment-models-subscriptions.md) |Diferentes|

No se puede crear un emparejamiento de redes virtuales entre dos redes virtuales implementadas mediante el modelo de implementación clásico. Si necesita conectar redes virtuales que se crearon a través del modelo de implementación clásica, puede usar una instancia de [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para conectar las redes virtuales. 

En este tutorial se emparejan redes virtuales de la misma región. La capacidad de emparejar redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. Complete los pasos de [Registrarse para la versión preliminar del emparejamiento de VNet global](#register) antes de intentar emparejar redes virtuales de diferentes regiones o, de lo contrario, el emparejamiento genera errores. La capacidad de conectar redes virtuales de diferentes regiones con [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) se encuentra disponible con carácter general y, por tanto, no se requiere el registro.

Puede usar [Azure Portal](#portal), Azure [PowerShell](#cli), la [interfaz de la línea de comandos](#powershell) (CLI) de Azure o la [plantilla de Azure Resource Manager](#template) para crear un emparejamiento de redes virtuales. Haga clic en cualquiera de los vínculos anteriores de herramientas para ir directamente a los pasos para crear un emparejamiento de redes virtuales con la herramienta de su preferencia.

## <a name="create-peering---azure-portal"></a>Creación de emparejamiento: Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com). La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
2. Haga clic en **+ Nuevo**, **Redes** y, luego, en **Red virtual**.
3. En la hoja **Crear red virtual**, escriba o seleccione valores para la configuración siguiente y, luego, haga clic en **Crear**:
    - **Nombre**: *myVnet1*
    - **Espacio de direcciones**: *10.0.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.0.0.0/24*
    - **Suscripción**: seleccione la suscripción
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba *myResourceGroup*
    - **Ubicación**: *este de EE. UU.*
4. Haga clic en **+ Nuevo**. En el campo **Buscar en el Marketplace**, escriba *Red virtual*. Haga clic en **Red virtual** cuando aparezca en los resultados de búsqueda. 
5. En la hoja **Red virtual**, seleccione **Clásica** en el cuadro **Seleccionar un modelo de implementación** y haga clic en **Crear**.
6. En la hoja **Crear red virtual**, escriba o seleccione valores para la configuración siguiente y, luego, haga clic en **Crear**:
    - **Nombre**: *myVnet2*
    - **Espacio de direcciones**: *10.1.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.1.0.0/24*
    - **Suscripción**: seleccione la suscripción
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione *myResourceGroup*
    - **Ubicación**: *este de EE. UU.*
7. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myResourceGroup*. Haga clic en **myResourceGroup** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para el grupo de recursos **myresourcegroup**. El grupo de recursos contiene las dos redes virtuales que se crearon en los pasos anteriores.
8. Haga clic en **myVNet1**.
9. En la hoja **myVnet1** que aparece, haga clic en **Emparejamientos** en la lista vertical de opciones que aparece al lado izquierdo de la hoja.
10. En la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos) que aparece, haga clic en **+ Agregar**
11. En la hoja **Agregar emparejamiento** que aparece, escriba o seleccione las opciones siguientes y, luego, haga clic en **Aceptar**:
     - **Nombre**: *myVnet1ToMyVnet2*
     - **Modelo de implementación de red virtual**: seleccione **Clásico**. 
     - **Suscripción**: seleccione la suscripción
     - **Red virtual**: haga clic en **Elegir una red virtual** y, luego, en **myVnet2**.
     - **Permitir acceso a red virtual:** asegúrese de que esté seleccionada la opción **Habilitado**.
    En este tutorial no se usa ninguna otra configuración. Para conocer todas las configuraciones de emparejamiento, lea [Manage virtual network peerings](virtual-network-manage-peering.md#create-a-peering) (Administración de emparejamientos de redes virtuales).
12. Una vez que hace clic en **Aceptar** en el paso anterior, se cierra la hoja **Agregar emparejamiento** y se vuelve a mostrar la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos). Unos segundos después, el emparejamiento que creó aparece en la hoja. El estado **Conectado** aparece en la columna **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnet1ToMyVnet2** que creó.

    El emparejamiento está ahora establecido. Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
14. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete-portal) de este artículo.

## <a name="cli"></a>Creación de emparejamiento: CLI de Azure

1. [Instale](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) la CLI de Azure 1.0 para crear la red virtual (clásica).
2. Abra una sesión de comandos e inicie sesión en Azure mediante el comando `azure login`.
3. Ejecute la CLI en modo de administración de servicios escribiendo el comando `azure config mode asm`.
4. Escriba el siguiente comando para crear la red virtual (clásica):
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Cree un grupo de recursos y una red virtual (Resource Manager). Puede usar la CLI 1.0 o 2.0 ([instalación](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). En este tutorial, la CLI 2.0 se usa para crear la red virtual (Resource Manager), puesto que debe usarse 2.0 para crear el emparejamiento. Ejecute el siguiente script de la CLI de búsqueda desde la máquina local con la CLI 2.0.4 o posterior instalada. Para obtener las opciones de ejecución de scripts de la CLI de Bash en un cliente Windows, vea [Ejecución de la CLI de Azure en Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). También puede ejecutar el script con Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Haga clic en el botón **Pruébelo** en el script siguiente, que invoca un Cloud Shell en el que puede iniciar sesión con su cuenta de Azure. Para ejecutar el script, haga clic en el botón **Copiar** y pegue el contenido en Cloud Shell en la nube y, a continuación, pulse `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Cree un emparejamiento de redes virtuales entre las dos redes virtuales creadas mediante los modelos de implementación diferentes. Copie el script siguiente en un editor de texto de su equipo. Reemplace `<subscription id>` con el Id. de suscripción. Si no conoce el Id. de suscripción, escriba el comando `az account show`. El valor de **id** en la salida es el identificador de la suscripción. Pegue el script modificado en la sesión de CLI y después pulse `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Una vez que se ejecute el script, revise los emparejamientos de la red virtual (Resource Manager). Copie el comando siguiente, péguelo en la sesión de la CLI y después presione `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    La salida muestra **Conectado** en la columna **PeeringState**. 

    Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
9. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli) de este artículo.

## <a name="powershell"></a>Creación de emparejamiento: PowerShell

1. Instale la versión más reciente de los módulos [Azure](https://www.powershellgallery.com/packages/Azure) y [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell.
3. En PowerShell, inicie sesión en Azure especificando el comando `Add-AzureAccount`.
4. Para crear una red virtual (clásica) con PowerShell, debe crear o modificar un archivo de configuración de red existente. Obtenga información sobre cómo [exportar, actualizar e importar archivos de configuración de red](virtual-networks-using-network-configuration-file.md). El archivo debe incluir el siguiente elemento **VirtualNetworkSite** para la red virtual que se usa en este tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importar un archivo de configuración de red modificada puede producir cambios en las redes virtuales (clásicas) existentes en la suscripción. Asegúrese de agregar solo la red virtual anterior y que no cambia o quita ninguna red virtual existente de la suscripción. 
5. Inicie sesión en Azure para crear la red virtual (Resource Manager) escribiendo el comando `login-azurermaccount`. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
6. Cree un grupo de recursos y una red virtual (Resource Manager). Copie el script, péguelo en PowerShell y, a continuación, pulse `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Cree un emparejamiento de redes virtuales entre las dos redes virtuales creadas mediante los modelos de implementación diferentes. Copie el script siguiente en un editor de texto de su equipo. Reemplace `<subscription id>` con el Id. de suscripción. Si no conoce el identificador de la suscripción, escriba el comando `Get-AzureRmSubscription` para verlo. El valor de **id** en la salida devuelta es el identificador de la suscripción. Para ejecutar el script, copie el script modificado del editor de texto, haga clic con el botón derecho en la sesión de PowerShell y, a continuación, pulse `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Una vez que se ejecute el script, revise los emparejamientos de la red virtual (Resource Manager). Copie el comando siguiente, péguelo en la sesión de PowerShell y después presione `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    La salida muestra **Conectado** en la columna **PeeringState**.

    Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
10. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-powershell) de este artículo.
 
## <a name="permissions"></a>Permisos

Las cuentas que use para crear un emparejamiento de redes virtuales deben tener el rol o los permisos necesarios. Por ejemplo, si está emparejando dos redes virtuales denominadas myVnet1 y myVnet2, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:
    
|Red virtual|Modelo de implementación|Rol|Permisos|
|---|---|---|---|
|myVnet1|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnet2|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminación de recursos
Cuando haya terminado este tutorial, es posible que quiera eliminar los recursos que creó en el tutorial, para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. En el cuadro de búsqueda del portal, escriba **myResourceGroup**. En los resultados de la búsqueda, haga clic en **myResourceGroup**.
2. En la hoja **myResourceGroup**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroup** y, luego, haga clic en **Eliminar**.

### <a name="delete-cli"></a>Azure CLI

1. Use la CLI de Azure 2.0 para eliminar la red virtual (Resource Manager) con el siguiente comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Use la CLI de Azure 1.0 para eliminar la red virtual (clásico) con los siguientes comandos:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Especifique el siguiente comando para eliminar la red virtual (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Para eliminar la red virtual (clásica) con PowerShell, debe modificar un archivo de configuración de red existente. Obtenga información sobre cómo [exportar, actualizar e importar archivos de configuración de red](virtual-networks-using-network-configuration-file.md). Quite el siguiente elemento VirtualNetworkSite para la red virtual que se usa en este tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importar un archivo de configuración de red modificada puede producir cambios en las redes virtuales (clásicas) existentes en la suscripción. Asegúrese de quitar solo la red virtual anterior y que no cambia o quita ninguna red virtual existente de la suscripción. 

## <a name="register"></a>Registrarse para la versión preliminar del emparejamiento de VNet global

La capacidad de emparejar redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. La funcionalidad está disponible en un conjunto limitado de regiones (inicialmente, Centro-oeste de EE. UU., Canadá central y Oeste de EE. UU. 2). Los emparejamientos de redes virtuales creadas entre redes virtuales de diferentes regiones pueden no ofrecer el mismo nivel de disponibilidad y confiabilidad que el emparejamiento entre redes virtuales de la misma región. Para las notificaciones más al día sobre disponibilidad y estado de esta característica, consulte la página de [actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) .

Para emparejar redes virtuales entre regiones, primero debe registrarse en la versión preliminar; para ello, complete los pasos siguientes (en la suscripción en que se encuentra cada red virtual que desea emparejar) con Azure PowerShell o la CLI de Azure:

### <a name="powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell e inicie sesión en Azure mediante el comando `Login-AzureRmAccount`.
3. Registre la suscripción en que se encuentra cada máquina virtual que desea emparejar para la versión preliminar; para ello, escriba los comandos siguientes:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    No complete los pasos descritos en las secciones de Azure Portal, CLI de Azure, PowerShell o plantilla de Resource Manager de este artículo hasta que la salida de **RegistrationState** que recibirá después de escribir el comando anterior sea **Registered** para las dos suscripciones.

### <a name="azure-cli"></a>CLI de Azure

1. [Instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Asegúrese de que utiliza la versión 2.0.18 o posterior de la CLI de Azure escribiendo el comando `az --version`. Si no la está usando, instale la versión más reciente.
3. Inicie sesión en Azure con el comando `az login`.
4. Regístrese para obtener la versión preliminar mediante estos comandos:

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    No complete los pasos descritos en las secciones de Azure Portal, CLI de Azure, PowerShell o plantilla de Resource Manager de este artículo hasta que la salida de **RegistrationState** que recibirá después de escribir el comando anterior sea **Registered** para las dos suscripciones.

## <a name="next-steps"></a>Pasos siguientes

- Conozca en profundidad las [restricciones y comportamientos importantes del emparejamiento de redes virtuales](virtual-network-manage-peering.md#requirements-and-constraints) antes de crear un emparejamiento de redes virtuales para su uso en el entorno de producción.
- Conozca toda la [configuración de emparejamiento de redes virtuales](virtual-network-manage-peering.md#create-a-peering).
- Obtenga información sobre cómo [crear una topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con emparejamiento de redes virtuales.
