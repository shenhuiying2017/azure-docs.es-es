---
title: "Creación de un emparejamiento de redes virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear un emparejamiento de redes virtuales entre dos redes virtuales."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>Creación de un emparejamiento de redes virtuales

En este tutorial aprenderá a crear un emparejamiento de redes virtuales entre dos redes virtuales. Emparejar dos redes virtuales permite que los recursos en distintas redes virtuales se comuniquen entre sí con el mismo ancho de banda y latencia que tendrían los recursos si estuvieran en la misma red virtual. Un emparejamiento de redes virtuales solo se puede crear entre dos redes virtuales que existen en la misma región de Azure. Para más información sobre el emparejamiento de redes virtuales, consulte el artículo de información general [Emparejamiento de redes virtuales](virtual-network-peering-overview.md). 

Si necesita conectar redes virtuales que existen en distintas regiones de Azure, puede usar una instancia de Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para conectarlas. 

Puede usar [Azure Portal](#portal), Azure [PowerShell](#powershell), la [interfaz de la línea de comandos](#cli) (CLI) de Azure o la [plantilla de Azure Resource Manager](#template) para crear un emparejamiento de redes virtuales. Haga clic en cualquiera de los vínculos anteriores de herramientas para ir directamente a los pasos para crear un emparejamiento de redes virtuales con la herramienta de su preferencia.

## <a name="portal"></a>Creación de emparejamiento: Azure Portal

Complete los pasos siguientes para crear un emparejamiento de redes virtuales entre dos redes virtuales implementadas mediante Resource Manager que existen en la misma suscripción. También puede [emparejar dos redes virtuales en distintas suscripciones](#different-subscriptions) o [emparejar una red virtual (Resource Manager) con una red virtual (clásica)](#different-models). 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
2. Haga clic en **+ Nuevo**, **Redes** y, luego, en **Red virtual**.
3. En la hoja **Red virtual**, en el cuadro **Seleccionar un modelo de implementación**, deje seleccionado **Resource Manager** y haga clic en **Crear**.
4. En la hoja **Crear red virtual**, escriba o seleccione valores para la configuración siguiente y, luego, haga clic en **Crear**:
    - **Nombre**: *myVnet1*
    - **Espacio de direcciones**: *10.0.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.0.0.0/24*
    - **Suscripción**: seleccione la suscripción
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba *myResourceGroup*
    - **Ubicación**: *este de EE. UU.*
5. Complete los pasos del 2 al 4 nuevamente y especifique los valores siguientes en el paso 4:
    - **Nombre**: *myVnet2*
    - **Espacio de direcciones**: *10.1.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.1.0.0/24*
    - **Suscripción**: seleccione la suscripción
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione *myResourceGroup*
    - **Ubicación**: *este de EE. UU.*
6. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myResourceGroup*. Haga clic en **myResourceGroup** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para el grupo de recursos **myresourcegroup**. El grupo de recursos contiene las dos redes virtuales que se crearon en los pasos anteriores.
7. Haga clic en **myVNet1**.
8. En la hoja **myVnet1** que aparece, haga clic en **Emparejamientos** en la lista vertical de opciones que aparece al lado izquierdo de la hoja.
9. En la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos) que aparece, haga clic en **+ Agregar**
10. En la hoja **Agregar emparejamiento** que aparece, escriba o seleccione las opciones siguientes y, luego, haga clic en **Aceptar**:
     - **Nombre**: *myVnet1ToMyVnet2*
     - **Modelo de implementación de red virtual**: seleccione **Resource Manager**. 
     - **Suscripción**: seleccione la suscripción
     - **Red virtual**: haga clic en **Elegir una red virtual** y, luego, en **myVnet2**.
     - **Permitir acceso a red virtual:** asegúrese de que esté seleccionada la opción **Habilitado**.
    En este tutorial no se usa ninguna otra configuración. Para conocer todas las configuraciones de emparejamiento, lea [Manage virtual network peerings](virtual-network-manage-peering.md#create-peering) (Administración de emparejamientos de redes virtuales).
11. Una vez que hace clic en **Aceptar** en el paso anterior, se cierra la hoja **Agregar emparejamiento** y se vuelve a mostrar la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos). Unos segundos después, el emparejamiento que creó aparece en la hoja. El estado **Iniciado** aparece en la columna **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnet1ToMyVnet2** que creó. Emparejó Vnet1 con Vnet2, pero ahora debe emparejar Vnet2 con Vnet1. Debe crear el emparejamiento en ambas direcciones para permitir que los recursos de las redes virtuales se comuniquen entre sí.
12. Complete nuevamente los pasos del 6 al 11 para myVnet2.  Asigne el nombre *myVnet2ToMyVnet1* al emparejamiento.
13. Unos segundos después de hacer clic en **Aceptar** para crear el emparejamiento de MyVnet2, el emparejamiento **myVnet2ToMyVnet1** que acaba de crear aparece con el estado **Conectado** en la columna **ESTADO DE EMPAREJAMIENTO**.
14. Complete nuevamente los pasos del 6 al 8 para MyVnet1. **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnet1ToVNet2** ahora también aparece como **Conectado**. El emparejamiento se establece correctamente una vez que ve el estado **Conectado** en la columna **ESTADO DE EMPAREJAMIENTO** para las dos redes virtuales del emparejamiento.
15. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
16. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete-portal) de este artículo.

Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Creación de emparejamiento: CLI de Azure

