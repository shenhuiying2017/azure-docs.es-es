---
title: "Verificación del tráfico con la Comprobación del flujo de IP en Azure Network Watcher (Azure Portal) | Microsoft Docs"
description: "En este artículo se describe cómo comprobar si se permite o se deniega el tráfico hacia o desde una máquina virtual."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 7db29c186cf6e6f3b40a680ab76f1d2763f806ba
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Compruebe si se permite o se deniega el tráfico hacia o desde una máquina virtual con la Comprobación del flujo de IP, un componente de Azure Network Watcher.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API de REST de Azure](network-watcher-check-ip-flow-verify-rest.md)


La Comprobación del flujo de IP es una característica de Network Watcher que permite comprobar si se permite el tráfico hacia o desde una máquina virtual. Se puede ejecutar la validación para el tráfico entrante o saliente. Este escenario es útil para averiguar si una máquina virtual puede comunicarse con un recurso externo o con otro recurso. La Comprobación del flujo de IP se puede usar para comprobar si las reglas de grupos de seguridad de red (NSG) se han configurado correctamente y solucionar los problemas de flujos que las reglas de NSG bloquean. Otra razón para usar la Comprobación del flujo de IP es asegurarse de que el NSG está bloqueando correctamente el tráfico que quiere bloquear.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create a Network Watcher](network-watcher-create.md) (Creación de una instancia de Network Watcher) o que ya tiene una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="scenario"></a>Escenario

Este escenario utiliza la Comprobación del flujo de IP para verificar si una máquina virtual puede comunicarse con otra a través del puerto 443. Si se deniega el tráfico, devuelve la regla de seguridad que está denegando ese tráfico. Para más información sobre la Comprobación del flujo de IP, vea la [introducción a la Comprobación del flujo de IP](network-watcher-ip-flow-verify-overview.md).

### <a name="run-ip-flow-verify"></a>Ejecución de la Comprobación del flujo de IP

Vaya a Network Watcher y haga clic en **Comprobación del flujo de IP**. Seleccione la máquina virtual y la interfaz de red de las que quiere verificar el tráfico. Escriba cualquier información adicional de filtrado y haga clic en **Comprobar**.

Después de hacer clic en **Comprobar**, se comprueba el flujo en función de los criterios establecidos. El resultado es **Acceso permitido** o **Acceso denegado**. Si se deniega el acceso, se proporciona las reglas de grupo de seguridad de red (NSG) y de seguridad que bloquean el tráfico. Si la denegación del tráfico es el comportamiento esperado, la regla es correcta.

> [!NOTE]
> La Comprobación del flujo de IP requiere que el recurso de máquina virtual esté asignado.

Como puede ver en la siguiente imagen, se permite el tráfico HTTPS saliente.

![Información general sobre la comprobación del flujo de IP][1]

Como se muestra en la imagen siguiente, el tráfico cambia a entrante y el puerto de entrada cambia a 123. Ahora se deniega el tráfico y aparece el mensaje "Acceso denegado" junto con las reglas del grupo de seguridad de red y de seguridad que deniegan el tráfico.

![resultados de flujo de ip][2]

## <a name="next-steps"></a>Pasos siguientes

Si se está bloqueando el tráfico y no debería ser así, vea [Administración de grupos de seguridad de red](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento de las reglas del grupo de seguridad de red y de seguridad definidas.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














