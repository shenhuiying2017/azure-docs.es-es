---
title: Control del enrutamiento y las aplicaciones virtuales con la CLI de Azure | Microsoft Docs
description: "Obtenga información sobre cómo controlar el enrutamiento y las aplicaciones virtuales con la CLI de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: 6d03903d662ecac24fd4afc47134ce9f39af484f


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli"></a>Creación de rutas definidas por el usuario (UDR) en la CLI de Azure

> [!div class="op_single_selector"]
- [PowerShell](virtual-network-create-udr-arm-ps.md)
- [CLI de Azure](virtual-network-create-udr-arm-cli.md)
- [Plantilla](virtual-network-create-udr-arm-template.md)
- [PowerShell (clásico)](virtual-network-create-udr-classic-ps.md)
- [CLI (clásico)](virtual-network-create-udr-classic-cli.md)


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y el clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](../azure-resource-manager/resource-manager-deployment-model.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo.
>

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede crear rutas definidas por el usuario en el [modelo de implementación clásico](virtual-network-create-udr-classic-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, cree primero el entorno de prueba mediante la implementación de [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

1. Ejecute el comando siguiente para crear una tabla de rutas para la subred front-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    Salida:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    Parámetros:
   
   * **-g (o --resource-group)**. Nombre del grupo de recursos donde se creará el UDR. En este escenario, *TestRG*.
   * **-l (o --location)**. Región de Azure donde se creará el UDR nuevo. En este escenario, *TestRG*.
   * **-n (or --name)**. Nombre del UDR nuevo. En este escenario, *UDR-FrontEnd*.
2. Ejecute el comando siguiente para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    Salida:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    Parámetros:
   
   * **-r (o --route-table-name)**. Nombre de la tabla de rutas a la que se agregará la ruta. En este escenario, *UDR-FrontEnd*.
   * **-a (o --address-prefix)**. Prefijo de dirección de la subred a la que se destinan los paquetes. En este escenario, *192.168.2.0/24*.
   * **-y (o --next-hop-type)**. Tipo de objeto al que se enviará el tráfico. Los valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*.
   * **-p (o --next-hop-ip-address**). Dirección IP para el próximo salto. En este escenario, *192.168.0.4*.
3. Ejecute el comando siguiente para asociar la tabla de rutas que se ha creado anteriormente a la subred **FrontEnd**:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Salida:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    Parámetros:
   
   * **-e (o --vnet-name)**. Nombre de la red virtual donde se encuentra la subred. En este escenario, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creación de la ruta definida por el usuario para la subred back-end
Para crear la tabla de rutas y la ruta necesaria para la subred back-end según el escenario anterior, siga los siguientes pasos:

1. Ejecute el comando siguiente para crear una tabla de rutas para la subred back-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. Ejecute el comando siguiente para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. Ejecute el comando siguiente para asociar la tabla de rutas a la subred **BackEnd**:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Habilitación del reenvío IP en FW1
Para habilitar el reenvío IP en la NIC que ha usado **FW1**, siga los pasos siguientes:

1. Ejecute el comando siguiente y observe el valor de **Habilitar reenvío IP**. Se debe establecer en *false*.

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    Salida:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. Ejecute el comando siguiente para habilitar el reenvío IP:

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    Salida:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    Parámetros:
   
   * **-f (o --enable-ip-forwarding)**. *true* o *false*.




<!--HONumber=Feb17_HO3-->


