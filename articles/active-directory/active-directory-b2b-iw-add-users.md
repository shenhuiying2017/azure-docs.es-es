---

title: "¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B a Azure Active Directory? | Microsoft Docs"
description: "La colaboración B2B de Active Directory de Azure permite a los trabajadores de la información agregar usuarios de su organización a Azure AD para acceder a aplicaciones corporativas."
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
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d9ffd4176e87b6b5ada882ff09f507665bda7b1d
ms.openlocfilehash: 7bad8269c3756979fe48d130a0cdfb02ceafad86


---

# <a name="how-do-information-workers-add-b2b-collaboration-users-to-azure-active-directory"></a>¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B a Azure Active Directory?

Los trabajadores de la información pueden utilizar el [panel de acceso de la aplicación](http://myapps.microsoft.com) para agregar usuarios de colaboración B2B a los grupos y las aplicaciones que se administran.

## <a name="information-workers-adding-b2b-collaboration-users-to-an-application"></a>Trabajadores de la información que agregan usuarios de colaboración B2B a una aplicación
Asigne usuarios de colaboración B2B a una aplicación como un trabajador de la información en una organización asociada, como se muestra en el siguiente vídeo:

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps]

  Si este vídeo no aparece incrustado, puede verlo [aquí](https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps).

## <a name="information-workers-adding-b2b-collaboration-users-to-a-group"></a>Trabajadores de la información que agregan usuarios de colaboración B2B a un grupo

Los trabajadores de la información pueden agregar del mismo modo usuarios de colaboración B2B a un grupo asignado que está habilitado para administrar grupos automáticamente.
> [!NOTE]
No puede agregar usuarios de colaboración B2B a un grupo dinámico o a uno que se ha sincronizado con la instancia local de Active Directory.

## <a name="add-without-invitation"></a>Opción para agregar sin invitación

Si el autor de la invitación pertenece a un rol que tiene privilegios de enumeración en el directorio de la organización del asociado, desde la que está agregando usuarios, los usuarios invitados se agregan a la organización invitadora sin necesidad de invitaciones.

Este es el escenario en el que resulta más útil:
1. Un usuario de la organización anfitriona (por ejemplo, WoodGrove) invita a un usuario de la organización asociada (por ejemplo, Sam@litware.com)) como invitado.
2. El administrador de la organización anfitriona configura directivas que permiten a Sam identificar y agregar otros usuarios de la organización del asociado (Litware).
4. Ahora, Sam puede agregar otros usuarios de Litware al directorio, los grupos o las aplicaciones de WoodGrove sin necesidad de canjear invitaciones. Si Sam tiene los privilegios de enumeración adecuados en Litware, se hará automáticamente.


* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Active Directory Azure](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


