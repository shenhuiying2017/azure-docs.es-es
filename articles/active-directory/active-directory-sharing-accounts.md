<properties
	pageTitle="Uso compartido de cuentas con Azure AD | Microsoft Azure"
	description="Describe cómo Azure Active Directory permite a las organizaciones compartir de forma segura las cuentas de aplicaciones locales y los servicios en la nube de consumidor."
	services="active-directory"
	documentationCenter=""
	authors="msStevenPo"
 	manager="stevenpo"
	editor=""/>

 <tags
	ms.service="active-directory"
 	ms.workload="identity"
 	ms.tgt_pltfrm="na"
 	ms.devlang="na"
 	ms.topic="article"
 	ms.date="10/13/2015"  
 	ms.author="stevenpo"/>

# Uso compartido de cuentas con Azure AD

A menudo en las organizaciones nos encontramos en situaciones en las que varias personas tienen que usar un único nombre de usuario y una contraseña. Esto suele ocurrir en dos casos:

- Al acceder a aplicaciones que requieren un inicio de sesión único y una contraseña para cada usuario, ya sean aplicaciones locales o servicios en la nube de consumidor (por ejemplo, cuentas de medios sociales corporativos).
- Al crear entornos multiusuario. Puede tener una sola cuenta local con privilegios elevados y que pueda usarse para realizar las actividades principales de instalación, administración y recuperación (por ejemplo, la cuenta local del "administrador global" para Office 365 o la cuenta raíz en Salesforce).

Tradicionalmente, estas cuentas se pueden compartir al distribuir las credenciales (nombre de usuario y contraseña) a las personas adecuadas o almacenarlas en una ubicación compartida donde varios agentes de confianza pueden tener acceso a ellos.

## ¿Cuál es el problema con el modelo tradicional de uso compartido?

El modelo tradicional de uso compartido tiene varias desventajas:

- Habilitar el acceso a las nuevas aplicaciones requiere distribuir las credenciales a todos los usuarios que necesiten acceso.
- Cada aplicación compartida puede requerir su propio conjunto de credenciales compartidas, por lo que los usuarios tienen que recordar varios conjuntos de credenciales. Cuando los usuarios tienen que recordar varias credenciales, aumenta el riesgo que recurrir a las prácticas peligrosas (por ejemplo, anotar las contraseñas).
- No se puede determinar quién tiene acceso a una aplicación.
- No se puede determinar quién accedió a la aplicación.
- Si tiene que quitar el acceso a una aplicación, debe actualizar las credenciales y volver a distribuirlas a todos los usuarios que deban acceder a esa aplicación.

## ¿Cómo ayuda Azure Active Directory?

Azure AD proporciona un enfoque nuevo para el uso de cuentas compartidas que elimina estos inconvenientes. El Administrador de Azure AD configura a qué aplicaciones puede acceder el usuario mediante el Panel de acceso. Para cada aplicación, el administrador elige el tipo de inicio de sesión único más adecuado para esa aplicación. Uno de estos tipos, el inicio de sesión único con contraseña, permite a Azure AD actuar como una variante de agente durante el proceso de inicio de sesión de dicha aplicación. El administrador puede fácilmente conceder o revocar el acceso a una aplicación y también saber, en un momento dado, quién tiene acceso a la cuenta y quién ha accedido a ella.

Los usuarios iniciar sesión una sola vez mediante su cuenta profesional, la misma cuenta que suelen usar para tener acceso a su correo electrónico o al escritorio. Pueden detectar y acceder solo a aquellas aplicaciones que tienen asignadas. Con cuentas compartidas, esta lista de aplicaciones puede incluir cualquier número de credenciales compartidas. El usuario final no necesita recordar ni anotar las distintas cuentas que pueden usar.

Las cuentas compartidas no solo aumentan la supervisión y mejoran la facilidad de uso, también mejoran la seguridad. Los usuarios con permisos para usar las credenciales no ven la contraseña compartida, sino que obtienen permiso para usar la contraseña como parte de un flujo de autenticación orquestado. Además, con algunas aplicaciones de inicio de sesión único con contraseña, tiene la opción de que Azure AD sustituya (actualizar) periódicamente la contraseña por largas y complejas contraseñas, con lo que se aumenta la seguridad de la cuenta.

Azure AD admite las cuentas compartidas para cualquier usuario de Enterprise Mobility Suite (EMS), con licencia Premium o Básico, en todos los tipos de aplicaciones de inicio de sesión único con contraseña. Puede compartir las cuentas para cualquiera de los miles de aplicaciones previamente integradas en la galería de aplicaciones y puede agregar su propia aplicación con autenticación por contraseña con [aplicaciones de inicio de sesión único personalizadas](active-directory-single-sign-on-newly-acquired-saas-apps.md).

Para usar Azure AD para compartir una cuenta, debe:

- Agregar una [galería de aplicaciones](https://azure.microsoft.com/es-ES/marketplace/active-directory/) o [aplicación personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) de la aplicación
- Configurar la aplicación para el inicio de sesión único (SSO) con contraseña
- Usar la [asignación basada en grupos](active-directory-accessmanagement-group-saasapps.md) y seleccionar la opción de especificar una credencial compartida
- Opcional: en algunas aplicaciones, como Facebook, Twitter o LinkedIn, puede habilitar la opción para [sustituir la contraseña automatizada de Azure AD](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx).

Con Azure AD puede hacer que su cuenta compartida sea más segura con Multi-Factor Authentication (MFA) (obtenga más información sobre la [protección de aplicaciones con Azure AD](multi-factor-authentication-get-started.md)) y puede delegar la capacidad para administrar quién tiene acceso a la aplicación mediante la administración de grupos de [autoservicio de Azure AD](active-directory-accessmanagement-self-service-group-management.md).

## ¿Cómo puedo comenzar?

En primer lugar, si aún no usa Azure AD y es un administrador de TI:

- [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory/) Puede suscribirse a una evaluación gratuita de 30 días hoy e implementar la primera solución en la nube en menos de 5 minutos desde este vínculo.

Entre las características de Azure AD que permiten el uso compartido de las cuentas se incluyen las siguientes:

- [Inicio de sesión único con contraseña](active-directory-passwords-getting-started.md)
- Agente de inicio de sesión único con contraseña
- [Asignación de grupos](active-directory-accessmanagement-self-service-group-management.md)
- Aplicaciones de contraseñas personalizadas
- [Panel/informes de uso de aplicaciones](active-directory-passwords-get-insights.md)
- Portales de acceso para usuarios finales
- [Proxy de aplicaciones](active-directory-application-proxy-get-started.md)
- [Active Directory Marketplace](http://azure.microsoft.com/marketplace/active-directory/all/)

Contenido relacionado:

- [Protección de aplicaciones con acceso condicional](active-directory-conditional-access.md)
- [Administración de grupos de autoservicio/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=Oct15_HO3-->