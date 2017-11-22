---
title: "Crear un emparejamiento de redes virtuales de Azure: diferentes modelos de implementación y suscripciones | Microsoft Docs"
description: "Obtenga información sobre cómo crear un emparejamiento de redes virtuales entre redes virtuales creadas mediante diferentes modelos de implementación de Azure que existen en diferentes suscripciones de Azure."
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
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 441bb0a269de400c82abc083118f5e0642523640
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Crear un emparejamiento de redes virtuales de Azure: diferentes modelos de implementación y suscripciones

En este tutorial aprenderá a crear un emparejamiento de redes virtuales entre dos redes virtuales creadas mediante diferentes modelos de implementación. Las redes virtuales se encuentran en suscripciones diferentes. Emparejar dos redes virtuales permite que los recursos en distintas redes virtuales se comuniquen entre sí con el mismo ancho de banda y latencia que tendrían los recursos si estuvieran en la misma red virtual. Obtenga más información sobre el [Emparejamiento de redes virtuales](virtual-network-peering-overview.md).

Los pasos para crear un emparejamiento de redes virtuales cambian en función de si las redes virtuales se encuentran en la misma suscripción o en suscripciones diferentes, y en función del [modelo de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con el que se han creado las redes virtuales. Para más información sobre cómo crear un emparejamiento de redes virtuales en otros escenarios, haga clic en el escenario en la tabla siguiente:

|Modelo de implementación de Azure  | Suscripción de Azure  |
|--------- |---------|
|[Ambas mediante Resource Manager](virtual-network-create-peering.md) |Iguales|
|[Ambas mediante Resource Manager](create-peering-different-subscriptions.md) |Diferentes|
|[Una mediante Resource Manager y la otra, clásico](create-peering-different-deployment-models.md) |Iguales|

No se puede crear un emparejamiento de redes virtuales entre dos redes virtuales implementadas mediante el modelo de implementación clásico. La posibilidad de emparejar redes virtuales creadas mediante diferentes modelos de implementación que existen en las diferentes suscripciones se encuentra actualmente en versión preliminar. Para completar este tutorial, primero debe [registrarse](#register) para utilizar la funcionalidad. En este tutorial se usan las redes virtuales de la misma región. La capacidad de emparejar redes virtuales de diferentes regiones se encuentra también en versión preliminar. Para utilizar esta funcionalidad, también debe [registrarse](#register). Las dos funcionalidades son independientes. Para completar este tutorial, solo debe registrar la funcionalidad para emparejar redes virtuales creadas a través de distintos modelos de implementación existentes en diferentes suscripciones. 

Al crear un emparejamiento de redes virtuales entre redes virtuales que se encuentran en suscripciones diferentes, las suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory. Si todavía no tiene un inquilino de Azure Active Directory, puede [crear uno](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rápidamente. 

La capacidad de conectar redes virtuales creadas a través del modelo de implementación, diferentes modelos de implementación, distintas regiones o suscripciones asociadas a los mismos inquilinos de Azure Active Directory o diferentes con Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) se encuentra en la versión preliminar y no requiere registro.

Puede usar [Azure Portal](#portal), la [interfaz de la línea de comandos](#cli) (CLI) de Azure o Azure [PowerShell](#powershell) para crear un emparejamiento de redes virtuales. Haga clic en cualquiera de los vínculos anteriores de herramientas para ir directamente a los pasos para crear un emparejamiento de redes virtuales con la herramienta de su preferencia.

## <a name="portal"></a>Creación de emparejamiento: Azure Portal

En este tutorial se usan cuentas diferentes para cada suscripción. Si está usando una cuenta que tiene permisos para ambas suscripciones, puede usar la misma cuenta para todos los pasos, omitir los pasos para cerrar sesión en el portal y para asignar a otro usuario permisos para las redes virtuales. Antes de realizar cualquiera de los pasos siguientes, debe registrarse para la versión preliminar. Para registrarse, complete los pasos de la sección [Registrarse para la versión preliminar](#register) de este artículo. Se producirá un error en los pasos restantes si no se registran las suscripciones para la vista preliminar.
 
1. Inicie sesión en [Azure Portal](https://portal.azure.com) como UserA. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
2. Haga clic en **+ Nuevo**, **Redes** y, luego, en **Red virtual**.
3. En la hoja **Crear red virtual**, escriba o seleccione valores para la configuración siguiente y, luego, haga clic en **Crear**:
    - **Nombre**: *myVnetA*
    - **Espacio de direcciones**: *10.0.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.0.0.0/24*
    - **Suscripción**: seleccione la suscripción A
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba *myResourceGroupA*
    - **Ubicación**: *este de EE. UU.*
4. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myVnetA*. Haga clic en **myVnetA** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para la red virtual **myVnetA**.
5. En la hoja **myVnetA** que aparece, haga clic en **Control de acceso (IAM)** en la lista vertical de opciones que aparece a la izquierda de la hoja.
6. En la hoja **myVnetA - Control de acceso (IAM)** que aparece, haga clic en **+ Agregar**.
7. En la hoja **Agregar permisos** que aparece, seleccione **Colaborador de la red** en el cuadro **Rol**.
8. En el cuadro **Seleccionar**, seleccione otro usuario (UserB) o escriba la dirección de correo de UserB para buscarlo. La lista de usuario que se muestra proviene del mismo inquilino de Azure Active Directory que la red virtual para la que configura el emparejamiento. Haga clic en UserB cuando aparezca en la lista.
9. Haga clic en **Guardar**.
10. Cierre sesión en el portal como UserA e inicie sesión como UserB.
11. Haga clic en **+ Nuevo**, escriba *Red virtual* en el cuadro **Buscar en el Marketplace** y después haga clic en **Red virtual** en los resultados de la búsqueda.
12. En la hoja **Virtual Network** que aparece, seleccione **Clásica** en el cuadro **Seleccionar un modelo de implementación** y haga clic en **Crear**.
13.   En el cuadro Crear red virtual (clásica) que aparece, escriba los valores siguientes:

    - **Nombre**: *myVnetB*
    - **Espacio de direcciones**: *10.1.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.1.0.0/24*
    - **Suscripción**: seleccione la suscripción B
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba *myResourceGroupB*
    - **Ubicación**: *este de EE. UU.*

14. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myVnetB*. Haga clic en **myVnetB** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para la red virtual **myVnetB**.
15. En la hoja **myVnetB** que aparece, haga clic en **Propiedades** en la lista vertical de opciones que aparece a la izquierda de la hoja. Copie el **ID. DE RECURSO**, ya que se usará en un paso posterior. El identificador de recurso es similar al ejemplo siguiente: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
16. Complete los pasos 5 a 9 para myVnetB y escriba **UserA** en el paso 8.
17. Cierre sesión en el portal como UserB e inicie sesión como UserA.
18. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myVnetA*. Haga clic en **myVnetA** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para la red virtual **myVnet**.
19. Haga clic en **myVnetA**.
20. En la hoja **myVnetA** que aparece, haga clic en **Emparejamientos** en la lista vertical de opciones que aparece a la izquierda de la hoja.
21. En la hoja **myVnetA - Emparejamientos** que aparece, haga clic en **+ Agregar**.
22. En la hoja **Agregar emparejamiento** que aparece, escriba o seleccione las opciones siguientes y, luego, haga clic en **Aceptar**:
     - **Nombre**: *myVnetAToMyVnetB*
     - **Modelo de implementación de red virtual**: seleccione **Clásico**.
     - **Conozco mi id. de recurso**: active esta casilla.
     - **Id. de recurso**: Escriba el identificador de recurso de myVnetB del paso 15.
     - **Permitir acceso a red virtual:** asegúrese de que esté seleccionada la opción **Habilitado**.
    En este tutorial no se usa ninguna otra configuración. Para conocer todas las configuraciones de emparejamiento, lea [Manage virtual network peerings](virtual-network-manage-peering.md#create-a-peering) (Administración de emparejamientos de redes virtuales).
23. Una vez que haga clic en **Aceptar** en el paso anterior, se cerrará la hoja **Agregar emparejamiento** y se volverá a mostrar la hoja **myVnetA - Emparejamientos**. Unos segundos después, el emparejamiento que creó aparece en la hoja. El estado **Conectado** aparece en la columna **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnetAToMyVnetB** que ha creado. El emparejamiento está ahora establecido. No es necesario emparejar la red virtual (clásica) a la red virtual (Resource Manager).

    Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

24. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
25. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete-portal) de este artículo.

## <a name="cli"></a>Creación de emparejamiento: CLI de Azure

En este tutorial se usan cuentas diferentes para cada suscripción. Si está usando una cuenta que tiene permisos para ambas suscripciones, puede usar la misma cuenta para todos los pasos, omitir los pasos para cerrar sesión en Azure y quitar las líneas del script que crean las asignaciones de roles de usuario. Reemplace UserA@azure.com y UserB@azure.com en todos los scripts siguientes por los nombres de usuario que está usando para UserA y UserB. 

Antes de realizar cualquiera de los pasos siguientes, debe registrarse para la versión preliminar. Para registrarse, complete los pasos de la sección [Registrarse para la versión preliminar](#register) de este artículo. Se producirá un error en los pasos restantes si no se registran las suscripciones para la vista preliminar.

1. [Instale](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) la CLI de Azure 1.0 para crear la red virtual (clásica).
2. Abra una sesión de la CLI e inicie sesión en Azure como UserB mediante el comando `azure login`.
3. Ejecute la CLI en modo de administración de servicios escribiendo el comando `azure config mode asm`.
4. Escriba el siguiente comando para crear la red virtual (clásica):
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. Los pasos restantes deben realizarse mediante un shell de Bash con la CLI de Azure 2.0.4 o una versión posterior [instalada](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), o mediante Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Haga clic en el botón **Pruébelo** en los scripts siguientes para abrir un Cloud Shell que inicia la sesión en su cuenta de Azure. Para obtener las opciones de ejecución de scripts de la CLI de Bash en un cliente Windows, vea [Uso de la CLI de Azure en Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Copie el script siguiente en un editor de texto de su equipo. Reemplace `<SubscriptionB-Id>` con el Id. de suscripción. Si no conoce el Id. de suscripción, escriba el comando `az account show`. El valor de **id** en la salida es el identificador de la suscripción. Copie el script modificado, péguelo en la sesión de la CLI 2.0 y, después, presione `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Cuando creó la red virtual (clásica) en el paso 4, Azure creó la red virtual en el grupo de recursos *Default-Networking*.
7. Cierre sesión en Azure como UserB e inicie sesión como UserA en la CLI 2.0.
8. Cree un grupo de recursos y una red virtual (Resource Manager). Copie el script siguiente, péguelo en la sesión de la CLI y después presione `Enter`. 

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

9. Cree un emparejamiento de redes virtuales entre las dos redes virtuales creadas mediante los modelos de implementación diferentes. Copie el script siguiente en un editor de texto de su equipo. Reemplace `<SubscriptionB-id>` con el Id. de suscripción. Si no conoce el Id. de suscripción, escriba el comando `az account show`. El valor de **id** en la salida es el identificador de la suscripción. Azure creó la red virtual (clásica) que creó en el paso 4 en un grupo de recursos denominado *Default-Networking*. Pegue el script modificado en la sesión de CLI y después presione `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Una vez que se ejecute el script, revise los emparejamientos de la red virtual (Resource Manager). Copie el script siguiente y péguelo en la sesión de la CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    La salida muestra **Conectado** en la columna **PeeringState**.

    Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

11. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
12. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli) de este artículo.

## <a name="powershell"></a>Creación de emparejamiento: PowerShell

En este tutorial se usan cuentas diferentes para cada suscripción. Si está usando una cuenta que tiene permisos para ambas suscripciones, puede usar la misma cuenta para todos los pasos, omitir los pasos para cerrar sesión en Azure y quitar las líneas del script que crean las asignaciones de roles de usuario. Reemplace UserA@azure.com y UserB@azure.com en todos los scripts siguientes por los nombres de usuario que está usando para UserA y UserB. 

Antes de realizar cualquiera de los pasos siguientes, debe registrarse para la versión preliminar. Para registrarse, complete los pasos de la sección [Registrarse para la versión preliminar](#register) de este artículo. Se producirá un error en los pasos restantes si no se registran las suscripciones para la vista preliminar.

1. Instale la versión más reciente de los módulos [Azure](https://www.powershellgallery.com/packages/Azure) y [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell.
3. En PowerShell, inicie sesión en la suscripción de UserB como UserB escribiendo el comando `Add-AzureAccount`.
4. Para crear una red virtual (clásica) con PowerShell, debe crear o modificar un archivo de configuración de red existente. Obtenga información sobre cómo [exportar, actualizar e importar archivos de configuración de red](virtual-networks-using-network-configuration-file.md). El archivo debe incluir el siguiente elemento **VirtualNetworkSite** para la red virtual que se usa en este tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Inicie sesión en la suscripción de UserB como UserB para usar los comandos de Resource Manager escribiendo el comando `login-azurermaccount`.
6. Asigne los permisos de UserA a la red virtual B. Copie el script siguiente en un editor de texto en su PC y reemplace `<SubscriptionB-id>` con el Id. de suscripción B. Si no conoce el Id. de suscripción, escriba el comando `Get-AzureRmSubscription` para verlo. El valor de **id** en la salida devuelta es el Id. de suscripción. Azure creó la red virtual (clásica) que creó en el paso 4 en un grupo de recursos denominado *Default-Networking*. Para ejecutar el script, copie el script modificado, péguelo en PowerShell y después pulse `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Cierre sesión en Azure como UserB e inicie sesión en la suscripción de UserA como UserA escribiendo el comando `login-azurermaccount`. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
8. Para crear la red virtual (Resource Manager) copie el siguiente script, péguelo en PowerShell y después presione `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Asigne los permisos de UserB a myVnetA. Copie el script siguiente en un editor de texto en su PC y reemplace `<SubscriptionA-Id>` con el Id. de la suscripción A. Si no conoce el Id. de la suscripción, escriba el comando `Get-AzureRmSubscription` para verlo. El valor de **id** en la salida devuelta es el Id. de suscripción. Pegue la versión modificada del script en PowerShell y después presione `Enter` para ejecutarlo.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie el script siguiente en un editor de texto de su equipo y reemplace `<SubscriptionB-id>` por el identificador de la suscripción B. Para emparejar myVnetA con myVNetB, copie el script modificado, péguelo en PowerShell y después presione `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Para ver el estado de emparejamiento de myVnetA, copie el siguiente script, péguelo en PowerShell y presione `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    El estado es **Conectado**. Cuando haya configurado el emparejamiento a myVnetA desde myVnetB, cambiará a **Conectado**.

    Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

12. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
13. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-powershell) de este artículo.

## <a name="permissions"></a>Permisos

Las cuentas que use para crear un emparejamiento de redes virtuales deben tener el rol o los permisos necesarios. Por ejemplo, si está emparejando dos redes virtuales denominadas myVnetA y myVnetB, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:
    
|Red virtual|Modelo de implementación|Rol|Permisos|
|---|---|---|---|
|myVnetA|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnetB|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminación de recursos
Cuando haya terminado este tutorial, es posible que quiera eliminar los recursos que creó en el tutorial, para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. En el cuadro de búsqueda del portal, escriba **myResourceGroupA**. En los resultados de la búsqueda, haga clic en **myResourceGroupA**.
2. En la hoja **myResourceGroupA**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroupA** y, luego, haga clic en **Eliminar**.
4. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myVnetB*. Haga clic en **myVnetB** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para la red virtual **myVnetB**.
5. En la hoja **myVnetB**, haga clic en **Eliminar**.
6. Para confirmar la eliminación, haga clic en **Sí** en el cuadro **Eliminar red virtual**.

### <a name="delete-cli"></a>Azure CLI

1. Inicie sesión en Azure con la CLI 2.0 para eliminar la red virtual (Resource Manager) con el siguiente comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Inicie sesión en Azure con la CLI 1.0 de Azure para eliminar la red virtual (clásica) con los siguientes comandos:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. En el símbolo del sistema de PowerShell, escriba el siguiente comando para eliminar la red virtual (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Para eliminar la red virtual (clásica) con PowerShell, debe modificar un archivo de configuración de red existente. Obtenga información sobre cómo [exportar, actualizar e importar archivos de configuración de red](virtual-networks-using-network-configuration-file.md). Quite el siguiente elemento VirtualNetworkSite para la red virtual que se usa en este tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

## <a name="register"></a>Registrarse para la versión preliminar

La posibilidad de emparejar redes virtuales creadas mediante diferentes modelos de implementación de Azure que existen en las diferentes suscripciones se encuentra actualmente en versión preliminar. Es posible que las características de la versión preliminar no tengan el mismo nivel de disponibilidad y confiabilidad que las características de la versión general. Para ver notificaciones más actualizadas sobre la disponibilidad y el estado de esta versión preliminar, vea la página de [actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network). 

En primer, registre la característica del modelo entre implementación y entre suscripciones antes de usarla. Complete los pasos siguientes en la suscripción de la red virtual que desee emparejar con Azure PowerShell o la CLI de Azure:

### <a name="powershell"></a>PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell e inicie sesión en Azure mediante el comando `Login-AzureRmAccount`.
3. Registre la suscripción en que se encuentra cada máquina virtual que desea emparejar para la versión preliminar; para ello, escriba los comandos siguientes:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    ```

    No complete los pasos descritos en las secciones de Azure Portal, CLI de Azure, PowerShell o plantilla de Resource Manager de este artículo hasta que la salida de **RegistrationState** que recibirá después de escribir los comandos anteriores sea **Registered** para las dos suscripciones.

> [!NOTE]
> En este tutorial se usan las redes virtuales de la misma región. La capacidad de emparejar redes virtuales de diferentes regiones se encuentra también en versión preliminar. Para registrarse en el emparejamiento global o entre regiones, complete los pasos del 1 al 4 de nuevo, con `-FeatureName AllowGlobalVnetPeering` en lugar de `-FeatureName AllowClassicCrossSubscriptionPeering`. Las dos funcionalidades son independientes. No es necesario registrar ambas, a menos que desee usar las dos. La funcionalidad está disponible en un conjunto limitado de regiones (inicialmente, Centro-oeste de EE. UU., Canadá central y Oeste de EE. UU. 2).

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

> [!NOTE]
> En este tutorial se usan las redes virtuales de la misma región. La capacidad de emparejar redes virtuales de diferentes regiones se encuentra también en versión preliminar. Para registrarse en el emparejamiento global o entre regiones, complete los pasos del 1 al 5 de nuevo, con `--name AllowGlobalVnetPeering` en lugar de `--name AllowClassicCrossSubscriptionPeering`. Las dos funcionalidades son independientes. No es necesario registrar ambas, a menos que desee usar las dos. La funcionalidad está disponible en un conjunto limitado de regiones (inicialmente, Centro-oeste de EE. UU., Canadá central y Oeste de EE. UU. 2).

## <a name="next-steps"></a>Pasos siguientes

- Conozca en profundidad las [restricciones y comportamientos importantes del emparejamiento de redes virtuales](virtual-network-manage-peering.md#requirements-and-constraints) antes de crear un emparejamiento de redes virtuales para su uso en el entorno de producción.
- Conozca toda la [configuración de emparejamiento de redes virtuales](virtual-network-manage-peering.md#create-a-peering).
- Obtenga información sobre cómo [crear una topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con emparejamiento de redes virtuales.
