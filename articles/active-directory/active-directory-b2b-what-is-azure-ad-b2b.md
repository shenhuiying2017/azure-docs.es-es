---
title: "Información sobre la versión preliminar de colaboración B2B de Azure Active Directory | Microsoft Docs"
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
ms.date: 03/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4bfd76a00ba694096ef0b045b14beb757ba0809f
ms.lasthandoff: 03/21/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>Información sobre la versión preliminar de colaboración B2B de Azure AD
En este artículo se analizan los objetivos, las características y las ventajas de actualizar la versión preliminar pública de colaboración B2B de Azure Active Directory (Azure AD).

Independientemente de que se trate de un profesional de TI o un trabajador de la información, puede usar muchas funcionalidades de colaboración B2B de Azure AD. Puede trabajar estrechamente con socios de otras organizaciones en todas partes, sin importar su tamaño, sector o requisitos de cumplimiento y gobernanza. Además, puede proporcionarles acceso a documentos, recursos y aplicaciones mientras mantiene el control total sobre los datos internos.

Si es desarrollador, puede usar las API B2B de Azure AD para escribir aplicaciones que reúnan organizaciones de forma segura. En el caso de usuarios que sean trabajadores de la información, el proceso y la navegación son sencillos.

## <a name="how-b2b-collaboration-works"></a>Cómo funciona la colaboración B2B

En la versión preliminar actual, los profesionales de TI y los trabajadores de la información establecen relaciones con organizaciones asociadas al agregar sus usuarios (uno o varios a la vez) a través de Azure Portal o de la API del administrador de invitaciones.

Los administradores pueden usar las experiencias del nuevo portal en Azure Portal (https://portal.azure.com) y PowerShell para establecer relaciones.

Los trabajadores de la información pueden usar las experiencias del Panel de acceso en http://myapps.microsoft.com.

Los desarrolladores pueden crear aplicaciones con la API del administrador de invitaciones de B2B de Azure AD para agregar usuarios de colaboración B2B y personalizar la invitación y los flujos de trabajo incorporados.

Los usuarios de colaboración B2B habitualmente se agregan mediante un proceso de invitación y canje. Aquí le mostramos cómo funciona:

1. John Doe de WoodGrove desea agregar a Sam Oogle como usuario mediante su dirección de Gmail (gsamoogle@gmail.com).

2. John Doe entra en el portal de WoodGrove (portal.azure.com) o en el Panel de acceso (myapps.microsoft.com), inicia sesión y, luego, agrega al usuario Sam Oogle a la aplicación, el grupo o el directorio de WoodGrove.

3. John Doe especifica un correo electrónico de invitación personalizado para enviarlo a Sam Oogle.

4. Cuando John Doe envía la invitación, el usuario Sam Oogle se crea en Azure AD para WoodGrove, tal como se muestra a continuación:

  ![La interfaz de usuario administrador en portal.azure.com](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Una vez que se crea el usuario, Azure AD envía un correo electrónico de invitación a Sam Oogle:

  ![Correo electrónico de invitación a Sam Oogle](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. En la invitación, Sam Oogle selecciona **Primeros pasos**.  
Se abre Azure Portal.

7. Sam Oogle inicia sesión en Azure Portal.

8. Azure AD actualiza el objeto de usuario Sam Oogle en Azure AD con información de su token, tal como se muestra a continuación:

  ![Perfil de usuario de Sam Oogle en Azure Portal](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. Ahora que el usuario Sam Oogle canjeó la invitación, puede tener acceso a los recursos de WoodGrove. Y, al igual que lo que ocurre con cualquier otro usuario en Azure AD, el administrador puede administrar al usuario Sam Oogle. A continuación se muestra la lista de usuarios:

  ![Lista de usuarios de Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Características de la versión preliminar pública
En función de los comentarios de los usuarios, la actualización de la versión preliminar pública de colaboración B2B brinda características clave que incluyen las siguientes:

* Para los administradores: obtenga las mejoras de la interfaz de usuario en [Azure Portal](https://portal.azure.com). Por ejemplo, los administradores pueden invitar a los usuarios de B2B al directorio o a cualquier grupo o aplicación.  

* Para los trabajadores de la información: obtenga las funcionalidades de invitación de autoservicio de colaboración B2B en el [Panel de acceso](https://myapps.microsoft.com). Los trabajadores de la información pueden invitar a usuarios de colaboración B2B a cualquier aplicación o grupo de autoservicio que administran.

* Permita que los usuarios invitados tengan cualquier dirección de correo electrónico. Ya sea que se trate de una dirección de Office 365 o una dirección de Microsoft Exchange local, una dirección outlook.com o cualquier dirección social (como Gmail, Yahoo!, etc.), los usuarios pueden tener acceso a la organización invitada mediante la creación de una cuenta de Azure AD o Microsoft.

* Cree un correo electrónico de invitación profesional con la marca de inquilino.

* Personalice la orientación del usuario; para ello, use las API de invitación.

* Configure la autenticación multifactor para los usuarios de colaboración B2B de la organización que invita.

* Delegue invitaciones a usuarios no administradores.

* Proporcione compatibilidad de PowerShell con colaboración B2B.

* Proporcione funcionalidades de auditoría e informes.

## <a name="help-us-shape-your-features"></a>Ayúdenos a dar forma a sus características
 
Escuchamos constantemente sus comentarios sobre las formas de mejorar la colaboración B2B. Le invitamos a participar en el debate, compartir los escenarios de usuario, los procedimientos recomendados y lo que le gusta de la colaboración B2B de Azure AD en la [comunidad técnica de Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
También a enviar sus ideas y vote por características futuras en el sitio web de [ideas de colaboración B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

## <a name="next-steps"></a>Pasos siguientes

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

