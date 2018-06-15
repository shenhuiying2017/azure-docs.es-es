---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 749cc5a5e5b8417abe602b7e37c103a26cc4dc03
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805189"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Cómo crear una red virtual clásica mediante la CLI de Azure
Puede utilizar la CLI de Azure para administrar los recursos de Azure desde el símbolo del sistema de cualquier equipo que ejecute Windows, Linux o bien OSX.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](../articles/cli-install-nodejs.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Para crear una red virtual y una subred, ejecute el comando **azure network vnet create**:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Resultado esperado:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Nombre de la red virtual que se va a crear. En este escenario, *TestVNet*
   * **-e (o --address-space)**. Espacio de direcciones de red virtual. Para este escenario, *192.168.0.0*
   * **-i (o -cidr)**. Máscara de red en formato CIDR. Para este escenario, *16*
   * **-n (o --subnet-name**). Nombre de la primera subred. Para este escenario, *FrontEnd*
   * **-p (or --subnet-start-ip)**. Dirección IP inicial de la subred o el espacio de direcciones de la subred. Para este escenario, *192.168.1.0*
   * **-r (o --subnet-cidr)**. Máscara de red en formato CIDR para subred. Para este escenario, *24*
   * **-l (or --location)**. Región de Azure donde se crea la red virtual. Para este escenario, *Centro de EE. UU.*
3. Para crear una subred, ejecute el comando **azure network subnet create**:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Salida esperada del comando anterior:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (o --vnet-name**. Nombre de la red virtual donde se creará la subred. Para este escenario, *TestVNet*.
   * **-n (o --name)**. Nombre de la nueva subred. Para este escenario, *BackEnd*.
   * **-a (o --address-prefix)**. Bloque CIDR de subred. Para este escenario, *192.168.2.0/24*.
4. Para ver las propiedades de la nueva red virtual, ejecute el comando **azure network vnet show**:
   
            azure network vnet show
   
    Salida esperada del comando anterior:
   
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

