---
title: "Implementación de una máquina virtual Linux en una red virtual de Azure existente mediante la CLI | Microsoft Docs"
description: "Implemente una máquina virtual Linux en una red virtual de Azure existente mediante la CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2fad20978f40150ef9f1cb44054da2ba66848bda
ms.openlocfilehash: 613ce9b27bc26643b2f46c490d7f550b370df998


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-cli"></a>Implementación de una máquina virtual Linux en una red virtual y un grupo de seguridad de red existentes mediante la CLI

En este artículo se describe cómo usar los marcadores CLI para implementar una máquina virtual en una red virtual existente (VNet) que está protegida mediante un grupo de seguridad de red (NSG).  Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);

- [archivos de clave SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandos rápidos

Reemplace los ejemplos por su propia configuración.

### <a name="create-the-resource-group"></a>Crear el grupo de recursos

```azurecli
azure group create myResourceGroup \
-l westus
```

### <a name="create-the-vnet"></a>Crear la red virtual

```azurecli
azure network vnet create myVNet \
-g myResourceGroup \
-a 10.10.0.0/24 \
-l westus
```

### <a name="create-the-nsg"></a>Crear el NSG

```azurecli
azure network nsg create myNSG \
-g myResourceGroup \
-l westus
```

### <a name="add-an-inbound-ssh-allow-rule"></a>Agregar regla de permiso de SSH entrante

```azurecli
azure network nsg rule create inboundSSH \
-g myResourceGroup \
-a myNSG \
-c Allow \
-p Tcp \
-r Inbound \
-y 100 \
-f Internet \
-o 22 \
-e 10.10.0.0/24 \
-u 22
```

### <a name="add-a-subnet-to-the-vnet"></a>Agregar una subred a la red virtual

```azurecli
azure network vnet subnet create mySubNet \
-g myResourceGroup \
-e myVNet \
-a 10.10.0.0/26 \
-o myNSG
```

### <a name="add-a-vnic-to-the-subnet"></a>Incorporación de un VNic a la subred

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Implementar la máquina virtual en la red virtual, en el grupo de seguridad de red y conectar la VNic

```azurecli
azure vm create myVM \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-n myVM \
-F myVNet \
-j mySubnet \
-N myVNic
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

Se recomienda que los recursos de Azure como las redes virtuales y los grupos de seguridad de red deben ser estáticos y de larga duración que rara vez se implementan.  Una vez que se ha implementado una red virtual, se puede volver a utilizar con nuevas implementaciones sin efectos adversos para la infraestructura.  Piense en una red virtual como un conmutador de red de hardware tradicional. En ese caso no necesitaría configurar un nuevo conmutador de hardware con cada implementación.  Con una red virtual configurada correctamente, podemos implementar nuevos servidores en esa red virtual una y otra vez con pocos, en caso de que haya, cambios necesarios durante la vida útil de la red virtual.

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

En primer lugar, se creará un grupo de recursos para organizar todo lo que se va a crear en este tutorial.  Para más información sobre los grupos de recursos de Azure, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Crear la red virtual

El primer paso es crear una red virtual en la que iniciar las máquinas virtuales.  La red virtual contiene una subred para este tutorial.  Para más información, consulte [Creación de una red virtual usando la CLI de Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Crear el NSG

La subred se crea de forma subyacente a un grupo de seguridad de red existente, por lo que crearemos el grupo antes de crear la subred.  Los grupos de seguridad de red de Azure equivalen a un firewall en la capa de red.  Para más información sobre los grupos de seguridad de red de Azure, consulte [Creación de grupos de seguridad de red en la CLI de Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Agregar regla de permiso de SSH entrante

La máquina virtual Linux debe tener acceso desde Internet por lo que es necesaria una regla que permita el tráfico entrante en el puerto 22 que pase a través de la red y vaya al puerto 22 de la máquina virtual Linux.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix Internet \
--source-port-range 22 \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Agregar una subred a la red virtual

Las máquinas virtuales dentro de la red virtual deben estar ubicadas en una subred.  Cada red virtual puede tener varias subredes.  Cree la subred y asóciela con el grupo de seguridad de red para le agregue un firewall.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Ahora, la subred ya se agregó dentro de la red virtual y se asoció con el grupo de seguridad de red y la regla de este.


## <a name="add-a-vnic-to-the-subnet"></a>Incorporación de un VNic a la subred

Las tarjetas de red virtual (VNic) son importantes ya que se pueden reutilizar conectándolas a diferentes máquinas virtuales, lo que hace que las VNic sean un recurso estático mientras que las máquinas virtuales pueden ser temporales.  Cree una VNic y asóciela a la subred que creó en el paso anterior.

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implemente la máquina virtual en la red virtual y el grupo de seguridad de red

Ahora tenemos una red virtual, una subred dentro de esa red virtual y un grupo de seguridad de red que actúa como un firewall para proteger nuestra subred bloqueando todo el tráfico entrante excepto el del puerto 22 para SSH.  Ahora se puede implementar la máquina virtual dentro de esta infraestructura de red existente.

Mediante la CLI de Azure y el comando `azure vm create`, se implementa la máquina virtual Linux en el grupo de recursos de Azure, la red virtual, la subred y la VNic ya existentes.  Para más información sobre el uso de la CLI para implementar una máquina virtual completa, consulte [Creación de un entorno de Linux completo mediante la CLI de Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create myVM \
--resource-group myResourceGroup \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name myVNic
```

Mediante el uso de los marcadores CLI para llamar a los recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente.  Permítanos insistir en que una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure.  

## <a name="next-steps"></a>Pasos siguientes

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Nov16_HO5-->


