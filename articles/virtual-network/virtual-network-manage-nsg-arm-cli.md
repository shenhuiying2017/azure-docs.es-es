---
title: "Administración de grupos de seguridad de red: CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a administrar grupos de seguridad de red mediante la interfaz de línea de comandos (CLI) 2.0 de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11ec0d3d9e33c06d4c0a164f7fba5dd5cca73872
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-network-security-groups-using-the-azure-cli-20"></a>Administración de grupos de seguridad de red con la CLI de Azure 2.0

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea 

Puede completar la tarea mediante una de las siguientes versiones de la CLI: 

- [CLI de Azure 1.0](virtual-network-manage-nsg-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager 
- [CLI de Azure 2.0](#View-existing-NSGs): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo).


[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>Requisito previo
Si todavía no lo ha hecho, instale y configure la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login). 


## <a name="view-existing-nsgs"></a>Consultar los NSG existentes
Para ver la lista de los grupos de seguridad de red en un grupo de recursos específico, ejecute el comando [az network nsg list](/cli/azure/network/nsg#list) con un formato de salida `-o table`:

```azurecli
az network nsg list -g RG-NSG -o table
```

Resultado esperado:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>Mostrar todas las reglas de un NSG
Para ver las reglas de un grupo de seguridad de red denominado **NSG-FrontEnd**, ejecute el comando [az network nsg show](/cli/azure/network/nsg#show) con el [filtro de consulta JMESPATH](/cli/azure/query-az-cli2) y el formato de salida `-o table`:

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

Resultado esperado:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> También puede usar el comando [az network nsg rule list](/cli/azure/network/nsg/rule#list) para mostrar solo las reglas personalizadas de un grupo de seguridad de red.
>

## <a name="view-nsg-associations"></a>Consultar las asociaciones de NSG

Para consultar cuáles son los recursos con los que está asociado el grupo de seguridad de red **NSG-FrontEnd**, ejecute el comando `az network nsg show` como se muestra a continuación. 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

Busque las propiedades **networkInterfaces** y **subnets** como se muestra a continuación:

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

En el ejemplo anterior, el NSG no está asociado a ninguna interfaz de red (NIC) y está asociado a una subred llamada **FrontEnd**.

## <a name="add-a-rule"></a>Agregar una regla
Para agregar una regla que permite el tráfico **de entrada** al puerto **443** desde cualquier máquina al grupo de seguridad de red **NSG-FrontEnd**, escriba el comando siguiente:

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

Resultado esperado:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>Cambiar una regla
Para cambiar la regla que se creó anteriormente y permitir el tráfico de entrada solo desde **Internet**, ejecute el comando [az network nsg rule update](/cli/azure/network/nsg/rule#update):

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

Resultado esperado:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>Eliminar una regla
Para eliminar la regla que se ha creado anteriormente, ejecute el comando siguiente:

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>Asociar un NSG a una NIC
Para asociar el grupo de seguridad de red **NSG-FrontEnd** a la NIC **TestNICWeb1**, ejecute el comando [az network nic update](/cli/azure/network/nic#update):

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

Resultado esperado:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>Desasociar un NSG de una NIC

Para desasociar el grupo de seguridad de red **NSG-FrontEnd** de la NIC **TestNICWeb1**, vuelva a ejecutar el comando [az network nsg rule update](/cli/azure/network/nsg/rule#update) pero sustituya el argumento `--network-security-group` por una cadena vacía (`""`).

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

En la salida, la clave `networkSecurityGroup` se establece en null.

## <a name="dissociate-an-nsg-from-a-subnet"></a>Desasociar un NSG de una subred
Para desasociar el grupo de seguridad de red **NSG-FrontEnd** de la subred **FrontEnd**, vuelva a ejecutar el comando [az network nsg rule update](/cli/azure/network/nsg/rule#update) pero sustituya el argumento `--network-security-group` por una cadena vacía (`""`).

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

En la salida, la clave `networkSecurityGroup` se establece en null.

## <a name="associate-an-nsg-to-a-subnet"></a>Asociación de un grupo de seguridad de red a una máquina virtual
Para volver a asociar el grupo de seguridad de red **NSG-FrontEnd** a la subred **FrontEnd**, ejecute el comando siguiente:

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

En la salida, la clave `networkSecurityGroup` tiene algo similar para el valor:

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>Eliminación de un grupo de seguridad de red
Solo se puede eliminar un NSG que no está asociado a ningún recurso. Para eliminar un NSG, siga estos pasos.

1. Para comprobar los recursos asociados a un NSG, ejecute `azure network nsg show` tal como aparece en [Consultar las asociaciones de NSG](#View-NSGs-associations).
2. Si el NSG está asociado a alguna NIC, ejecute `azure network nic set` tal como aparece en [Desasociar un NSG de una NIC](#Dissociate-an-NSG-from-a-NIC) para cada NIC. 
3. Si el NSG está asociado a alguna subred, ejecute `azure network vnet subnet set` tal como aparece en [Desasociar un NSG de una subred](#Dissociate-an-NSG-from-a-subnet) para cada subred.
4. Para eliminar el grupo de seguridad de red, ejecute el siguiente comando:

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>Pasos siguientes
* [Habilite el registro](virtual-network-nsg-manage-log.md) para los NSG.

