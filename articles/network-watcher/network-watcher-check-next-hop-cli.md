---
title: "Búsqueda del próximo salto con Next Hop de Azure Network Watcher: CLI de Azure | Microsoft Docs"
description: "En este artículo se describe cómo encontrar el tipo del próximo salto y la dirección IP mediante la funcionalidad Next Hop con la CLI de Azure."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 625618d200d1049b419128879a49f9e58f3a7627
ms.lasthandoff: 02/23/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli"></a>Obtenga más información sobre el tipo del próximo salto con la funcionalidad Next Hop de Azure Network Watcher mediante la CLI de Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [API de REST de Azure](network-watcher-check-next-hop-rest.md)


Next Hop es una característica de Network Watcher que permite obtener el tipo del próximo salto y la dirección IP para una máquina virtual especificada. Esta característica es útil para determinar si el tráfico que sale de una máquina virtual atraviesa una puerta de enlace, Internet o redes virtuales para llegar a su destino.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se usa la CLI de Azure para averiguar el tipo de próximo salto y la dirección IP.

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Creación de una instancia de Network Watcher](network-watcher-create.md) para crear un monitor de red. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo usa Next Hop, una característica de Network Watcher, para averiguar el tipo de próximo salto y la dirección IP de un recurso. Para más información acerca de Next Hop, consulte la [introducción a Next Hop](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Obtención del próximo salto

Para obtener el próximo salto, llamamos al cmdlet `azure netowrk watcher next-hop`. Pasamos al cmdlet el grupo de recursos de Network Watcher, NetworkWatcher, el identificador de la máquina virtual, la dirección IP de origen y dirección IP de destino. En este ejemplo, la dirección IP de destino es una máquina virtual en otra red virtual. Hay una puerta de enlace de red virtual entre las dos redes virtuales.

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Si la máquina virtual tiene varias NIC y el reenvío de IP está habilitado en cualquiera de ellas, debe especificarse el parámetro de NIC (-i nic-id). De lo contrario, es opcional.

## <a name="review-results"></a>Revisión de los resultados

Una vez finalizado, se proporcionan los resultados. Se devuelve la dirección IP del próximo salto y el tipo de recurso que es.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

La lista siguiente muestra los valores de NextHopType disponibles actualmente:

**Tipo de próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>Pasos siguientes

Aprenda cómo revisar la configuración del grupo de seguridad de red mediante programación en [Auditoría de NSG con Network Watcher](network-watcher-nsg-auditing-powershell.md).

