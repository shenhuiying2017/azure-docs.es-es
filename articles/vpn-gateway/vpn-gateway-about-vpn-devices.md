---
title: Acerca de los dispositivos VPN para las conexiones de VPN Gateway de sitio a sitio para Azure Virtual Networks | Microsoft Docs
description: "Este artículo describe los dispositivos VPN y los parámetros de IPsec para las conexiones de puerta de enlace de VPN de sitio a sitio de VPN Gateway y contiene vínculos a ejemplos y a las instrucciones de configuración."
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
ms.date: 12/01/2016
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: d85451d84f605f8472da66574cfec3ba212884ea
ms.openlocfilehash: f7ecd6b2e590122c4794cd0a81754da9398ba320


---
# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Acerca de los dispositivos VPN para las conexiones de puerta de enlace de VPN de sitio a sitio
Para configurar una conexión VPN de sitio a sitio (S2S), se requiere un dispositivo VPN. Las conexiones de sitio a sitio pueden usarse para crear una solución híbrida o siempre que desee una conexión segura entre su red local y la red virtual. Este artículo trata sobre los dispositivos VPN compatibles y los parámetros de configuración.

> [!NOTE]
> Al configurar una conexión de sitio a sitio, una dirección IP IPv4 pública es necesaria para el dispositivo VPN.                                                                                                                                                                               
>
>

