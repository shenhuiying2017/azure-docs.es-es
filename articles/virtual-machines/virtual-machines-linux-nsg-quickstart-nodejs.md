---
title: "Apertura de puertos para una máquina virtual Linux con la CLI de Azure 1.0 | Microsoft Docs"
description: "Más información sobre cómo abrir un puerto o crear un punto de conexión a la máquina virtual Linux mediante el modelo de implementación de Azure Resource Manager y la CLI de Azure 1.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 652a8a0dcb3216e9b762b85de56673beda74edc4
ms.openlocfilehash: b29b7fc56e1797d9daa33ff18df0fe5590b12612

---

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Apertura de puertos y puntos de conexión para una máquina virtual Linux en Azure con la CLI de Azure 1.0
En Azure, se abre un puerto o se crea un punto de conexión a una máquina virtual creando un filtro de red en una subred o una interfaz de red de máquina virtual. Estos filtros, que controlan el tráfico entrante y saliente, se colocan en un grupo de seguridad de red y se asocian al recurso que va a recibir dicho tráfico. Vamos a usar un ejemplo común de tráfico web en el puerto 80. Este artículo muestra cómo abrir un puerto a una máquina virtual mediante la CLI de Azure 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](#quick-commands): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0 (versión preliminar)](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI de última generación para el modelo de implementación de Resource Manager


## <a name="quick-commands"></a>Comandos rápidos
Para crear un grupo de seguridad de red y las reglas, tendrá que tener [la CLI de Azure 1.0](../xplat-cli-install.md) instalada y usar el modo de Resource Manager:

```azurecli
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `myNetworkSecurityGroup` y `myVnet`.

Cree el grupo de seguridad de red y escriba sus propios nombres y su propia ubicación según sea adecuado. En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup` en la ubicación `WestUS`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Agregue una regla que permita el tráfico HTTP hacia el servidor web (esta se puede ajustar para su propio escenario, por ejemplo, el acceso de SSH o la conectividad de base de datos). En el ejemplo siguiente, se crea una regla denominada `myNetworkSecurityGroupRule` para permitir el tráfico TCP en el puerto 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Asocie el grupo de seguridad de red con la interfaz de red (NIC) de la máquina virtual. En el ejemplo siguiente, se asocia una NIC existente denominada `myNic` con el grupo de seguridad de red llamado `myNetworkSecurityGroup`:

```azurecli
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Como alternativa, puede asociar el grupo de seguridad de red a una subred de red virtual, en lugar de únicamente a la interfaz de red en una única máquina virtual. En el ejemplo siguiente, se asocia una subred existente denominada `mySubnet` en la red virtual `myVnet` con el grupo de seguridad de red llamado `myNetworkSecurityGroup`:

```azurecli
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
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




<!--HONumber=Jan17_HO4-->


