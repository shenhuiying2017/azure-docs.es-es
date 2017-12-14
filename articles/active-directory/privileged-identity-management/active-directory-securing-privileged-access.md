---
title: "Protección del acceso con privilegios en Azure AD | Microsoft Docs"
description: Un tema que explica los enfoques para proteger el acceso con privilegios en Azure, Azure Active Directory y Microsoft Online Services.
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: e1bc0f27b14beef91b4deb68dc625d75195445fb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="securing-privileged-access-in-azure-ad"></a>Protección del acceso con privilegios en Azure AD
La protección del acceso con privilegios es un primer paso esencial para ayudar a proteger los activos empresariales en las organizaciones modernas. Las cuentas con privilegios son cuentas que administran los sistemas de TI. Estas cuentas son el objetivo de los ciberatacantes ya que les proporcionan acceso a los datos y los sistemas de una organización. Para proteger el acceso con privilegios, debe aislar las cuentas y los sistemas del riesgo de exposición a usuarios malintencionados.

Cada vez más usuarios disfrutan del acceso con privilegios mediante los servicios en la nube. Por ejemplo, los administradores globales de Office 365, los administradores de suscripciones de Azure y los usuarios que tienen acceso administrativo en máquinas virtuales o en aplicaciones de SaaS.

Microsoft recomienda seguir este plan a la hora de [proteger el acceso con privilegios](https://technet.microsoft.com/library/mt631194.aspx).

Para los clientes que usan Azure Active Directory, Office 365 u otros servicios y aplicaciones de Microsoft, estos principios se aplican tanto si las cuentas de usuario se administran y autentican en Active Directory como en Azure Active Directory. En las secciones siguientes se proporciona más información sobre las características de Azure AD que respaldan la protección del acceso con privilegios.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
Para aumentar la seguridad de la autenticación de administrador, debe exigir la verificación en dos pasos antes de conceder privilegios. La verificación en dos pasos es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios.

Azure Multi-Factor Authentication (MFA) es una solución de verificación en dos pasos, que ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece autenticación segura mediante una gama de opciones de verificación sencillas, que incluyen:

- llamadas de teléfono
- mensajes de texto
- notificaciones de aplicación móvil
- códigos de verificación de aplicación móvil
- tokens OATH de terceros

Para obtener información general sobre cómo funciona Azure Multi-Factor Authentication, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

Para más información, consulte [MFA for Office 365 and MFA for Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)(MFA para Office 365 y MFA para Azure).

## <a name="time-bound-privileges"></a>Privilegios de tiempo limitado
Algunas organizaciones pueden encontrarse con que tienen demasiados usuarios en roles con privilegios elevados. Un usuario podría haberse agregado al rol de una determinada actividad, por ejemplo, registrarse en un servicio, pero luego no usar esos privilegios con frecuencia.

Para reducir el tiempo de exposición de los privilegios y aumentar la visibilidad sobre su uso, puede limitar a los usuarios a tomar los privilegios "justo a tiempo" (JIT) o asignar estos roles para una duración reducida con la confianza de que los privilegios se revocarán automáticamente. Para Azure Active Directory, Azure Resources (versión preliminar) y Microsoft Online Services, puede usar [Privileged Identity Management (PIM) de Azure AD](http://aka.ms/AzurePIM).

![Panel de PIM][2]

## <a name="attack-detection"></a>Detección de ataques
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) proporciona una vista consolidada en eventos de riesgo y posibles puntos vulnerables que afectan a las identidades de su organización. En función de los eventos de riesgo, Identity Protection calcula un nivel de riesgo del usuario para cada usuario, lo que le permite configurar las directivas basadas en riesgos para proteger automáticamente las identidades de su organización. Estas directivas, junto con otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden bloquear automáticamente al usuario u ofrecer sugerencias que incluyen restablecimientos de contraseña y cumplimiento de la autenticación multifactor.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Acceso condicional
Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que elige al autenticar a un usuario, antes de permitirle el acceso a una aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación.

## <a name="related-articles"></a>Artículos relacionados
* Habilitación de [Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Habilitación de [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Habilitación de [Azure AD Identity Protection](../active-directory-identityprotection.md)
* Habilitación de los [controles de acceso condicional](../active-directory-conditional-access-azure-portal.md)

Para más información sobre la creación de un plan de seguridad completo, consulte la sección sobre responsabilidades del cliente y plan de acción en el documento [Microsoft Cloud Security for Enterprise Architects](http://aka.ms/securecustomer) (Seguridad de Microsoft Cloud para los arquitectos de empresa). Para más información sobre cómo interactuar con los servicios de Microsoft para que le ayuden con alguno de estos temas, póngase en contacto con su representante de Microsoft o visite nuestra [página de soluciones de ciberseguridad](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
