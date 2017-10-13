---
title: "Información general sobre la administración y la supervisión de la seguridad en Azure | Microsoft Docs"
description: " Azure proporciona mecanismos de seguridad que ayudan en la administración y la supervisión de servicios en la nube y máquinas virtuales de Azure.  En este artículo se proporciona información general sobre estas características y servicios básicos de seguridad. "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 6787877deabafd0b7308e190cb45b4036049b05b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-management-and-monitoring-overview"></a>Información general sobre la administración y la supervisión de la seguridad en Azure
Azure proporciona mecanismos de seguridad que ayudan en la administración y la supervisión de servicios en la nube y máquinas virtuales de Azure. En este artículo se proporciona información general sobre estas características y servicios básicos de seguridad. Además, se incluyen vínculos a artículos que ofrecen detalles de cada uno de estos servicios o características para que pueda tener más información al respecto.

La seguridad de los servicios en la nube de Microsoft se basa en la colaboración y la responsabilidad compartida entre usted y Microsoft. Responsabilidad compartida significa que Microsoft es responsable de Microsoft Azure y de la seguridad física de sus centros de datos (mediante el uso de protecciones de seguridad, como puertas de entrada con notificación de bloqueo, barreras y restricciones). Además, Azure proporciona fuertes niveles de seguridad en la nube en la capa de software que satisfacen las necesidades de seguridad, privacidad y cumplimiento de sus exigentes clientes.

Usted es el propietario de sus datos e identidades, es responsable de protegerlos, de la seguridad de los recursos locales y de la seguridad de los componentes de nube sobre los que tiene el control. Microsoft proporciona controles de seguridad y funcionalidades que ayudan a proteger los datos y las aplicaciones. Su grado de responsabilidad de la seguridad se basa en el tipo de servicio en la nube.

En el siguiente gráfico se resume cómo se reparte la responsabilidad entre Microsoft y el cliente.

![Responsabilidad compartida][1]

Para un análisis más profundo sobre la administración de la seguridad, consulte [Administración de la seguridad en Azure](azure-security-management.md).

Estas son las características principales que se tratan en este artículo:

* Control de acceso basado en roles
* Antimalware
* Multi-Factor Authentication
* ExpressRoute
* Puertas de enlace de red virtual
* Privileged Identity Management
* Protección de identidad
* Centro de seguridad

## <a name="role-based-access-control"></a>Control de acceso basado en roles
El control de acceso basado en roles (RBAC) de Azure permite realizar una administración avanzada del acceso a los recursos de Azure. Con RBAC, puede conceder a los usuarios únicamente el grado de acceso que necesitan para realizar sus trabajos.  RBAC también puede ayudarle a garantizar que cuando los usuarios dejan la organización, pierden el acceso a los recursos en la nube.

Más información:

