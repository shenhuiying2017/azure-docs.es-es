---
title: "Apertura de puertos para una máquina virtual Linux con la CLI de Azure 2.0 | Microsoft Docs"
description: "Más información sobre cómo abrir un puerto o crear un punto de conexión a la máquina virtual Linux mediante el modelo de implementación de Azure Resource Manager y la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: d176187fe465264b5f433260de5178b48ca9dd4a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Abrir puertos y puntos de conexión para una máquina virtual de Linux con la CLI de Azure
En Azure, se abre un puerto o se crea un punto de conexión a una máquina virtual creando un filtro de red en una subred o una interfaz de red de máquina virtual. Estos filtros, que controlan el tráfico entrante y saliente, se colocan en un grupo de seguridad de red y se asocian al recurso que va a recibir dicho tráfico. Vamos a usar un ejemplo común de tráfico web en el puerto 80. Este artículo muestra cómo abrir un puerto a una máquina virtual mediante la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](nsg-quickstart-nodejs.md).


## <a name="quick-commands"></a>Comandos rápidos
Para crear reglas y un grupo de seguridad de red, necesita la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente instalada y con la sesión iniciada en una cuenta de Azure mediante [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *myNetworkSecurityGroup* y *myVnet*.

Cree el grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente se crea un grupo de seguridad de red denominado *myNetworkSecurityGroup* en la ubicación *eastus*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Agregue una regla con [az network nsg rule create](/cli/azure/network/nsg/rule#create) que permita el tráfico HTTP hacia el servidor web (o adáptela a su propio escenario, por ejemplo, el acceso de SSH o la conectividad de base de datos). En el ejemplo siguiente, se crea una regla denominada *myNetworkSecurityGroupRule* para permitir el tráfico TCP en el puerto 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

Asocie el grupo de seguridad de red con la interfaz de red (NIC) de la máquina virtual con [az network nic update](/cli/azure/network/nic#update). En el ejemplo siguiente, se asocia una NIC existente denominada *myNic* con el grupo de seguridad de red llamado*myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Como alternativa, puede asociar el grupo de seguridad de red a una subred de red virtual con [az network vnet subnet update](/cli/azure/network/vnet/subnet#update), en lugar de únicamente a la interfaz de red en una única máquina virtual. En el ejemplo siguiente, se asocia una subred existente denominado *mySubnet* de la red virtual *myVnet* con el grupo de seguridad de red llamado *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](tutorial-virtual-network.md#secure-network-traffic).

Para las aplicaciones web de alta disponibilidad, debe colocar las máquinas virtuales detrás de una instancia de Azure Load Balancer. El equilibrador de carga distribuye el tráfico a las máquinas virtuales, con un grupo de seguridad de red que proporciona el filtrado del tráfico. Para más información, consulte [Equilibrio de la carga de máquinas virtuales Linux en Azure para crear una aplicación de alta disponibilidad](tutorial-load-balancer.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

* [Información general sobre Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [¿Qué es un grupo de seguridad de red?](../../virtual-network/virtual-networks-nsg.md)
