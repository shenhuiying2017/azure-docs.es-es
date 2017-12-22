---
title: "Control de enrutamiento en Azure Virtual Network - CLI - Clásico | Microsoft Docs"
description: "Aprenda cómo controlar el enrutamiento en redes virtuales mediante la CLI de Azure en el modelo de implementación clásico"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 8fcb98723e7e872c932908e3456dc8680deb0901
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Control del enrutamiento y uso de aplicaciones virtuales (clásico) mediante la CLI de Azure

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [CLI de Azure](virtual-network-create-udr-arm-cli.md)
> * [Plantilla](virtual-network-create-udr-arm-template.md)
> * [PowerShell (clásico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (clásico)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación clásico. También puede [controlar el enrutamiento y el uso de aplicaciones virtuales en el modelo de implementación del Administrador de recursos](virtual-network-create-udr-arm-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos según aparecen en este documento, cree primero el entorno mostrado en la [creación de una red virtual (clásico) mediante la CLI de Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

1. Ejecute el siguiente comando para cambiar al modo clásico:

    ```azurecli
    azure config mode asm
    ```

    Salida:

        info:    New mode is asm

2. Ejecute el siguiente comando para crear una tabla de rutas para la subred front-end:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Salida:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parámetros:
   
   * **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *TestRG*.
   * **-n (o --name)**. Nombre del nuevo grupo de seguridad de red. En este escenario, *NSG-FrontEnd*.
3. Ejecute el siguiente comando para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la máquina virtual **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Salida:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parámetros:
   
   * **-r (o --route-table-name)**. Nombre de la tabla de rutas a la que se agregará la ruta. En este escenario, *UDR-FrontEnd*.
   * **-a (o --address-prefix)**. Prefijo de dirección de la subred a la que se destinan los paquetes. En este escenario, *192.168.2.0/24*.
   * **-t (o --next-hop-type)**. Tipo de objeto al que se enviará el tráfico. Los valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*.
   * **-p (o --next-hop-ip-address**). Dirección IP para el próximo salto. En este escenario, *192.168.0.4*.
4. Ejecute el siguiente comando para asociar la tabla de rutas que se ha creado a la subred **FrontEnd**:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Salida:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parámetros:
   
   * **-t (o --vnet-name)**. Nombre de la red virtual donde se encuentra la subred. En este escenario, *TestVNet*.
   * **-n (o --subnet-name**. Nombre de la subred a la que se agregará la tabla de rutas. En este escenario, *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creación de la ruta definida por el usuario para la subred back-end
Para crear la tabla de rutas y la ruta necesarias para la subred back-end según el escenario, siga estos pasos:

1. Ejecute el siguiente comando para crear una tabla de rutas para la subred back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Ejecute el comando siguiente para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Ejecute el siguiente comando para asociar la tabla de rutas a la subred **BackEnd**:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

