---
title: "Preguntas más frecuentes sobre el acceso condicional de Azure Active Directory | Microsoft Docs"
description: "Preguntas más frecuentes sobre el acceso condicional  "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bca8a683f8a99496d7847a95e7e25ce8b6a777b2


---
# <a name="azure-active-directory-conditional-access-faq"></a>Preguntas más frecuentes acerca del acceso condicional de Azure Active Directory
## <a name="which-applications-work-with-conditional-access-policies"></a>¿Qué aplicaciones funcionan con directivas de acceso condicional?
**R:** consulte el tema [Compatibilidad con el acceso condicional de las aplicaciones](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>¿Se aplican directivas de acceso condicional a usuarios de colaboración B2B o invitados?
**R:** se exigen las directivas a usuarios de la colaboración B2B. Sin embargo, en algunos casos, puede que un usuario no sea capaz de cumplir los requisitos de la directiva si, por ejemplo, una organización no admite la autenticación multifactor. 

Actualmente, la directiva no se exige a los usuarios invitados de SharePoint. La relación de invitado se mantiene dentro de SharePoint. Las cuentas de usuarios invitados no están sujetas a las directivas de acceso del servidor de autenticación. El acceso de invitado se puede administrar en SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>¿Se aplica la directiva de SharePoint Online también a OneDrive para la Empresa?
**R:** Sí.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>¿Por qué no se puede establecer una directiva en las aplicaciones cliente, como Word o Outlook?
**R:** una directiva de acceso condicional establece los requisitos de acceso a un servicio y se exige cuando se realiza la autenticación en dicho servicio. La directiva no se establece directamente en una aplicación cliente, sino que se aplica cuando llama a un servicio. Por ejemplo, una directiva establecida en SharePoint se aplicará a los clientes que llamen a SharePoint, mientras que una directiva establecida en Exchange se aplicará a Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>¿Se aplica una directiva de acceso condicional a las cuentas de servicio?
**R:** las directivas de acceso condicional se aplican a todas las cuentas de servicio. Aquí se incluyen las cuentas de usuario que se utilizan como cuentas de servicio. En muchos casos, una cuenta de servicio que se ejecuta en modo desatendido no puede satisfacer una directiva. Por ejemplo, esto sucede cuando se requiere MFA. En estos casos, las cuentas de los servicios se pueden excluir de una directiva, para lo que se usa la configuración de administración de directivas de acceso condicional. Aquí encontrará más información acerca de cómo aplicar una directiva a los usuarios.




<!--HONumber=Nov16_HO3-->


