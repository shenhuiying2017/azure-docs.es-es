---
title: "Búsqueda del próximo salto con Próximo salto de Azure Network Watcher (Azure Portal) | Microsoft Docs"
description: "En este artículo se describe cómo encontrar el tipo del próximo salto y la dirección IP mediante la funcionalidad Próximo salto en Azure Portal."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Obtenga más información sobre el tipo del próximo salto con la funcionalidad Próximo salto de Azure Network Watcher mediante el portal.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API de REST de Azure](network-watcher-check-next-hop-rest.md)

Próximo salto es una característica de Network Watcher que permite obtener el tipo del próximo salto y la dirección IP para una máquina virtual especificada. Esta característica es útil para determinar si el tráfico que sale de una máquina virtual atraviesa una puerta de enlace, Internet o redes virtuales para llegar a su destino.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo usa Próximo salto para averiguar el tipo de próximo salto y la dirección IP de un recurso. Para más información sobre Próximo salto, vea [introducción a Próximo salto](network-watcher-next-hop-overview.md).

En este escenario:

* Recuperará el próximo salto de una máquina virtual.

## <a name="get-next-hop"></a>Obtención del próximo salto

### <a name="step-1"></a>Paso 1

Vaya al recurso de Network Watcher en Azure Portal.

### <a name="step-2"></a>Paso 2

Haga clic en **Próximo salto** en el panel de navegación, seleccione la máquina virtual y la interfaz de red, rellene la IP de origen y de destino y haga clic en el botón **Próximo salto**.

> [!NOTE]
> Un requisito del próximo salto es que el recurso de máquina virtual esté asignado para ejecutarse.

![información general sobre obtención del próximo salto][1]

### <a name="step-3"></a>Paso 3

Después de completar la tarea, se muestran los resultados. Se muestran la dirección IP y el tipo de dispositivo del próximo salto. En la siguiente tabla se muestran los valores devueltos disponibles en el portal.

**Tipo de próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

Si se utilizó una ruta personalizada para enrutar este tráfico, se muestra también la ruta definida por el usuario (UDR) con los resultados.

![resultados de obtención del próximo salto][2]

## <a name="next-steps"></a>Pasos siguientes

Aprenda cómo revisar la configuración del grupo de seguridad de red mediante programación en [NSG Auditing with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Auditoría de NSG con Network Watcher).

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














