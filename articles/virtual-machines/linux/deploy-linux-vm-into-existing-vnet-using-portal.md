---
title: "Implementación de máquinas virtuales Linux en redes existentes con Azure Portal | Microsoft Docs"
description: "Implemente una máquina virtual Linux en una red virtual de Azure existente mediante el portal."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---

# Implementación de una máquina virtual Linux en una red virtual de Azure existente mediante Azure Portal
<a id="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal" class="xliff"></a>

En este artículo se muestra cómo implementar una máquina virtual (VM) en una red virtual existente (VNet). Los recursos de Azure como redes virtuales y grupos de seguridad de red deben ser recursos estáticos y de larga duración que rara vez se implementen. Una vez que se ha implementado una red virtual, se puede volver a utilizar con implementaciones continuas sin efectos adversos para la infraestructura. Piense en una red virtual como un conmutador de red de hardware tradicional. En ese caso, no necesitaría configurar un nuevo conmutador de hardware con cada implementación.  

Con una red virtual configurada correctamente, podemos seguir implementando nuevos servidores en esa red virtual una y otra vez con pocos, en caso de que haya, cambios necesarios durante la vida útil de la red virtual.

## Creación del grupo de recursos
<a id="create-the-resource-group" class="xliff"></a>

En primer lugar, creará un grupo de recursos para organizar todo lo que vaya a crear en este tutorial. Para más información sobre los grupos de recursos de Azure, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## Crear la red virtual
<a id="create-the-vnet" class="xliff"></a>

A continuación, creará una red virtual en la que iniciar las máquinas virtuales. La red virtual contiene una subred y está asociada con el grupo de seguridad de red con esta subred en un paso posterior.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## Incorporación de un VNic a la subred
<a id="add-a-vnic-to-the-subnet" class="xliff"></a>

Las tarjetas de red virtual (VNics) son importantes ya que puede conectarlas a distintas máquinas virtuales. En este enfoque la vNic se mantiene como recurso estático mientras que las máquinas virtuales pueden ser temporales. Cree una VNic y asóciela a la subred que creó en el paso anterior.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## Creación del grupo de seguridad de red
<a id="create-the-network-security-group" class="xliff"></a>

Los grupos de seguridad de red de Azure equivalen a un firewall en el nivel de red. Para más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../../virtual-network/virtual-networks-nsg.md)

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## Agregar regla de permiso de SSH entrante
<a id="add-an-inbound-ssh-allow-rule" class="xliff"></a>

La máquina virtual debe tener acceso desde Internet por lo que es necesario una regla que permita que el tráfico entrante en el puerto 22 pase por la red y vaya al puerto 22 de la máquina virtual creada.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## Asociación del grupo de seguridad de red a la subred
<a id="associate-the-nsg-with-the-subnet" class="xliff"></a>

Con la red virtual y la subred que ha creado, asocie el grupo de seguridad de red a la subred. Los grupos de seguridad de red se pueden asociar con una subred entera o una VNic individual. Gracias al firewall que filtra el tráfico en el nivel de subred, todas las VNics y máquinas virtuales dentro de la subred están protegidas por el grupo de seguridad de red. Otro enfoque es la asociación del grupo de seguridad de red con una sola VNic y la protección de una sola máquina virtual.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## Implemente la máquina virtual en la red virtual y el grupo de seguridad de red
<a id="deploy-the-vm-into-the-vnet-and-nsg" class="xliff"></a>

Mediante Azure Portal se implementa la máquina virtual Linux en el grupo de recursos de Azure, la red virtual, la subred y la VNic ya existentes.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Mediante el uso del portal para elegir recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente. Una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure.  

## Pasos siguientes
<a id="next-steps" class="xliff"></a>

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](../windows/cli-deploy-templates.md)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](create-cli-complete.md)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](create-ssh-secured-vm-from-template.md)

