---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 865f4a963dfbaa7b8392865f8b0c468734673ba0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197642"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar la puerta de enlace de red local "gatewayIpAddress"

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. La dirección IP de puerta de enlace se puede cambiar sin quitar una conexión de puerta de enlace VPN existente (si tiene una). Para modificar la dirección IP de puerta de enlace, reemplace los valores "Site2" y "TestRG1" por los suyos propios mediante el comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Compruebe que la dirección IP sea correcta en la salida:

```
"gatewayIpAddress": "23.99.222.170",
```