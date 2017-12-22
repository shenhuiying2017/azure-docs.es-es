---
title: "Visualización de la topología de Azure Network Watcher (API de REST) | Microsoft Docs"
description: "En este artículo se describe cómo usar la API de REST para consultar la topología de red."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Visualización de la topología de Network Watcher con la API de REST

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

En este escenario, se recupera la información de la topología. ARMclient se usa para llamar a la API de REST con PowerShell. ARMClient se encuentra en Chocolatey en [ARMClient en Chocolatey](https://chocolatey.org/packages/ARMClient)

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo recupera la respuesta de la topología para un grupo de recursos determinado.

## <a name="log-in-with-armclient"></a>Inicio de sesión con ARMClient

Inicie sesión en armclient con las credenciales de Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Recuperación de la topología

En el ejemplo siguiente, se solicita la topología de la API de REST.  El ejemplo se parametriza para aportar flexibilidad en la creación de un ejemplo.  Reemplace todos los valores dentro de \< \>.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

La respuesta siguiente es un ejemplo de una respuesta reducida que se devuelve cuando se recupera la topología para un grupo de recursos:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a visualizar los registros de flujo de grupo de seguridad de red con Power BI en el artículo [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualización de registros de flujo de grupo de seguridad de red con Power BI).

