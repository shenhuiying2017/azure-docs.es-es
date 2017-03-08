---
title: "Implementación de una máquina virtual Linux en la red existente con la CLI de Azure 2.0 | Microsoft Docs"
description: "Obtenga información acerca de cómo implementar una máquina virtual Linux en una red virtual existente mediante la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 67d4fee2fc59651903d4c02d1fce84c7b81e5da1
ms.openlocfilehash: c56ad780a1d67102d23c84a18c712ae48cec1eb6
ms.lasthandoff: 02/27/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network"></a>Implementación de una máquina virtual Linux en una red virtual existente

En este artículo se muestra cómo utilizar la CLI de Azure 2.0 para implementar una máquina virtual (VM) en una red virtual existente. Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);
- [archivos de clave SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos necesarios. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](#detailed-walkthrough).

Para crear este entorno personalizado, necesita la versión más reciente de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) instalada y con la sesión iniciada en una cuenta de Azure mediante [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `myVnet` y `myVM`.

**Requisitos previos:** grupo de recursos de Azure, red virtual y subred, grupo de seguridad de red con SSH entrante y una tarjeta de interfaz de red virtual.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementación de la máquina virtual en la infraestructura de la red virtual

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

Se recomienda que los recursos de Azure como las redes virtuales y los grupos de seguridad de red sean estáticos y de larga duración que se implementan con poca frecuencia. Una vez que se ha implementado una red virtual, se puede volver a utilizar con nuevas implementaciones sin efectos adversos para la infraestructura. Piense en una red virtual como un conmutador de red de hardware tradicional. En ese caso no necesitaría configurar un nuevo conmutador de hardware con cada implementación. Con una red virtual configurada correctamente, podemos seguir implementando nuevas máquinas virtuales en esa red virtual una y otra vez con pocos, en caso de que haya, cambios necesarios durante la vida útil de la red virtual.

Para crear este entorno personalizado, necesita la versión más reciente de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) instalada y con la sesión iniciada en una cuenta de Azure mediante [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `myVnet` y `myVM`.

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

En primer lugar, se crea un grupo de recursos de Azure para organizar todo lo que se va a crear en este tutorial. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cree el grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westus`:

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>Crear la red virtual

Vamos a crear una red virtual de Azure en la que iniciar las máquinas virtuales. Para obtener más información sobre las redes virtuales, consulte [Creación de una red virtual mediante la CLI de Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada `myVnet` y una subred llamada `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>Creación del grupo de seguridad de red

Los grupos de seguridad de red de Azure equivalen a un firewall en el nivel de red. Para obtener más información sobre los grupos de seguridad de red, consulte [Creación de grupos de seguridad de red en la CLI de Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cree el grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Agregar regla de permiso de SSH entrante

La máquina virtual con Linux debe tener acceso desde Internet, por lo que es necesaria una regla que permita el tráfico entrante en el puerto 22 que pase a través de la red y vaya al puerto 22 de la máquina virtual con Linux. Agregue una regla de entrada para el grupo de seguridad de red con [az network nsg rule create](/cli/azure/network/nsg/rule#create). En el ejemplo siguiente se crea una regla denominada `myNetworkSecurityGroupRuleSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>Asociación de la subred con el grupo de seguridad de red

Las reglas del grupo de seguridad de red se pueden aplicar a una subred o una interfaz de red virtual específica. Vamos a asociar el grupo de seguridad de red a nuestra subred. Asocie la subred al grupo de seguridad de red con [az network vnet subnet update](/cli/azure/network/vnet/subnet#update):

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>Adición de una tarjeta de interfaz de red virtual a la subred

Las tarjetas de interfaz de red virtual (VNics) son importantes porque las puede reutilizar conectándolas a diferentes máquinas virtuales. Esta reutilización le permite mantener la tarjeta de interfaz de red como un recurso estático, mientras que las máquinas virtuales pueden ser temporales. Cree una tarjeta de interfaz de red y asóciela a la subred con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementación de la máquina virtual en la infraestructura de la red virtual

Ahora tenemos una red virtual, una subred y un grupo de seguridad de red que actúa como un firewall para proteger nuestra subred bloqueando todo el tráfico entrante excepto el del puerto 22 para SSH. Ahora se puede implementar la máquina virtual dentro de esta infraestructura de red existente.

Creee la máquina virtual con [az vm create](/cli/azure/vm#create). Para más información sobre las marcas que se deben usar con la CLI de Azure 2.0 para implementar una máquina virtual completa, consulte [Creación de un entorno de Linux completo mediante la versión preliminar de la CLI de Azure 2.0](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

En el ejemplo siguiente se crea una máquina virtual mediante Azure Managed Disks. Estos discos se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Para más información acerca de los discos administrados, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Introducción a los discos administrados de Azure). Si desea usar discos no administrados, consulte la nota adicional a continuación.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

Si usa Azure Managed Disks, omita este paso. Si desea usar discos no administrados, debe agregar los parámetros adicionales siguientes al comando de continuación para crear los discos no administrados en la cuenta de almacenamiento denominada `mystorageaccount`: 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

Mediante el uso de los marcadores CLI para llamar a los recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente. Insistamos en que una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure. En este ejemplo, no creamos ni asignamos una dirección IP pública a la tarjeta de interfaz de red, por lo que esta máquina virtual no es accesible públicamente a través de Internet. Para obtener más información, consulte [Creación de una máquina virtual con una dirección IP pública estática mediante la CLI de Azure](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de los modos de crear máquinas virtuales en Azure, vea los siguientes recursos:

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

