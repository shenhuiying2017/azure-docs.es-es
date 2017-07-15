---
title: "Implementación de máquinas virtuales Linux en una red existente con la CLI de Azure 1.0 | Microsoft Docs"
description: "Implementación de una máquina virtual Linux en una red virtual existente mediante la CLI de Azure 1.0"
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
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---

# Implementación de una máquina virtual Linux en una red virtual de Azure existente mediante la CLI de Azure 1.0
<a id="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10" class="xliff"></a>

En este artículo se muestra cómo usar la CLI de Azure 1.0 para implementar una máquina virtual (VM) en una red virtual (VNet) existente. Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);
- [archivos de clave SSH pública y privada](mac-create-ssh-keys.md).


## Versiones de la CLI para completar la tarea
<a id="cli-versions-to-complete-the-task" class="xliff"></a>
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](#quick-commands): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos


## Comandos rápidos
<a id="quick-commands" class="xliff"></a>

Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos necesarios. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough).

Requisitos previos: grupo de recursos, red virtual, NSG con SSH entrante, subred. Reemplace los ejemplos por su propia configuración.

### Implementación de la máquina virtual en la infraestructura de la red virtual
<a id="deploy-the-vm-into-the-virtual-network-infrastructure" class="xliff"></a>

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## Tutorial detallado
<a id="detailed-walkthrough" class="xliff"></a>

Los recursos de Azure, como las redes virtuales y los grupos de seguridad de red, deben ser recursos estáticos y de larga duración que se implementen en raras ocasiones. Una vez que se ha implementado una red virtual, se puede volver a utilizar con nuevas implementaciones sin efectos adversos para la infraestructura. Imagine que una red virtual es un conmutador de red de hardware tradicional. No sería preciso configurar un conmutador nuevo en cada implementación. Con una red virtual configurada correctamente, podemos seguir implementando nuevos servidores en esa red virtual una y otra vez con pocos, en caso de que haya, cambios necesarios durante la vida útil de la red virtual.

## Creación del grupo de recursos
<a id="create-the-resource-group" class="xliff"></a>

En primer lugar, creará un grupo de recursos para organizar todo lo que vaya a crear en este tutorial. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

```azurecli
azure group create myResourceGroup --location eastus
```

## Crear la red virtual
<a id="create-the-vnet" class="xliff"></a>

El primer paso es crear una red virtual en la que iniciar las máquinas virtuales. La red virtual contiene una subred para este tutorial. Para más información, consulte [Creación de una red virtual usando la CLI de Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## Creación del grupo de seguridad de red
<a id="create-the-network-security-group" class="xliff"></a>

La subred se crea detrás de un grupo de seguridad de red existente, de modo que cree primero el grupo de seguridad de red y después la subred. Los grupos de seguridad de red de Azure equivalen a un firewall en el nivel de red. Para más información sobre los grupos de seguridad de red de Azure, consulte [Creación de grupos de seguridad de red en la CLI de Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md).

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## Agregar regla de permiso de SSH entrante
<a id="add-an-inbound-ssh-allow-rule" class="xliff"></a>

La máquina virtual debe tener acceso desde Internet por lo que es necesario una regla que permita que el tráfico entrante en el puerto 22 pase por la red y vaya al puerto 22 de la máquina virtual.

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

## Agregar una subred a la red virtual
<a id="add-a-subnet-to-the-vnet" class="xliff"></a>

Las máquinas virtuales dentro de la red virtual deben estar ubicadas en una subred. Cada red virtual puede tener varias subredes. Cree la subred y asóciela con el grupo de seguridad de red.

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

La subred se agrega ahora dentro de la red virtual asociada con la regla y el grupo de seguridad de red.


## Incorporación de un VNic a la subred
<a id="add-a-vnic-to-the-subnet" class="xliff"></a>

Las tarjetas de interfaz de red virtual (VNics) son importantes porque las puede reutilizar conectándolas a diferentes máquinas virtuales. En este enfoque la vNic se mantiene como recurso estático mientras que las máquinas virtuales pueden ser temporales. Cree una VNic y asóciela a la subred que creó en el paso anterior.

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## Implemente la máquina virtual en la red virtual y el grupo de seguridad de red
<a id="deploy-the-vm-into-the-vnet-and-nsg" class="xliff"></a>

Ahora tiene una red virtual y una subred dentro de ella, además de un grupo de seguridad de red que sirve para proteger la subred al bloquear todo el tráfico de entrada, excepto el del puerto 22 para SSH. Ahora se puede implementar la máquina virtual dentro de esta infraestructura de red existente.

Mediante la CLI de Azure y el comando `azure vm create`, se implementa la máquina virtual Linux en el grupo de recursos de Azure, la red virtual, la subred y la VNic ya existentes. Para más información sobre el uso de la CLI para implementar una máquina virtual completa, consulte [Creación de un entorno de Linux completo mediante la CLI de Azure](create-cli-complete.md)

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

Por medio de marcadores de CLI para llamar a los recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente. Una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure.  

## Pasos siguientes
<a id="next-steps" class="xliff"></a>

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](../windows/cli-deploy-templates.md)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](create-cli-complete.md)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](create-ssh-secured-vm-from-template.md)