Si el dispositivo no aparece en la tabla de [dispositivos VPN validadoss](#devicetable), consulte la sección [Dispositivos VPN no validados](#additionaldevices) de este artículo. Es posible que el dispositivo funcione aún con Azure. Para obtener soporte con los dispositivos VPN, póngase en contacto con el fabricante.

**Elementos que hay que tener en cuenta para consultar las tablas:**

* Ha habido un cambio de terminología del enrutamiento estático y dinámico. Es probable que realice la ejecución en ambos términos. No hay ningún cambio de funcionalidad; solo cambian los nombres.
  * Enrutamiento estático = PolicyBased
  * Enrutamiento dinámico = RouteBased
* Las especificaciones de puerta de enlace de VPN de alto rendimiento y de puerta de enlace de VPN RouteBased son las mismas, a menos que se indique lo contrario. Por ejemplo, los dispositivos VPN validados que son compatibles con las puertas de enlace de VPN RouteBased también son compatibles con la puerta de enlace de VPN de alto rendimiento de Azure.

## <a name="a-namedevicetableavalidated-vpn-devices"></a><a name="devicetable"></a>Dispositivos VPN validados
Hemos validado un conjunto de dispositivos VPN estándar en colaboración con proveedores de dispositivos. Todos los dispositivos de las familias de dispositivos incluidos en la lista siguiente deben funcionar con las puertas de enlace de VPN de Azure. Consulte el artículo [Acerca de Puerta de enlace de VPN](vpn-gateway-about-vpngateways.md) para comprobar el tipo de puerta de enlace que debe crear para la solución que desea configurar.

Con el fin de configurar el dispositivo VPN, consulte los vínculos correspondientes a la familia de dispositivos apropiada. Para obtener soporte con los dispositivos VPN, póngase en contacto con el fabricante.

| **Proveedor** | **Familia de dispositivos** | **Versión mínima de sistema operativo** | **PolicyBased** | **RouteBased** |
| --- | --- | --- | --- | --- |
| Allied Telesis |Enrutadores VPN de la serie AR |2.9.2 |Próximamente |No compatible |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall de la serie F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Instrucciones de configuración](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Instrucciones de configuración](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall de la serie X |Barracuda Firewall 6.5 |[Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |No compatible |
| Brocade |Vyatta 5400 vRouter |Virtual Router 6.6R3 GA |[Instrucciones de configuración](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |No compatible |
| Punto de comprobación |Puerta de enlace de seguridad |R75.40<br>R75.40VS |[Instrucciones de configuración](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Instrucciones de configuración](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |No compatible |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Ejemplos de Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 y superior |[Instrucciones de integración](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |No compatible |
| Dell SonicWALL |Serie TZ, serie NSA<br>Serie SuperMassive<br>Serie E-Class NSA |SonicOS 5.8.x<br>[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)<br>[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Guía de configuración para SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guía de configuración para SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Guía de configuración para SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guía de configuración para SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |Serie BIG-IP |12.0 |[Instrucciones de configuración](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Instrucciones de configuración](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.x |[Instrucciones de configuración](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Instrucciones de configuración](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Serie SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Instrucciones de configuración](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |No compatible |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Serie J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Servicio de acceso remoto y enrutamiento |Windows Server 2012 |No compatible |[Ejemplos de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |N/D |[Guía de instalación](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guía de instalación](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Próximamente) |No compatible |
| Palo Alto Networks |Todos los dispositivos que ejecutan PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 o posterior<br>RouteBased: 7.0.5 o posterior |[Instrucciones de configuración](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Instrucciones de configuración](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |Todo |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Instrucciones de configuración](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Instrucciones de configuración](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) Los enrutadores de la serie ISR 7200 solo admiten VPN PolicyBased.

## <a name="a-nameadditionaldevicesanon-validated-vpn-devices"></a><a name="additionaldevices"></a>Dispositivos VPN no validados
Si el dispositivo no aparece en la tabla de dispositivos VPN validados, es posible que todavía funcione con una conexión de sitio a sitio. Verifique que el dispositivo VPN cumple los requisitos mínimos descritos en la sección Requisitos de las puertas de enlace del artículo [Información acerca de VPN Gateway](vpn-gateway-about-vpngateways.md). Los dispositivos que cumplen los requisitos mínimos deberían funcionar correctamente también con las puertas de enlace de VPN. Póngase en contacto con el fabricante del dispositivo para obtener instrucciones adicionales de soporte técnico y configuración.

## <a name="editing-device-configuration-samples"></a>Editar los ejemplos de configuración de dispositivos
Después de descargar el ejemplo de configuración del dispositivo VPN proporcionado, deberá reemplazar algunos de los valores para reflejar la configuración de su entorno.

**Para editar una muestra:**

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

## <a name="ipsec-parameters"></a>Parámetros de IPsec
> [!NOTE]
> Aunque los valores que se enumeran en la siguiente tabla son compatibles con la Puerta de enlace de VPN de Azure, actualmente no hay ninguna forma de especificar o seleccionar una combinación específica en la Puerta de enlace de VPN de Azure. Debe especificar las restricciones en el dispositivo VPN local. Además, tiene que fijar el tamaño de segmento máximo en 1350.
>
>

### <a name="ike-phase-1-setup"></a>Configuración de la fase 1 de IKE
| **Propiedad** | **PolicyBased** | **Puerta de enlace de VPN de alto rendimiento o estándar y RouteBased** |
| --- | --- | --- |
| Versión de IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticación |Clave previamente compartida |Clave previamente compartida |
| Algoritmos de cifrado |AES256 AES128 3DES |AES256 3DES |
| Algoritmo hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Vida útil (tiempo) de la asociación de seguridad (SA) de la fase 1 |28.800 segundos |10.800 segundos |

### <a name="ike-phase-2-setup"></a>Configuración de la fase 2 de IKE
| **Propiedad** | **PolicyBased** | **Puerta de enlace de VPN de alto rendimiento o estándar y RouteBased** |
| --- | --- | --- |
| Versión de IKE |IKEv1 |IKEv2 |
| Algoritmo hash |SHA1(SHA128) |SHA1(SHA128) |
| Vida útil (tiempo) de la asociación de seguridad (SA) de la fase 2 |3.600 segundos |3.600 segundos |
| Vida útil (rendimiento) de la asociación de seguridad (SA) de la fase 2 |102.400.000 KB |- |
| Ofertas de autenticación y cifrado de SA de IPsec (en orden de preferencia) |1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D |Vea *Ofertas de asociación de seguridad (SA) con IPsec de puerta de enlace basada en enrutamiento* (a continuación) |
| Confidencialidad directa perfecta (PFS) |No |No (*) |
| Detección de cuellos del mismo nivel |No compatible |Compatible |

(*) La puerta de enlace de Azure como respondedor IKE puede aceptar los grupos PFS DH 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>ofertas de asociación de seguridad (SA) con IPsec de puerta de enlace basada en enrutamiento
En la siguiente tabla encontrará una lista de las ofertas de autenticación y cifrado de SA de IPsec. Las ofertas se enumeran en el orden de preferencia en el que se presentan o se aceptan.

| **Ofertas de autenticación y cifrado de SA de IPsec** | **Puerta de enlace de Azure como iniciador** | **Puerta de enlace de Azure como respondedor** |
| --- | --- | --- |
| 1 |ESP AES_256 SHA |ESP AES_128 SHA |
| 2 |ESP AES_128 SHA |ESP 3_DES MD5 |
| 3 |ESP 3_DES MD5 |ESP 3_DES SHA |
| 4 |ESP 3_DES SHA |AH SHA1 con ESP AES_128 con HMAC nulo |
| 5 |AH SHA1 con ESP AES_256 con HMAC nulo |AH SHA1 con ESP 3_DES con HMAC nulo |
| 6 |AH SHA1 con ESP AES_128 con HMAC nulo |AH MD5 con ESP 3_DES con HMAC nulo, sin vida útil propuesta |
| 7 |AH SHA1 con ESP 3_DES con HMAC nulo |AH SHA1 con ESP 3_DES SHA1, sin vida útil |
| 8 |AH MD5 con ESP 3_DES con HMAC nulo, sin vida útil propuesta |AH MD5 con ESP 3_DES MD5, sin vida útil |
| 9 |AH SHA1 con ESP 3_DES SHA1, sin vida útil |ESP DES MD5 |
| 10 |AH MD5 con ESP 3_DES MD5, sin vida útil |ESP DES SHA1, sin vida útil |
| 11 |ESP DES MD5 |AH SHA1 con ESP DES HMAC nulo, sin vida útil propuesta |
| 12 |ESP DES SHA1, sin vida útil |AH MD5 con ESP DES HMAC nulo, sin vida útil propuesta |
| 13 |AH SHA1 con ESP DES HMAC nulo, sin vida útil propuesta |AH SHA1 con ESP DES SHA1, sin vida útil |
| 14 |AH MD5 con ESP DES HMAC nulo, sin vida útil propuesta |AH MD5 con ESP DES MD5, sin vida útil |
| 15 |AH SHA1 con ESP DES SHA1, sin vida útil |ESP SHA, sin vida útil |
| 16 |AH MD5 con ESP DES MD5, sin vida útil |ESP MD5, sin vida útil |
| 17 |- |AH SHA, sin vida útil |
| 18 |- |AH MD5, sin vida útil |

* Puede especificar el cifrado IPsec ESP NULL con puertas de enlace de VPN de alto rendimiento y RouteBased. El cifrado basado en null no proporciona protección de datos en tránsito, solo se debe usar al máximo rendimiento y es necesaria la mínima latencia.  Los clientes pueden elegir usarlo en escenarios de comunicación entre redes virtuales o cuando se aplique el cifrado en otra parte de la solución.
* Para conectividad entre locales a través de Internet, use la configuración de la puerta de enlace de VPN de Azure predeterminada con los algoritmos de cifrado y hash de las tablas anteriores para garantizar la seguridad de su comunicación crítica.



<!--HONumber=Dec16_HO1-->


