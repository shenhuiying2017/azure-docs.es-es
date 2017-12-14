---
title: 'Redes de Azure Stack: diferencias y consideraciones'
description: Aprenda sobre las diferencias y consideraciones al trabajar con redes en Azure Stack.
services: azure-stack
keywords: 
author: ScottNapolitan
ms.author: victorh
ms.date: 9/25/2017
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 3c72c58e63335f1cb440811e283bd742b8124161
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="considerations-for-azure-stack-networking"></a>Consideraciones para las redes de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Las redes en Azure Stack proporcionan muchas de las características que se encuentran en Azure, con algunas diferencias que debe comprender antes de empezar la implementación.


Este artículo proporciona información general sobre las consideraciones únicas para las redes y sus características en Azure Stack. Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el tema [Key considerations](azure-stack-considerations.md) (Consideraciones clave).


## <a name="cheat-sheet-networking-differences"></a>Hoja de referencia rápida: diferencias de servicios de red

|Servicio | Característica | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | DNS multiinquilino | Compatible| Todavía no se admite|
| |Registros AAAA de DNS|Compatible|No compatible|
| |Zonas DNS por suscripción|100 (valor predeterminado)<br>Puede aumentarse a petición.|100|
| |Conjuntos de registros DNS por zona|5000 (valor predeterminado)<br>Puede aumentarse a petición.|5000|
||Servidores de nombres para la delegación de zona|Azure proporciona cuatro servidores de nombres para cada zona de usuario (inquilino) que se crea.|Azure Stack proporciona dos servidores de nombres para cada zona de usuario (inquilino) que se crea.|
| Red virtual|Emparejamiento de redes virtuales de Azure|Conecte dos redes virtuales de la misma región mediante la red troncal de Azure.|Todavía no se admite|
| |Direcciones IPv6|Puede asignar una dirección IPv6 como parte de la [configuración de la interfaz de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Se admite solo IPv4.|
|Puertas de enlace de VPN|Puerta de enlace de VPN de punto a sitio|Compatible|Todavía no se admite|
| |Puerta de enlace de VNET a VNET|Compatible|Todavía no se admite|
| |SKU de puerta de enlace de VPN|Compatibilidad con SKU básicas, GW1, GW2, GW3, alto rendimiento estándar, ultra alto rendimiento. |Compatibilidad con SKU básicas, estándar y de alto rendimiento.|
|Equilibrador de carga|Direcciones IP públicas IPv6|Compatibilidad para asignar una dirección IP pública IPv6 a un equilibrador de carga.|Se admite solo IPv4.|
|Network Watcher|Funciones de supervisión de red de inquilinos de Network Watcher|Compatible|Todavía no se admite|
|CDN|Perfiles de Content Delivery Network|Compatible|Todavía no se admite|
|puerta de enlace de aplicaciones|Equilibrio de carga de nivel 7|Compatible|Todavía no se admite|
|Traffic Manager|Dirija el tráfico entrante para obtener un rendimiento y una confiabilidad óptimos de las aplicaciones.|Compatible|Todavía no se admite|
|ExpressRoute|Configure una conexión privada rápida con los servicios en la nube de Microsoft desde su infraestructura local o una instalación de colocalización.|Compatible|Compatibilidad para conectar Azure Stack a un circuito de Express Route.|

## <a name="next-steps"></a>Pasos siguientes

[DNS en Azure Stack](azure-stack-dns.md)
