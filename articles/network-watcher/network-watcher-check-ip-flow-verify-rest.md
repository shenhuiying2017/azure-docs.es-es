---
title: "Comprobación del tráfico con la Comprobación del flujo de IP de Azure Network Watcher con REST | Microsoft Docs"
description: "En este artículo se describe cómo comprobar si se permite o se deniega el tráfico hacia o desde una máquina virtual"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 18b98300ee103e3f3118f6db4e1c1a0e169eecad
ms.lasthandoff: 03/04/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Comprobar si se permite o se deniega el tráfico con la Comprobación del flujo de IP, un componente de Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [API de REST de Azure](network-watcher-check-ip-flow-verify-rest.md)

La Comprobación del flujo de IP es una característica de Network Watcher que permite comprobar si se permite el tráfico hacia o desde una máquina virtual. Se puede ejecutar la validación para el tráfico entrante o saliente. Este escenario es útil para averiguar si una máquina virtual puede comunicarse con un recurso externo o con un back-end. Comprobación del flujo de IP se puede usar para comprobar si las reglas de grupos de seguridad de red (NSG) se han configurado correctamente y solucionar los problemas de flujos que las reglas de NSG bloquean. Otra razón para usar la Comprobación del flujo de IP es asegurarse de que el NSG está bloqueando correctamente el tráfico que quiere bloquear.

## <a name="before-you-begin"></a>Antes de empezar

ARMclient se usa para llamar a la API de REST con PowerShell. ARMClient se encuentra en Chocolatey en [ARMClient en Chocolatey](https://chocolatey.org/packages/ARMClient)

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Escenario

Este escenario usa la Comprobación del flujo de IP para verificar si una máquina virtual puede comunicarse con otra a través del puerto 443. Si se deniega el tráfico, devuelve la regla de seguridad que está denegando ese tráfico. Para más información sobre la Comprobación del flujo de IP, vea [Introducción a la comprobación del flujo de IP](network-watcher-ip-flow-verify-overview.md).

En este escenario, podrá:

* Recuperación de una máquina virtual
* Llamar a la Comprobación del flujo de IP
* Comprobar los resultados

## <a name="log-in-with-armclient"></a>Inicio de sesión con ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperación de una máquina virtual

Ejecute el siguiente script para devolver una máquina virtual. El siguiente código necesita valores para las variables:

* **subscriptionId**: el identificador de suscripción que se usa.
* **resourceGroupName**: el nombre de un grupo de recursos que contiene máquinas virtuales.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

La información que se necesita es el identificador del tipo `Microsoft.Compute/virtualMachines`. Los resultados deberían parecerse al siguiente código de ejemplo:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Llamar a la Comprobación del flujo de IP

En el ejemplo siguiente se crea una solicitud para comprobar el tráfico de una máquina virtual especificada. La respuesta se devuelve si se permite el tráfico o si se deniega el tráfico. Si se deniega el tráfico, también devuelve la regla que bloquea el tráfico.

> [!NOTE]
> La Comprobación del flujo de IP requiere que el recurso de máquina virtual esté asignado.

El script requiere el identificador de recurso de una máquina virtual y de una tarjeta de interfaz de red en la máquina virtual. Estos valores los proporciona la salida anterior.

> [!Important]
> En todas las llamadas a la REST de Network Watcher, el nombre del grupo de recursos del identificador URI de la solicitud es el que contiene la instancia de Network Watcher, no los recursos sobre los que realiza las acciones de diagnóstico.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Descripción de los resultados

La respuesta que recibe indica si el tráfico se permite o deniega. La respuesta es similar a uno de los ejemplos siguientes:

**Se permite**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Se deniega**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Pasos siguientes

Si el tráfico sufre bloqueos, y no debería ser así, consulte [Administración de grupos de seguridad de red mediante el portal](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para más información acerca de los grupos de seguridad de red.













