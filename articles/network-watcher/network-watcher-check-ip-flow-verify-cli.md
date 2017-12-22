---
title: "Comprobación del tráfico con IP Flow Verify en Azure Network Watcher con la CLI de Azure | Microsoft Docs"
description: "En este artículo se describe cómo comprobar si se permite o se deniega el tráfico hacia o desde una máquina virtual con la CLI de Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3f4a7d3f96a08b3296dd1abfec8abfbcb9759e9f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Compruebe si se permite o se deniega el tráfico hacia o desde una máquina virtual con IP Flow Verify, un componente de Azure Network Watcher

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API de REST de Azure](network-watcher-check-ip-flow-verify-rest.md)


Comprobación del flujo de IP es una característica de Network Watcher que permite comprobar si se permite el tráfico hacia o desde una máquina virtual. Este escenario es útil para averiguar si una máquina virtual puede comunicarse con un recurso externo o con un back-end. Comprobación del flujo de IP se puede usar para comprobar si las reglas de grupos de seguridad de red (NSG) se han configurado correctamente y solucionar los problemas de flujos que las reglas de NSG bloquean. Otra razón para usar la Comprobación del flujo de IP es asegurarse de que el NSG está bloqueando correctamente el tráfico que quiere bloquear.

En este artículo se usa la CLI de próxima generación para el modelo de implementación de Resource Manager, la CLI de Azure 2.0, que está disponible para Windows, Mac y Linux.

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create a Network Watcher](network-watcher-create.md) (Creación de una instancia de Network Watcher) o que ya tiene una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="scenario"></a>Escenario

Este escenario utiliza IP Flow Verify para comprobar si una máquina virtual pueden comunicarse con una dirección IP de Bing conocida. Si se deniega el tráfico, devuelve la regla de seguridad que está denegando ese tráfico. Para más información sobre IP Flow Verify, consulte la [introducción a IP Flow Verify](network-watcher-ip-flow-verify-overview.md).

## <a name="get-a-vm"></a>Obtención de una máquina virtual

La Comprobación del flujo de IP comprueba el tráfico hacia o desde la dirección IP de una máquina virtual, hacia o desde un destino remoto. Se necesita el identificador de una máquina virtual para ejecutar el cmdlet. Si ya conoce el identificador de la máquina virtual que se va a usar, puede omitir este paso.

```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>Obtención de las NIC

Se necesita la dirección IP de una NIC en la máquina virtual; en este ejemplo, se recuperan las NIC de una máquina virtual. Si ya conoce la dirección IP que desea probar en la máquina virtual, puede omitir este paso.

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>Ejecución de la Comprobación del flujo de IP

Ahora que tenemos la información necesaria para ejecutar el cmdlet `az network watcher test-ip-flow`, lo ejecutamos para comprobar el tráfico. En este ejemplo, usamos la primera dirección IP en la primera NIC.

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> Comprobación del flujo de IP requiere que el recurso de máquina virtual esté asignado para ejecución.

## <a name="review-results"></a>Revisión de los resultados

Después de ejecutar `az network watcher test-ip-flow`, se devuelven los resultados. Los siguientes son los resultados devueltos por el paso anterior.

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>Pasos siguientes

Si se está bloqueando el tráfico y no debería ser así, consulte [Administración de grupos de seguridad de red](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento del grupo de seguridad de red y de las reglas de seguridad definidas.

Para más información sobre cómo auditar la configuración de NSG, consulte [Automate NSG auditing with Azure Network Watcher Security group view](network-watcher-nsg-auditing-powershell.md) (Automatización de la auditoría de grupos de seguridad de red (NSG) con la vista de grupos de seguridad de Azure Network Watcher).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
