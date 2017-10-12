---
title: Escenarios avanzados con Azure MFA y VPN de terceros
description: "Guías paso a paso para la configuración de Azure MFA para integrarse con Cisco, Citrix y Juniper."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: kgremban
ms.openlocfilehash: 61cf29acac22d546e1b75604f799993ad6ff9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Escenarios avanzados con Azure Multi-Factor Authentication y soluciones de VPN de terceros
Azure Multi-Factor Authentication se puede usar para conectar fácilmente con una variedad de soluciones VPN de terceros. Este artículo se centra la aplicación VPN de Cisco® ASA, la aplicación VPN SSL de NetScaler de Citrix y la aplicación VPN SSL de Juniper Networks Secure Access o Pulse Secure Connect Secure. Se han elaborado guías de configuración para abordar estas tres aplicaciones comunes. También se puede integrar Servidor Multi-Factor Authentication con la mayoría de los demás sistemas que utilizan RADIUS, LDAP, IIS o autenticación basada en notificaciones en AD FS. Puede encontrar más detalles en las [configuraciones de Servidor MFA](multi-factor-authentication-get-started-server.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Aplicación VPN de Cisco ASA y Azure Multi-Factor Authentication
Azure Multi-Factor Authentication se integra con su aplicación VPN de Cisco® ASA para ofrecer más seguridad para el acceso al portal y los inicios de sesión VPN de Cisco AnyConnect®.  Se puede usar el protocolo LDAP o RADIUS.  Seleccione una de las siguientes opciones para descargar las guías de configuración paso a paso detalladas.

| Guía de configuración | Description |
| --- | --- |
| [Configuración de Cisco ASA con Anyconnect VPN y Azure MFA para LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integración de la aplicación VPN de Cisco ASA con Azure MFA mediante LDAP |
| [Configuración de Cisco ASA con Anyconnect VPN y Azure MFA para RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integración perfecta de la aplicación VPN de Cisco ASA con Azure MFA mediante RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>VPN SSL de Citrix NetScaler y Azure Multi-Factor Authentication
Azure Multi-Factor Authentication se integra con su aplicación VPN SSL de Citrix NetScaler para ofrecer más seguridad para el acceso al portal y los inicios de sesión VPN SSL de Cisco NetScaler.  Se puede usar el protocolo LDAP o RADIUS.  Seleccione una de las siguientes opciones para descargar las guías de configuración paso a paso detalladas.

| Guía de configuración | Description |
| --- | --- |
| [Configuración de VPN SSL de Citrix NetScaler y Azure MFA para LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integración de su VPN SSL de Citrix NetScaler con la aplicación de Azure MFA mediante LDAP |
| [Configuración de VPN SSL de Citrix NetScaler y Azure MFA para RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integración de su aplicación VPN SSL de Citrix NetScaler con Azure MFA mediante RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Aplicación VPN SSL de Juniper/Pulse Secure y Azure Multi-Factor Authentication
Azure Multi-Factor Authentication se integra con su aplicación VPN SSL de Juniper/Pulse Secure para ofrecer más seguridad para el acceso al portal y los inicios de sesión VPN SSL de Juniper/Pulse Secure.  Se puede usar el protocolo LDAP o RADIUS.  Seleccione una de las siguientes opciones para descargar las guías de configuración paso a paso detalladas.

| Guía de configuración | Description |
| --- | --- |
| [Configuración de VPN SSL de Juniper/Pulse Secure y Azure MFA para LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integración de su VPN SSL de Juniper/Pulse Secure con la aplicación de Azure MFA mediante LDAP |
| [Configuración de VPN SSL de Juniper/Pulse Secure y Azure MFA para RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integración de su VPN SSL de Juniper/Pulse Secure con Azure MFA mediante RADIUS |

## <a name="next-steps"></a>Pasos siguientes

- [Aumento de la infraestructura de autenticación existente con la extensión de NPS para Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md)

- [Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)