---
title: "Comprobación de la conectividad con Azure Network Watcher: CLI de Azure 2.0 | Microsoft Docs"
description: "Esta página explica cómo usar la comprobación de conectividad con Network Watcher usando la CLI de Azure 2.0"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 81b0f76538b74edf1ebdfb40ff9190e5072e71c8
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="check-connectivity-with-azure-network-watcher-using-azure-cli-20"></a>Comprobación de la conectividad con Azure Network Watcher usando la CLI de Azure 2.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [API de REST de Azure](network-watcher-connectivity-rest.md)

Aprenda a usar la conectividad para comprobar si se puede establecer una conexión TCP directa de una máquina virtual a un punto de conexión determinado.

Este artículo le guiará por algunos escenarios de comprobación de conectividad.

* [Comprobación de la conectividad a una máquina virtual](#check-connectivity-to-a-virtual-machine)
* [Problemas de validación de enrutamiento](#validate-routing-issues)
* [Comprobación de la latencia del sitio web](#check-website-latency)
* [Comprobación de la conectividad a un punto de conexión de almacenamiento](#check-connectivity-to-a-storage-endpoint)

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se da por hecho que tiene los siguientes recursos:

* Una instancia de Network Watcher en la región cuya conectividad quiere comprobar.

* Máquinas virtuales con las que comprobar la conectividad.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

> [!IMPORTANT]
> La comprobación de conectividad requiere una extensión de máquina virtual `AzureNetworkWatcherExtension`. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/windows/extensions-nwa.md), y en una máquina virtual con Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="register-the-preview-capability"></a>Registro de la funcionalidad de versión preliminar 

La comprobación de conectividad está actualmente en versión preliminar pública; para utilizar esta característica tiene que registrarla. Para ello, ejecute el siguiente ejemplo de la CLI

```azurecli 
az feature register --namespace Microsoft.Network --name AllowNetworkWatcherConnectivityCheck

az provider register --namespace Microsoft.Network 
``` 

Para comprobar que el registro se realizó correctamente, ejecute el siguiente comando de la CLI:

```azurecli
az feature show --namespace Microsoft.Network --name AllowNetworkWatcherConnectivityCheck 
```

Si la característica se registró correctamente, la salida debería coincidir con lo siguiente: 

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowNetworkWatcherConnectivityCheck",
  "name": "Microsoft.Network/AllowNetworkWatcherConnectivityCheck",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
``` 

## <a name="check-connectivity-to-a-virtual-machine"></a>Comprobación de la conectividad a una máquina virtual

En este ejemplo se comprueba la conectividad a una máquina virtual de destino a través del puerto 80.

### <a name="example"></a>Ejemplo

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Response

La siguiente respuesta procede del ejemplo anterior.  En esta respuesta, `ConnectionStatus` es **Unreachable** (inaccesible). Se puede ver que ninguno de los sondeos enviados se pudo realizar. Error de conectividad en la aplicación virtual debido a una `NetworkSecurityRule` configurada por el usuario denominada **UserRule_Port80**, que se configuró para bloquear el tráfico entrante en el puerto 80. Esta información puede utilizarse para investigar problemas de conexión.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Problemas de validación de enrutamiento

En el ejemplo se comprueba la conectividad entre una máquina virtual y un punto de conexión remoto.

### <a name="example"></a>Ejemplo

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Response

En el ejemplo siguiente, `connectionStatus` se muestra como **Unreachable** (inaccesible). En los detalles de `hops`, puede ver en `issues` que el tráfico se ha bloqueado debido a una `UserDefinedRoute`.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Comprobación de la latencia del sitio web

En el siguiente ejemplo se comprueba la conectividad con un sitio web.

### <a name="example"></a>Ejemplo

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address http://bing.com --dest-port 80
```

### <a name="response"></a>Response

En la siguiente respuesta, puede ver `connectionStatus` se muestra como **Reachable** (accesible). Cuando una conexión se establece correctamente, se proporcionan los valores de latencia.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Comprobación de la conectividad a un punto de conexión de almacenamiento

En el ejemplo siguiente se comprueba la conectividad de una máquina virtual a una cuenta de almacenamiento de blog.

### <a name="example"></a>Ejemplo

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Response

El siguiente json es la respuesta de ejemplo que procede de la ejecución del cmdlet anterior. Como la comprobación es correcta, la propiedad `connectionStatus` se muestra como **Reachable** (accesible).  Se proporcionan los detalles sobre el número de saltos necesarios para llegar a la latencia y al blob de almacenamiento.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a automatizar capturas de paquetes con las alertas de máquina virtual en el artículo sobre cómo [crear una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md)

Para comprobar si se permite cierto tráfico hacia o desde la máquina virtual, vea cómo [consultar la Comprobación del flujo de IP](network-watcher-check-ip-flow-verify-portal.md)

