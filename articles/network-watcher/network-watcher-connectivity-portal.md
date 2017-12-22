---
title: "Comprobación de la conectividad con Azure Network Watcher: Azure Portal | Microsoft Docs"
description: "Esta página explica cómo usar la comprobación de conectividad con Network Watcher usando Azure Portal"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Comprobación de la conectividad con Azure Network Watcher usando Azure Portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [API de REST de Azure](network-watcher-connectivity-rest.md)

Aprenda a usar la conectividad para comprobar si se puede establecer una conexión TCP directa de una máquina virtual a un punto de conexión determinado.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se da por hecho que tiene los siguientes recursos:

* Una instancia de Network Watcher en la región cuya conectividad quiere comprobar.

* Máquinas virtuales con las que comprobar la conectividad.

> [!IMPORTANT]
> La comprobación de conectividad requiere una extensión de máquina virtual `AzureNetworkWatcherExtension`. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/windows/extensions-nwa.md), y en una máquina virtual con Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="check-connectivity-to-a-virtual-machine"></a>Comprobación de la conectividad a una máquina virtual

En este ejemplo se comprueba la conectividad a una máquina virtual de destino a través del puerto 80.

Vaya a Network Watcher y haga clic en **Comprobación de conectividad (versión preliminar)**. Seleccione la máquina virtual para la que desea comprobar la conectividad. En la sección **Destino** elija **Seleccione una máquina virtual** y elija la máquina virtual correcta y el puerto para probar.

Una vez que pulse **Comprobar**, se comprueba la conectividad entre las máquinas virtuales en el puerto especificado. En el ejemplo, la máquina virtual de destino es inaccesible y se muestra una lista de saltos.

![Resultados de la comprobación de la conectividad de una máquina virtual][1]

## <a name="check-remote-endpoint-connectivity"></a>Comprobación de la conectividad de un punto de conexión remoto

Para comprobar la conectividad y la latencia de un punto de conexión remoto, elija el botón de radio **Especificar manualmente** en la sección **Destino**, especifique la dirección URL y el puerto y haga clic en **Comprobar**.  Se utiliza para puntos de conexión remotos como sitios web y puntos de conexión de almacenamiento.

![Resultados de la comprobación de conectividad para un sitio web][2]

## <a name="next-steps"></a>Pasos siguientes

Aprenda a automatizar capturas de paquetes con las alertas de máquina virtual en el artículo sobre cómo [crear una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md)

Para comprobar si se permite cierto tráfico hacia o desde la máquina virtual, vea cómo [consultar la Comprobación del flujo de IP](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
