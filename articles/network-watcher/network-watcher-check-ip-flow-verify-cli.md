---
title: "Comprobación del tráfico con IP Flow Verify en Azure Network Watcher con la CLI de Azure | Microsoft Docs"
description: "En este artículo se describe cómo comprobar si se permite o se deniega el tráfico hacia o desde una máquina virtual con la CLI de Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 3c0287783e3f2b7acaeefd87acbda30885f93c22
ms.lasthandoff: 02/23/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Compruebe si se permite o se deniega el tráfico hacia o desde una máquina virtual con IP Flow Verify, un componente de Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [API de REST de Azure](network-watcher-check-ip-flow-verify-rest.md)

IP Flow Verify es una característica de Network Watcher que permite comprobar si se permite el tráfico hacia o desde una máquina virtual. Este escenario es útil para averiguar si una máquina virtual puede comunicarse con un recurso externo o con un back-end. IP Flow Verify se puede usar para comprobar si las reglas de grupos de seguridad de red (NSG) se han configurado correctamente y solucionar los problemas de flujos que las reglas de NSG bloquean. Otra razón para usar IP Flow Verify es asegurarse de que el NSG está bloqueando correctamente el tráfico que quiere bloquear.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Creación de una instancia de Network Watcher](network-watcher-create.md) o que ya tiene una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Escenario

Este escenario utiliza IP Flow Verify para comprobar si una máquina virtual pueden comunicarse con una dirección IP de Bing conocida. Si se deniega el tráfico, devuelve la regla de seguridad que está denegando ese tráfico. Para más información sobre IP Flow Verify, consulte la [introducción a IP Flow Verify](network-watcher-ip-flow-verify-overview.md).


## <a name="get-a-vm"></a>Obtención de una máquina virtual

IP Flow Verify comprueba el tráfico hacia o desde la dirección IP de una máquina virtual, hacia o desde un destino remoto. Se necesita el identificador de una máquina virtual para ejecutar el cmdlet. Si ya conoce el identificador de la máquina virtual que se va a usar, puede omitir este paso.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Obtención de las NIC

Se necesita la dirección IP de una NIC en la máquina virtual; en este ejemplo, se recuperan las NIC de una máquina virtual. Si ya conoce la dirección IP que desea probar en la máquina virtual, puede omitir este paso.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Ejecución de IP Flow Verify

Ahora que tenemos la información necesaria para ejecutar el cmdlet `network watcher ip-flow-verify`, lo ejecutamos para comprobar el tráfico. En este ejemplo, usamos la primera dirección IP en la primera NIC.

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> IP Flow Verify requiere que el recurso de máquina virtual esté asignado para ejecución.

## <a name="review-results"></a>Revisión de los resultados

Después de ejecutar `network watcher ip-flow-verify`, se devuelven los resultados. Los siguientes son los resultados devueltos por el paso anterior.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Pasos siguientes

Si se está bloqueando el tráfico y no debería ser así, consulte [Administración de grupos de seguridad de red](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento del grupo de seguridad de red y de las reglas de seguridad definidas.

Para más información sobre cómo auditar la configuración de NSG, consulte [Automate NSG auditing with Azure Network Watcher Security group view](network-watcher-nsg-auditing-powershell.md) (Automatización de la auditoría de grupos de seguridad de red (NSG) con la vista de grupos de seguridad de Azure Network Watcher).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png

