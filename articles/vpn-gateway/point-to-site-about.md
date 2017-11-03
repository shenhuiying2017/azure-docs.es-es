---
title: Acerca de las conexiones VPN de punto a sitio de Azure | Microsoft Docs
description: "Este artículo le ayudará a comprender las conexiones de punto a sitio y a decidir qué tipo de autenticación de puerta de enlace de VPN P2S utilizar."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 010/19/2017
ms.author: cherylmc
ms.openlocfilehash: 0e31d58de113f737a48b6d3091650226f04ec69a
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2017
---
# <a name="about-point-to-site-vpn"></a>Acerca de las conexiones VPN de punto a sitio

Una conexión de puerta de enlace de VPN de punto a sitio (P2S) permite crear una conexión segura a la red virtual desde un equipo cliente individual. Se establece una conexión de punto a sitio al iniciarla desde el equipo cliente. Esta solución resulta útil para los teletrabajadores que deseen conectarse a redes virtuales de Azure desde una ubicación remota, por ejemplo, desde casa o un congreso. La conexión VPN de punto a sitio también es una solución útil en comparación con la conexión VPN de sitio a sitio cuando solo necesitan conectarse a la red virtual algunos clientes. Este artículo se aplica al modelo de implementación de Resource Manager.

## <a name="protocol"></a>¿Qué protocolo utiliza P2S?

La conexión VPN de punto a sitio usa uno de los siguientes protocolos:

* El protocolo de túnel de sockets seguro (SSTP), que es un protocolo VPN propio basado en SSL. Una solución de VPN basada en SSL puede penetrar firewalls, puesto que la mayoría de ellos abre el puerto TCP 443, que utiliza SSL. El protocolo SSTP solo se admite en dispositivos Windows. Azure es compatible con todas las versiones de Windows que tienen SSTP (Windows 7 y versiones posteriores).

* La conexión VPN IKEv2, una solución de VPN con protocolo de seguridad de Internet basada en estándares. La conexión VPN IKEv2 puede utilizarse para la conexión desde dispositivos Mac (versión de OSX 10.11 y versiones posteriores).

Si tiene un entorno de cliente mixto con dispositivos Windows y Mac, configure tanto SSTP como IKEv2.

>[!NOTE]
>Actualmente, IKEv2 para P2S está en su versión preliminar y está disponible para el modelo de implementación del Administrador de recursos.
>

## <a name="authentication"></a>¿Cómo se autentican los clientes VPN de punto a sitio?

Para que Azure acepte una conexión VPN de punto a sitio, el usuario primero debe autenticarse. Azure ofrece dos mecanismos para autenticar los usuarios que se conectan.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticación con certificados nativos de Azure

Con la autenticación con certificados nativos de Azure, para autenticar el usuario que se conecta, se usa un certificado de cliente presente en el dispositivo. Los certificados de cliente se generan a partir de un certificado raíz de confianza y se instalan en cada equipo cliente. Puede, o bien, usar un certificado raíz generado mediante una solución empresarial, o bien, generar un certificado autofirmado.

La validación del certificado de cliente se realiza mediante la puerta de enlace de VPN durante el establecimiento de la conexión VPN P2S. El certificado raíz es necesario para la validación y se debe cargar en Azure. 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticación mediante el servidor de dominio de Active Directory (AD)

La autenticación de dominio de AD permite a los usuarios conectarse a Azure con sus credenciales de dominio de la organización. Requiere un servidor RADIUS que se integre con el servidor de AD. Las organizaciones también pueden aprovechar las implementaciones existentes de RADIUS.   
 El servidor RADIUS puede implementarse de forma local o en la red virtual de Azure. Durante la autenticación, la puerta de enlace de VPN de Azure actúa como acceso directo y reenvía los mensajes de autenticación entre el servidor RADIUS y el dispositivo que se conecta. Por lo tanto, la disponibilidad de la puerta de enlace del servidor RADIUS es importante. Si el servidor RADIUS está presente de forma local, se necesita una conexión VPN S2S de Azure en el sitio local para la disponibilidad.  
 El servidor RADIUS también se integra con los servicios de certificados de AD. Esto le permite usar el servidor RADIUS y la implementación de certificados de empresa para la autenticación de certificados de P2S como alternativa a la autenticación de certificados de Azure. La ventaja es que no es necesario cargar certificados raíz y revocados en Azure.

Los servidores RADIUS también se integran con otros sistemas de identidad externos. Esto abre una gran cantidad de opciones de autenticación para VPN P2S, incluidas las opciones de multifactor.

>[!NOTE]
>Actualmente, la autenticación RADIUS para P2S se encuentra en versión preliminar.
>

![point-to-site]](./media/point-to-site-about/p2s.png "De punto a sitio")

### <a name="configuration-requirements-for-client-devices"></a>Requisitos de configuración para los dispositivos cliente

Los usuarios utilizan los clientes VPN nativos en los dispositivos Windows y Mac para P2S. Azure proporciona un archivo zip de configuración de cliente VPN con la configuración que necesitan estos clientes nativos para conectarse a Azure.

  * Para los dispositivos Windows, la configuración de cliente VPN consta de un paquete de instalación que los usuarios instalan en sus dispositivos.
  * Para los dispositivos Mac, consta del archivo mobileconfig que los usuarios instalan en sus dispositivos.

El archivo zip también proporciona los valores de algunas de las opciones de configuración importantes del lado de Azure que puede usar para crear su propio perfil para estos dispositivos. Algunos de los valores incluyen la dirección de puerta de enlace de VPN, los tipos de túnel configurados, las rutas y el certificado raíz para la validación de la puerta de enlace.

### <a name="which-gateway-skus-support-p2s-vpn"></a>¿Qué SKU de puerta de enlace admiten VPN de punto a sitio?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Las pruebas comparativas de rendimiento agregado se basan en las mediciones de varios túneles agregados a través de una sola puerta de enlace. No es un rendimiento garantizado debido a las condiciones del tráfico de Internet y a los comportamientos de las aplicaciones.
* Puede encontrar más información sobre los precios en la página Precios. 
* La información del SLA (acuerdo de nivel de servicio) puede encontrarse en la página SLA.

## <a name="faqcert"></a>Preguntas más frecuentes acerca de la autenticación de certificado nativa de Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Preguntas más frecuentes acerca de la autenticación RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Pasos siguientes

[Configuración de conexiones de punto a sitio: autenticación con RADIUS](point-to-site-how-to-radius-ps.md)

[Configuración de conexiones de punto a sitio: autenticación con certificados nativos de Azure](vpn-gateway-howto-point-to-site-rm-ps.md)
