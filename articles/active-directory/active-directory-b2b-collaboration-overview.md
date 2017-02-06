---
title: "Colaboración de Azure Active Directory B2B | Microsoft Docs"
description: "La colaboración de Azure Active Directory B2B permite a los socios comerciales tener acceso a sus aplicaciones corporativas, con cada uno de sus usuarios representados por una cuenta de Azure AD única."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f6a044c-0b36-4a1d-8080-33e28696c42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7c56dee52201c95c2a0677355e2b4edd2f113259


---
# <a name="azure-active-directory-b2b-collaboration"></a>Colaboración de Azure Active Directory B2B
Gracias a la colaboración B2B de Azure Active Directory (Azure AD), podrá habilitar el acceso a sus aplicaciones corporativas desde identidades administradas por socios. Puede crear relaciones entre empresas invitando y autorizando a los usuarios de empresas asociadas para tener acceso a su recursos. La complejidad se reduce debido a que cada empresa federa una vez con Azure Active Directory y cada usuario está representado por una sola cuenta de Azure AD. La seguridad aumenta si los partners comerciales administran sus cuentas en Azure AD, ya que se revoca el acceso cuando se eliminan los usuarios de partners de sus organizaciones y se impide el acceso no deseado mediante la pertenencia en directorios internos. Para los socios comerciales que ya no tienen Azure AD, la colaboración de B2B tiene una experiencia de registro simplificada para ofrecer las cuentas de Azure AD a sus socios comerciales.

* Los socios comerciales usan sus propias credenciales de inicio de sesión, lo que le libera de la administración de un directorio de socios externos y de la necesidad de quitar el acceso cuando los usuarios dejen la organización asociada.
* Administre el acceso a sus aplicaciones de manera independiente de su ciclo de vida de la cuenta de su socio comercial. Esto significa, por ejemplo, que puede revocar el acceso sin tener que solicitar al departamento de TI de su socio comercial que haga nada.

## <a name="capabilities"></a>Capacidades
La colaboración de B2B simplifica la administración y mejora la seguridad del acceso del socio a recursos corporativos, incluidas las aplicaciones SaaS, como Office 365, Salesforce, servicios de Azure y todas las aplicaciones para notificaciones móviles, de nube y locales. La colaboración de B2B permite a los socios administrar sus propias cuentas y las empresas pueden aplicar directivas de seguridad para el acceso de los socios.

La colaboración de Azure Active Directory B2B es sencilla de configurar con registro simplificado para socios de todos los tamaños incluso si no disponen de su propio Azure Active Directory mediante un proceso comprobado por correo electrónico. También es sencillo de mantener sin directorios externos o configuraciones de federación por socio.

## <a name="b2b-collaboration-process"></a>Proceso de colaboración B2B
1. La colaboración de Azure AD B2B permite a un administrador de compañía invitar y autorizar a un conjunto de usuarios externos cargando un archivo de valores separados por comas (CSV) de no más de 2000 líneas en el portal de colaboración de B2B.

   ![Diálogo de carga de archivos CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)
2. El portal enviará invitaciones por correo electrónico a estos usuarios externos.
3. El usuario invitado iniciará sesión en una cuenta profesional existente con Microsoft (administrada en Azure AD) u obtendrá una nueva cuenta profesional en Azure AD.
4. Cuando haya iniciado sesión, se le redirigirá al usuario a la aplicación que se compartió con él.

No se admiten actualmente invitaciones a direcciones de correo electrónico de consumidor (por ejemplo, Gmail o [*comcast.net*](http://comcast.net/)).

Para obtener más información sobre el funcionamiento de la colaboración de B2B, eche un vistazo a [este vídeo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Pasos siguientes
Consulte otros artículos nuestros sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Cómo funciona](active-directory-b2b-how-it-works.md)
* [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
* [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
* [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
* [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Dec16_HO4-->


