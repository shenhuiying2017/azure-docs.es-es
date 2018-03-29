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
ms.openlocfilehash: da682e3e947c3f8643610e42ad22fb9d742796f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
Puede comprobar que la conexión se realizó correctamente mediante el uso del cmdlet "Get-AzureRmVirtualNetworkGatewayConnection", con o sin "-Debug". 

1. Puede usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione "A" para ejecutar "todo". En el ejemplo, " -Name" hace referencia al nombre de la conexión que desea probar.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Cuando el cmdlet haya finalizado, consulte los valores. En el ejemplo siguiente, el estado de conexión aparece como "Conectado" y pueden verse los bytes de entrada y salida.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```