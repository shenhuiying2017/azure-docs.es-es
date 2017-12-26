---
title: Acerca de los dispositivos VPN para las conexiones de Azure ente locales | Microsoft Docs
description: "En este artículo se describen los dispositivos VPN y los parámetros de IPsec de las conexiones entre locales de VPN Gateway S2S. Se proporcionan vínculos a ejemplos e instrucciones de configuración."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: yushwang
ms.openlocfilehash: bb6f9f4df9afa9d0c1a75fbb1166798a2aef4bb4
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Acerca de los dispositivos VPN y los parámetros de IPsec/IKE para conexiones de VPN Gateway de sitio a sitio

Para configurar una conexión VPN entre locales de sitio a sitio (S2S) mediante una puerta de enlace de VPN se requiere un dispositivo VPN. Las conexiones de sitio a sitio pueden usarse para crear una solución híbrida o siempre que desee conexiones seguras entre su red local y la red virtual. Este artículo incluye la lista de dispositivos VPN validados y una lista de parámetros IPsec/IKE para las puertas de enlace de VPN.

> [!IMPORTANT]
> Si tiene problemas de conectividad entre los dispositivos VPN locales y las puertas de enlace de VPN, consulte [Problemas conocidos de compatibilidad de dispositivos](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>Elementos que hay que tener en cuenta para consultar las tablas:

* Ha habido un cambio terminológico en las puertas de enlace de VPN de Azure. Solo han cambiado los nombres. No hay ningún cambio de funcionalidad.
  * Enrutamiento estático = PolicyBased
  * Enrutamiento dinámico = RouteBased
* Las especificaciones de VPN Gateway HighPerformance y VPN Gateway RouteBased son las mismas, a menos que se indique lo contrario. Por ejemplo, los dispositivos VPN validados que son compatibles con las puertas de enlace de VPN RouteBased también son compatibles con las puertas de enlace de VPN HighPerformance.

## <a name="devicetable"></a>Dispositivos VPN validados y guías de configuración de dispositivos

> [!NOTE]
> Al configurar una conexión de sitio a sitio, una dirección IP IPv4 pública es necesaria para el dispositivo VPN.
>

En colaboración con proveedores de dispositivos, hemos validado un conjunto de dispositivos VPN estándar. Todos los dispositivos de las familias de dispositivos en la lista siguiente deben trabajar con puertas de enlace de VPN. Consulte la información [acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype) para entender el tipo de VPN utilizado (PolicyBased o RouteBased) para la solución VPN Gateway que desea configurar.

Con el fin de configurar el dispositivo VPN, consulte los vínculos correspondientes a la familia de dispositivos apropiada. Los vínculos a las instrucciones de configuración se proporcionan dentro de lo posible. Para obtener soporte para los dispositivos VPN, póngase en contacto con el fabricante.

|**Proveedor**          |**Familia de dispositivos**     |**Versión mínima de sistema operativo** |**Instrucciones de configuración PolicyBased** |**Instrucciones de configuración RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |No compatible  |[Guía de configuración](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |Enrutadores VPN de la serie AR |2.9.2                  |Próximamente     |No compatible  |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall de la serie F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Guía de configuración](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Guía de configuración](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall de la serie X |Barracuda Firewall 6.5 |[Guía de configuración](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |No compatible |
| Brocade            |Vyatta 5400 vRouter   |Virtual Router 6.6R3 GA|[Guía de configuración](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |No compatible |
| Punto de comprobación |Security Gateway |R77.30 |[Guía de configuración](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Guía de configuración](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |[Guía de configuración*](vpn-gateway-3rdparty-device-config-cisco-asa.md) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Ejemplos de configuración**](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 y superior |[Guía de configuración](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |No compatible |
| F5 |Serie BIG-IP |12.0 |[Guía de configuración](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Guía de configuración](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Guía de configuración](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |Serie SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Guía de configuración](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |No compatible |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Serie J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Ejemplos de configuración](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Servicio de acceso remoto y enrutamiento |Windows Server 2012 |No compatible |[Ejemplos de configuración](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |N/D |[Guía de configuración](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |No compatible |
| Palo Alto Networks |Todos los dispositivos que ejecutan PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 o posterior<br>RouteBased: 7.1.4 |[Guía de configuración](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Guía de configuración](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| ShareTech | UTM de próxima generación (serie NU) | 9.0.1.3 | No compatible | [Guía de configuración](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Serie TZ, serie NSA<br>Serie SuperMassive<br>Serie E-Class NSA |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |No compatible |[Guía de configuración](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | Firewall de última generación XG | XG v17 | | [Guía de configuración](https://community.sophos.com/kb/127546) |
| WatchGuard |Todo |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Guía de configuración](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Guía de configuración](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> (*) Las versiones de Cisco ASA 8.4 y posteriores incorporan compatibilidad con IKEv2, puede conectarse a la puerta de enlace de VPN de Azure mediante la directiva personalizada de IPsec/IKE con la opción "UsePolicyBasedTrafficSelectors". Puede consultar este [artículo de procedimientos](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (**) Los enrutadores de la serie ISR 7200 solo admiten VPN basadas en directivas.

## <a name="additionaldevices"></a>Dispositivos VPN no validados

Si el dispositivo no aparece en la tabla de dispositivos VPN validados, es posible que todavía funcione con una conexión de sitio a sitio. Póngase en contacto con el fabricante del dispositivo para obtener instrucciones adicionales de soporte técnico y configuración.

## <a name="editing"></a>Edición de ejemplos de configuración de dispositivos

Después de descargar el ejemplo de configuración del dispositivo VPN proporcionado, deberá reemplazar algunos de los valores para reflejar la configuración de su entorno.

### <a name="to-edit-a-sample"></a>Para editar una muestra:

1. Abra el ejemplo con el Bloc de notas.
2. Busque y reemplace todas las cadenas de <*texto*> por los valores que pertenezcan al entorno. Asegúrese de incluir < y >. Cuando se especifica un nombre, el nombre que seleccione debe ser único. Si un comando no funciona, consulte la documentación del fabricante del dispositivo.

| **Texto de ejemplo** | **Cambiar a** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Nombre elegido para este objeto. Ejemplo: miRedLocal |
| &lt;RP_AzureNetwork&gt; |Nombre elegido para este objeto. Ejemplo: miRedAzure |
| &lt;RP_AccessList&gt; |Nombre elegido para este objeto. Ejemplo: miListaAccesoAzure |
| &lt;RP_IPSecTransformSet&gt; |Nombre elegido para este objeto. Ejemplo: miConjuntoTransIPSec |
| &lt;RP_IPSecCryptoMap&gt; |Nombre elegido para este objeto. Ejemplo: miAsignCifradoIPSec |
| &lt;SP_AzureNetworkIpRange&gt; |Especifique el rango. Ejemplo: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Especifique la máscara de subred. Ejemplo: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Especifique el rango local. Ejemplo: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Especifique la máscara de subred local. Ejemplo: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Esta información es específica de la red virtual y se encuentra en el Portal de administración como **Dirección IP de puerta de enlace**. |
| &lt;SP_PresharedKey&gt; |Esta información es específica de la red virtual y se encuentra en el Portal de administración, en Administrar clave. |

## <a name="ipsec"></a>Parámetros de IPsec/IKE

> [!IMPORTANT]
> 1. Las tablas siguientes contienen las combinaciones de algoritmos y parámetros que usan las puertas de enlace de VPN de Azure en la configuración predeterminada. Para puertas de enlace de VPN basadas en enrutamiento creadas mediante el modelo de implementación de Azure Resource Management, puede especificar una directiva personalizada en cada conexión individual. Consulte [Configuración de directiva IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obtener instrucciones detalladas.
>
> 2. Además, tiene que fijar el **MSS** de TCP en **1350**. O, si los dispositivos VPN no admiten la fijación de MSS, también puede establecer el **MTU** en la interfaz de túnel en **1400** bytes.
>

En las tablas siguientes:

* SA = Asociación de seguridad
* La fase 1 de IKE también se denomina "Modo principal"
* La fase 2 de IKE también se denomina "Modo rápido"

### <a name="ike-phase-1-main-mode-parameters"></a>Parámetros de la fase 1 de IKE (Modo principal)

| **Propiedad**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Versión de IKE           |IKEv1              |IKEv2              |
| Grupo Diffie-Hellman  |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticación |Clave previamente compartida     |Clave previamente compartida     |
| Algoritmos de cifrado y hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Vigencia de SA           |28.800 segundos     |28.800 segundos     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parámetros de la fase 2 de IKE (modo rápido)

| **Propiedad**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Versión de IKE                   |IKEv1          |IKEv2                                        |
| Algoritmos de cifrado y hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Ofertas de SA de QM del tipo routebased](#RouteBasedOffers) |
| Vigencia de SA (tiempo)            |3.600 segundos  |27 000 segundos                                |
| Vigencia de SA (bytes)           |102.400.000 KB | -                                           |
| Confidencialidad directa perfecta (PFS) |No             |[Ofertas de SA de QM del tipo routebased](#RouteBasedOffers) |
| Dead Peer Detection (DPD)     |No compatible  |Compatible                                    |


### <a name ="RouteBasedOffers"></a>Ofertas de asociación de seguridad de IPsec (SA de modo rápido de IKE) de VPN del tipo routebased

En la tabla siguiente se enumeran las ofertas de SA de IPsec (modo rápido de IKE). Las ofertas se enumeran en el orden de preferencia en el que se presentan o se aceptan.

#### <a name="azure-gateway-as-initiator"></a>Puerta de enlace de Azure como iniciador

|-  |**Cifrado**|**Autenticación**|**Grupo PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |

#### <a name="azure-gateway-as-responder"></a>Puerta de enlace de Azure como respondedor

|-  |**Cifrado**|**Autenticación**|**Grupo PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |
| 7 |DES           |SHA1              |None         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20 ||AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |None         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Puede especificar el cifrado IPsec ESP NULL con puertas de enlace de VPN RouteBased y HighPerformance. El cifrado basado en null no proporciona protección de datos en tránsito, solo se debe usar al máximo rendimiento y es necesaria la mínima latencia. Los clientes pueden elegir usarlo en escenarios de comunicación entre redes virtuales o cuando se aplique el cifrado en otra parte de la solución.
* Para conectividad entre locales a través de Internet, use la configuración de la puerta de enlace de VPN de Azure predeterminada con los algoritmos de cifrado y hash de las tablas anteriores para garantizar la seguridad de su comunicación crítica.

## <a name="known"></a>Problemas conocidos de compatibilidad de dispositivos

> [!IMPORTANT]
> Estos son los problemas conocidos de compatibilidad entre dispositivos VPN de terceros y puertas de enlace de VPN de Azure. El equipo de Azure está trabajando activamente con los proveedores para solucionar los problemas enumerados aquí. Una vez que se resuelvan, esta página se actualizará con la información más reciente. Consúltela periódicamente.
>
>

### <a name="feb-16-2017"></a>16 de febrero de 2017

**Dispositivos de Palo Alto Networks con una versión anterior a la 7.1.4** para VPN basada en rutas de Azure: si usa dispositivos VPN de Palo Alto Networks con una versión de PAN-OS anterior a la 7.1.4 y experimenta problemas de conectividad a las puertas de enlace de VPN basadas en rutas de Azure, realice los siguientes pasos:

1. Compruebe la versión de firmware del dispositivo de Palo Alto Networks. Si su versión de PAN-OS es anterior a la 7.1.4, actualice a esta versión.
2. En el dispositivo de Palo Alto Networks, cambie la duración de la fase 2 SA (o SA de modo rápido) a 28 800 segundos (8 horas) al conectarse a la puerta de enlace de VPN de Azure.
3. Si sigue experimentando problemas de conectividad, presente una solicitud de soporte técnico desde Azure Portal.
