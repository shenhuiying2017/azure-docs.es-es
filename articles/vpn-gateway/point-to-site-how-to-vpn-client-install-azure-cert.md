---
title: Instalación de un certificado de cliente para conexiones de punto a sitio | Azure
description: Instale un certificado de cliente Mac o Windows para la autenticación de certificados de conexiones de punto a sitio.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
ms.locfileid: "30318000"
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Instalación de un certificado de cliente para conexiones de punto a sitio con autenticación de certificados de Azure

Todos los clientes que se conectan a una red virtual mediante la autenticación de certificados de Azure de conexiones de punto a sitio requieren un certificado de cliente. Este artículo le ayuda a instalar un certificado de cliente que se usa para la autenticación cuando se conecta a una red virtual mediante una conexión de punto a sitio.

## <a name="generate"></a>Generación y exportación de un certificado de cliente

Puede generar un certificado de cliente desde un certificado raíz que se ha generado mediante una solución de una entidad de certificación empresarial o desde un certificado raíz autofirmado. Consulte las instrucciones de [PowerShell](vpn-gateway-certificates-point-to-site.md) o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para conocer los pasos. Después de generar los certificados de cliente, expórtelos como archivos .pfx. No olvide incluir la cadena de certificados completa al exportar.

## <a name="installwin"></a>Instalación de certificado: Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Instalación de certificado: Mac

Los clientes VPN de Mac son compatibles únicamente con el modelo de implementación de Resource Manager. No son compatibles con el modelo de implementación clásica.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Continúe con los pasos de configuración de conexiones de punto a sitio.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portal de Azure clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
