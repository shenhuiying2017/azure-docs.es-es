---
title: Modificación de los prefijos de direcciones IP de la puerta de enlace de red local y la IP de la puerta de enlace | Microsoft Docs
description: Este artículo explica paso a paso cómo cambiar los prefijos de direcciones IP de la puerta de enlace de red local.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: cherylmc

---
# Modificación de la configuración de la puerta de enlace de red local mediante PowerShell
A veces, cambia la configuración de la puerta de enlace de red local AddressPrefix o GatewayIPAddress. Las instrucciones siguientes lo ayudarán a modificar la configuración de la puerta de enlace de red local. También puede modificar estos valores en el Portal de Azure.

## Antes de empezar
Necesitará instalar la versión más reciente de los cmdlets de PowerShell del Administrador de recursos de Azure. Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell.

## Procedimiento para modificar los prefijos de direcciones IP
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## Procedimiento para modificar la dirección IP de la puerta de enlace
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Pasos siguientes
Puede comprobar la conexión de la puerta de enlace. Consulte [Comprobación de una conexión de puerta de enlace](vpn-gateway-verify-connection-resource-manager.md).

<!---HONumber=AcomDC_0810_2016-->