---
title: "Modificación de los prefijos de direcciones IP de puertas de enlace de red local y la dirección IP de VPN Gateway | Azure | Portal| Microsoft Docs"
description: "Este artículo explica paso a paso cómo cambiar los prefijos de direcciones IP de la puerta de enlace de red local con Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: ebe57f497a007c5219c3b7f543ffb3bae9d161a8
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificación de la configuración de la puerta de enlace de red local mediante Azure Portal

A veces, cambia la configuración de la puerta de enlace de red local AddressPrefix o GatewayIPAddress. Este artículo muestra cómo modificar la configuración de puerta de enlace de red local. También puede modificar estas configuraciones mediante un método diferente, si selecciona una opción diferente de la lista siguiente:

> [!div class="op_single_selector"]
> * [Portal de Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI de Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a>Modificación de los prefijos de direcciones IP

Cuando modifique prefijos de la dirección IP, los pasos que seguir dependen de si la puerta de enlace de red local dispone de una conexión.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a>Modificación de la dirección IP de la puerta de enlace

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. Cuando cambie la dirección IP pública, los pasos que seguir dependen de si la puerta de enlace de red local dispone de una conexión.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Puede comprobar la conexión de la puerta de enlace. Consulte [Comprobación de una conexión de puerta de enlace](vpn-gateway-verify-connection-resource-manager.md).
