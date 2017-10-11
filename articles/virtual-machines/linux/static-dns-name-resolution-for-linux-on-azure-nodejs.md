---
title: "Uso de DNS interno para la resolución de nombre de máquina virtual en Azure | Microsoft Docs"
description: "Uso de DNS interno para la resolución de nombre de máquina virtual en Azure."
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
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: bfba2cf38a0624e8480a32bf153f391d820da5a1
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Uso de DNS interno para la resolución de nombre de máquina virtual en Azure

Este artículo muestra cómo establecer nombres de DNS internos estáticos para máquinas virtuales Linux mediante tarjetas NIC virtuales (VNic) y los nombres de etiqueta DNS. Los nombres de DNS estáticos se utilizan para los servicios de infraestructura permanente como un servidor de compilación Jenkins, que se usa para este documento o un servidor de Git.

Los requisitos son:

* [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);
* [archivos de clave SSH pública y privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](#quick-commands): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI de última generación para el modelo de implementación de administración de recursos


## <a name="quick-commands"></a>Comandos rápidos

Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos necesarios. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](#detailed-walkthrough).  

Requisitos previos: grupo de recursos, red virtual, NSG con SSH entrante, subred.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Creación de VNic con un nombre de DNS interno estático

La marca de la CLI de `-r` sirve para configurar la etiqueta de DNS, que proporciona el nombre de DNS estático de VNic.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Implementación de la máquina virtual en la red virtual y NSG, y conexión de VNic

`-N` se conecta VNic a la nueva máquina virtual durante la implementación en Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

Una integración completa continua y una infraestructura de implementación continua (CiCd) en Azure requiere que determinados servidores sean servidores estáticos o de larga duración.  Se recomienda que los recursos de Azure como las redes virtuales (vNet) y los grupos de seguridad de red (NSG) sean estáticos y de larga duración que se implementan con poca frecuencia.  Una vez que se ha implementado una red virtual, se puede volver a utilizar con nuevas implementaciones sin efectos adversos para la infraestructura.  La adición a esta red estática de un R Serverepositorio de Git y un servidor de automatización Jenkins proporciona CiCd a los entornos de desarrollo o prueba.  

Los nombres de DNS internos solo pueden resolverse dentro de una red virtual de Azure.  Dado que los nombres de DNS son internos, no pueden resolverse en Internet exterior, lo que proporciona una seguridad adicional a la infraestructura.

_Reemplace los ejemplos por su propia nomenclatura._

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Se precisa un grupo de recursos para organizar todo lo que se va a crear en este tutorial.  Para más información sobre los grupos de recursos de Azure, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Crear la red virtual

El primer paso es crear una red virtual en la que iniciar las máquinas virtuales.  La red virtual contiene una subred para este tutorial.  Para más información, consulte [Creación de una red virtual usando la CLI de Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Crear el NSG

La subred se crea de forma subyacente a un grupo de seguridad de red existente, por lo que crearemos el grupo antes de crear la subred.  Los grupos de seguridad de red de Azure equivalen a un firewall en la capa de red.  Para más información sobre los grupos de seguridad de red de Azure, consulte [Creación de grupos de seguridad de red en la CLI de Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

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
--source-address-prefix * \
--source-port-range * \
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

## <a name="creating-static-dns-names"></a>Creación de nombres de DNS estáticos

Azure es muy flexible, pero para utilizar nombres de DNS para la resolución de nombres de máquinas virtuales, debe crearlos como tarjetas de red virtual (VNic) con el etiquetado de DNS.  Las VNic son importantes ya que se pueden reutilizar conectándolas a diferentes máquinas virtuales, lo que hace que las VNic sean un recurso estático mientras que las máquinas virtuales pueden ser temporales.  Mediante el uso del etiquetado de DNS, se pueden habilitar la resolución de nombre simple desde otras máquinas virtuales en la red virtual.  El uso de nombres que se resuelven permite que otras máquinas virtuales tengan acceso al servidor de automatización mediante el nombre de DNS `Jenkins` o el servidor de Git como `gitrepo`.  Cree una VNic y asóciela a la subred que creó en el paso anterior.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implemente la máquina virtual en la red virtual y el grupo de seguridad de red

Ahora tenemos una red virtual, una subred dentro de esa red virtual y un grupo de seguridad de red que actúa como un firewall para proteger nuestra subred bloqueando todo el tráfico entrante excepto el del puerto 22 para SSH.  Ahora se puede implementar la máquina virtual dentro de esta infraestructura de red existente.

Mediante la CLI de Azure y el comando `azure vm create`, se implementa la máquina virtual Linux en el grupo de recursos de Azure, la red virtual, la subred y la VNic ya existentes.  Para más información sobre el uso de la CLI para implementar una máquina virtual completa, consulte [Creación de un entorno de Linux completo mediante la CLI de Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Mediante el uso de los marcadores CLI para llamar a los recursos existentes, se indica a Azure que implemente la máquina virtual dentro de la red existente.  Permítanos insistir en que una vez que se ha implementado una red virtual y una subred, estas pueden dejarse como recursos estáticos o permanentes dentro de su región de Azure.  

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
