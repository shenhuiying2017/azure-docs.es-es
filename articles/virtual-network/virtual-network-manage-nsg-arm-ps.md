---
title: "Administración de grupos de seguridad de red (Azure PowerShell) | Microsoft Docs"
description: "Obtenga información sobre cómo administrar grupos de seguridad de red mediante PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca7f4926ca4edf9d20612aca74f6ae5f0ed847b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-network-security-groups-using-powershell"></a>Administración de grupos de seguridad de red mediante PowerShell

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Recuperar información
Puede consultar los NSG existentes, recuperar las reglas de un NSG existente y averiguar cuáles son los recursos a los que está asociado un NSG.

### <a name="view-existing-nsgs"></a>Consultar los NSG existentes
Para consultar todos los NSG existentes en una suscripción, ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup`.

Resultado esperado:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Para consultar la lista de NSG de un grupo de recursos específico, ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup`.

Resultado esperado:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>Mostrar todas las reglas de un NSG
Para consultar las reglas de un NSG llamado **NSG-FrontEnd**, escriba el siguiente comando:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

Resultado esperado:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> También puede usar `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` para enumerar las reglas predeterminadas desde el NSG **NSG-FrontEnd**.
> 

### <a name="view-nsgs-associations"></a>Consultar las asociaciones de NSG
Para consultar cuáles son los recursos con los que está asociado el NSG **NSG-FrontEnd**, ejecute el siguiente comando:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

Busque las propiedades **NetworkInterfaces** y **Subnets** como se muestra a continuación:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

En el ejemplo anterior, el NSG no está asociado a ninguna interfaz de red (NIC), sino a una subred llamada **FrontEnd**.

## <a name="manage-rules"></a>Administrar las reglas
Puede agregar reglas a un NSG existente, editar reglas existentes y quitar reglas.

### <a name="add-a-rule"></a>Agregar una regla
Para agregar una regla que permita el tráfico **de entrada** al puerto **443** desde cualquier máquina al grupo de seguridad de red **NSG-FrontEnd**, complete los pasos siguientes:

1. Ejecute el siguiente comando para recuperar el NSG existente y almacenarlo en una variable:

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Ejecute el siguiente comando para agregar una regla al NSG:

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Para guardar los cambios hechos en el NSG, ejecute el siguiente comando:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    La salida esperada que solo muestra las reglas de seguridad:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Cambiar una regla
Para cambiar la regla que se creó anteriormente y permitir el tráfico de entrada solo desde **Internet** , siga estos pasos.

1. Ejecute el siguiente comando para recuperar el NSG existente y almacenarlo en una variable:

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Ejecute el comando siguiente con la nueva configuración de regla:

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Para guardar los cambios hechos en el NSG, ejecute el siguiente comando:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    La salida esperada que solo muestra las reglas de seguridad:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Eliminar una regla
1. Ejecute el siguiente comando para recuperar el NSG existente y almacenarlo en una variable:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Ejecute el siguiente comando para quitar la regla del NSG:

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. Para guardar los cambios realizados en el NSG, ejecute el comando siguiente:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    La salida esperada que solo muestra las reglas de seguridad. Observe que ya no aparece **https-rule**:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Administrar las asociaciones
Puede asociar un NSG a subredes y NIC. También puede desasociar un NSG de cualquier recurso al que está asociado.

### <a name="associate-an-nsg-to-a-nic"></a>Asociar un NSG a una NIC
Para asociar el grupo de seguridad de red **NSG-FrontEnd** a la NIC **TestNICWeb1**, complete los pasos siguientes:

1. Ejecute el siguiente comando para recuperar el NSG existente y almacenarlo en una variable:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Ejecute el siguiente comando para recuperar la NIC existente y almacenarla en una variable:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. Para establecer la propiedad **NetworkSecurityGroup** de la variable **NIC** en el valor de la variable **NSG**, escriba el siguiente comando:

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. Para guardar los cambios hechos en la NIC, ejecute el siguiente comando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    La salida esperada que solo muestra la propiedad **NetworkSecurityGroup** :
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Desasociar un NSG de una NIC
Para desasociar el grupo de seguridad de red **NSG-FrontEnd** de la NIC **TestNICWeb1**, complete los pasos siguientes:

1. Ejecute el siguiente comando para recuperar la NIC existente y almacenarla en una variable:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. Para definir la propiedad **NetworkSecurityGroup** de la variable **NIC** en **$null**, ejecute el siguiente comando:

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. Para guardar los cambios hechos en la NIC, ejecute el siguiente comando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    La salida esperada que solo muestra la propiedad **NetworkSecurityGroup** :
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Desasociar un NSG de una subred
Para desasociar el grupo de seguridad de red **NSG-FrontEnd** de la subred **FrontEnd**, complete los pasos siguientes:

1. Ejecute el siguiente comando para recuperar la red virtual existente y almacenarla en una variable:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Ejecute el siguiente comando para recuperar la subred **FrontEnd** y almacenarla en una variable:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Para establecer la propiedad **NetworkSecurityGroup** de la variable **subnet** en **$null**, escriba el siguiente comando:

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. Para guardar los cambios hechos en la subred, ejecute el siguiente comando:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    La salida esperada que solo muestra las propiedades de la subred **FrontEnd** . Observe que no hay una propiedad para **NetworkSecurityGroup**:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Asociación de un grupo de seguridad de red a una máquina virtual
Para asociar el grupo de seguridad de red **NSG-FrontEnd** a la subred **FrontEnd**, complete los pasos siguientes:

1. Ejecute el siguiente comando para recuperar la red virtual existente y almacenarla en una variable:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Ejecute el siguiente comando para recuperar la subred **FrontEnd** y almacenarla en una variable:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Ejecute el siguiente comando para recuperar el NSG existente y almacenarlo en una variable:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. Para establecer la propiedad **NetworkSecurityGroup** de la variable **subnet** en **$null**, ejecute el siguiente comando:

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. Para guardar los cambios hechos en la subred, ejecute el siguiente comando:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    La salida esperada que solo muestra la propiedad **NetworkSecurityGroup** de la subred **FrontEnd**:
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Eliminación de un grupo de seguridad de red
Solo se puede eliminar un NSG que no está asociado a ningún recurso. Para eliminar un NSG, siga estos pasos.

1. Para comprobar los recursos asociados a un NSG, ejecute `azure network nsg show` tal como aparece en [Consultar las asociaciones de NSG](#View-NSGs-associations).
2. Si el NSG está asociado a alguna NIC, ejecute `azure network nic set` tal como aparece en [Desasociar un NSG de una NIC](#Dissociate-an-NSG-from-a-NIC) para cada NIC. 
3. Si el NSG está asociado a alguna subred, ejecute `azure network vnet subnet set` tal como aparece en [Desasociar un NSG de una subred](#Dissociate-an-NSG-from-a-subnet) para cada subred.
4. Para eliminar el grupo de seguridad de red, ejecute el siguiente comando:

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > El parámetro `-Force` garantiza que no necesite confirmar la eliminación.
   > 

## <a name="next-steps"></a>Pasos siguientes
* [Habilite el registro](virtual-network-nsg-manage-log.md) para los NSG.

