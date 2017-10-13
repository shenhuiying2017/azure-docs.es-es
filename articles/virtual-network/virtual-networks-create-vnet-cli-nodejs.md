---
title: "Creación de una red virtual mediante la CLI de Azure 1.0 | Microsoft Docs"
description: Aprenda a crear una red virtual mediante la CLI de Azure 1.0 | Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creación de una red virtual mediante la CLI de Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure cuenta con dos modelos de implementación: Azure Resource Manager y el clásico. Microsoft recomienda crear recursos mediante el modelo de implementación de Resource Manager. Para más información acerca de las diferencias entre los dos modelos, lea el artículo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Descripción de los modelos de implementación de Azure).

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 2.0](virtual-networks-create-vnet-arm-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.
- [CLI de Azure 1.0](#create-a-virtual-network): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Crear una red virtual

Para crear una red virtual mediante la CLI de Azure, realice los siguientes pasos:

1. Instale y configure la CLI de Azure siguiendo los pasos del artículo [Instalación y configuración de la interfaz de la CLI de Azure](../cli-install-nodejs.md).

2. Creación de una red virtual y una subred:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Resultado esperado:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Parámetros usados:

   * **--vnet**. Nombre de la red virtual que se va a crear. En este escenario, *TestVNet*
   * **-e (o --address-space)**. Espacio de direcciones de red virtual. En este escenario, *192.168.0.0*
   * **-i (o -cidr)**. Máscara de red en formato CIDR. En este escenario, *16*.
   * **-n (o --subnet-name**). Nombre de la primera subred. En este escenario, *FrontEnd*.
   * **-p (or --subnet-start-ip)**. Dirección IP inicial de la subred o el espacio de direcciones de la subred. En este escenario, *192.168.1.0*.
   * **-r (o --subnet-cidr)**. Máscara de red en formato CIDR para subred. En este escenario, *24*.
   * **-l (o --location)**. Región de Azure donde se crea la red virtual. En este escenario, *Central US*.

3. Creación de una subred:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Resultado esperado:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Parámetros usados:

   * **-t (o --vnet-name**. Nombre de la red virtual donde se creará la subred. En este escenario, *TestVNet*.
   * **-n (or --name)**. Nombre de la nueva subred. En este escenario, *BackEnd*.
   * **-a (or --address-prefix)**. Bloque CIDR de subred. En este escenario, *192.168.2.0/24*.
   
4. Para ver las propiedades de la red virtual nueva:

    ```azurecli
    azure network vnet show
    ```
   
    Resultado esperado:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Pasos siguientes

Aprenda a conectar:

- Una máquina virtual (VM) a una red virtual, para lo cual debería leer el artículo [Creación de una máquina virtual Linux](../virtual-machines/linux/quick-create-cli.md). En lugar de crear una red virtual y la subred en los pasos de los artículos, puede seleccionar una red virtual y una subred a la que conectar una máquina virtual.
- La red virtual a otras redes virtuales. Para ello, lea el artículo [Conexión de redes virtuales](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- La red virtual a una red local mediante una red privada virtual (VPN) de sitio a sitio o un circuito ExpressRoute. Aprenda cómo hacerlo leyendo los artículos [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Conexión de una red virtual a una red local mediante una VPN de sitio a sitio) y [Conexión de una red virtual a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).