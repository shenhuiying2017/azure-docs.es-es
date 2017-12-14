---
title: Uso compartido de cuentas mediante Azure AD | Microsoft Docs
description: "Describe cómo Azure Active Directory permite a las organizaciones compartir de forma segura las cuentas de aplicaciones locales y los servicios en la nube de consumidor."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: e2d77104-d978-46a3-bfea-03ffdf3b61e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3b6a83d91ec5d8466669655d6c3bd7ae7b42dd2f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="sharing-accounts-with-azure-ad"></a>Uso compartido de cuentas con Azure AD
## <a name="overview"></a>Información general
A veces, las organizaciones necesitan usar un nombre de usuario y una contraseña únicos para varias personas. Esto sucede normalmente en dos casos:

* Al obtener acceso a aplicaciones que requieren un inicio de sesión único y una contraseña para cada usuario, ya sean aplicaciones locales o servicios en la nube de consumidor (por ejemplo, cuentas de medios sociales corporativos).
* Al crear entornos multiusuario. En este caso puede tener una sola cuenta local que tenga privilegios elevados y que se use para realizar actividades básicas de configuración, administración y recuperación. Por ejemplo, puede tener la cuenta local de "administrador global" de Office 365 o la cuenta raíz en Salesforce.

Tradicionalmente, estas cuentas se comparten al distribuir las credenciales (nombre de usuario y contraseña) a las personas adecuadas o almacenándolas en una ubicación compartida donde varios agentes de confianza pueden tener acceso a ellas.

El modelo tradicional de uso compartido tiene varias desventajas:

* Habilitar el acceso a las nuevas aplicaciones requiere distribuir las credenciales a todos los usuarios que necesiten acceso.
* Cada aplicación compartida puede requerir su propio conjunto de credenciales compartidas, por lo que los usuarios tienen que recordar varios conjuntos de credenciales. Cuando los usuarios tienen que recordar muchas credenciales, aumenta el riesgo de que recurran a prácticas peligrosas. (por ejemplo, anotar las contraseñas).
* No se puede determinar quién tiene acceso a una aplicación.
* No se puede determinar quién *accedió* a una aplicación.
* Si tiene que quitar el acceso a una aplicación, debe actualizar las credenciales y volver a distribuirlas a todos los usuarios que necesiten acceder a esa aplicación.

## <a name="azure-active-directory-account-sharing"></a>Uso compartido de cuentas de Azure Active Directory
Azure AD proporciona un enfoque nuevo para el uso de cuentas compartidas que elimina estos inconvenientes.

El administrador de Azure AD configura las aplicaciones a las que un usuario puede tener acceso mediante el panel de acceso y la selección del tipo de inicio de sesión único que mejor se adapta a esa aplicación. Uno de estos tipos, el *inicio de sesión único con contraseña*, permite a Azure AD actuar como una especie de "intermediario" durante el proceso de inicio de sesión de esa aplicación.

Los usuarios inician sesión una sola vez con sus cuentas profesionales. Estas cuentas suelen ser las que se usan habitualmente para obtener acceso al correo electrónico o al escritorio. Pueden detectar y acceder solo a aquellas aplicaciones que tienen asignadas. Con cuentas compartidas, esta lista de aplicaciones puede incluir cualquier número de credenciales compartidas. El usuario final no necesita recordar ni anotar las distintas cuentas que puede que use.

Las cuentas compartidas no solo aumentan la supervisión y mejoran la facilidad de uso, también optimizan la seguridad. Los usuarios con permisos para usar las credenciales no ven la contraseña compartida, sino que obtienen permiso para usar la contraseña como parte de un flujo de autenticación orquestado. Además, algunas aplicaciones de SSO con contraseña le ofrecen la opción de usar Azure AD para sustituir (actualizar) periódicamente las contraseñas. El sistema usa contraseñas largas y complejas, lo que aumenta la seguridad de la cuenta. Gracias a ello, el administrador puede conceder o revocar fácilmente el acceso a una aplicación y saber quién tiene acceso a la cuenta y quién ha accedido a ella previamente.

Azure AD admite las cuentas compartidas de cualquier usuario de Enterprise Mobility Suite (EMS), con licencia Premium o Básica, en todos los tipos de aplicaciones de inicio de sesión único con contraseña. Puede compartir las cuentas de las miles de aplicaciones previamente integradas en la galería de aplicaciones y puede agregar su propia aplicación de autenticación mediante contraseña con [aplicaciones de inicio de sesión único personalizadas](active-directory-enterprise-apps-manage-sso.md).

Entre las características de Azure AD que permiten el uso compartido de las cuentas se incluyen las siguientes:

* [Inicio de sesión único con contraseña](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Agente de inicio de sesión único con contraseña
* [Asignación de grupos](active-directory-accessmanagement-self-service-group-management.md)
* Aplicaciones de contraseñas personalizadas
* [Panel/informes de uso de aplicaciones](active-directory-passwords-get-insights.md)
* Portales de acceso para usuarios finales
* [Proxy de aplicaciones](active-directory-application-proxy-get-started.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Uso compartido de una cuenta
Para usar Azure AD para compartir una cuenta, debe hacer lo siguiente:

* Agregar una aplicación de la [galería de aplicaciones](https://azure.microsoft.com/marketplace/active-directory/) o una [aplicación personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
* Configurar la aplicación para el inicio de sesión único (SSO) con contraseña
* Usar la [asignación basada en grupos](active-directory-accessmanagement-group-saasapps.md) y seleccionar la opción para escribir una credencial compartida.
* Opcional: en algunas aplicaciones, como Facebook, Twitter o LinkedIn, puede habilitar la opción para [reasignar la contraseña automatizada de Azure AD](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

También puede hacer que su cuenta compartida sea más segura con Multi-Factor Authentication (MFA) (más información acerca de la [protección de aplicaciones con Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) y puede delegar la capacidad para administrar quién tiene acceso a la aplicación mediante la administración de grupos de [autoservicio de Azure AD](active-directory-accessmanagement-self-service-group-management.md).

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Protección de aplicaciones con acceso condicional](active-directory-conditional-access-azure-portal.md)
* [Administración de grupos de autoservicio/SSAA](active-directory-accessmanagement-self-service-group-management.md)

