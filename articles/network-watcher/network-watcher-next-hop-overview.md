---
title: Introducción al próximo salto en Azure Network Watcher | Microsoft Docs
description: En este artículo se proporciona una introducción a la funcionalidad de próximo salto de Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180392"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Uso del Próximo salto para diagnosticar problemas de enrutamiento de máquina virtual

El tráfico de una máquina virtual se envía a un destino en función de las rutas efectivas asociadas con una interfaz de red (NIC). La funcionalidad Próximo salto obtiene el tipo de próximo salto y la dirección IP de un paquete de una máquina virtual y una NIC específicas. Conocer el próximo salto le ayuda a determinar si se está dirigiendo tráfico hacia el destino deseado o si el tráfico no se está enviando a ningún sitio. Una configuración incorrecta de las rutas, en la que un determinado tráfico se dirige a una ubicación local o a un dispositivo virtual, puede causar problemas de conectividad. La funcionalidad Próximo salto también devuelve la tabla de ruta asociada con el próximo salto. Si la ruta se define como una ruta definida por el usuario, se devolverá esa ruta. De lo contrario, la funcionalidad devolverá **Ruta de sistema**.

![información general sobre próximo salto](./media/network-watcher-next-hop-overview/figure1.png)

Los próximos saltos que pueden obtenerse por la función de próximo salto son los siguientes:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* None

Para obtener más información sobre cada tipo de próximo salto, consulte [Enrutamiento del tráfico de redes virtuales](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo utilizar el próximo salto para diagnosticar problemas de enrutamiento de red de VM, consulte [ Diagnosticar un problema de enrutamiento de red de máquina virtual con Azure Portal](diagnose-vm-network-routing-problem.md).