El script siguiente:

- Requiere la versión 2.0.4 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Uso de la CLI de Azure en Windows).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

1. Cree un grupo de recursos y dos redes virtuales.
    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    #
    ```
2. Cree un emparejamiento de redes virtuales entre las dos redes virtuales.
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    #
    ```
3. Una vez que se ejecute el script, revise los emparejamientos de cada red virtual. Copie el comando siguiente y péguelo en la ventana de comandos:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
Vuelva a ejecutar el comando anterior, reemplazando *myVnet1* por *myVnet2*. La salida de ambos comandos muestra **Conectado** en la columna **PeeringState**.
4. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
5. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli) de este artículo.

Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Creación de emparejamiento: PowerShell

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar una sesión de PowerShell, vaya a **Inicio**, escriba **powershell** y, luego, haga clic en **PowerShell**.
3. En la ventana de PowerShell, inicie sesión en Azure con el comando `login-azurermaccount`. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
4. En el explorador, copie el script siguiente y haga clic con el botón derecho en la ventana de PowerShell para ejecutar el script que crea un grupo de recursos y dos redes virtuales:
    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    #
    ```
5. En el explorador, copie el script siguiente y haga clic con el botón derecho en la ventana de PowerShell para ejecutar el script que crea un emparejamiento de redes virtuales entre las dos redes virtuales:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    #
    ```
6. Para revisar las subredes de la red virtual, copie el comando siguiente y, luego, péguelo en la ventana de PowerShell:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

Vuelva a ejecutar el comando anterior, reemplazando *myVnet1* por *myVnet2*. La salida de ambos comandos muestra **Conectado** en la columna **PeeringState**.
7. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
8. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-powershell) de este artículo.

Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Creación de emparejamiento: plantilla de Resource Manager

1. Consulte [Creación de un emparejamiento de red virtual](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) mediante una plantilla de Resource Manager. Las instrucciones se proporcionan con la plantilla para implementar la plantilla mediante Azure Portal, PowerShell o la CLI de Azure. Inicie sesión en la herramienta de su preferencia para implementar la plantilla con una cuenta que tenga los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
2. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
3. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete) de este artículo, ya sea mediante Azure Portal, PowerShell o la CLI de Azure.

## <a name="different-models"></a>Emparejamiento de redes virtuales creadas mediante diferentes modelos de implementación

