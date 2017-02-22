---
title: "Creación de una red virtual mediante la CLI de Azure | Microsoft Docs"
description: Aprenda a crear una red virtual mediante la CLI de Azure | Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: ba7a67b8ae57da165f45bd3552a3dfac5f4ef64b
ms.openlocfilehash: 406fd637485799557edbd29fd6223ae535900818


---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creación de una red virtual mediante la CLI de Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure cuenta con dos modelos de implementación: Azure Resource Manager y el clásico. Microsoft recomienda crear recursos mediante el modelo de implementación de Resource Manager. Para más información acerca de las diferencias entre los dos modelos, lea el artículo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Descripción de los modelos de implementación de Azure).
 
En este artículo se describe cómo crear una red virtual con el modelo de implementación de Resource Manager mediante la interfaz de la línea de comandos (CLI) de Azure. También puede crear una red virtual mediante Resource Manager con otras herramientas o crear una red virtual a través del modelo de implementación clásica seleccionando una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Plantilla](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (clásico)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (clásico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (clásico)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Crear una red virtual

Para crear una red virtual mediante la CLI de Azure, realice los siguientes pasos:

1. Instale y configure la CLI de Azure siguiendo los pasos del artículo [Instalación y configuración de la interfaz de la CLI de Azure](../xplat-cli-install.md).

2. Ejecute el siguiente comando para crear una red virtual y una subred:

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
   * **-l (or --location)**. Región de Azure donde se creará la red virtual. En este escenario, *Central US*.
3. Ejecute el siguiente comando para crear una subred:

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
4. Ejecute el siguiente comando para ver las propiedades de la nueva red virtual:

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

- Una máquina virtual (VM) a una red virtual, para lo cual debería leer el artículo [Creación de una máquina virtual Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md). En lugar de crear una red virtual y la subred en los pasos de los artículos, puede seleccionar una red virtual y una subred a la que conectar una máquina virtual.
- La red virtual a otras redes virtuales. Para ello, lea el artículo [Conexión de redes virtuales](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- La red virtual a una red local mediante una red privada virtual (VPN) de sitio a sitio o un circuito ExpressRoute. Aprenda cómo hacerlo mediante los artículos [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Conexión de una red virtual a una red local mediante una VPN de sitio a sitio) y [Vinculación de una red virtual a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).


<!--HONumber=Feb17_HO3-->


