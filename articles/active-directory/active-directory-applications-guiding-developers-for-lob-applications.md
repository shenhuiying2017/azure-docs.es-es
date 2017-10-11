---
title: Desarrollo de aplicaciones para Azure AD | Microsoft Docs
description: "Escrito para los profesionales de TI, este artículo ofrece instrucciones para integrar las aplicaciones de Azure con Active Directory."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b119be9c06d8c1ccc8e747168429e6c2d2e7a8f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Desarrollo de aplicaciones de línea de negocio para Azure Active Directory
Esta guía proporciona información general sobre el desarrollo de aplicaciones de línea de negocio (LoB) para Azure Active Directory (AD) y se ha diseñado expresamente para administradores globales de Active Directory y Office 365.

## <a name="overview"></a>Información general
La creación de aplicaciones integradas con Azure AD proporciona a los usuarios de la organización inicio de sesión único con Office 365. Si tiene la aplicación en Azure AD, podrá controlar la directiva de autenticación de dicha aplicación. Para obtener más información sobre el acceso condicional y cómo proteger las aplicaciones con Multi-Factor Authentication (MFA), consulte [Configuración de las reglas de acceso](active-directory-conditional-access-azuread-connected-apps.md).

Registro de la aplicación para utilizar Azure Active Directory Registrar la aplicación significa que los programadores pueden usar Azure AD para autenticar usuarios y solicitar acceso a recursos de usuarios; por ejemplo, correo electrónico, calendario y documentos.

Cualquier miembro del directorio (no invitados) puede registrar una aplicación, conocido también como *crear un objeto de aplicación*.

El registro de una aplicación permite a cualquier usuario hacer lo siguiente:

* Obtener una identidad para su aplicación que Azure AD reconoce
* Obtener uno o más secretos o claves que la aplicación puede usar para autenticarse a sí misma en AD
* Identificar la aplicación, por ejemplo, con un nombre o logotipo personalizado en el Portal de Azure
* Aplicar las características de autorización de Azure AD en su aplicación, como las siguientes:

  * Control de acceso basado en rol (RBAC)
  * Azure Active Directory como servidor de autorización de OAuth (proteger una API expuesta por la aplicación)
* Indicar los permisos necesarios para que la aplicación funcione según lo previsto, como:

      - Permisos de la aplicación (solo administradores globales). Por ejemplo: pertenencia a roles en otra aplicación de Azure AD o respecto a una suscripción, un grupo de recursos o un recurso de Azure
      - Permisos delegados (cualquier usuario) Por ejemplo: Azure AD, inicio de sesión y perfil de lectura

> [!NOTE]
> De forma predeterminada, cualquier miembro puede registrar una aplicación. Para obtener información sobre cómo restringir permisos de registro de aplicaciones a miembros específicos, consulte [Cómo se agregan aplicaciones a Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

A continuación, se indica lo que el administrador global tendrá que hacer para ayudar a los desarrolladores a que su aplicación esté lista para la producción:

* Configurar las reglas de acceso (directiva de acceso/MFA)
* Configurar la aplicación para requerir asignación de usuarios y asignar usuarios
* Suprimir la experiencia de consentimiento del usuario predeterminada

## <a name="configure-access-rules"></a>Configuración de las reglas de acceso
Configure las reglas de acceso por aplicación a las aplicaciones SaaS. Por ejemplo, puede obligar a utilizar MFA o solo permitir el acceso a usuarios de redes de confianza. En el documento de [configuración de las reglas de acceso](active-directory-conditional-access-azuread-connected-apps.md)se muestran los detalles pertinentes.

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurar la aplicación para requerir asignación de usuarios y asignar usuarios
De forma predeterminada, los usuarios pueden acceder a las aplicaciones sin asignación previa. Sin embargo, si la aplicación expone roles o si quiere que aparezca en el panel de acceso de un usuario, debe requerir la asignación de usuarios.

[Necesidad de asignación de usuarios](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Si es un suscriptor de Azure AD Premium o Enterprise Mobility Suite (EMS), se recomienda encarecidamente utilizar grupos. La asignación de grupos a la aplicación permite delegar la administración del acceso continuo al propietario del grupo. Puede crear el grupo o pedir a la parte responsable de la organización que lo haga usando sus recursos de administración de grupos.

[Asignación de usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md)  
[Asignación de grupos a una aplicación](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Supresión del consentimiento del usuario
De forma predeterminada, los usuarios otorgan su consentimiento para iniciar sesión. El consentimiento, que se solicita para conceder permisos a una aplicación, puede desconcertar a los usuarios que no estén familiarizados con la toma de este tipo de decisiones.

Para aplicaciones de confianza, puede otorgar el consentimiento para la aplicación en nombre de su organización con el fin de simplificar la experiencia de los usuarios.

Para obtener más información sobre el consentimiento de usuarios y cómo se utiliza esta característica en Azure, consulte [Integración de aplicaciones con Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Artículos relacionados
* [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)
* [Acceso condicional de Azure en versión de vista previa para aplicaciones SaaS](active-directory-conditional-access-azuread-connected-apps.md)
* [Administración del acceso a las aplicaciones con Azure AD](active-directory-managing-access-to-apps.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
