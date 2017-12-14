---
title: "Comparación de Azure AD Join con Azure Active Directory Domain Services | Microsoft Docs"
description: "Elección entre Azure AD Join y Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: fb28c7a66d26119444dd720c2e134f2818d46cfc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Elección entre Azure Active Directory Join y Azure Active Directory Domain Services
En este artículo se describen las diferencias entre Azure Active Directory (AD) Join y Azure AD Domain Services para ayudarle a elegir lo que más le convenga según sus casos de uso.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Dispositivos registrados con Azure AD y unidos a Azure AD
Azure AD le permite administrar la identidad de los dispositivos usados por su organización y controlar el acceso a los recursos corporativos desde estos dispositivos. Los usuarios pueden registrar su propio dispositivo personal con Azure AD, lo que proporcionará una identidad al dispositivo. En consecuencia, Azure AD puede autenticar el dispositivo cuando un usuario inicia sesión en Azure AD y utiliza el dispositivo para acceder a los recursos protegidos. Además, puede administrar el dispositivo con software de administración de dispositivos móviles (MDM), como Microsoft Intune. Esta característica permite restringir el acceso a recursos confidenciales desde dispositivos administrados que cumplan las directivas corporativas.

También puede unir dispositivos propiedad de la organización a Azure AD. Este mecanismo ofrece las mismas ventajas que registrar un dispositivo personal con Azure AD. Además, los usuarios pueden iniciar sesión en el dispositivo con sus credenciales corporativas. Los dispositivos unidos a Azure AD ofrecen las siguientes ventajas:
* Inicio de sesión único (SSO) en aplicaciones protegidas por Azure AD.
* Itinerancia de configuraciones de usuario que cumplen las directivas corporativas entre dispositivos.
* Acceso a la Tienda Windows para empresas con sus credenciales corporativas.
* Windows Hello para empresas
* Acceso restringido a aplicaciones y recursos desde dispositivos que cumplen las directivas corporativas.

| **Tipo de dispositivo** | **Plataformas de dispositivo** | **Mecanismo** |
|:---| --- | --- |
| Dispositivos personales | Windows 10, iOS, Android y macOS | Registrado en Azure AD |
| Dispositivo propiedad de la organización no unido a AD local | Windows 10 | Unido a Azure AD |
| Dispositivo propiedad de la organización unido a AD local | Windows 10 | Híbrido unido a Azure AD |

En un dispositivo registrado con Azure AD o unido a Azure AD, la autenticación de usuario se realiza mediante modernos protocolos basados en OpenID Connect y OAuth. Estos protocolos están diseñados para funcionar a través de Internet y son excelentes para escenarios móviles donde los usuarios acceden a recursos corporativos desde cualquier lugar.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Unión a un dominio administrado de Azure AD Domain Services
Azure AD Domain Services proporciona un dominio de AD administrado en una red virtual de Azure. Puede unir maquinas a este dominio administrado mediante mecanismos de unión a dominio tradicionales. Al dominio administrado pueden unirse clientes de Windows (Windows 7 y Windows 10) y máquinas de Windows Server. Además, también puede unir máquinas de Linux y macOS al dominio administrado. Al unir una máquina a un dominio de AD, los usuarios pueden iniciar sesión en la máquina con sus credenciales corporativas. Estas máquinas se pueden administrar mediante una directiva de grupo para garantizar el cumplimiento de las directivas de seguridad de la organización.

En una máquina unida a un dominio, la autenticación de usuario se realiza con protocolos de autenticación NTLM o Kerberos. La máquina unida al dominio necesita una línea de visión a los controladores del dominio administrado para que funcione la autenticación de usuario. Por lo tanto, la máquina unida al dominio debe estar en la misma red virtual que el dominio administrado. Como alternativa, la máquina unida al dominio debe conectarse al dominio administrado a través de una red virtual emparejada o una conexión de ExpressRoute o VPN de sitio a sitio. Por lo tanto, este mecanismo no es idóneo para dispositivos móviles o que se conecten a recursos fuera de la red corporativa.

> [!NOTE]
> Técnicamente, es posible unir una estación de trabajo de cliente local a un dominio administrado a través de una conexión de ExpressRoute o VPN de sitio a sitio. No obstante, para dispositivos de usuario final, se recomienda encarecidamente registrar el dispositivo con Azure AD (dispositivos personales) o unirlo a Azure AD (dispositivos corporativos). Este mecanismo funciona mejor por Internet y permite a los usuarios finales trabajar desde cualquier lugar. Azure AD Domain Services es ideal para máquinas virtuales Windows o Linux Server implementadas en redes virtuales de Azure en las que están implementadas sus aplicaciones.


## <a name="summary---key-differences"></a>Resumen - Diferencias clave
| **Aspecto** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| Dispositivo controlado por | Azure AD | Dominio administrado con Azure AD Domain Services |
| Representación en el directorio | Objetos de dispositivo en el directorio de Azure AD | Objetos de equipo en el dominio administrado por AAD-DS |
| Autenticación | Protocolos basados en OAuth y OpenID Connect | Protocolos de Kerberos y NTLM |
| Administración | Software de administración de dispositivos móviles (MSM), como Intune | Directiva de grupo |
| Redes | Funciona a través de Internet | Las máquinas deben estar en la misma red virtual que el dominio administrado.|
| Ideal para... | Dispositivos móviles o de escritorio de usuario final | Máquinas virtuales de servidor implementadas en Azure |


## <a name="next-steps"></a>Pasos siguientes
### <a name="learn-more-about-azure-ad-domain-services"></a>Más información sobre Azure AD Domain Services
* [Información general de Azure AD Domain Services](active-directory-ds-overview.md)
* [Características](active-directory-ds-features.md)
* [Escenarios de implementación](active-directory-ds-scenarios.md)
* [Averiguar si Azure AD Domain Services se adapta a sus casos de uso](active-directory-ds-comparison.md)
* [Información sobre la sincronización de Azure AD Domain Services con el directorio Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Obtenga más información acerca de Azure AD Join
* [Introducción a la administración de dispositivos en Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Introducción a Azure AD Domain Services
* [Habilitación de Azure AD Domain Services mediante Azure Portal](active-directory-ds-getting-started.md)
