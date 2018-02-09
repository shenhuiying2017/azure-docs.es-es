---
title: "Consideraciones de integración de red para sistemas integrados de Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de lo que puede hacer para planear la integración de red del centro de datos con Azure Stack de varios nodos."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: a198ff5fe7135e17301025d6a712236b76be0ede
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="network-connectivity"></a>Conectividad de red
En este artículo se ofrece información sobre la infraestructura de red de Azure Stack que le ayudará decidir cuál es la mejor forma de integrar Azure Stack en su entorno de red existente. 

> [!NOTE]
> Para resolver nombres de DNS externos de Azure Stack (por ejemplo, www.bing.com), deberá proporcionar servidores DNS para reenviar las solicitudes de DNS. Para más información acerca de los requisitos de DNS de Azure Stack, consulte [Integración del centro de datos de Azure Stack: DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Diseño de la red física
La solución Azure Stack requiere una infraestructura física resistente y de alta disponibilidad para admitir sus operaciones y servicios. El siguiente diagrama representa nuestro diseño recomendado:

![Diseño de red de Azure Stack recomendado](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Redes lógicas
Las redes lógicas representan una abstracción de la infraestructura de red física subyacente. Se utilizan para organizar y simplificar las asignaciones de red para hosts, máquinas virtuales y servicios. Como parte de la creación de redes lógicas, se crean sitios de red para definir las redes de área local virtual (VLAN), subredes IP y pares de subredes IP/VLAN que se asocian a la red lógica en cada ubicación física.

En la siguiente tabla se muestran las redes lógicas y los intervalos de subred IPv4 asociados para los que se debe planear:

| Red lógica | DESCRIPCIÓN | Tamaño | 
| -------- | ------------- | ------------ | 
| VIP pública | Direcciones IP públicas para un pequeño conjunto de servicios de Azure Stack, con las demás usadas por máquinas virtuales de inquilinos. La infraestructura de Azure Stack utiliza 32 direcciones de esta red. Si tiene previsto usar App Service y los proveedores de recursos de SQL, se usan 7 direcciones más. | /26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestructura del conmutador | Direcciones IP de punto a punto con fines de enrutamiento, interfaces de administración de conmutador dedicado y direcciones de bucle invertido asignadas al conmutador. | /26 | 
| Infraestructura | Se utiliza para que los componentes internos de Azure Stack se comuniquen. | /24 |
| Privada | Se utiliza para la red de almacenamiento y las VIP privadas. | /24 | 
| BMC | Se utiliza para comunicarse con los BMC en los hosts físicos. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Infraestructura de red
La infraestructura de red de Azure Stack consta de varias redes lógicas que están configuradas en los conmutadores. El diagrama siguiente muestra estas redes lógicas y cómo integrarlas con la parte superior del rack (Tor), el controlador de administración de placa base (BMC) y los conmutadores (red de cliente) de borde.

![Conexiones del conmutador y diagrama de red lógica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Red de BMC
Esta red está dedicada a conectar todos los controladores de administración de la placa base (también conocidos como procesadores de servicio, por ejemplo, iDRAC, iLO, iBMC, etc.) a la red de administración. Si está presente, el host del ciclo de vida del hardware (HLH) se ubica en esta red y puede proporcionar software específico del OEM para el mantenimiento o la supervisión del hardware. 

### <a name="private-network"></a>Red privada
Esta red /24 (254 direcciones IP de host) es particular de la región de Azure Stack (no se expande más allá de los dispositivos de conmutación de borde de la región Azure Stack) y se divide en dos subredes:

- **Red de almacenamiento**. Una red /25 (126 direcciones IP de host) se utiliza para admitir el uso del tráfico de almacenamiento de espacios directo y el bloque de mensajes del servidor (SMB) y la migración en vivo de máquinas virtuales. 
- **Red IP virtual interna**. Una red /25 dedicada únicamente a una red IP virtual de uso interno para el equilibrador de carga de software.

### <a name="azure-stack-infrastructure-network"></a>Red de la infraestructura de Azure Stack
Esta red /24 está dedicada a los componentes internos de Azure Stack para que puedan comunicarse e intercambiar datos entre ellos. Esta subred requiere direcciones IP enrutables pero se mantiene privada para la solución mediante el uso de listas de control de acceso (ACL). No se espera que se enrute más allá de los conmutadores de borde, excepto por un intervalo pequeño equivalente en tamaño a una red /27 que utilizan algunos de estos servicios cuando requieren acceso a recursos externos o a Internet. 

### <a name="public-infrastructure-network"></a>Red de la infraestructura pública
Esta red /27 es el intervalo pequeño de la subred de infraestructura de Azure Stack mencionada anteriormente, no requiere direcciones IP públicas, pero sí requiere acceso a Internet a través de NAT o proxy transparente. Esta red se asignará al sistema de consola de recuperación de emergencia (ERCS), la máquina virtual de ERCS requiere acceso a Internet durante el registro a Azure y debe enrutarse a su red de administración para la solución de problemas.

### <a name="public-vip-network"></a>Red IP virtual pública
La red IP virtual pública se asigna al controlador de red en Azure Stack. No es una red lógica en el conmutador. El equilibrador de carga de software utiliza el grupo de direcciones y asigna redes /32 a las cargas de trabajo de inquilino. En la tabla de enrutamiento de conmutador, estas direcciones IP /32 se publican como una ruta disponible a través de BGP. Esta red contiene las direcciones IP externas accesibles o públicas. La infraestructura de Azure Stack usa por lo menos ocho direcciones de esta red IP virtual pública, mientras que el resto lo utilizan las máquinas virtuales de inquilino. El tamaño de red en esta subred puede variar desde un mínimo de /26 (64 hosts) a un máximo de /22 (1022 hosts); le recomendamos que planee una red /24.

### <a name="switch-infrastructure-network"></a>Red de la infraestructura de conmutadores
Esta red /26 es la subred que contiene las subredes IP /30 (2 IP de host) punto a punto enrutables y los bucles invertidos que son subredes /32 dedicadas a la administración de conmutadores en banda y al ID de router de BGP. Este rango de direcciones IP debe enrutarse externamente desde la solución Azure Stack a su centro de datos, y pueden ser IP privadas o públicas.

### <a name="switch-management-network"></a>Red de administración de conmutadores
Esta red /29 (6 IP de host) está dedicada a conectar los puertos de administración de los conmutadores. Permite el acceso fuera de banda para la implementación, administración y solución de problemas. Se calcula a partir de la red de infraestructura de conmutadores mencionada anteriormente.

## <a name="publish-azure-stack-services"></a>Publicar servicios de Azure Stack
Debe poner disponibles los servicios de Azure Stack para los usuarios externos de Azure Stack. Azure Stack configura varios puntos de conexión para sus roles de infraestructura. Estos puntos de conexión se asignan a VIP desde el grupo de direcciones IP públicas. Se crea una entrada DNS para cada punto de conexión de la zona DNS externa, que se ha especificado durante la implementación. Por ejemplo, el portal de usuarios se asigna a la entrada de host de DNS de portal.*&lt;region>.&lt;fqdn>*.

### <a name="ports-and-urls"></a>Puertos y direcciones URL
Para que los servicios de Azure Stack (como los portales, Azure Resource Manager, DNS, etc.) estén disponible para las redes externas, debe permitir tráfico entrante en estos puntos de conexión para las direcciones URL, puertos y protocolos específicos.
 
En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional, debe permitir puertos y direcciones URL concretos para la comunicación [entrante](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) y [saliente](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound). Estos incluyen puertos y direcciones URL de identidad, distribución de Marketplace, revisión y actualización, registro y datos de uso.

## <a name="next-steps"></a>pasos siguientes
[Conectividad de borde](azure-stack-border-connectivity.md)