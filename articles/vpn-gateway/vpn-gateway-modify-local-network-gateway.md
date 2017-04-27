---
title: "Modificación de los prefijos de direcciones IP de puertas de enlace de red local y la dirección IP de VPN Gateway | Azure | PowerShell | Microsoft Docs"
description: "Este artículo explica paso a paso cómo cambiar los prefijos de direcciones IP de la puerta de enlace de red local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a26cbe2172dc27c152246d70b7f6b504ec4a08a5
ms.lasthandoff: 04/12/2017


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificación de la configuración de la puerta de enlace de red local mediante PowerShell
A veces, cambia la configuración de la puerta de enlace de red local AddressPrefix o GatewayIPAddress. Las instrucciones siguientes lo ayudarán a modificar la configuración de la puerta de enlace de red local. También puede modificar estos valores en el Portal de Azure.

## <a name="before-you-begin"></a>Antes de empezar
Necesitará instalar la versión más reciente de los cmdlets de PowerShell del Administrador de recursos de Azure. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener más información sobre cómo instalar los cmdlets de PowerShell.

## <a name="to-modify-ip-address-prefixes"></a>Procedimiento para modificar los prefijos de direcciones IP
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Procedimiento para modificar la dirección IP de la puerta de enlace
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Puede comprobar la conexión de la puerta de enlace. Consulte [Comprobación de una conexión de puerta de enlace](vpn-gateway-verify-connection-resource-manager.md).


