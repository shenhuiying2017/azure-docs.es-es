---
title: "Búsqueda de Next Hop con Azure Network Watcher (REST) | Microsoft Docs"
description: "En este artículo se describe cómo encontrar el tipo del próximo salto y la dirección IP usando la funcionalidad Next Hop con la API de REST de Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Descubra cuál es el tipo del próximo salto que usa la funcionalidad Next Hop en Azure Network Watcher mediante la API de REST de Azure.

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API de REST de Azure](network-watcher-check-next-hop-rest.md)

Next Hop es una característica de Network Watcher que permite obtener el tipo del próximo salto y la dirección IP para una máquina virtual especificada. Esta funcionalidad es útil para determinar si el tráfico que sale de una máquina virtual atraviesa una puerta de enlace, Internet o redes virtuales para llegar a su destino.

## <a name="before-you-begin"></a>Antes de empezar

ARMclient se usa para llamar a la API de REST con PowerShell. ARMClient se encuentra en Chocolatey en [ARMClient en Chocolatey](https://chocolatey.org/packages/ARMClient)

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo usa Next Hop, una característica de Network Watcher, para averiguar el tipo de próximo salto y la dirección IP de un recurso. Para más información acerca de Next Hop, consulte la [introducción a Next Hop](network-watcher-next-hop-overview.md).

En este escenario va a:

* Recuperar el próximo salto para una máquina virtual.

## <a name="log-in-with-armclient"></a>Inicio de sesión con ARMClient

Inicie sesión en armclient con las credenciales de Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperación de una máquina virtual

Ejecute el siguiente script para devolver una máquina virtual. Esta información es necesaria para ejecutar el próximo salto.

El código siguiente necesita valores para las siguientes variables:

- **subscriptionId**: el identificador de suscripción que se usará.
- **resourceGroupName**: el nombre del grupo de recursos que contiene máquinas virtuales.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

En el resultado siguiente, se usa el id. de la máquina virtual en este ejemplo:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Obtención del próximo salto

Cuando se ha creado el encabezado de autorización, se puede recuperar el próximo salto de la máquina virtual. Para que funcione el ejemplo de código, se deben reemplazar los valores siguientes.

> [!Important]
> En las llamadas a la API de REST de Network Watcher, el nombre del grupo de recursos en el identificador URI de la solicitud es el grupo de recursos que contiene Network Watcher, y no los recursos sobre los que realiza las acciones de diagnóstico.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Un requisito del próximo salto es que el recurso de máquina virtual esté asignado para ejecutarse.

## <a name="results"></a>Results

El fragmento de código siguiente es un ejemplo del resultado recibido. Los resultados contienen los siguientes valores:

* **nextHopType**: este valor es uno de los siguientes valores: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway o None.
* **nextHopIpAddress**: la dirección IP del próximo salto.
* **routeTableId**: el valor es un identificador URI para la tabla de enrutamiento asociada con la ruta o, si no se especifica ninguna ruta definida por el usuario, se devuelve el valor de *ruta del sistema*.

Estos son los resultados en formato json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Pasos siguientes

Cuando haya descubierto el próximo salto de una máquina virtual, puede ver la seguridad de sus recursos de red en el artículo [Introducción a las vistas de seguridad](network-watcher-security-group-view-overview.md).














