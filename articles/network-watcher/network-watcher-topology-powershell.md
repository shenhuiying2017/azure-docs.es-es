---
title: "Visualización de la topología de Azure Network Watcher (PowerShell) | Microsoft Docs"
description: "En este artículo se describe cómo usar PowerShell para consultar una topología de red."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>Visualización de la topología de Network Watcher con PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API DE REST](network-watcher-topology-rest.md)

La característica Topología de Network Watcher proporciona una representación visual de los recursos de red de una suscripción. En el portal, esta visualización se muestra automáticamente. La información subyacente a la vista de topología en el portal se puede obtener mediante PowerShell.
Esta funcionalidad hace que la información de la topología sea más versátil porque otras herramientas pueden usar los datos para crear la visualización.

La interconexión se modela en dos relaciones.

- **Contención**: por ejemplo, una red virtual contiene una subred que incluye una NIC.
- **Asociación**: por ejemplo, la NIC está asociada a una máquina virtual.

La lista siguiente muestra las propiedades que se devuelven cuando se consulta la API de REST de Topología.

* **name**: nombre del recurso.
* **id**: identificador URI del recurso.
* **location**: ubicación donde existe el recurso.
* **associations**: lista de asociaciones para el objeto al que se hace referencia.
    * **name**: nombre del recurso al que se hace referencia.
    * **resourceId**: identificador URI del recurso al que se hace referencia en la asociación.
    * **associationType**: este valor hace referencia a la relación entre los objetos secundario y primario. Los valores válidos son **Contains** o **Associated**.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se usa el cmdlet `Get-AzureRmNetworkWatcherTopology` para recuperar la información de la topología. También hay un artículo sobre cómo [recuperar la topología de red con la API de REST](network-watcher-topology-rest.md).

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo recupera la respuesta de la topología para un grupo de recursos determinado.

## <a name="retrieve-network-watcher"></a>Recuperación de Network Watcher

El primer paso consiste en recuperar la instancia de Network Watcher. La variable `$networkWatcher` se pasa al cmdlet `Get-AzureRmNetworkWatcherTopology`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Recuperación de la topología

El cmdlet `Get-AzureRmNetworkWatcherTopology` recupera la topología para un grupo de recursos determinado.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Results

Los resultados devueltos tienen una propiedad llamada "Resources", que contiene el cuerpo de la respuesta JSON para el cmdlet `Get-AzureRmNetworkWatcherTopology`.  La respuesta contiene los recursos que hay en el grupo de seguridad de red y sus asociaciones (es decir, Contains, Associated).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a visualizar los registros de flujo de grupo de seguridad de red con Power BI en el artículo [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualización de registros de flujo de grupo de seguridad de red con Power BI).


