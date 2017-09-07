---   
title: "Introducción a las directivas SSL para Azure Application Gateway| Microsoft Docs"
description: "Más información sobre cómo Azure Application Gateway le permite configurar la directiva SSL"
services: application gateway
documentationcenter: na
author: amsriva
manager: 
editor: 
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: dae04a093af8512d27edbae8d41bd58ccc33df53
ms.contentlocale: es-es
ms.lasthandoff: 08/30/2017

---
   

# <a name="application-gateway-ssl-policy-overview"></a>Introducción a la directiva SSL de Application Gateway

Application Gateway le permite centralizar la administración de certificados SSL y reducir la sobrecarga de cifrado y descifrado de una granja de servidores de back-end. Este control centralizado de SSL, también le permite especificar una directiva SSL central que se adapte a sus requisitos de seguridad de la organización. Esto le ayuda a cumplir los requisitos de cumplimiento normativo así como las directrices de seguridad y los procedimientos recomendados.

La directiva SSL incluye el control de la versión del protocolo SSL, así como de los conjuntos de cifrado y el orden en el que se usan los cifrados durante un protocolo de enlace SSL. Con este fin, Application Gateway proporciona dos mecanismos para permitir a los clientes controlar la directiva SSL: una directiva predefinida o una personalizada.

## <a name="predefined-ssl-policy"></a>Directiva SSL predefinida

Application Gateway tiene tres directivas de seguridad predefinidas. Puede configurar la puerta de enlace con cualquiera de estas directivas para conseguir el nivel adecuado de seguridad. Los nombres de directiva se anotan según el año y mes en el que se configuraron. Cada directiva ofrece diferentes versiones de protocolos SSL y conjuntos de cifrado. Se recomienda usar las directivas SSL más recientes para garantizar la máxima seguridad SSL. En la actualidad, Application Gateway permite a los usuarios elegir entre una de las siguientes directivas predefinidas.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Propiedad  |Valor  |
|---|---|
|Nombre     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Valor predeterminado| True (si no se ha especificado ninguna directiva predefinida) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Propiedad  |Valor  |
|   ---      |  ---       |
|Nombre     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Valor predeterminado| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Propiedad  |Valor  |
|---|---|
|Nombre     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Valor predeterminado| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Directiva SSL personalizada

Si una directiva SSL predefinida se debe configurar de acuerdo con sus requisitos, deberá definir su propia directiva SSL personalizada. En este caso, tiene un control completo sobre la versión mínima compatible del protocolo SSL, así como de los conjuntos de cifrado y su orden de prioridad.
 
Versiones del protocolo SSL:

* SSL 2.0 y 3.0 están deshabilitados de forma predeterminada en todas las instancias de Application Gateway. Estas versiones de protocolo no son configurables.
* La definición de la directiva SSL personalizada le ofrece la opción de seleccionar cualquiera de los siguientes tres protocolos como la versión mínima de protocolo SSL para la puerta de enlace: TLSv1_0, TLSv1_1, TLSv1_2.
* Si no se define ninguna directiva SSL, las tres (TLSv1_0, TLSv1_1 y TLSv1_2) se habilitarían.

Conjunto de cifrado:

Application Gateway admite los siguientes conjuntos de cifrado desde los que se puede elegir la directiva personalizada. El orden de los conjuntos de cifrado determina el orden de prioridad durante la negociación SSL.

Conjuntos de cifrado disponibles:

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>Pasos siguientes

[Configurar directiva SSL en Application Gateway](application-gateway-configure-ssl-policy-powershell.md)

