---
title: 'Acceso condicional a aplicaciones locales: Azure AD | Microsoft Docs'
description: "Explica cómo configurar el acceso condicional para que se tenga acceso remoto con el proxy de la aplicación de Azure AD a las aplicaciones que se publiquen."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 463946256f9e335fa6d98fc904835e5c3dc2725e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>Trabajo con acceso condicional en el proxy de la aplicación de Azure AD

>[!NOTE]
>Este artículo se aplica a Portal de Azure clásico, que se va a retirar. Se recomienda usar [Azure Portal](https://portal.azure.com). En Azure Portal, las aplicaciones del proxy de la aplicación tienen las mismas características de acceso condicional que otras aplicaciones SaaS. Para más información sobre el acceso condicional, consulte [Introducción al acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Ahora puede configurar reglas de acceso para conceder acceso condicional a las aplicaciones publicadas con el proxy de aplicación. Esto le permite:

* Exigir la autenticación multifactor por aplicación
* Exigir la autenticación multifactor solo cuando los usuarios no están en el trabajo
* Impedir que los usuarios tengan acceso a la aplicación cuando no se encuentran en el trabajo

Estas reglas pueden aplicarse a todos los usuarios y grupos o solo a usuarios y grupos específicos. La regla se aplica de forma predeterminada a todos los usuarios que tienen acceso a la aplicación. Sin embargo, también puede restringirse a usuarios que sean miembros de los grupos de seguridad especificados.  

Cuando un usuario tiene acceso a una aplicación federada que usa OAuth 2.0, OpenID Connect, SAML o WS-Federation, se evalúan las reglas de acceso. Además, las reglas de acceso se evalúan con OAuth 2.0 y OpenID Connect cuando se usa un token de actualización para adquirir un token de acceso.

## <a name="conditional-access-prerequisites"></a>Requisitos previos de acceso condicional
* Suscripción a Azure Active Directory Premium
* Un inquilino de Azure Active Directory administrado o federado
* Los inquilinos federados requieren Multi-Factor Authentication (MFA)  
    ![Configurar reglas de acceso - exigir Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configuración de Multi-Factor Authentication por aplicación
1. Inicie sesión como administrador en el Portal de Azure clásico.
2. Vaya a Active Directory y seleccione el directorio en el que desea habilitar el proxy de la aplicación.
3. Haga clic en **Aplicaciones** y desplácese hacia abajo hasta la sección **Reglas de acceso**. La sección Reglas de acceso solo aparece para aplicaciones publicadas con el proxy de la aplicación que usa autenticación federada.
4. Habilite la regla seleccionando **Habilitar reglas de acceso** como **Activada**.
5. Especifique los usuarios y grupos a los que se aplican las reglas. Use el botón **Agregar grupo** para seleccionar uno o más grupos a los que se aplica la regla de acceso. Este cuadro de diálogo también sirve para quitar grupos seleccionados.  Cuando las reglas se seleccionan para que se apliquen a grupos, las reglas de acceso solo se aplican a los usuarios que pertenezcan a uno de los grupos de seguridad especificados.  

   * Para excluir explícitamente grupos de seguridad de la regla, active **Excepto** y especifique uno o más grupos. No se requiere a los usuarios que sean miembros de un grupo de la lista Excepto que realicen autenticación multifactor.  
   * Si un usuario se configuró con la característica de autenticación multifactor por usuario, esta configuración tiene prioridad sobre las reglas de autenticación multifactor de la aplicación. Esto significa que un usuario configurado con Multi-Factor Authentication por usuario tiene que realizar autenticación multifactor aunque esté exento de las reglas de autenticación multifactor de la aplicación. Obtenga más información sobre [Multi-Factor Authentication y la configuración por usuario](../multi-factor-authentication/multi-factor-authentication.md).
6. Seleccione la regla de acceso que quiere establecer:

   * **Requerir autenticación multifactor**: los usuarios a los que se apliquen reglas de acceso tienen que llevar a cabo autenticación multifactor para tener acceso a la aplicación a la que se aplica la regla.
   * **Requerir autenticación multifactor fuera del trabajo**: los usuarios que intenten tener acceso a la aplicación desde una dirección IP de confianza no tendrán que realizar autenticación multifactor. Los intervalos de direcciones IP de confianza pueden configurarse en la página de configuración de Multi-Factor Authentication.
   * **Bloquear acceso cuando no está en trabajo**: los usuarios que intentan obtener acceso a la aplicación desde fuera de su red corporativa no podrán tener acceso a la aplicación.

## <a name="configuring-mfa-for-federation-services"></a>Configuración de MFA para servicios de federación
En el caso de los inquilinos federados, puede que Azure Active Directory o el servidor local de AD FS ejecute Multi-Factor Authentication (MFA). De forma predeterminada, MFA se produce en cualquier página hospedada por Azure Active Directory. Para configurar MFA local, ejecute Windows PowerShell y use la propiedad –SupportsMFA para definir el módulo de Azure AD.

En el ejemplo siguiente se muestra cómo habilitar MFA local mediante el cmdlet [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) en el inquilino contoso.com: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Además de establecer esta marca, la instancia de AD FS de inquilinos federados debe configurarse para llevar a cabo Multi-Factor Authentication. Siga las instrucciones para la [implementación local de Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Consulte también
* [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
* [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)

Para ver las últimas noticias y actualizaciones, consulte el [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)