* [Blog del equipo de Active Directory sobre RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware
Con Azure, puede utilizar software antimalware de los principales proveedores de seguridad, como Microsoft, Symantec, Trend Micro, McAfee y Kaspersky, para proteger las máquinas virtuales de archivos malintencionados, adware y otras amenazas.

Microsoft Antimalware ofrece la posibilidad de instalar un agente antimalware tanto para roles PaaS como máquinas virtuales. Esta característica, basada en System Center Endpoint Protection, lleva a la nube tecnología de seguridad local de demostrada eficacia.

También ofrecemos una profunda integración con los productos [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ y [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ de Trend en la plataforma de Azure. DeepSecurity es una solución antivirus y SecureCloud es una solución de cifrado. DeepSecurity se implementará dentro de máquinas virtuales con un modelo de extensión. Con la interfaz de usuario del portal y PowerShell, puede elegir usar DeepSecurity dentro de nuevas máquinas virtuales que se están implementando, o en máquinas virtuales existentes que ya están implementadas.

Symantec End Point Protection (SEP) también se admite en Azure. Gracias a la integración del portal, los clientes tienen la posibilidad de especificar su intención de usar SEP dentro de una máquina virtual. SEP puede instalarse en una máquina virtual nueva a través de Azure Portal, o bien en una máquina virtual existente mediante PowerShell.

Más información:

* [Implementación de soluciones antimalware en máquinas virtuales de Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware para Cloud Services y Virtual Machines de Azure](azure-security-antimalware.md)
* [Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure](../virtual-machines/windows/classic/install-trend.md)
* [Instalación y configuración de Endpoint Protection en una máquina virtual de Azure](../virtual-machines/windows/classic/install-symantec.md)
* [New Antimalware Options for Protecting Azure Virtual Machines – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-factor Authentication (MFA) es un método de autenticación que requiere el uso de más de un método de verificación y agrega un segundo nivel de seguridad crítico a las transacciones e inicios de sesión del usuario. MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona autenticación sólida mediante una gran variedad de opciones de verificación, como llamadas telefónicas, mensajes de texto, notificaciones de aplicaciones móviles, códigos de verificación y tokens OATH de terceros.

Más información:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [¿Qué es Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* [Cómo funciona Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
Microsoft Azure ExpressRoute le permite ampliar sus redes de local en la nube de Microsoft a través de una conexión privada y dedicada que facilita un proveedor de conectividad. Con ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Microsoft Azure, Office 365 y CRM Online. La conectividad puede ser desde una red de conectividad universal (IP VPN), una red Ethernet de punto a punto, o una conexión cruzada virtual a través de un proveedor de conectividad en una instalación de ubicación compartida. Las conexiones ExpressRoute no pasan por la red pública de Internet. Esto permite a las conexiones de ExpressRoute ofrecer más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones normales a través de Internet.

Más información:

* [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Puertas de enlace de red virtual
Las puertas de enlace de VPN, también llamadas puertas de enlace de red virtual de Azure, se usan para enviar tráfico de red entre las redes virtuales y las ubicaciones locales. También se usan para enviar el tráfico entre varias redes virtuales dentro de Azure (red virtual a red virtual).  Las puertas de enlace de VPN proporcionan conectividad local segura entre Azure y su infraestructura.

Más información:

* [Información acerca de las puertas de enlace de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure Network Security Overview](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
En ocasiones, los usuarios necesitan llevar a cabo operaciones con privilegios en recursos de Azure u otras aplicaciones para SaaS. A menudo esto significa que las organizaciones tienen que concederles acceso con privilegios permanente en Azure Active Directory (Azure AD). Este hecho representa un riesgo creciente para la seguridad de los recursos hospedados en la nube, ya que las organizaciones no pueden supervisar todo lo que deberían lo que hacen esos usuarios con su acceso con privilegios.
Además, si se pone en peligro la seguridad de una cuenta de usuario que tiene acceso con privilegios, esta situación podría afectar a la seguridad general en la nube. Privileged Identity Management de Azure AD ayuda a resolver este riesgo al reducir el tiempo de exposición de los privilegios y aumentar la visibilidad sobre el uso.  

Privileged Identity Management introduce el concepto de administrador temporal de un rol o acceso de administrador "justo a tiempo", para referirse al usuario que debe realizar un proceso de activación para ese rol asignado. El proceso de activación cambia la asignación del usuario a un rol en Azure AD de inactiva a activa durante un periodo de tiempo especificado, como 8 horas.

Más información:

* [Administración de identidades con privilegios de Azure AD](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Introducción a Privileged Identity Management de Azure AD](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Protección de identidad
Azure Active Directory (AD) Identity Protection proporciona una vista consolidada de actividades de inicio de sesión sospechosas y posibles vulnerabilidades que ayudan a proteger su negocio. Identity Protection detecta las actividades sospechosas de usuarios e identidades con privilegios (administrador) según determinadas señales, como ataques por fuerza bruta, pérdida de credenciales, inicios de sesión de ubicaciones desconocidas y dispositivos infectados.

Al proporcionar notificaciones y soluciones recomendadas, Identity Protection ayuda a mitigar los riesgos en tiempo real. Asimismo, calcula la gravedad del riesgo para el usuario y permite configurar directivas basadas en el riesgo que ayudan a proteger automáticamente el acceso a la aplicación frente a futuras amenazas.

Más información:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Presentación de Azure AD e Identity: versión preliminar de Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de seguridad
Azure Security Center ayuda a evitar, detectar y responder a amenazas, al tiempo que proporciona más visibilidad y control de la seguridad de los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

Security Center ayuda a optimizar y supervisar la seguridad de los recursos de Azure de la manera siguiente:

* Le permite definir directivas para los recursos de su suscripción de Azure de acuerdo con las necesidades de seguridad de su empresa y el tipo de aplicaciones o confidencialidad de los datos de cada suscripción.
* Supervisa el estado de las máquinas virtuales, las redes y las aplicaciones de Azure.
* Proporciona una lista de alertas de seguridad, entre las que se incluyen alertas de soluciones de socios integradas, junto con la información que necesita investigar rápidamente, así como recomendaciones sobre cómo hacer frente a un ataque.

Más información:

* [Introducción al Centro de seguridad de Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
