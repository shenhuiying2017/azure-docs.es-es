---
title: "¿Qué es la versión preliminar de colaboración B2B de Azure Active Directory? | Microsoft Docs"
description: "La colaboración con Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas."
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
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 14028332ecc17478230d035206e8e00f6af67f7b
ms.openlocfilehash: 8b6bf676dfa0df45d59763db70655d85fa08809a
ms.lasthandoff: 02/23/2017


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>¿Qué es la versión preliminar de colaboración B2B de Azure AD?

Las funcionalidades de colaboración B2B de Azure AD permiten a los profesionales de TI y a los trabajadores de la información trabajar estrechamente con sus asociados en cualquier otra organización del planeta. Pueden proporcionar acceso a documentos, recursos y aplicaciones al mismo tiempo que conservan el pleno control de los datos internos. Los desarrolladores pueden usar las API de negocio a negocio de Azure AD para escribir aplicaciones que unen a dos organizaciones de una manera segura, lo que resulta ideal para los trabajadores de la información e intuitivo para navegar.

Las funcionalidades de colaboración B2B de Azure AD permiten a organizaciones de todos los tamaños y sectores, con independencia de sus requisitos de cumplimiento y gobierno, trabajar de manera fácil y segura con colaboradores de todo el mundo. Ese es el objetivo de esta actualización de la versión preliminar pública de colaboración B2B.

## <a name="how-does-it-work"></a>¿Cómo funciona?

En la versión preliminar actual, para establecer una relación con una organización, los profesionales de TI y los trabajadores de la información pueden agregar usuarios de otra organización uno a uno o varios a la vez a través del portal o de la API del administrador de invitaciones. Para tal fin, los administradores pueden usar las experiencias del nuevo portal en Azure Portal (https://portal.azure.com) y PowerShell. Y los trabajadores de información pueden usar las experiencias del Panel de acceso en http://myapps.microsoft.com. Los desarrolladores pueden crear aplicaciones con la API del administrador de invitaciones de B2B de Azure AD para agregar usuarios de colaboración B2B y personalizar la invitación y los flujos de trabajo incorporados.

Los usuarios de colaboración B2B normalmente se incorporan mediante un proceso de invitación + canje. Aquí le mostramos cómo funciona:

1. John Doe de WoodGrove desea agregar a Sam Oogle mediante su dirección de gmail gsamoogle@gmail.com.

2. John entra en el portal de WoodGrove (portal.azure.com) o en el panel de acceso (myapps.microsoft.com), inicia sesión y agrega al usuario en el directorio de WoodGrove, o al grupo o a la aplicación.

3. John especifica un correo electrónico de invitación personalizado para enviarlo a Sam.

4. En cuanto termina, el usuario siguiente se creará en AD en WoodGrove (la captura de pantalla se ha obtenido de la experiencia de usuario de un administrador en portal.azure.com):

  ![se agrega el usuario](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. En cuanto se agrega el usuario, Azure AD envía un correo electrónico de invitación a Sam:

  ![correo electrónico de invitación enviado a Sam](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Ahora Sam selecciona **Introducción** e inicia sesión. En este momento, Azure AD actualiza su objeto de usuario en el directorio con la información de su token (la captura de pantalla se ha obtenido de la experiencia de usuario de un administrador [Azure Portal](https://portal.azure.com)):

  ![se rellena el perfil de usuario](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. Ahora que se ha canjeado la invitación de Sam, puede acceder a los recursos de WoodGrove y, además, el administrador puede administrarlo como a cualquier otro usuario del directorio (la captura de pantalla se ha obtenido de la experiencia de usuario de un administrador [Azure Portal](https://portal.azure.com)):

  ![Sam ya es un usuario de Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Características de la versión preliminar pública
Ha utilizado las funcionalidades de colaboración B2B disponibles en la versión preliminar pública y ha proporcionado unos comentarios excelentes. Y se han tenido muy en cuenta. Se han empaquetado todas las mejoras realizadas en esta actualización de la de versión preliminar pública. Se trata de las principales características de la actualización de la versión preliminar pública de colaboración B2B:

1. Mejoras de la experiencia de usuario de administrador en la experiencia con B2B
  - Incorporación en https://portal.azure.com
  - Capacidad para que los administradores inviten a usuarios de B2B al directorio, o bien a cualquier grupo o aplicación

2. Funcionalidades de invitación de autoservicio de colaboración B2B para los trabajadores de la información en el Panel de acceso: https://myapps.microsoft.com. Los trabajadores de la información pueden invitar a usuarios de colaboración B2B a cualquier aplicación o grupo de autoservicio que administran.

3. Ahora se puede invitar a un usuario con cualquier dirección de correo electrónico. Si el usuario tiene una dirección de correo electrónico de Microsoft Exchange local o de Office365, una dirección de correo electrónico de outlook.com, cualquier dirección de correo electrónico social (Gmail, Yahoo, etc.), ahora pueden acceder sin problemas a la organización invitada con la creación incorporada y rápida de una cuenta de Azure AD o de una cuenta de Microsoft.

4. Puede beneficiarse de un correo electrónico de invitación profesional y con la marca de inquilino.

5. Gran capacidad para personalizar las incorporaciones mediante las API de invitación.

6. Multi-factor Authentication para los usuarios de colaboración B2B en la organización que invita.

7. Capacidad de delegar invitaciones en usuarios no administradores.

8. Compatibilidad con PowerShell para colaboración B2B.

9. Funcionalidades de auditoría e informes.

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)

* [Auditoría y generación de informes de usuarios de colaboración B2B](active-directory-b2b-auditing-and-reporting.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

