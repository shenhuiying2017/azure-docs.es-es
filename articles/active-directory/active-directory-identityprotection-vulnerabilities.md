---
title: Vulnerabilidades detectadas por Azure Active Directory Identity Protection | Microsoft Docs
description: "Información general de las vulnerabilidades detectadas por Azure Active Directory Identity Protection."
services: active-directory
keywords: "azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 364873ff54099a6123e40b12e819d1745751f285
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas por Azure Active Directory Identity Protection
Los puntos vulnerables son puntos débiles de su entorno que pueden ser aprovechados por un atacante. Se recomienda solucionar estas vulnerabilidades para mejorar la posición de seguridad de su organización y evitar que los atacantes se aprovechen de ellas.


![vulnerabilidades](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilidades")



En las siguientes secciones se ofrece información general de las vulnerabilidades de las que informa Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registro de la autenticación multifactor no configurado
Este punto vulnerable ayuda a controlar la implementación de Azure Multi-Factor Authentication en su organización. 

Azure Multi-Factor Authentication proporciona una segunda capa de seguridad a la autenticación de usuario. Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones sencillas, como llamadas telefónicas, mensajes de texto o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran.

Se recomienda pedir Azure Multi-Factor Authentication para los inicios de sesión de usuario. La autenticación multifactor desempeña un papel clave en las directivas de acceso condicional basadas en riesgos disponibles mediante Identity Protection.

Para obtener más detalles, consulte [Qué es Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Aplicaciones en la nube no administradas
Este punto vulnerable ayuda a identificar aplicaciones en la nube no administradas en su organización.

En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones en la nube que usan los miembros de su organización para realizar su trabajo. Es fácil ver por qué a los administradores les podría preocupar el acceso no autorizado a datos corporativos, la posible pérdida de datos y otros riesgos de seguridad. 

Le recomendamos que su organización implemente Cloud App Discovery para detectar aplicaciones en la nube no administradas, así como administrar estas aplicaciones con Azure Active Directory.

Para obtener más detalles, consulte [Búsqueda de aplicaciones de nube no administradas con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de seguridad de administración de identidades con privilegios
Este punto vulnerable ayuda a detectar y resolver alertas acerca de las identidades con privilegios en su organización.  

Para que los usuarios puedan realizar operaciones con privilegios, las organizaciones tienen que proporcionar a sus usuarios acceso con privilegios temporal o permanente en Azure AD, recursos de Azure u Office 365, y otras aplicaciones de SaaS. Cada uno de estos usuarios con privilegios aumenta la superficie de ataque de su organización. Este punto vulnerable ayuda a identificar a los usuarios con acceso con privilegios innecesarios y realizar la acción apropiada para reducir o eliminar el riesgo que suponen. 

Recomendamos que la organización utilice Privileged Identity Management de Azure AD para administrar, controlar y supervisar las identidades con privilegios y su acceso a recursos en Azure AD y en otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

Para obtener más detalles, consulte [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="see-also"></a>Otras referencias
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

