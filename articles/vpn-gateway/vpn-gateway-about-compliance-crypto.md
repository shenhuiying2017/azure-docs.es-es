---
title: "Acerca de los requisitos criptográficos y las puertas de enlace de VPN de Azure | Microsoft Docs"
description: "Este artículo describe los requisitos criptográficos y las puertas de enlace de VPN de Azure"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 7b0736eeaab387312206f94322684c020894f6c5
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Acerca de los requisitos criptográficos y las puertas de enlace de VPN de Azure

Este artículo describe cómo puede configurar las puertas de enlace de VPN de Azure para satisfacer los requisitos criptográficos para los túneles de VPN de S2S entre entornos y las conexiones entre redes virtuales dentro de Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Acerca de los parámetros de la directiva de IPsec e IKE para puertas de enlace de VPN de Azure
El protocolo IPsec e IKE estándar admite una gran variedad de algoritmos criptográficos en diversas combinaciones. Si los clientes no solicitan una combinación específica de algoritmos y parámetros criptográficos, las puertas de enlace de VPN de Azure utilizan un conjunto de predeterminado de propuestas. Los conjuntos de directivas predeterminados se eligieron para maximizar la interoperabilidad con una amplia gama de dispositivos VPN de terceros en las configuraciones predeterminadas. Como resultado, las directivas y el número de propuestas no pueden cubrir todas las posibles combinaciones de algoritmos criptográficos disponibles y puntos fuertes clave.

La directiva predeterminada establecida para la puerta de enlace de VPN de Azure se muestra en el documento: [Acerca de los dispositivos VPN y los parámetros de IPsec o IKE para conexiones de VPN Gateway de sitio a sitio](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos criptográficos
Para las comunicaciones que requieren determinados algoritmos o parámetros criptográficos, normalmente debido a los requisitos de seguridad o de conformidad con normas, ahora los clientes pueden configurar sus puertas de enlace de VPN de Azure para usar una directiva personalizada de IPsec o IKE con determinados algoritmos criptográficos y puntos fuertes clave, en lugar de los conjuntos de directivas predeterminados de Azure.

Por ejemplo, las directivas de modo principal de IKEv2 para puertas de enlace de VPN de Azure usan solo Grupo Diffie-Hellman 2 (1024 bits), mientras que es posible que los clientes necesiten especificar grupos más sólidos que se usarán en IKE, por ejemplo, Grupo 14 (2048 bits), Grupo 24 (grupo de MODP de 2048 bits) o ECP (grupos de curva elíptica), 256 o 384 bits (Grupo 19 y Grupo 20, respectivamente). También se aplican requisitos similares a las directivas de modo rápido de IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Directiva personalizada de IPsec o IKE con puertas de enlace de VPN de Azure
Ahora las puertas de enlace de VPN de Azure admiten directivas de IPsec o IKE personalizadas y por conexión. Puede elegir una combinación específica de algoritmos criptográficos para IPsec e IKE con nivel de clave deseado para una conexión de S2S o entre redes virtuales, como se muestra en el ejemplo siguiente:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Puede crear una directiva de IPsec o IKE y aplicarla a una conexión nueva o existente. El flujo de trabajo se menciona a continuación:

1. Cree las redes virtuales, las puertas de enlace de VPN o las puertas de enlace de red local para su topología de conectividad, como se describe en otros documentos y procedimientos.
2. Cree una directiva IPsec o IKE.
3. Puede aplicar la directiva cuando se crea una conexión de S2S o entre redes virtuales
4. Si ya se ha creado la conexión, puede aplicar la directiva a una conexión existente o actualizarla.


## <a name="ipsecike-policy-faq"></a>P+F sobre directivas de IPsec o IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-ipsecikepolicy-faq-include.md)]


## <a name="next-steps"></a>Pasos siguientes
Consulte [Configure IPsec/IKE policy](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Configuración de la directiva de IPsec o IKE) para obtener instrucciones paso a paso acerca de cómo configurar directivas personalizadas de IPsec o IKE en una conexión.

Consulte también [Conexión de varios dispositivos VPN basados en directivas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obtener más información sobre la opción UsePolicyBasedTrafficSelectors.
