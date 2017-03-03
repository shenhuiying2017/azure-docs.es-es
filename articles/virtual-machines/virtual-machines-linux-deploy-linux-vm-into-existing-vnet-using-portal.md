---
title: "Implementación de VM de Linux en redes existentes - Azure Portal | Microsoft Docs"
description: "Implemente una máquina virtual Linux en una red virtual de Azure existente mediante el portal."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 8e75aa3b38df512dc93031d5a0e9047febe7ab74
ms.lasthandoff: 01/26/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>Implementación de una máquina virtual Linux en una red virtual y un grupo de seguridad de red existentes mediante el portal

En este artículo se describe cómo implementar una máquina virtual en una red virtual (VNet) existente.  Se recomienda que los recursos de Azure como las redes virtuales y los grupos de seguridad de red (NSG) deben ser estáticos y de larga duración que rara vez se implementan.  Una vez que se ha implementado una red virtual, se puede volver a utilizar con implementaciones continuas sin efectos adversos para la infraestructura.  Piense en una red virtual como un conmutador de red de hardware tradicional. En ese caso no necesitaría configurar un nuevo conmutador de hardware con cada implementación.  

Con una red virtual configurada correctamente, podemos implementar nuevos servidores en esa red virtual una y otra vez con pocos, en caso de que haya, cambios necesarios durante la vida útil de la red virtual.

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

En primer lugar, se implementará un grupo de recursos para organizar todo lo que se va a crear en este tutorial.  Para más información sobre los grupos de recursos de Azure, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createResourceGroup](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Crear la red virtual

El primer paso es crear una red virtual en la que iniciar las máquinas virtuales.  La red virtual contiene una subred y el grupo de seguridad de red se asociará a esta subred en un paso posterior.

![createVNet](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Incorporación de un VNic a la subred

Las tarjetas de red virtual (VNic) son importantes ya que se pueden conectar a diferentes máquinas virtuales, lo que hace que las VNic sean un recurso estático mientras que las máquinas virtuales pueden ser temporales. Cree una VNic y asóciela a la subred que creó en el paso anterior.

![createVNic](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>Crear el NSG

Los grupos de seguridad de red de Azure equivalen a un firewall en la capa de red. Para más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

![createNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Agregar regla de permiso de SSH entrante

La máquina virtual Linux debe tener acceso desde Internet por lo que se debe crear una regla que permita el tráfico entrante en el puerto 22 que pase a través de la red y vaya al puerto 22 de la máquina virtual Linux.

![createInboundSSH](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Asociación del grupo de seguridad de red a la subred

Una vez creadas la red virtual y la subred, se asocia el grupo de seguridad de red a la subred.  Los grupos de seguridad de red se pueden asociar con una subred completa o una VNic individual.  Con el firewall filtrando el tráfico en el nivel de la subred, todas las VNic y las máquinas virtuales dentro de la subred están protegidas por el grupo de seguridad de red, al contrario del caso en el que el grupo de seguridad de red se asocia a solo una VNic y protege solo una máquina virtual.

![associateNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implemente la máquina virtual en la red virtual y el grupo de seguridad de red

Mediante Azure Portal se implementa la máquina virtual Linux en el grupo de recursos de Azure, la red virtual, la subred y la VNic ya existentes.

![createVM](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Mediante el uso del portal para elegir los recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente.  Permítanos insistir en que una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure.  

## <a name="next-steps"></a>Pasos siguientes

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

