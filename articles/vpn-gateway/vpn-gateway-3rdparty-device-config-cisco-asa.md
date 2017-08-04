---
title: "Ejemplo de configuración: Dispositivo Cisco ASA conectado a Puertas de enlace de VPN de Azure | Microsoft Docs"
description: "En este artículo se proporciona un ejemplo de configuración para dispositivos Cisco ASA conectados a Puertas de enlace de VPN de Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Ejemplo de configuración: dispositivo Cisco ASA (IKEv2/no BGP)
En este artículo se proporcionan configuraciones de ejemplo para dispositivos Cisco ASA conectados a Puertas de enlace de VPN de Azure.

## <a name="device-at-a-glance"></a>Detalles del dispositivo

|                        |                                   |
| ---                    | ---                               |
| Fabricante del dispositivo          | Cisco                             |
| Modelo de dispositivo           | ASA (Adaptive Security Appliance) |
| Versión de destino         | 8.4+                              |
| Modelo probado           | ASA 5505                          |
| Versión probada         | 9.2                               |
| Versión de IKE            | **IKEv2**                         |
| BGP                    | **No**                            |
| Tipo de puerta de enlace de VPN de Azure | Puerta de enlace de VPN **basada en rutas**       |
|                        |                                   |

> [!NOTE]
> 1. La configuración siguiente conecta un dispositivo Cisco ASA a un puerta de enlace de VPN **basada en rutas** de Azure mediante la directiva IPsec o IKE personalizada con la opción "UserPolicyBasedTrafficSelectors", como se describe en [este artículo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
> 2. Se requiere que los dispositivos ASA usen **IKEv2** con configuraciones basadas en listas de acceso, no basadas en VTI.
> 3. Consulte las especificaciones del proveedor de dispositivos VPN para asegurarse de que los dispositivos VPN locales admiten la directiva.

## <a name="vpn-device-requirements"></a>Requisitos del dispositivo VPN
Las puertas de enlace de VPN de Azure usan los conjuntos de protocolos IPsec o IKE estándar para establecer túneles VPN S2S. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obtener los parámetros detallados del protocolo IPsec o IKE, y los algoritmos criptográficos predeterminados para las puertas de enlace de VPN de Azure. También puede especificar la combinación exacta de algoritmos criptográficos y ventajas clave para una conexión específica, como se describe en [Acerca de los requisitos criptográficos](vpn-gateway-about-compliance-crypto.md). Si selecciona una combinación específica de algoritmos criptográficos y ventajas clave, asegúrese de que se usan las especificaciones correspondientes de los dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Un solo túnel VPN
Esta topología consta de un solo túnel VPN S2S entre una puerta de enlace de VPN de Azure y el dispositivo VPN local. Opcionalmente puede configurar BGP a través del túnel VPN.

![túnel único](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Consulte [Configuración de un solo túnel](vpn-gateway-3rdparty-device-config-overview.md#singletunnel) para obtener instrucciones paso a paso sobre cómo crear las configuraciones de Azure.

### <a name="network-and-vpn-gateway-information"></a>Información de puerta de enlace VPN y de red
En esta sección se muestran los parámetros de este ejemplo.

| **Parámetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefijos de dirección de red virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP de la puerta de enlace de VPN de Azure         | Azure_Gateway_Public_IP      |
| Prefijos de direcciones locales | 10.51.0.0/16<br>10.52.0.0/16 |
| Dirección IP del dispositivo VPN local    | OnPrem_Device_Public_IP     |
| *ASN de BGP de red virtual                | 65010                        |
| *IP del par BGP de Azure           | 10.12.255.30                 |
| *ASN de BGP local         | 65050                        |
| *IP del par BGP local     | 10.52.255.254                |
|                              |                              |

* (*) Parámetros opcionales solo para BGP.

### <a name="ipsecike-policy--parameters"></a>Parámetros y directivas de IPsec o IKE

En la tabla siguiente se enumeran los algoritmos y parámetros de IPsec o IKE que se usan en el ejemplo. Consulte las especificaciones del dispositivo VPN para asegurarse de que todos los algoritmos enumerados anteriormente son compatibles con los modelos de dispositivo VPN y las versiones de firmware.

| **IPsec o IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Cifrado IKEv2 | AES256                               |
| Integridad de IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| Cifrado IPsec | AES256                               |
| Integridad de IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Vigencia de SA (QM)   | 7200 segundos                         |
| Selector de tráfico | UsePolicyBasedTrafficSelectors $True |
| Clave previamente compartida   | PreSharedKey                         |
|                  |                                      |

- (*) En algunos dispositivos, la integridad de IPsec debe ser "null" si se usa AES GCM como algoritmo de cifrado de IPsec.

### <a name="device-notes"></a>Notas del dispositivo

>[!NOTE]
>
> 1. La compatibilidad con IKEv2 requiere ASA versión 8.4 y versiones posteriores.
> 2. La compatibilidad con grupos DH y PFS superiores (por encima del Grupo 5) requiere la versión ASA 9.x.
> 3. Cifrado IPsec con AES-GCM e integridad IPsec con compatibilidad con SHA-256, SHA-384, SHA-512 requiere la versión ASA 9.x en el hardware ASA más reciente; **no** se admite ASA 5505, 5510, 5520, 5540, 5550, 5580. (Compruebe las especificaciones del fabricante para confirmarlo).
>


### <a name="sample-device-configurations"></a>Ejemplos de configuraciones de dispositivo
En el script siguiente se proporciona un ejemplo de configuración basado en la topología y los parámetros mencionados anteriormente. La configuración del túnel VPN de S2S consta de las siguientes partes:

1. Interfaces y rutas
2. Listas de acceso
3. Directiva y parámetros de IKE (Fase 1 o Modo principal)
4. Directiva y parámetros de IPsec (Fase 2 o Modo rápido)
5. Otros parámetros (bloqueo MSS de TCP, etc.)

>[!IMPORTANT] 
>Asegúrese de completar la configuración adicional que se enumera a continuación y reemplace los marcadores de posición por los valores reales:
> 
> - Configuración de interfaz para interfaces internas y externas
> - Rutas para las redes internas o privadas, y públicas o externas
> - Asegúrese de que todos los nombres y números de directiva son únicos en el dispositivo
> - Asegúrese de que se admiten algoritmos criptográficos en el dispositivo
> - Reemplace los siguientes marcadores de posición por los valores reales
>   - Nombre de la interfaz externa: "outside"
>   - Azure_Gateway_Public_IP
>   - OnPrem_Device_Public_IP
>   - Pre_Shared_Key de IKE
>   - Nombres de la red virtual y la puerta de enlace de red local (VNetName, LNGName)
>   - Prefijos de direcciones de red de la red virtual y local
>   - Máscaras de red correctas

#### <a name="sample-configuration"></a>Configuración de ejemplo

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Comandos de depuración simples

Estos son algunos comandos de ASA para fines de depuración:

1. Mostrar las asociaciones de seguridad de IPsec e IKE
    - "show crypto ipsec sa"
    - "show crypto ikev2 sa"
2. Entrando en modo de depuración: Esto puede provocar mucho ruido en la consola.
    - "debug crypto ikev2 platform <level>"
    - "debug crypto ikev2 protocol <level>"
3. Enumerar configuraciones actuales
    - "show run": muestra las configuraciones actuales en el dispositivo; puede usar los diferentes subcomandos para enumerar partes específicas de la configuración. Por ejemplo, "show run crypto", "show run access-list", "show run tunnel-group", etc.


## <a name="next-steps"></a>Pasos siguientes
Consulte [Configuración activa-activa de puertas de enlace de VPN para conexiones entre locales y de red virtual a red virtual](vpn-gateway-activeactive-rm-powershell.md) para ver los pasos para configurar de modo activo-activo conexiones entre locales y de red virtual a red virtual.


