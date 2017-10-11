---
title: "¿Qué es la colaboración B2B de Azure Active Directory? | Microsoft Docs"
description: "La colaboración B2B de Azure Active Directory posibilita las relaciones entre empresas al permitir que los asociados comerciales tengan acceso de forma selectiva a las aplicaciones corporativas."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>¿Qué es la colaboración B2B de Azure AD?

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Las funcionalidades de colaboración de Azure AD negocio a negocio (B2B) permiten que cualquier organización con Azure AD funcione de forma segura con usuarios de cualquier otra organización, pequeña o grande; con o sin Azure AD; de hecho, con o sin una organización de TI. 

Las organizaciones que usan Azure AD pueden proporcionar acceso a documentos, recursos y aplicaciones a sus asociados, mientras mantienen un control total sobre sus propios datos corporativos. Los desarrolladores pueden usar las API de negocio a negocio de Azure AD para escribir aplicaciones que unan a dos organizaciones de una manera más segura. Además, la navegación resulta bastante sencilla para los usuarios finales.

El 97 % de nuestros clientes nos ha comentado que la colaboración B2B en Azure AD es muy importante para ellos.

![gráfico circular](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

Desde comienzos de abril de 2017, aproximadamente 3 millones de usuarios ya usan las funcionalidades de colaboración B2B en Azure AD. Y más del 23 % de organizaciones de Azure AD que tienen más de 10 usuarios ya se benefician de estas funcionalidades.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Entre las principales ventajas de la colaboración B2B de Azure AD para su organización, se encuentran las siguientes:

### <a name="work-with-any-user-from-any-partner"></a>Permite trabajar con cualquier usuario de cualquier asociado.

* Los asociados usan sus propias credenciales.

* Los asociados no necesitan usar Azure AD.

* No se necesita ningún directorio externo o instalación compleja.

### <a name="simple-and-secure-collaboration"></a>Colaboración sencilla y segura

* Proporciona acceso a cualquier aplicación o datos corporativos, al aplicar avanzadas directivas de autorización con tecnología de Azure AD.

* Fácil para los usuarios

* Seguridad de nivel empresarial para aplicaciones y datos.

### <a name="no-management-overhead"></a>Sin sobrecarga de administración.

* Sin administración externa de contraseñas o cuentas.

* Sin administración del ciclo de vida de cuenta manual o sincronización.

* Sin sobrecarga administrativa externa.

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Adición fácil de usuarios de colaboración B2B a la organización

Los administradores pueden agregar usuarios de colaboración (invitados) de B2B en [Azure Portal](https://portal.azure.com).

![agregar usuarios invitados](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Permiso a los colaboradores para que traigan su propia identidad

Los colaboradores de B2B pueden iniciar sesión con la identidad que elijan. Si el usuario no tiene una cuenta de Microsoft o de Azure AD, se crea una en su nombre en el momento del canje de la oferta.

![opción de identidad inicio de sesión](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Delegación a propietarios de grupos y aplicaciones 
Los propietarios de aplicaciones y grupos pueden agregar usuarios de B2B directamente a cualquier aplicación que les interese, ya sea una aplicación de Microsoft o no. Los administradores pueden delegar el permiso para agregar usuarios de B2B a los usuarios no administradores. Los usuarios no administradores pueden utilizar el [panel de acceso a la aplicación de Azure AD](https://myapps.microsoft.com) para agregar los usuarios de colaboración B2B a aplicaciones o grupos.

![panel de acceso](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![agregar miembro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Protección de las directivas de autorización al contenido corporativo

Las directivas de acceso condicional, como la autenticación multifactor, se pueden aplicar:
- En el nivel de inquilino
- En el nivel de aplicación
- A usuarios específicos para proteger los datos y las aplicaciones empresariales.

![agregar miembro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Uso de nuestras API y código de ejemplo para crear fácilmente aplicaciones para realizar una incorporación
Incorpore a los asociados externos de manera personalizada según las necesidades de su organización.

Con las [API de invitación de colaboración B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) puede personalizar sus experiencias de incorporación, incluida la creación de portales de suscripción de autoservicio. Proporcionamos código de ejemplo para un portal de autoservicio [en Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portal de suscripción](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Con la colaboración B2B de Azure AD, puede obtener toda la funcionalidad de Azure AD para proteger sus relaciones con los asociados de forma que los usuarios finales la encuentren sencilla e intuitiva. Por tanto, únase a las miles de organizaciones que ya utilizan B2B de Azure AD para facilitar la colaboración externa.

## <a name="next-steps"></a>Pasos siguientes

* Las experiencias del administrador se encuentran en [Azure Portal](https://portal.azure.com).

* Las experiencias del trabajador de la información están disponibles en el [panel de acceso](https://myapps.microsoft.com).

* Y, como siempre, conéctese con el equipo del producto para obtener comentarios, conversaciones y sugerencias a través de nuestra [Comunidad tecnológica de Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Auditoría y generación de informes de usuarios de colaboración B2B](active-directory-b2b-auditing-and-reporting.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
