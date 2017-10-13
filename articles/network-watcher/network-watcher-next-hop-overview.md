---
title: "Introducción al próximo salto en Azure Network Watcher | Microsoft Docs"
description: "En esta página se proporciona una introducción a la funcionalidad de próximo salto de Network Watcher."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bb2ca0486b3b3d27a77b70927cb3cbfbeac12c7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Introducción al próximo salto en Azure Network Watcher

El tráfico de una máquina virtual se envía a un destino en función de las rutas efectivas asociadas con una NIC. La funcionalidad Próximo salto obtiene el tipo de próximo salto y la dirección IP de un paquete de una máquina virtual y una NIC específicas. Esto sirve para determinar si el paquete se dirige hacia el destino o si el tráfico se está enviando a un agujero negro. Una configuración incorrecta de las rutas por parte del usuario, en la que un determinado tráfico se dirige a una ubicación local o a un dispositivo virtual, puede causar problemas de conectividad. La funcionalidad Próximo salto también devuelve la tabla de ruta asociada con el próximo salto. Al consultar un próximo salto, si la ruta se define como una ruta definida por el usuario, se devolverá esa ruta. De lo contrario, la funcionalidad devolverá "Ruta de sistema".

![información general sobre próximo salto][1]

La siguiente es una lista de los tipos de próximo salto que se pueden devolver al consultar la funcionalidad Próximo salto.

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

### <a name="next-steps"></a>Pasos siguientes

Aprenda a usar el próximo salto para detectar problemas de conectividad de red visitando [Comprobación del próximo salto en una máquina virtual](network-watcher-check-next-hop-portal.md)

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png













