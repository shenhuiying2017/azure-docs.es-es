---
title: "Uso compartido externo de Office 365 y colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "referencia de asignación de notificaciones para la colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Uso compartido externo de Office 365 y colaboración B2B de Azure Active Directory

Técnicamente, es lo mismo utilizar el uso compartido externo de Office&365; (OneDrive, SharePoint Online, grupos unificados, etc.) y la colaboración B2B de Azure Active Directory (Azure AD). Todos los usos compartidos externos (excepto OneDrive o SharePoint Online), incluidos los **invitados de grupos unificados**, ya están usando la API de invitación de colaboración B2B de Azure AD para el uso compartido.

OneDrive y SharePoint Online (ODSP) tienen un administrador de invitaciones independiente porque su compatibilidad con el uso compartido externo empezó antes de que se ofreciera de forma inherente como parte del tejido de Azure AD. Con el tiempo, el uso compartido externo de ODSP ha acumulado varias características y muchos millones usuarios usan el patrón de uso compartido integrado del producto. Estamos trabajando con ODSP para incorporarlo a las API de invitación de Azure AD B2B (consúltese en esta documentación), con el fin de unificar todas las ventajas de extremo a extremo y aportar todas las innovaciones en las experiencias que crea Azure AD. Mientras tanto, existen algunas diferencias sutiles entre el funcionamiento del uso compartido externo de ODSP y cómo funciona Azure AD B2B:

1. ODSP agrega el usuario al directorio después de que el usuario haya canjeado su invitación. Por lo tanto, hasta que el usuario no haya canjeado, no verá lo verá en el Portal de Azure AD. Si un usuario recibe una invitación de otro sitio, se genera una nueva invitación. Sin embargo, cuando se utiliza la colaboración B2B, agregamos al usuario inmediatamente en la invitación para que aparezca en todas partes.

2. La experiencia de canje de ODSP es diferente a la de la colaboración B2B.

3. Sin embargo, sí que se parecen cuando el usuario canjea la invitación.

4. Los usuarios invitados de la colaboración B2B se pueden seleccionar en los cuadros diálogos del uso compartido de ODSP. Los usuarios invitados de ODSP aparecen también en Azure AD una vez hayan canjeado su invitación.

5. Para utilizar el uso compartido externo de ODSP junto con la colaboración B2B de una forma controlada por los administradores, establezca la configuración del uso compartido externo de ODSP en **Only allow sharing with external users already in the directory** (Solo permitir uso compartido con usuarios externos que ya se encuentren en el directorio). Los usuarios pueden acceder a los sitios compartidos externamente y elegir entre colaboradores externos que ha agregado el administrador. El administrador puede agregar los colaboradores externos a través de la API de invitación de colaboración B2B.

![La configuración del uso compartido externo de ODSP](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


