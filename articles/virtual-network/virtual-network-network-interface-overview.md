---
title: Interfaces de red de Azure| Microsoft Docs
description: "Aprenda sobre las interfaces de red de Azure y cómo se usan con máquinas virtuales."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 395cff80b3f97b6340e15f370c13f783e2f5dde3
ms.lasthandoff: 02/28/2017


---
# <a name="what-are-network-interfaces"></a>¿Qué son las interfaces de red?

Una interfaz de red (NIC) es la interconexión entre una máquina virtual (VM) y la red de software subyacente. Este artículo explica qué es una interfaz de red y cómo se utiliza en el modelo de implementación de Azure Resource Manager.

Microsoft recomienda implementar nuevos recursos utilizando el modelo de implementación de Resource Manager, pero también puede implementar máquinas virtuales con conectividad de red en el modelo de implementación [clásico](virtual-network-ip-addresses-overview-classic.md) . Si está familiarizado con el modelo clásico, hay diferencias importantes en las cuestiones relacionadas con las redes de máquinas virtuales en el modelo de implementación de Resource Manager. Infórmese sobre las diferencias leyendo el artículo sobre [Redes de máquinas virtuales (modelo clásico)](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

En Azure, una interfaz de red:

1. Es un recurso que se puede crear o eliminar, y que tiene sus propios valores configurables.
2. Tiene que estar conectada a una subred de una instancia de Azure Virtual Network (VNet) cuando se crea. Si no está familiarizado con las redes virtuales, puede informarse leyendo el artículo [Información general sobre redes virtuales](virtual-networks-overview.md) . La interfaz de red tiene que estar conectada a una red virtual que exista en la misma [ubicación](https://azure.microsoft.com/regions) y [suscripción](../azure-glossary-cloud-terminology.md#subscription) de Azure en la que se encuentre la NIC. Una vez creada la NIC, puede cambiar la subred a la que está conectada, pero no la red virtual.
3. Tiene un nombre asignado que no se puede cambiar una vez creada la NIC. El nombre tiene que ser único dentro de un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups)de Azure, pero no es necesario que sea único dentro de la suscripción, de la ubicación de Azure en la que se crea o de la red virtual a la que está conectada. Normalmente se crean varias NIC dentro de una suscripción de Azure. Se recomienda que diseñe una convención de nomenclatura que haga más fácil la administración de varias interfaces de red que el uso de nombres predeterminados. Consulte [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md) (Convenciones de nomenclatura recomendadas para los recursos de Azure) para obtener sugerencias.
4. Se puede conectar a una máquina virtual, pero a una sola máquina virtual que se encuentre en la misma ubicación que la NIC.
5. Tiene una dirección MAC, que se mantiene con la interfaz de red mientras esta permanezca conectada a una máquina virtual. La dirección MAC se mantiene tanto si se reinicia la máquina virtual (desde dentro del sistema operativo), como si se detiene (desasigna) y se inicia usando Azure Portal, Azure PowerShell o la interfaz de la línea de comandos de Azure. Si se desconecta de una máquina virtual y se conecta a otra máquina virtual diferente, la interfaz de red recibe una dirección MAC diferente. Si se elimina la interfaz de red, la dirección MAC se asigna a otras interfaces de red.
6. Tiene que tener una dirección IP dinámica o estática **privada** *IPv4* principal asignada.
7. Puede tener uno o varios recursos de dirección IP pública asociados a ella. Para más información, lea la documentación sobre la [asignación de varias direcciones IP por NIC](virtual-network-multiple-ip-addresses-portal.md).
8. Admite Accelerated Networking con virtualización de E/S de raíz única (SR-IOV) para tamaños específicos de máquina virtual que ejecuten versiones específicas del sistema operativo Microsoft Windows Server. Para más información acerca de esta característica de versión preliminar, lea el artículo [Accelerated Networking para una máquina virtual](virtual-network-accelerated-networking-powershell.md) .
9. Puede recibir tráfico no destinado a direcciones IP privadas que tenga asignadas, si está habilitado el reenvío IP para la NIC. Si una máquina virtual está ejecutando software de firewall por ejemplo, enruta paquetes no destinados a sus propias direcciones IP. La máquina virtual tiene que ejecutar software capaz de enrutar o reenviar el tráfico, pero para ello es necesario que esté habilitado el reenvío IP para una NIC.
10. A menudo se crea en el mismo grupo de recursos que la máquina virtual a la que está conectada o la misma red virtual a la que está conectada, aunque no es necesario que sea así.

## <a name="vms-with-multiple-network-interfaces"></a>Máquinas virtuales con varias interfaces de red
Se pueden asociar varias NIC a una máquina virtual pero, al hacerlo, tenga en cuenta lo siguiente:  

* El tamaño de la máquina virtual debe admitir varias NIC. Para más información acerca de los tamaños de máquina virtual que admiten varias NIC, consulte los artículos sobre [Tamaños de máquinas virtuales de Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) o [Tamaños de la máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-sizes.md).
* La máquina virtual se debe crear con al menos dos NIC. Si la máquina virtual se crea con solo una NIC, incluso aunque el tamaño de la máquina virtual admita más de una, no se podrán asociar NIC adicionales a la máquina virtual después de crearla. Siempre que la máquina virtual se cree con al menos dos NIC, podrá asociar NIC adicionales a la máquina virtual después de crearla, siempre y cuando el tamaño de la máquina virtual admita más de dos NIC.  
* Puede desasociar NIC secundarias (la NIC principal no se puede desasociar) de una máquina virtual si esta tiene al menos tres NIC asociadas a ella. No se puede desasociar NIC si la máquina virtual tiene dos o menos NIC conectadas a ella.  
* El orden de las NIC desde la máquina virtual será aleatorio y también podría cambiar en todas las actualizaciones de infraestructura de Azure. Sin embargo, las direcciones IP y las direcciones MAC Ethernet correspondientes seguirán siendo las mismas. Por ejemplo, suponga que el sistema operativo identifica NIC1 de Azure como Eth1. Eth1 tiene la dirección IP 10.1.0.100 y la dirección MAC 00-0D-3A-B0-39-0D. Después de actualizar y reiniciar una infraestructura de Azure, el sistema operativo puede identificar ahora a Azure NIC1 como Eth2, pero las direcciones IP y MAC serán las mismas que tenían cuando el sistema operativo identificó a Azure NIC1 como Eth1. Cuando es un cliente quien ejecuta un reinicio, el orden de NIC seguirá siendo el mismo en el sistema operativo.  
* Si la máquina virtual es miembro de un [conjunto de disponibilidad](../azure-glossary-cloud-terminology.md#availability-set), todas las máquinas virtuales dentro del conjunto de disponibilidad deben tener una sola NIC o varias. Si las máquinas virtuales tienen varias NIC, no es necesario que el número que cada una de ellas tiene sea el mismo, siempre que cada máquina virtual tenga al menos dos NIC.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a crear una máquina virtual con una sola NIC leyendo el artículo sobre [creación de una máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Aprenda a crear una máquina virtual con varias interfaces de red leyendo el artículo sobre [implementación de una máquina virtual con varias NIC](virtual-network-deploy-multinic-arm-ps.md) .
* Para obtener información acerca de cómo crear una NIC con varias configuraciones de IP consulte el artículo sobre [asignación de varias direcciones IP para máquinas virtuales de Azure](virtual-network-multiple-ip-addresses-powershell.md) .


