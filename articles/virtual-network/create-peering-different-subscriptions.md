---
title: 'Crear un emparejamiento de redes virtuales de Azure: Resource Manager, suscripciones diferentes | Microsoft Docs'
description: Aprenda a crear un emparejamiento de redes virtuales entre redes virtuales creadas mediante Resource Manager que existen en diferentes suscripciones de Azure.
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
ms.openlocfilehash: 89ecd5ac2b8816e4efc5f8bf37dd7390bbf39ae8
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Crear un emparejamiento de redes virtuales: Resource Manager, suscripciones diferentes 

En este tutorial aprenderá a crear un emparejamiento de redes virtuales entre dos redes virtuales creadas mediante Resource Manager. Las redes virtuales se encuentran en suscripciones diferentes. Emparejar dos redes virtuales permite que los recursos en distintas redes virtuales se comuniquen entre sí con el mismo ancho de banda y latencia que tendrían los recursos si estuvieran en la misma red virtual. Obtenga más información sobre el [Emparejamiento de redes virtuales](virtual-network-peering-overview.md). 

Los pasos para crear un emparejamiento de redes virtuales cambian en función de si las redes virtuales se encuentran en la misma suscripción o en suscripciones diferentes, y en función del [modelo de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con el que se han creado las redes virtuales. Para más información sobre cómo crear un emparejamiento de redes virtuales en otros escenarios, haga clic en el escenario en la tabla siguiente:

|Modelo de implementación de Azure  | Suscripción de Azure  |
|--------- |---------|
|[Ambas mediante Resource Manager](virtual-network-create-peering.md) |Iguales|
|[Una mediante Resource Manager y la otra clásica](create-peering-different-deployment-models.md) |Iguales|
|[Una mediante Resource Manager y la otra, clásico](create-peering-different-deployment-models-subscriptions.md) |Diferentes|

No se puede crear un emparejamiento de redes virtuales entre dos redes virtuales implementadas mediante el modelo de implementación clásico. Si necesita conectar redes virtuales que se crearon a través del modelo de implementación clásica, puede usar una instancia de [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para conectar las redes virtuales. 

En este tutorial se emparejan redes virtuales de la misma región. La capacidad de emparejar redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. Complete los pasos de [Registrarse para la versión preliminar del emparejamiento de VNet global](#register) antes de intentar emparejar redes virtuales de diferentes regiones o, de lo contrario, el emparejamiento genera errores. La capacidad de conectar redes virtuales de diferentes regiones con [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) se encuentra disponible con carácter general y, por tanto, no se requiere el registro.

Puede usar [Azure Portal](#portal), Azure [PowerShell](#cli), la [interfaz de la línea de comandos](#powershell) (CLI) de Azure o la [plantilla de Azure Resource Manager](#template) para crear un emparejamiento de redes virtuales. Haga clic en cualquiera de los vínculos anteriores de herramientas para ir directamente a los pasos para crear un emparejamiento de redes virtuales con la herramienta de su preferencia.

## <a name="portal"></a>Creación de emparejamiento: Azure Portal

En este tutorial se usan cuentas diferentes para cada suscripción. Si está usando una cuenta que tiene permisos para ambas suscripciones puede usar la misma cuenta para todos los pasos, y omitir los pasos para cerrar sesión en el portal y para asignar a otro usuario permisos para las redes virtuales.

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
8. En el cuadro **Seleccionar**, seleccione otro usuario (UserB) o escriba la dirección de correo electrónico de UserB para buscarlo. La lista de usuario que se muestra proviene del mismo inquilino de Azure Active Directory que la red virtual para la que configura el emparejamiento.
9. Haga clic en **Guardar**.
10. En la hoja **myVnetA - Control de acceso (IAM)** que aparece, haga clic en **Propiedades** en la lista vertical de opciones que aparece a la izquierda de la hoja. Copie el **ID. DE RECURSO**, ya que se usará en un paso posterior. El identificador de recurso es similar al ejemplo siguiente: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Cierre sesión en el portal como UserA e inicie sesión como UserB.
12. Complete los pasos del 2 al 3 y especifique o seleccione los valores siguientes en el paso 3:

    - **Nombre**: *myVnetB*
    - **Espacio de direcciones**: *10.1.0.0/16*
    - **Nombre de subred**: *default*
    - **Intervalo de direcciones de subred**: *10.1.0.0/24*
    - **Suscripción**: seleccione la suscripción B
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba *myResourceGroupB*
    - **Ubicación**: *este de EE. UU.*

13. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myVnetB*. Haga clic en **myVnetB** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para la red virtual **myVnetB**.
14. En la hoja **myVnetB** que aparece, haga clic en **Propiedades** en la lista vertical de opciones que aparece a la izquierda de la hoja. Copie el **ID. DE RECURSO**, ya que se usará en un paso posterior. El identificador de recurso es similar al ejemplo siguiente: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Haga clic en **Control de acceso (IAM)** en la hoja **myVnetB** y complete los pasos del 5 al 10 para myVnetB (en el paso 8, escriba **UserA**).
16. Cierre sesión en el portal como UserB e inicie sesión como UserA.
17. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myVnetA*. Haga clic en **myVnetA** cuando aparezca en los resultados de la búsqueda. Aparece una hoja para la red virtual **myVnet**.
18. Haga clic en **myVnetA**.
19. En la hoja **myVnetA** que aparece, haga clic en **Emparejamientos** en la lista vertical de opciones que aparece a la izquierda de la hoja.
20. En la hoja **myVnetA - Emparejamientos** que aparece, haga clic en **+ Agregar**.
21. En la hoja **Agregar emparejamiento** que aparece, escriba o seleccione las opciones siguientes y, luego, haga clic en **Aceptar**:
     - **Nombre**: *myVnetAToMyVnetB*
     - **Modelo de implementación de red virtual**: seleccione **Resource Manager**.
     - **Conozco mi id. de recurso**: active esta casilla.
     - **Id. de recurso**: escriba el identificador de recurso del paso 14.
     - **Permitir acceso a red virtual:** asegúrese de que esté seleccionada la opción **Habilitado**.
    En este tutorial no se usa ninguna otra configuración. Para conocer todas las configuraciones de emparejamiento, lea [Manage virtual network peerings](virtual-network-manage-peering.md#create-a-peering) (Administración de emparejamientos de redes virtuales).
22. Una vez que haga clic en **Aceptar** en el paso anterior, se cerrará la hoja **Agregar emparejamiento** y se volverá a mostrar la hoja **myVnetA - Emparejamientos**. Unos segundos después, el emparejamiento que creó aparece en la hoja. El estado **Iniciado** aparece en la columna **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnetAToMyVnetB** que ha creado. Ha emparejado myVnetA con myVnetB, pero ahora debe emparejar myVnetB con myVnetA. Debe crear el emparejamiento en ambas direcciones para permitir que los recursos de las redes virtuales se comuniquen entre sí.
23. Cierre sesión en el portal como UserA e inicie sesión como UserB.
24. Complete nuevamente los pasos del 17 al 21 para MyVnetB. En el paso 21, asigne al emparejamiento el nombre *myVnetBToMyVnetA*, seleccione *myVnetA* para **Red Virtual** y escriba el identificador del paso 10 en el cuadro **Id. de recurso**.
25. Unos segundos después de hacer clic en **Aceptar** para crear el emparejamiento de myVnetB, el emparejamiento **myVnetBToMyVnetA** que acaba de crear aparece con el estado **Conectado** en la columna **ESTADO DE EMPAREJAMIENTO**.
26. Cierre sesión en el portal como UserB e inicie sesión como UserA.
27. Complete nuevamente los pasos del 17 al 19. El **ESTADO DE EMPAREJAMIENTO** correspondiente al emparejamiento **myVnetAToVNetB** ahora también aparece como **Conectado**. El emparejamiento se establece correctamente una vez que ve el estado **Conectado** en la columna **ESTADO DE EMPAREJAMIENTO** para las dos redes virtuales del emparejamiento. Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
28. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
29. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete-portal) de este artículo.

## <a name="cli"></a>Creación de emparejamiento: CLI de Azure

En este tutorial se usan cuentas diferentes para cada suscripción. Si está usando una cuenta que tiene permisos para ambas suscripciones, puede usar la misma cuenta para todos los pasos, omitir los pasos para cerrar sesión en Azure y quitar las líneas del script que crean las asignaciones de roles de usuario. Reemplace UserA@azure.com y UserB@azure.com en todos los scripts siguientes por los nombres de usuario que está usando para UserA y UserB.

El script siguiente:

- Requiere la versión 2.0.4 o posterior de la CLI de Azure. Para encontrar la versión ejecute `az --version`. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Uso de la CLI de Azure en Windows). 

En lugar de instalar la CLI y sus dependencias, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Haga clic en el botón **Pruébelo** en el script siguiente, que invoca un Cloud Shell en el que puede iniciar sesión con su cuenta de Azure. 

1. Abra una sesión de la CLI e inicie sesión en Azure como UserA mediante el comando `azure login`. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
2. Copie el script siguiente en un editor de texto del equipo, reemplace `<SubscriptionA-Id>` por el identificador de SubscriptionA, copie el script modificado, péguelo en la sesión de la CLI y pulse `Enter`. Si no conoce el identificador de la suscripción, escriba el comando "az account show". El valor de **id** en la salida es el identificador de la suscripción.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. Cierre sesión en Azure como UserA mediante el comando `az logout` e inicie sesión en Azure como UserB. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
4. Cree myVnetB. Copie el contenido del script del paso 2 en un editor de texto del equipo. Reemplace `<SubscriptionA-Id>` por el identificador de SubscriptionB. Cambie 10.0.0.0/16 a 10.1.0.0/16, y reemplace todas las apariciones de A por B, y todas las apariciones de B por A. Copie el script modificado, péguelo en la sesión de la CLI y pulse `Enter`. 
5. Cierre sesión en Azure como UserB e inicie sesión en Azure como UserA.
6. Cree un emparejamiento de redes virtuales myVnetA a myVnetB. Copie el contenido del script siguiente en un editor de texto del equipo. Reemplace `<SubscriptionB-Id>` por el identificador de SubscriptionB. Para ejecutar el script, copie el script modificado, péguelo en la sesión de la CLI y pulse ENTRAR.
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Compruebe el estado de emparejamiento de myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    El estado es **Iniciado**. Cuando haya creado el emparejamiento a myVnetA desde myVnetB, cambiará a **Conectado**.

8. Cierre sesión en Azure como UserA e inicie sesión en Azure como UserB.
9. Cree el emparejamiento de myVnetB a myVnetA. Copie el contenido del script del paso 6 en un editor de texto del equipo. Reemplace `<SubscriptionB-Id>` por el identificador de SubscriptionA, todas las apariciones de A por B, y todas las apariciones de B por A. Una vez realizados los cambios, copie el script modificado, péguelo en la sesión de la CLI y pulse `Enter`.
10. Compruebe el estado de emparejamiento de myVnetB. Copie el contenido del script del paso 7 en un editor de texto del equipo. Reemplace A por B en el nombre del grupo de recursos y de la red virtual, copie el script, pegue el script modificado en la sesión de la CLI y pulse `Enter`. El estado de emparejamiento es **Conectado**. El estado de emparejamiento de myVnetA cambiará a **Conectado** después de que haya creado el emparejamiento de myVnetB a myVnetA. Puede volver a iniciar sesión como UserA en Azure y realizar el paso 7 de nuevo para comprobar el estado de emparejamiento de myVnetA. 

    > [!NOTE]
    > El emparejamiento no se habrá establecido mientras el estado de emparejamiento de ambas redes virtuales no sea **Conectado**.

11. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
12. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli) de este artículo.

Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Creación de emparejamiento: PowerShell

En este tutorial se usan cuentas diferentes para cada suscripción. Si está usando una cuenta que tiene permisos para ambas suscripciones, puede usar la misma cuenta para todos los pasos, omitir los pasos para cerrar sesión en Azure y quitar las líneas del script que crean las asignaciones de roles de usuario. Reemplace UserA@azure.com y UserB@azure.com en todos los scripts siguientes por los nombres de usuario que está usando para UserA y UserB.

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell.
3. En PowerShell, inicie sesión en Azure como UserA. Para ello, escriba el comando `login-azurermaccount`. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
4. Cree un grupo de recursos y una red virtual A. Copie el script siguiente en un editor de texto del equipo. Reemplace `<SubscriptionA-Id>` por el identificador de SubscriptionA. Si no conoce el identificador de la suscripción, escriba el comando `Get-AzureRmSubscription` para verlo. El valor de **id** en la salida devuelta es el identificador de la suscripción. Para ejecutar el script, copie el script modificado, péguelo en PowerShell y pulse `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Cierre sesión en Azure como UserA e inicie sesión como UserB. La cuenta con la que inicie sesión debe tener todos los permisos necesarios para crear un emparejamiento de redes virtuales. Para detalles, consulte la sección [Permisos](#permissions) de este artículo.
6. Copie el contenido del script del paso 4 en un editor de texto del equipo. Reemplace `<SubscriptionA-Id>` por el identificador de SubscriptionB. Cambie 10.0.0.0/16 a 10.1.0.0/16. Reemplace todas las apariciones de A por B, y todas las apariciones de B por A. Para ejecutar el script, copie el script modificado, péguelo en PowerShell y pulse `Enter`.
7. Cierre sesión como UserB en Azure e inicie sesión como UserA.
8. Cree el emparejamiento de myVnetA a myVnetB. Copie el script siguiente en un editor de texto del equipo. Reemplace `<SubscriptionB-Id>` por el identificador de SubscriptionB. Para ejecutar el script, copie el script modificado, péguelo en PowerShell y pulse `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Compruebe el estado de emparejamiento de myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    El estado es **Iniciado**. Cuando haya configurado el emparejamiento a myVnetA desde myVnetB, cambiará a **Conectado**.

10. Cierre sesión en Azure como UserA e inicie sesión como UserB.
11. Cree el emparejamiento de myVnetB a myVnetA. Copie el contenido del script del paso 8 en un editor de texto del equipo. Reemplace `<SubscriptionB-Id>` por el identificador de la suscripción A y cambie todas las A por B y todas las B por A. Para ejecutar el script, copie el script modificado, péguelo en PowerShell y luego presione `Enter`.
12. Compruebe el estado de emparejamiento de myVnetB. Copie el contenido del script del paso 9 en un editor de texto del equipo. Reemplace A por B en el nombre del grupo de recursos y de la red virtual. Para ejecutar el script, pegue el script modificado en PowerShell y pulse `Enter`. El estado es **Conectado**. El estado de emparejamiento de **myVnetA** cambiará a **Conectado** después de que haya creado el emparejamiento de **myVnetB** a **myVnetA**. Puede volver a iniciar sesión como UserA en Azure y realizar el paso 9 de nuevo para comprobar el estado de emparejamiento de myVnetA. 

    > [!NOTE]
    > El emparejamiento no se habrá establecido mientras el estado de emparejamiento de ambas redes virtuales no sea **Conectado**.

    Los recursos de Azure que cree en cualquiera de las redes virtuales ahora se pueden comunicar entre sí mediante sus direcciones IP. Si usa la resolución de nombres predeterminada de Azure para las redes virtuales, los recursos de las redes virtuales no pueden resolver nombres entre las redes virtuales. Si desea resolver nombres entre las redes virtuales de un emparejamiento, debe crear su propio servidor DNS. Obtenga información sobre cómo configurar la [resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

13. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
14. **Opcional**: para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-powershell) de este artículo.

## <a name="template"></a>Creación de emparejamiento: plantilla de Resource Manager

1. Para crear una red virtual y asignar los [permisos](#permissions) correspondientes a la cuenta en cada suscripción, complete los pasos de las secciones [Portal](#portal), [CLI de Azure](#cli) o [PowerShell](#powershell) de este artículo.
2. Guarde el texto que sigue a un archivo en el equipo local. Reemplace `<subscription ID>` por el identificador de suscripción de UserA. Puede guardar el archivo como vnetpeeringA.json, por ejemplo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. Inicie sesión en Azure como UserA e implemente la plantilla mediante el [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine) o la [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique el nombre de archivo que ha guardado en el paso 2 para el texto del archivo json de ejemplo.
4. Copie el archivo json de ejemplo del paso 2 en un archivo del equipo y realice cambios en las líneas que comienzan por:
    - **name**: cambie *myVnetA/myVnetAToMyVnetB* por *myVnetB/myVnetBToMyVnetA*.
    - **id**: reemplace `<subscription ID>` por el identificador de suscripción de UserB y cambie *myVnetB* por *myVnetA*.
5. Vuelva a realizar el paso 3, con la sesión de Azure iniciada como UserB.
6. **Opcional**: Si bien este tutorial no aborda la creación de máquinas virtuales, puede crear una máquina virtual en cada red virtual y conectar de una máquina virtual a la otra para así validar la conectividad.
7. **Opcional**: Para eliminar los recursos que crea en este tutorial, complete los pasos que aparecen en la sección [Eliminación de recursos](#delete) de este artículo, ya sea mediante Azure Portal, PowerShell o la CLI de Azure.

## <a name="permissions"></a>Permisos

Las cuentas que use para crear un emparejamiento de redes virtuales deben tener el rol o los permisos necesarios. Por ejemplo, si está emparejando dos redes virtuales denominadas **myVnetA** y **myVnetB**, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:
    
|Red virtual|Rol|Permisos|
|---|---|---|
|myVnetA|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|myVnetB|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminación de recursos
Cuando haya terminado este tutorial, es posible que quiera eliminar los recursos que creó en el tutorial, para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. Inicie sesión en Azure Portal como UserA.
2. En el cuadro de búsqueda del portal, escriba **myResourceGroupA**. En los resultados de la búsqueda, haga clic en **myResourceGroupA**.
3. En la hoja **myResourceGroupA**, haga clic en el icono **Eliminar**.
4. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroupA** y, luego, haga clic en **Eliminar**.
5. Cierre sesión en el portal como UserA e inicie sesión como UserB.
6. Complete los pasos del 2 al 4 para myResourceGroupB.

### <a name="delete-cli"></a>Azure CLI

1. Inicie sesión en Azure como UserA y ejecute el comando siguiente:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Cierre sesión en Azure como UserA e inicie sesión como UserB.
3. Ejecute el comando siguiente:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Inicie sesión en Azure como UserA y ejecute el comando siguiente:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Cierre sesión en Azure como UserA e inicie sesión como UserB.
3. Ejecute el comando siguiente:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

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
