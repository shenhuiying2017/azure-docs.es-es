---
title: Control del enrutamiento y las aplicaciones virtuales con la CLI de Azure 2.0 | Microsoft Docs
description: "Obtenga información sobre cómo controlar el enrutamiento y las aplicaciones virtuales con la CLI de Azure 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Creación de rutas definidas por el usuario (UDR) en la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [CLI de Azure](virtual-network-create-udr-arm-cli.md)
> * [Plantilla](virtual-network-create-udr-arm-template.md)
> * [PowerShell: implementación clásica](virtual-network-create-udr-classic-ps.md)
> * [CLI: implementación clásica](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea 

Puede completar la tarea mediante una de las siguientes versiones de la CLI: 

- [CLI de Azure 1.0](virtual-network-create-udr-arm-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager 
- [CLI de Azure 2.0](#Create-the-UDR-for-the-front-end-subnet): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo).

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, cree primero el entorno de prueba mediante la implementación de [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

1. Cree una tabla de rutas para la subred front-end con el comando [az network route-table create](/cli/azure/network/route-table#create):

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    Salida:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. Cree una ruta que envíe todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la máquina virtual **FW1** (192.168.0.4) con el comando [az network route-table route create](/cli/azure/network/route-table/route#create):

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    Salida:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    Parámetros:

    * **--route-table-name**. Nombre de la tabla de rutas a la que se agregará la ruta. En este escenario, *UDR-FrontEnd*.
    * **--address-prefix**. Prefijo de dirección de la subred a la que se destinan los paquetes. En este escenario, *192.168.2.0/24*.
    * **--next-hop-type**. Tipo de objeto al que se enviará el tráfico. Los valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*.
    * **--next-hop-ip-address**. Dirección IP para el próximo salto. En este escenario, *192.168.0.4*.

3. Ejecute el comando [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) para asociar la tabla de rutas creada anteriormente a la subred **FrontEnd**:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    Salida:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    Parámetros:
    
    * **--vnet-name**. Nombre de la red virtual donde se encuentra la subred. En este escenario, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creación de la ruta definida por el usuario para la subred back-end

Para crear la tabla de rutas y la ruta necesaria para la subred back-end según el escenario anterior, siga los siguientes pasos:

1. Ejecute el comando siguiente para crear una tabla de rutas para la subred back-end:

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. Ejecute el comando siguiente para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4):

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. Ejecute el comando siguiente para asociar la tabla de rutas a la subred **BackEnd**:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Habilitación del reenvío IP en FW1

Para habilitar el reenvío IP en la NIC que ha usado **FW1**, siga los pasos siguientes:

1. Ejecutar el comando [az network nic show](/cli/azure/network/nic#show) con un filtro JMESPATH para mostrar el valor actual **enable-ip-forwarding** de **Habilitar reenvío IP**. Se debe establecer en *false*.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    Salida:

        false

2. Ejecute el comando siguiente para habilitar el reenvío IP:

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    Puede examinar la salida que se transmite a la consola de o simplemente vuelva a probar el valor **enableIpForwarding** específico:

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    Salida:

        true

    Parámetros:

    **--ip-forwarding**: *true* o *false*.

