---
title: "Solución del problema de que no puede establecerse la conexión VPN de sitio a sitio de Azure"
description: "Obtenga información acerca de cómo solucionar el problema de que la conexión VPN de sitio a sitio deje de funcionar repentinamente y no pueda volver a conectarse."
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/21/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: a72a46e4c2c93fdb3ab1a26c8854adb8cdc488a5
ms.contentlocale: es-es
ms.lasthandoff: 06/22/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Solución de problemas: la conexión VPN de sitio a sitio de Azure no puede conectarse y deja de funcionar

Configure la conexión VPN de sitio a sitio entre la red local y una red virtual de Microsoft Azure. La conexión VPN deja de funcionar de repente y no se puede volver a conectar. Este artículo proporciona pasos de solución de problemas para ayudarlo a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

Para resolver el problema, primero intente [restablecer la puerta de enlace de red virtual de Azure](vpn-gateway-resetgw-classic.md) y restablecer el túnel desde el dispositivo VPN local. Si el problema continua, siga estos pasos para identificar la causa del problema.

### <a name="prerequisite-step"></a>Paso de requisito previo

Comprobación del tipo de puerta de enlace de red virtual de Azure:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Compruebe la página **Introducción** de la puerta de enlace de red virtual para el tipo de información.
    
    ![La información general de la puerta de enlace](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Paso 1: Comprobación de si el dispositivo VPN local está validado

1. Compruebe si está usando una [versión de SO y dispositivo VPN validada](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides). Si el dispositivo no es un dispositivo VPN validado, tendrá que ponerse en contacto con el fabricante del dispositivo para ver si hay algún problema de compatibilidad.
2. Asegúrese de que el dispositivo VPN esté configurado correctamente. Para obtener más información, vea [Edición de ejemplos de configuración de dispositivos](/vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-keypsk"></a>Paso 2: Comprobación de la clave compartida (PSK)

Compare la **clave compartida** del dispositivo VPN local y la VPN de red virtual para asegurarse de que las claves coinciden. 

Para ver la PSK para la conexión VPN de Azure, use uno de los siguientes métodos:

**Portal de Azure**

1. Vaya a Puerta de enlace de red virtual > Conexión sitio a sitio creada.
2. En la sección **Configuración**, haga clic en **Clave compartida**.
    
    ![Clave compartida](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Para el modo de Resource Manager

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para el modo clásico

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Paso 3: Comprobación de los IP del mismo nivel de VPN

-   La definición de IP en el objeto **Puerta de enlace de red local** en Azure debe coincidir con el IP de dispositivo local.
-   La definición de IP de la puerta de enlace de Azure establecida en el dispositivo local debe coincidir con el IP de puerta de enlace de Azure.

### <a name="step-4-nsg-and-udr-on-gateway-subnet"></a>Paso 4: NSG y UDR en la subred de puerta de enlace

Busque y quite Enrutamiento definido por el usuario (UDR) o Grupos de seguridad de red (NSG) en la subred de puerta de enlace y, a continuación, pruebe el resultado. Si se resuelve el problema, valide la configuración de NSG o UDR aplicada.

### <a name="step-5-check-on-premises-vpn-device-external-interface-address"></a>Paso 5: Comprobación de la dirección de la interfaz externa del dispositivo VPN local

- Si la dirección IP accesible desde Internet del dispositivo VPN está incluida en la definición de **Red local** en Azure, es posible que experimente desconexiones esporádicas.
- La interfaz externa del dispositivo debe estar directamente en Internet. No debería haber ninguna traducción de direcciones de red o firewall entre Internet y el dispositivo.
-  Si configura la agrupación en clústeres de firewall para que tenga una IP virtual, debe interrumpir el clúster y exponer el dispositivo VPN directamente a una interfaz pública con la que la puerta de enlace pueda interactuar.

### <a name="step-6-verify--subnets-match-exactly-azure-policy-based-gateways"></a>Paso 6: Comprobación de que las subredes coinciden exactamente (puertas de enlace basadas en directivas de Azure)

-   Compruebe que las subredes coinciden exactamente entre Azure Virtual Network y las definiciones locales para Azure Virtual Network.
-   Compruebe que las subredes coinciden exactamente entre **Puerta de enlace de red local** y las definiciones locales para la red local.

### <a name="step-5-verify-azure-gateway-health-probe"></a>Paso 5: Comprobación del sondeo de estado de la puerta de enlace de Azure

1. Vaya a https://&lt;SuIPdePuertadeenlacedeRedVirtual&gt;:8081/healthprobe
2. Haga clic en la advertencia de certificado.
3. Si recibe una respuesta, se considera que la puerta de enlace de red virtual es correcta. Si no recibe una respuesta, puede que la puerta de enlace no sea correcta o que haya un NSG en la subred de puerta de enlace que provoque el problema. El siguiente texto es una respuesta de ejemplo:

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-pfs-feature-enabled"></a>Paso 7: Comprobación de si el dispositivo VPN local cuenta con la característica Confidencialidad directa perfecta (PFS) habilitada

La característica Confidencialidad directa perfecta puede provocar problemas de desconexión. Si el dispositivo VPN cuenta con la característica Confidencialidad directa perfecta habilitada, deshabilítela. A continuación, actualice la directiva IPsec de puerta de enlace de red virtual.

## <a name="next-steps"></a>Pasos siguientes

-   [Configuración de una conexión de sitio a sitio en una red virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configuración de la directiva IPsec/IKE para conexiones VPN de sitio a sitio](vpn-gateway-ipsecikepolicy-rm-powershell.md)

