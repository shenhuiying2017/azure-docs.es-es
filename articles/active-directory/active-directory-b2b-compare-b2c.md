---

title: "Comparación de la colaboración B2B y B2C de Azure Active Directory | Microsoft Docs"
description: "¿Cuál es la diferencia entre la colaboración B2B de Azure Active Directory y Azure AD B2C?"
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
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 72e7e57185d882a5f3243e2705aed54ee8347798
ms.openlocfilehash: 5d157a936317bbf66cfc3634d4d08ea3483d137b


---

# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparación de la colaboración B2B y B2C de Azure Active Directory

La colaboración B2B de Azure Active Directory (Azure AD) y Azure AD B2C permiten trabajar con usuarios externos en Azure AD. Pero, ¿cómo se comparan?


Funcionalidades de la colaboración B2B |     Oferta independiente de Azure AD B2C
-------- | --------
Destinatarios: organizaciones que desean proporcionar acceso a los datos corporativos, los recursos y las aplicaciones a los usuarios de cualquier otra organización, mediante la utilización de cualquier identidad que elijan. | Finalidad: aplicaciones web y móviles orientadas a los clientes, ya sean clientes individuales, ciudadanos o clientes de instituciones u organizaciones (que no sean sus empleados o colaboradores externos), que usan cualquier identidad de su elección.
Identidades compatibles: empleados con cuentas profesionales o educativas, asociados con cuentas profesionales o educativas o cualquier dirección de correo electrónico. Pronto se admitirá la federación directa.  | Identidades compatibles: usuarios consumidores con cuentas de aplicación local (cualquier dirección de correo electrónico o nombre de usuario) o cualquier identidad social compatible con federación directa.
En qué directorio se encuentran los usuarios asociados: los usuarios asociados de organizaciones externas se administran en el mismo directorio que los empleados, pero con una anotación especial. Estos usuarios externos pueden administrarse del mismo modo que los empleados, pueden agregarse a los mismos grupos, etc.  | En qué directorio se encuentran las entidades de usuarios consumidores: en el directorio de aplicaciones. Se administra de manera independiente del directorio de asociados y de empleados de la organización (si existe).
Se admite el inicio de sesión único (SSO) en todas las aplicaciones conectadas a Azure AD (incluidas las aplicaciones locales), como, por ejemplo, Office 365, y también se admiten las aplicaciones SaaS de Microsoft y de terceros (como Salesforce, Box, Workday, etc.).  |  Se admite el inicio de sesión único para aplicaciones propiedad de los clientes dentro de los inquilinos de Azure AD B2C. No se admite el inicio de sesión único en Office 365 ni en otras aplicaciones SaaS de Microsoft y de terceros.
Ciclo de vida de socio comercial: administrado por la organización anfitriona o invitadora.  | Ciclo de vida de cliente: mediante autoservicio o administrado por la aplicación.
Directiva de seguridad y cumplimiento: administradas por la organización anfitriona o invitadora.  | Directiva de seguridad y cumplimiento: administradas por la aplicación.
Personalización de marca: se utiliza la marca de la organización anfitriona o invitadora.  |    Personalización de marca: administrada por la aplicación. Normalmente suele tratarse de productos con marca, y la organización pasa a un segundo plano.
Más información: [entrada de blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentación](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Más información: [página de producto](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [documentación](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)
* [Obtención de soporte técnico para colaboración B2B](active-directory-b2b-support.md)



<!--HONumber=Feb17_HO2-->


