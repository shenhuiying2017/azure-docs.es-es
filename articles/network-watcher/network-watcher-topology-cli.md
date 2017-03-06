---
title: "Visualización de la topología de Azure Network Watcher con la CLI de Azure | Microsoft Docs"
description: "En este artículo se describe cómo usar la CLI de Azure para consultar una topología de red."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 404e621bc1d16d15aeb705a1055ed4c2b9e251a2
ms.lasthandoff: 02/23/2017

---

# <a name="view-network-watcher-topology-with-azure-cli"></a>Visualización de la topología de Network Watcher con la CLI de Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [API de REST](network-watcher-topology-rest.md)

La característica Topology de Network Watcher proporciona una representación visual de los recursos de red de una suscripción. En el portal, esta visualización se muestra automáticamente. La información subyacente a la vista de topología en el portal se puede obtener mediante PowerShell.
Esta funcionalidad hace que la información de la topología sea más versátil porque otras herramientas pueden usar los datos para crear la visualización.

La interconexión se modela en dos relaciones.

- **Contención**: por ejemplo, una red virtual contiene una subred que contiene una NIC.
- **Asociación**: por ejemplo, la NIC está asociada a una máquina virtual.

La lista siguiente muestra las propiedades que se devuelven cuando se consulta la API de REST Topology.

* **name**: nombre del grupo de recursos.
* **id**: URI del recurso.
* **location**: ubicación donde existe el recurso.
* **associations**: lista de asociaciones para el objeto al que se hace referencia.
    * **name**: nombre del recurso al que se hace referencia.
    * **resourceId**: uri del recurso al que se hace referencia en la asociación.
    * **associationType**: este valor hace referencia a la relación entre los objetos secundario y primario. Los valores válidos son **Contains** o **Associated**.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se usa el cmdlet `network watcher topology` para recuperar la información de la topología. También hay un artículo sobre cómo [recuperar la topología de red con la API de REST](network-watcher-topology-rest.md).

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Creación de una instancia de Network Watcher](network-watcher-create.md) para crear un monitor de red.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo recupera la respuesta de la topología para un grupo de recursos determinado.

## <a name="retrieve-topology"></a>Recuperación de la topología

El cmdlet `network watcher topology` recupera la topología para un grupo de recursos determinado. Agregue el argumento "--json" para ver la salida en formato JSON.

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Results

Los resultados devueltos tienen una propiedad llamada "Resources", que contiene el cuerpo de la respuesta JSON para el cmdlet `network watcher topology`.  La respuesta contiene los recursos que hay en el grupo de seguridad de red y sus asociaciones (es decir, Contains, Associated).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las reglas de seguridad que se aplican a los recursos de red, consulte la [información general de la vista de grupos de seguridad](network-watcher-security-group-view-overview.md).

