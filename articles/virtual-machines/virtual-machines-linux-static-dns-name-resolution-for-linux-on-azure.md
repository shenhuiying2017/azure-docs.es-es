---
title: "Uso de DNS interno para la resolución de nombres de máquina virtual con la CLI de Azure 2.0 | Microsoft Docs"
description: "Creación de tarjetas de interfaz de red virtual y uso de DNS interno para la resolución de nombres de máquina virtual en Azure con la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 1aeb983730f732a021b828c658cc741f8659c487
ms.openlocfilehash: 06554f939a6c4f2336f68676612df51c673afbb2
ms.lasthandoff: 02/27/2017


---

# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Creación de tarjetas de interfaz de red virtual y uso de DNS interno para la resolución de nombres de máquina virtual en Azure
En este artículo se muestra cómo establecer nombres de DNS internos estáticos para máquinas virtuales Linux mediante tarjetas de interfaz de red virtual (vNic) y nombres de etiqueta DNS mediante la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](virtual-machines-linux-static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Los nombres de DNS estáticos se utilizan para los servicios de infraestructura permanente como un servidor de compilación Jenkins, que se usa para este documento o un servidor de Git.

Los requisitos son:

* [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);
* [archivos de clave SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos necesarios. En el resto del documento puede encontrar información más detallada y el contexto de cada paso, comenzando [aquí](#detailed-walkthrough). Para realizar estos pasos, necesita tener instalada la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente y haber iniciado sesión en una cuenta de Azure mediante [az login](/cli/azure/#login).

Requisitos previos: grupo de recursos, red virtual y subred, grupo de seguridad de red con SSH entrante.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Creación de una tarjeta de interfaz de red virtual con un nombre DNS interno estático
Cree la vNic con [az network nic create](/cli/azure/network/nic#create). La marca de la CLI `--internal-dns-name` sirve para configurar la etiqueta DNS, que proporciona el nombre de DNS para la tarjeta de interfaz de red virtual (vNic). En el ejemplo siguiente se crea una vNic llamada `myNic`, se conecta a la red virtual `myVnet` y se crea un registro de nombre de DNS interno llamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implementación de una máquina virtual y su conexión a la vNic
Cree la máquina virtual con [az vm create](/cli/azure/vm#create). La marca `--nics` conecta la vNic a la máquina virtual durante la implementación en Azure. En el ejemplo siguiente se crea una máquina virtual llamada `myVM` con Azure Managed Disks y se conecta la vNic llamada `myNic` del paso anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

Una integración completa continua y una infraestructura de implementación continua (CiCd) en Azure requiere que determinados servidores sean servidores estáticos o de larga duración. Se recomienda que los recursos de Azure como las redes virtuales y los grupos de seguridad de red sean estáticos y de larga duración que se implementan con poca frecuencia. Una vez que se ha implementado una red virtual, se puede volver a utilizar con nuevas implementaciones sin efectos adversos para la infraestructura. Más adelante puede agregar un servidor de repositorio GIT o un servidor de automatización de Jenkins para proporcionar CiCd a esta red virtual para los entornos de desarrollo y prueba.  

Los nombres de DNS internos solo pueden resolverse dentro de una red virtual de Azure. Dado que los nombres de DNS son internos, no pueden resolverse en Internet exterior, lo que proporciona una seguridad adicional a la infraestructura.

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `myNic` y `myVM`.

## <a name="create-the-resource-group"></a>Creación del grupo de recursos
En primer lugar, cree el grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Crear la red virtual

El siguiente paso es crear una red virtual en las que iniciar las máquinas virtuales. En este tutorial, la red virtual contiene una subred. Para más información sobre las redes virtuales de Azure, consulte [Creación de una red virtual mediante la CLI de Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada `myVnet` y una subred llamada `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Creación del grupo de seguridad de red
Los grupos de seguridad de red de Azure equivalen a un firewall en el nivel de red. Para más información sobre los grupos de seguridad de red, consulte [Creación de grupos de seguridad de red en la CLI de Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Cree el grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adición de una regla de entrada para permitir SSH
Agregue una regla de entrada para el grupo de seguridad de red con [az network nsg rule create](/cli/azure/network/nsg/rule#create). En el ejemplo siguiente se crea una regla denominada `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Asociación de la subred al grupo de seguridad de red
Para asociar la subred al grupo de seguridad de red, use [az network vnet subnet update](/cli/azure/network/vnet/subnet#update). En el ejemplo siguiente se asocia el nombre de la subred `mySubnet` al grupo de seguridad de red llamado `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Creación de la tarjeta de interfaz de red virtual y los nombres de DNS estáticos
Azure es muy flexible, pero para usar nombres de DNS para la resolución de nombres de máquina virtual, debe crear tarjetas de interfaz de red virtual (VNic) que incluyan una etiqueta DNS. Las vNics son importantes ya que puede reutilizarlas conectándolas a diferentes máquinas virtuales a través del ciclo de vida de infraestructura. En este enfoque la vNic se mantiene como recurso estático mientras que las máquinas virtuales pueden ser temporales. Mediante el uso de etiquetado de DNS en la vNic, se puede habilitar la resolución de nombres sencilla desde otras máquinas virtuales de la red virtual. El uso de nombres que se resuelven permite que otras máquinas virtuales tengan acceso al servidor de automatización mediante el nombre de DNS `Jenkins` o el servidor de Git como `gitrepo`.  

Cree la vNic con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crea una vNic llamada `myNic`, se conecta a la red virtual `myVnet``myVnet` y se crea un registro de nombre de DNS interno llamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementación de la máquina virtual en la infraestructura de la red virtual
Ahora tenemos una red virtual, una subred, una vNic y un grupo de seguridad de red que actúa como firewall para proteger nuestra subred al bloquear todo el tráfico entrante excepto el del puerto 22 para SSH. Ahora se puede implementar la máquina virtual dentro de esta infraestructura de red existente.

Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual llamada `myVM` con Azure Managed Disks y se conecta la vNic llamada `myNic` del paso anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Mediante el uso de los marcadores CLI para llamar a los recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente. Permítanos insistir en que una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure.  

## <a name="next-steps"></a>Pasos siguientes

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

