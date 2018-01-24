---
title: "Colaboración B2B de Azure Active Directory para organizaciones híbridas | Microsoft Docs"
description: "Proporcione a los asociados acceso tanto a los recursos locales como de nube con la colaboración B2B de Azure AD."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Colaboración B2B de Azure Active Directory para organizaciones híbridas

En una organización híbrida, donde tiene tanto recursos locales como en la nube, puede que quiera conceder a los asociados externos acceso a estos recursos. Para acceder a los recursos locales, puede administrar las cuentas de los asociados localmente en su entorno local de Active Directory. Los asociados inician sesión con las credenciales de sus cuentas de asociado para acceder a los recursos de la organización. Para conceder a los asociados acceso los recurso de nube mediante estas mismas credenciales, ahora puede usar Azure Active Directory (Azure AD) Connect para sincronizar las cuentas de asociado con la nube como usuarios B2B de Azure AD (es decir, usuarios con UserType = Invitado).

## <a name="identify-unique-attributes-for-usertype"></a>Identificación de atributos únicos para UserType

Antes de permitir la sincronización del atributo UserType, primero debe decidir cómo obtener el atributo UserType de Active Directory local. En otras palabras, ¿qué parámetros del entorno local son únicos para los colaboradores externos? Determine un parámetro que distinga estos colaboradores externos de los miembros de su propia organización.

Los dos enfoques comunes para ello son los siguientes:

- Designe un atributo de Active Directory local sin usar (por ejemplo, Atributodeextensión1) que se usará como el atributo de origen. 
- Como alternativa, obtenga el valor del atributo UserType de otras propiedades. Por ejemplo, querrá sincronizar todos los usuarios como Invitado si su atributo UserPrincipalName de Active Directory local finaliza con el dominio *@partners.fabrikam123.org*.
 
Para conocer los requisitos detallados de atributos, consulte [Habilitar la sincronización de UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configuración de Azure AD Connect para sincronizar los usuarios con la nube

Después de identificar el atributo único, puede configurar Azure AD Connect para sincronizar estos usuarios con la nube como usuarios de Azure AD B2B (es decir, usuarios con UserType = Invitado). Desde un punto de vista de autorización, estos usuarios no se distinguen de los usuarios B2B creados mediante el proceso de invitación a la colaboración B2B de Azure AD.

Para obtener instrucciones de implementación, consulte [Habilitar la sincronización de UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>pasos siguientes

- Para información general sobre la colaboración B2B de Azure AD, consulte [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Para información general sobre Azure AD Connect, consulte [Integración de los directorios locales con Azure Active Directory](connect/active-directory-aadconnect.md).

