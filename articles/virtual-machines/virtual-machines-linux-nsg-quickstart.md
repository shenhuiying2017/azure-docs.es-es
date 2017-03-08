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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7f3abdd63e43713d9d1f7ff28e44efc08167fddb
ms.openlocfilehash: bb5065d0b541c7f8a51cd4c508b2d5455cc82957
ms.lasthandoff: 02/27/2017


---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-with-the-azure-cli-20"></a>Apertura de puertos y puntos de conexión para una máquina virtual Linux en Azure con la CLI de Azure 2.0
En Azure, se abre un puerto o se crea un punto de conexión a una máquina virtual creando un filtro de red en una subred o una interfaz de red de máquina virtual. Estos filtros, que controlan el tráfico entrante y saliente, se colocan en un grupo de seguridad de red y se asocian al recurso que va a recibir dicho tráfico. Vamos a usar un ejemplo común de tráfico web en el puerto 80. Este artículo muestra cómo abrir un puerto a una máquina virtual mediante la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](virtual-machines-linux-nsg-quickstart-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Comandos rápidos
Para crear reglas y un grupo de seguridad de red, necesita la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente instalada y con la sesión iniciada en una cuenta de Azure mediante [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `myNetworkSecurityGroup` y `myVnet`.

Cree el grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup` en la ubicación `westus`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Agregue una regla con [az network nsg rule create](/cli/azure/network/nsg/rule#create) que permita el tráfico HTTP hacia el servidor web (o adáptela a su propio escenario, por ejemplo, el acceso de SSH o la conectividad de base de datos). En el ejemplo siguiente, se crea una regla denominada `myNetworkSecurityGroupRule` para permitir el tráfico TCP en el puerto 80:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 80 --access allow
```

Asocie el grupo de seguridad de red con la interfaz de red (NIC) de la máquina virtual con [az network nic update](/cli/azure/network/nic#update). En el ejemplo siguiente, se asocia una NIC existente denominada `myNic` con el grupo de seguridad de red llamado `myNetworkSecurityGroup`:

```azurecli
az network nic update --resource-group myResourceGroup --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Como alternativa, puede asociar el grupo de seguridad de red a una subred de red virtual con [az network vnet subnet update](/cli/azure/network/vnet/subnet#update), en lugar de únicamente a la interfaz de red en una única máquina virtual. En el ejemplo siguiente, se asocia una subred existente denominada `mySubnet` en la red virtual `myVnet` con el grupo de seguridad de red llamado `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update --resource-group myResourceGroup \
    --vnet-name myVnet --name mySubnet --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Los grupos de seguridad de red y las reglas de ACL también se pueden definir como parte de las plantillas de Azure Resource Manager. Más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita utilizar el enrutamiento de puerto para asignar un único puerto externo a un puerto interno de su máquina virtual, use un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, puede exponer el puerto TCP 8080 externamente y dirigir el tráfico al puerto TCP 80 en una máquina virtual. Puede aprender sobre la [creación de un equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

* [Información general sobre Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
* [Información general de Azure Resource Manager para equilibradores de carga](../load-balancer/load-balancer-arm.md)


