---
title: Exigencia de Multi-Factor Authentication | Microsoft Docs
description: "Obtenga información sobre cómo requerir Multi-Factor Authentication (MFA) para identidades con privilegios con la extensión de Privileged Identity Management de Azure Active Directory."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b778774fa23be8219db3f716d79bac324a7de9d3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Exigencia de MFA en Privileged Identity Management de Azure AD
Se recomienda exigir Multi-Factor Authentication (MFA) para todos los administradores. De esta forma, se reduce el riesgo de ataques debidos a que una contraseña se ha puesto en peligro.

Puede exigir que los usuarios completen un desafío de MFA cuando inician sesión. La entrada de blog [MFA for Office 365 and MFA for Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) (MFA para Office 365 y MFA para Azure) compara lo que se incluye en las suscripciones de Office y Azure con las características de la oferta de Multi-Factor Authentication de Microsoft Azure.

También puede exigir que los usuarios realicen un desafío de MFA cuando activan un rol en PIM de Azure AD. De este modo, si el usuario no realizó un desafío de MFA cuando inició sesión, se le pedirá que lo haga en PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exigencia de MFA en Privileged Identity Management de Azure AD
Al administrar identidades en PIM como administrador de roles con privilegios, puede ver las alertas que recomienda MFA para las cuentas con privilegios. Haga clic en la alerta de seguridad en el panel de PIM y se abrirá una nueva hoja con una lista de las cuentas de administrador que deben exigir MFA.  Para exigir MFA, seleccione varios roles y haga clic en el botón **Corregir**; también puede hacer clic en el botón de puntos suspensivos situado junto a cada rol y luego hacer clic en el botón **Corregir**.

> [!IMPORTANT]
> Ahora mismo, Azure MFA solo funciona con cuentas profesionales o educativas, no con cuentas de Microsoft (por lo habitual una cuenta personal que se usa para iniciar sesión en servicios de Microsoft, como Skype, Xbox, Outlook.com, etc.). Por este motivo, todo aquel que use una cuenta de Microsoft no puede ser un administrador apto ya que no podrá usar MFA para activar su rol. Si estos usuarios tienen que seguir administrando cargas de trabajo con una cuenta de Microsoft, conviértalos en administradores permanentes por el momento.
> 
> 

Además, puede cambiar el requisito de MFA para un rol específico haciendo clic en él en la sección Roles del panel de PIM. Luego, haga clic en **Configuración** en la hoja de roles y seleccione **Habilitar** en Multi-Factor Authentication.

## <a name="how-azure-ad-pim-validates-mfa"></a>Cómo PIM de Azure AD valida MFA
Hay dos opciones para validar MFA cuando un usuario activa un rol.

La opción más sencilla es depender de Azure MFA en el caso de los usuarios que activan un rol con privilegios. Para ello, primero compruebe que dichos usuarios cuentan con una licencia, si es necesario, y que se han registrado en Azure MFA. Más información en [Introducción a Azure Multi-Factor Authentication en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Se recomienda, aunque no es necesario, configurar Azure AD para imponer MFA en estos usuarios cuando inicien sesión. Esto se debe a que PIM de Azure AD realizará las comprobaciones de MFA.

Si los usuarios se autentican en el entorno local, también puede hacer que el proveedor de identidades se encargue de MFA. Por ejemplo, si ha configurado Servicios de federación de AD para exigir la autenticación basada en tarjeta inteligente antes de acceder a Azure AD, el artículo [Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) incluye instrucciones para configurar AD FS para enviar notificaciones a Azure AD. Cuando un usuario intenta activar un rol, PIM de Azure AD aceptará que ya se ha validado MFA para el usuario cuando reciba las notificaciones adecuadas.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

