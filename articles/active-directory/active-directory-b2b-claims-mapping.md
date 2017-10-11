---
title: "Asignación de notificaciones de usuario de colaboración B2B de Azure Active Directory | Microsoft Docs"
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
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Asignación de notificaciones de usuario de colaboración B2B de Azure Active Directory

Azure Active Directory (Azure AD) admite la personalización de las notificaciones emitidas en el token SAML para los usuarios de colaboración B2B. Cuando un usuario se autentique en la aplicación, Azure AD emitirá un token SAML a la aplicación que contiene información (o notificaciones) sobre el usuario que lo identifica de forma única. De forma predeterminada, dicha información incluye el nombre de usuario, la dirección de correo electrónico, el nombre y los apellidos del usuario. Las notificaciones enviadas en el token SAML a la aplicación se pueden ver o editar en la pestaña Atributos.

Hay dos razones posibles por qué tendría que editar las notificaciones emitidas en el token SAML.

1. La aplicación se ha creado para requerir un conjunto diferente de URI o valores de notificación.

2. La aplicación requiere que la notificación NameIdentifier tenga un valor que no sea el del nombre principal de usuario almacenado en Azure Active Directory.

  ![Visualización de notificaciones en el token SAML](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Para obtener información sobre cómo agregar y editar notificaciones, consulte este artículo sobre la personalización de notificaciones: [Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory](develop/active-directory-saml-claims-customization.md). Para los usuarios de colaboración B2B, por motivos de seguridad, se evita realizar la asignación de NameID y UPD entre inquilinos.


## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)
