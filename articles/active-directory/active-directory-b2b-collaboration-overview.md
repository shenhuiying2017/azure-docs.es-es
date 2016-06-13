<properties
   pageTitle="Colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="La colaboración de Azure Active Directory B2B permite a los socios comerciales tener acceso a sus aplicaciones corporativas, con cada uno de sus usuarios representados por una cuenta de Azure AD única."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="curtand"/>

# Colaboración de Azure Active Directory B2B

Gracias a la colaboración B2B de Azure Active Directory (Azure AD), podrá habilitar el acceso a sus aplicaciones corporativas desde identidades administradas por socios. Puede crear relaciones entre empresas invitando y autorizando a los usuarios de empresas asociadas para tener acceso a su recursos. La complejidad se reduce debido a que cada empresa federa una vez con Azure Active Directory y cada usuario está representado por una sola cuenta de Azure AD. La seguridad aumenta si los partners comerciales administran sus cuentas en Azure AD, ya que se revoca el acceso cuando se eliminan los usuarios de partners de sus organizaciones y se impide el acceso no deseado mediante la pertenencia en directorios internos. Para los socios comerciales que ya no tienen Azure AD, la colaboración de B2B tiene una experiencia de registro simplificada para ofrecer las cuentas de Azure AD a sus socios comerciales.

-   Los socios comerciales usan sus propias credenciales de inicio de sesión, lo que le libera de la administración de un directorio de socios externos y de la necesidad de quitar el acceso cuando los usuarios dejen la organización asociada.

-   Administre el acceso a sus aplicaciones de manera independiente de su ciclo de vida de la cuenta de su socio comercial. Esto significa, por ejemplo, que puede revocar el acceso sin tener que solicitar al departamento de TI de su socio comercial que haga nada.

## Capacidades

La colaboración de B2B simplifica la administración y mejora la seguridad del acceso del socio a recursos corporativos, incluidas las aplicaciones SaaS, como Office 365, Salesforce, servicios de Azure y todas las aplicaciones para notificaciones móviles, de nube y locales. La colaboración de B2B permite a los socios administrar sus propias cuentas y las empresas pueden aplicar directivas de seguridad para el acceso de los socios.

La colaboración de Azure Active Directory B2B es sencilla de configurar con registro simplificado para socios de todos los tamaños incluso si no disponen de su propio Azure Active Directory mediante un proceso comprobado por correo electrónico. También es sencillo de mantener sin directorios externos o configuraciones de federación por socio.

## Proceso de colaboración B2B

1. La colaboración de Azure AD B2B permite a un administrador de compañía invitar y autorizar a un conjunto de usuarios externos cargando un archivo de valores separados por comas (CSV) de no más de 2000 líneas en el portal de colaboración de B2B.

  ![Diálogo de carga de archivos CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. El portal enviará invitaciones por correo electrónico a estos usuarios externos.

3. El usuario invitado iniciará sesión en una cuenta profesional existente con Microsoft (administrada en Azure AD) u obtendrá una nueva cuenta profesional en Azure AD.

4. Cuando haya iniciado sesión, se le redirigirá al usuario a la aplicación que se compartió con él.

No se admiten actualmente las invitaciones a las direcciones de correo electrónico de consumidor (por ejemplo, gmail o [*comcast.net*](http://comcast.net/)).

Para obtener más información sobre el funcionamiento de la colaboración de B2B, eche un vistazo a [este vídeo](http://aka.ms/aadshowb2b).

## Pasos siguientes
Consulte otros artículos nuestros sobre la colaboración B2B de Azure AD:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0601_2016-->