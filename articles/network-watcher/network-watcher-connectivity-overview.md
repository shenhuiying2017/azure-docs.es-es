---
title: "Introducción a la comprobación de conectividad en Azure Network Watcher | Microsoft Docs"
description: "En esta página se proporciona una introducción a la funcionalidad de conectividad de Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 16ceef9c923b6a933a5caf752991b466346e0ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-connectivity-check-in-azure-network-watcher"></a>Introducción a la comprobación de conectividad en Azure Network Watcher

La característica de conectividad de Network Watcher proporciona la capacidad de comprobar una conexión TCP directa de una máquina virtual con una máquina virtual (VM), un nombre de dominio completo (FQDN), un URI o una dirección IPv4. Los escenarios de red son complejos, se implementan mediante grupos de seguridad de red, firewalls, rutas definidas por el usuario y recursos proporcionados por Azure. Las configuraciones complejas dificultan la solución de problemas de conectividad. Network Watcher ayuda a reducir la cantidad de tiempo en buscar y detectar problemas de conectividad. Los resultados devueltos pueden proporcionar información sobre si un problema de conectividad se debe a una plataforma o a un problema de configuración del usuario. La conectividad se puede comprobar con [PowerShell](network-watcher-connectivity-powershell.md), la [CLI de Azure](network-watcher-connectivity-cli.md) y la [API de REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> La comprobación de conectividad requiere una extensión de máquina virtual `AzureNetworkWatcherExtension`. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/windows/extensions-nwa.md), y en una máquina virtual con Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="response"></a>Response

En la tabla siguiente se muestran las propiedades que se devuelven cuando una comprobación de conectividad ha terminado de ejecutarse.

|Propiedad  |Descripción  |
|---------|---------|
|ConnectionStatus     | Estado de la comprobación de conectividad. Los resultados posibles son **Reachable** y **Unreachable**.        |
|AvgLatencyInMs     | Promedio de latencia durante la comprobación de conectividad en milisegundos. (Solo se muestra si el estado de la comprobación es accesible).        |
|MinLatencyInMs     | Latencia mínima durante la comprobación de conectividad en milisegundos. (Solo se muestra si el estado de la comprobación es accesible).        |
|MaxLatencyInMs     | Latencia máxima durante la comprobación de conectividad en milisegundos. (Solo se muestra si el estado de la comprobación es accesible).        |
|ProbesSent     | Número de sondeos enviados durante la comprobación. El valor máximo es 100.        |
|ProbesFailed     | Número de sondeos con error durante la comprobación. El valor máximo es 100.        |
|Hops     | Ruta salto por salto desde el origen al destino.        |
|Hops[].Type     | Tipo de recurso. Los valores posibles son **Source**, **VirtualAppliance**, **VnetLocal** e **Internet**.        |
|Hops[].Id | Identificador único del salto.|
|Hops[].Address | Dirección IP del salto.|
|Hops[].ResourceId | Identificador de recurso del salto si este es un recurso de Azure. Si se trata de un recurso de Internet, el identificador de recurso es **Internet**. |
|Hops[].NextHopIds | Identificador único del siguiente salto.|
|Hops[].Issues | Colección de problemas encontrados durante la comprobación en ese salto. Si no hubiera ningún problema, el valor está en blanco.|
|Hops[].Issues[].Origin | Salto actual donde se produjo el problema. Los valores posibles son:<br/> **Inbound**: El problema se encuentra en el enlace entre el salto anterior y el salto actual.<br/>**Outbound**: El problema se encuentra en el enlace entre el salto actual y el salto anterior.<br/>**Local**: El problema se encuentra en el salto actual.|
|Hops[].Issues[].Severity | La gravedad del problema detectado. Los valores posibles son **Error** and **Warning**. |
|Hops[].Issues[].Type |Tipo de problema encontrado. Los valores posibles son: <br/>**CPU**<br/>**Memoria**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Detalles relacionados con el problema encontrado.|
|Hops[].Issues[].Context[].key |Clave del par clave-valor devuelta.|
|Hops[].Issues[].Context[].value |Valor del par clave-valor devuelto.|

A continuación, se muestra un ejemplo de un problema encontrado en un salto.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Tipo de error

La comprobación de conectividad devuelve tipos de error acerca de la conexión. En la tabla siguiente se proporciona una lista de los tipos de errores actuales devueltos.

|Escriba  |Descripción  |
|---------|---------|
|CPU     | Alta utilización de CPU.       |
|Memoria     | Alta utilización de memoria.       |
|GuestFirewall     | El tráfico se bloquea debido a una configuración de firewall de máquina virtual.        |
|DNSResolution     | Error en la resolución DNS para la dirección de destino.        |
|NetworkSecurityRule    | Una regla de NSG bloquea el tráfico (se devuelve la regla).        |
|UserDefinedRoute|El tráfico se interrumpe debido a una ruta del sistema o definida por el usuario. |

### <a name="next-steps"></a>Pasos siguientes

Para información sobre cómo comprobar la conectividad con un recurso, visite: [Check connectivity with Azure Network Watcher](network-watcher-connectivity-powershell.md) (Comprobación de la conectividad con Azure Network Watcher).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png