Los pasos que se describieron en las secciones anteriores explicaban cómo crear un emparejamiento de redes virtuales entre dos redes virtuales creadas mediante el modelo de implementación de Resource Manager. También puede crear un emparejamiento entre una red virtual (Resource Manager) y una red virtual (clásica). No se puede crear un emparejamiento de redes virtuales entre dos redes virtuales creadas mediante el modelo de implementación clásica. Para más información sobre los modelos de implementación de Azure, lea el artículo [Descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para crear el emparejamiento de redes virtuales, complete los pasos del 1 al 11 que aparecen en la sección [Portal](#portal) de este artículo, pero elija el modelo de implementación **Clásica** cuando cree myVnet2 en el paso 3.

Cuando crea un emparejamiento de red virtual entre una red virtual (Resource Manager) y una red virtual (clásica), solo configura el emparejamiento a la red virtual (clásica) desde la red virtual (Resource Manager). Después de crear el emparejamiento para la red virtual (Resource Manager), el estado de su emparejamiento es **Actualizando**. El estado cambia automáticamente a **Conectado** después de unos segundos, porque no es necesario crear un emparejamiento para la red virtual (clásica). 

> [!NOTE]
> Aunque no se aborda esta opción en este tutorial, podría ajustar los scripts que aparecen en las secciones [CLI de Azure](#cli) y [PowerShell](#powershell) de este artículo para que reflejen las diferencias que se abarcan en esta sección.

## <a name="different-subscriptions"></a>Emparejamiento de redes virtuales en diferentes suscripciones

Los pasos que se describieron en las secciones anteriores explicaban cómo crear un emparejamiento de redes virtuales entre dos redes virtuales en la misma suscripción. También puede crear un emparejamiento entre redes virtuales en suscripciones diferentes, siempre que ambas suscripciones estén asociadas al mismo inquilino de Azure Active Directory. Si todavía no tiene un inquilino de AD, puede [crear uno](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rápidamente. Si las suscripciones están asociadas a distintos inquilinos de Active Directory, no puede crear un emparejamiento de redes virtuales entre redes virtuales en suscripciones diferentes. Los pasos para crear el emparejamiento son ligeramente distintos, en función del modelo de implementación mediante el cual se crearon las redes virtuales, como se indica a continuación:

### <a name="different-subscriptions-same-deployment-model"></a>Ambas redes virtuales se crearon mediante Resource manager

1. Complete los pasos del 1 al 7 que aparecen en la sección [Portal](#portal) de este artículo, pero elija una suscripción distinta (asociada al mismo inquilino de Azure Active Directory) para myVnet2 en el paso 5 de la que eligió para myVnet1 en el paso 4.
2. En la hoja **myVnet1** que aparece, haga clic en **Control de acceso (IAM)** en la lista vertical de opciones que aparece al lado izquierdo de la hoja.
3. En la hoja **myVnet1 - Access control (IAM)** (myVnet1: control de acceso [IAM]) que aparece, haga clic en **+ Agregar**.
4. En la hoja **Agregar permisos** que aparece, seleccione **Colaborador de la red** en el cuadro **Rol**.
5. En el cuadro **Seleccionar**, seleccione un nombre de usuario o escriba una dirección de correo electrónico para buscar uno. La lista de usuario que se muestra proviene del mismo inquilino de Azure Active Directory que la red virtual para la que configura el emparejamiento.
6. Haga clic en **Guardar**.
7. En la hoja **myVnet1 - Access control (IAM)** que aparece, haga clic en **Propiedades** en la lista vertical de opciones que aparece al lado izquierdo de la hoja. Copie el **ID. DE RECURSO** , que tiene un aspecto similar al ejemplo siguiente: /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1
8. Complete los pasos del 1 al 7 de esta sección para myVnet2.
9. Para asegurarse de que la autorización se habilitó correctamente, cierre la sesión de Azure y vuelva a iniciarla a continuación. Si usó distintas cuentas para las dos redes virtuales, cierre la sesión de ambas cuentas y vuelva a iniciarla en las dos.
10. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myResourceGroup*. Haga clic en **myResourceGroup** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para el grupo de recursos **myresourcegroup**. El grupo de recursos contiene las dos redes virtuales que se crearon en los pasos anteriores.
11. Haga clic en **myVNet1**.
12. En la hoja **myVnet1** que aparece, haga clic en **Emparejamientos** en la lista vertical de opciones que aparece al lado izquierdo de la hoja.
13. En la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos) que aparece, haga clic en **+ Agregar**
14. En la hoja **Agregar emparejamiento** que aparece, escriba o seleccione las opciones siguientes y, luego, haga clic en **Aceptar**:
     - **Nombre**: *myVnet1ToMyVnet2*
     - **Modelo de implementación de red virtual**: seleccione **Clásico**. 
     - **Conozco mi id. de recurso**: active esta casilla.
     - **Id. de recurso**: escriba el id. de recurso para myVnet2. Este cuadro solo aparece cuando se activa la casilla **Conozco mi id. de recurso**.
     - **Permitir acceso a red virtual:** asegúrese de que esté seleccionada la opción **Habilitado**.
    En este tutorial no se usa ninguna otra configuración. Para conocer todas las configuraciones de emparejamiento, lea [Manage virtual network peerings](virtual-network-manage-peering.md#create-peering) (Administración de emparejamientos de redes virtuales).
15. Una vez que hace clic en **Aceptar** en el paso anterior, se cierra la hoja **Agregar emparejamiento** y se vuelve a mostrar la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos). Unos segundos después, el emparejamiento que creó aparece en la hoja. El estado **Iniciado** aparece en la columna **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnet1ToMyVnet2** que creó. Emparejó Vnet1 con Vnet2, pero ahora debe emparejar Vnet2 con Vnet1. Debe crear el emparejamiento en ambas direcciones para permitir que los recursos de las redes virtuales se comuniquen entre sí.
16. Complete los pasos del 10 al 14 de esta sección nuevamente para myVnet2.  Asigne el nombre *myVnet2ToMyVnet1* al emparejamiento y escriba el id. de recurso de myVnet1 en **Id. de recurso**.
17. Unos segundos después de hacer clic en **Aceptar** para crear el emparejamiento de MyVnet2, el emparejamiento **myVnet2ToMyVnet1** que acaba de crear aparece con el estado **Conectado** en la columna **ESTADO DE EMPAREJAMIENTO**.
18. Complete los pasos del 10 al 11 de esta sección nuevamente para MyVnet1. **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnet1ToVNet2** ahora también aparece como **Conectado**. El emparejamiento se establece correctamente una vez que ve el estado **Conectado** en la columna **ESTADO DE EMPAREJAMIENTO** para las dos redes virtuales del emparejamiento.
19. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
20. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete-portal) de este artículo.

### <a name="different-subscriptions-different-deployment-models"></a>Una red virtual (Resource Manager), una red virtual (clásica)
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. Regístrese para la versión preliminar. No puede emparejar redes virtuales creadas mediante diferentes modelos de implementación que existen en distintas suscripciones hasta que se registre para la versión preliminar. No se puede registrar para la versión preliminar en el portal ni mediante la CLI de Azure. Solo se puede registrar mediante PowerShell. Complete las tareas siguientes para registrarse para obtener la versión preliminar:
    - Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Para iniciar una sesión de PowerShell desde un equipo Windows, vaya a **Inicio**, escriba **powershell** y, luego, haga clic en **PowerShell**.
    - En la ventana de PowerShell, inicie sesión en Azure con el comando `login-azurermaccount`. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
    - Registre la funcionalidad de versión preliminar para ambas suscripciones de Azure, escribiendo los comandos siguientes desde PowerShell: 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    Regístrese para la versión preliminar mientras está conectado en cada suscripción. No continúe con los pasos siguientes hasta que la salida de **RegistrationState** que reciba después de escribir el comando siguiente indique **Registrada** en ambas suscripciones.

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. Completes los pasos del 1 al 7 que aparecen en la sección [Portal](#portal) de este artículo con los cambios siguientes:
    - Elija una suscripción distinta (asociada al mismo inquilino de Azure Active Directory) para myVnet2 en el paso 5 de la que eligió para myVnet1 en el paso 4.
    - Elija **Clásica** en el paso 3 cuando cree myVnet2.
3. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myResourceGroup*. Haga clic en **myResourceGroup** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para el grupo de recursos **myresourcegroup**. El grupo de recursos contiene las dos redes virtuales que se crearon en los pasos anteriores.
4. Haga clic en **myVNet1**.
5. Complete los pasos del 2 al 9 que aparecen en la sección [Ambas redes virtuales se crearon mediante Resource Manager](#different-subscriptions-same-deployment-model) de este artículo.
6. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myResourceGroup*. Haga clic en **myResourceGroup** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para el grupo de recursos **myresourcegroup**. El grupo de recursos contiene las dos redes virtuales que se crearon en los pasos anteriores.
7. Haga clic en **myVNet1**.
8. En la hoja **myVnet1** que aparece, haga clic en **Emparejamientos** en la lista vertical de opciones que aparece al lado izquierdo de la hoja.
9. En la hoja **myVnet1 - Peerings** (myVnet1: emparejamientos) que aparece, haga clic en **+ Agregar**
10. En la hoja **Agregar emparejamiento** que aparece, escriba o seleccione las opciones siguientes y, luego, haga clic en **Aceptar**:
     - **Nombre**: *myVnet1ToMyVnet2*
     - **Modelo de implementación de red virtual**: seleccione **Clásico**. 
     - **Conozco mi id. de recurso**: active esta casilla.
     - **Id. de recurso**: escriba el id. de recurso para myVnet2. Este cuadro solo aparece cuando se activa la casilla **Conozco mi id. de recurso**.
     - **Permitir acceso a red virtual:** asegúrese de que esté seleccionada la opción **Habilitado**.
    En este tutorial no se usa ninguna otra configuración. Para conocer todas las configuraciones de emparejamiento, lea [Manage virtual network peerings](virtual-network-manage-peering.md#create-peering) (Administración de emparejamientos de redes virtuales).

    Cuando crea un emparejamiento de red virtual entre una red virtual (Resource Manager) y una red virtual (clásica), solo configura el emparejamiento a la red virtual (clásica) desde la red virtual (Resource Manager). Después de crear el emparejamiento para la red virtual (Resource Manager), el estado de su emparejamiento es **Actualizando**. El estado cambia automáticamente a **Conectado** después de unos segundos, porque no es necesario crear un emparejamiento para la red virtual (clásica). 
11. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
12. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete-portal) de este artículo.

> [!NOTE]
> Aunque no se aborda esta opción en este tutorial, podría ajustar los scripts que aparecen en las secciones [CLI de Azure](#cli) y [PowerShell](#powershell) de este artículo para que reflejen las diferencias que se abarcan en esta sección.

## <a name="permissions"></a>Permisos

Las cuentas que use para crear un emparejamiento de redes virtuales deben tener el rol o los permisos necesarios. Por ejemplo, si está emparejando dos redes virtuales denominadas VNet1 y VNet2, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:
    
|Red virtual|Modelo de implementación|Rol|Permisos|
|---|---|---|---|
|VNet1|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|VNet2|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminación de recursos
Cuando haya terminado este tutorial, es posible que quiera eliminar los recursos que creó en el tutorial, para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. En el cuadro de búsqueda del portal, escriba **myResourceGroup**. En los resultados de la búsqueda, haga clic en **myResourceGroup**.
2. En la hoja **myResourceGroup**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroup** y, luego, haga clic en **Eliminar**.

### <a name="delete-cli"></a>Azure CLI

En un shell de comandos de Linux, macOS o Windows, escriba el comando siguiente. También puede hacer clic en el botón Pruébelo que se encuentra en la esquina superior derecha del bloque de código siguiente para iniciar Cloud Shell. Luego, use el botón Copiar para copiar el código de ejemplo y pegarlo en Cloud Shell.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

En el símbolo del sistema de PowerShell, escriba el comando siguiente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

- Conozca en profundidad las [restricciones y comportamientos importantes del emparejamiento de redes virtuales](virtual-network-manage-peering.md#about-peering) antes de crear un emparejamiento de redes virtuales para su uso en el entorno de producción.
- Conozca toda la [configuración de emparejamiento de redes virtuales](virtual-network-manage-peering.md#create-peering).
- Obtenga información sobre cómo [crear una topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con emparejamiento de redes virtuales.

