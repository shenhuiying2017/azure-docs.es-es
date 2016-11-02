
<properties
	pageTitle="Referencia técnica del acceso condicional de Azure Active Directory | Microsoft Azure"
	description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
    services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="07/26/2016"
	ms.author="markvi"/>

# Referencia técnica del acceso condicional de Azure Active Directory

## Servicios habilitados con acceso condicional
Las reglas de acceso condicional se admiten en varios tipos de aplicaciones de Azure AD. Esta lista incluye:

- Aplicaciones federadas de la Galería de aplicaciones de Azure AD
- Aplicaciones de inicio de sesión único con contraseña de la Galería de aplicaciones de Azure AD
- Aplicaciones registradas con el proxy de aplicación de Azure
- Aplicaciones desarrolladas de línea de negocio y multiinquilino registradas con Azure AD
- Visual Studio Online
- Azure RemoteApp
- 	Dynamics CRM
- Yammer de Microsoft Office 365
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online (incluye OneDrive para la Empresa)


## Habilitación de reglas de acceso

Cada regla puede habilitarse o deshabilitarse en función de la aplicación. Cuando las reglas estén **ACTIVADAS**, se habilitarán y se aplicarán a los usuarios que accedan a la aplicación. Cuando estén **DESACTIVADAS**, no se usarán ni afectarán a la experiencia de inicio de sesión de los usuarios.

## Aplicación de reglas a usuarios específicos
Las reglas se pueden aplicar a conjuntos específicos de usuarios basados enun grupo de seguridad mediante la configuración de **Aplicar a**. **Aplicar a** puede establecerse en **Todos los usuarios** o **Grupos**. Cuando esté establecido en **Todos los usuarios**, las reglas se aplicarán a cualquier usuario con acceso a la aplicación. La opción **Grupos** permite seleccionar grupos de seguridad y distribución específicos; las reglas solo se aplicarán a estos grupos.

Al implementar una regla, se suele aplicar primero a un conjunto limitado de usuarios que sean miembros de grupos de proyectos piloto. Una vez finalizada la regla, puede aplicarse a **Todos los usuarios**. Esto hará que la regla se pueda exigir a todos los usuarios de la organización.

Asimismo, se pueden excluir de la directiva grupos seleccionados mediante la opción **Excepto**. Aunque aparezcan en un grupo incluido, todos los miembros de estos grupos quedarán excluidos.

## Redes "en el trabajo"


Las reglas de acceso condicional que utilizan una red "en el trabajo" dependen de intervalos de direcciones IP de confianza que se han configurado en Azure AD, o del uso de la notificación "dentro de la red corporativa" de AD FS. Estas reglas incluyen:

- Requerir autenticación multifactor fuera del trabajo
- Bloquear el acceso fuera del trabajo

Opciones para especificar redes "en el trabajo"

1. Configurar intervalos de direcciones IP de confianza en la [página de configuración de la autenticación multifactor](../multi-factor-authentication/multi-factor-authentication-whats-next.md). La directiva de acceso condicional usará los intervalos configurados en cada solicitud de autenticación y emisión de tokens para evaluar las reglas.
2. Configurar el uso de la notificación "dentro de la red corporativa"; esta opción se puede usar con directorios federados mediante AD FS. [Aprenda más sobre las notificaciones "dentro de la red corporativa"](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configurar intervalos de direcciones IP públicas. En la pestaña Configurar de su directorio, puede configurar direcciones IP públicas. Estas direcciones se usarán en el acceso condicional como direcciones IP "en el trabajo", lo que permite la configuración de intervalos adicionales por encima del límite de 50 direcciones IP que se aplica en la página de configuración de MFA.



## Reglas basadas en la confidencialidad de las aplicaciones

Las reglas se configuran por aplicación, lo que permite a los servicios de alto valor estar protegidos sin que se vea afectado el acceso a otros servicios. Se pueden configurar reglas de acceso condicional en la pestaña **Configurar** de la aplicación.

Reglas que se ofrecen actualmente:

- **Requerir autenticación multifactor**
 - Todos los usuarios a los que se aplica la directiva deberán autenticarse mediante la autenticación multifactor al menos una vez.
 
- **Requerir autenticación multifactor fuera del trabajo**
 - Si se aplica esta directiva, se exigirá a todos los usuarios que realicen la autenticación multifactor al menos una vez si acceden al servicio desde una ubicación remota fuera del trabajo. Si pasan de una ubicación en el trabajo a una ubicación remota, se les exigirá que realicen la autenticación multifactor cuando accedan al servicio.
 
- **Bloquear el acceso fuera del trabajo**
 - Cuando los usuarios pasan de una ubicación en el trabajo a una ubicación remota, se les bloqueará si se les aplica la directiva "Bloquear acceso cuando no está en el trabajo". Se les volverá a permitir el acceso cuando estén en una ubicación de trabajo.


## Temas relacionados

- [Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0727_2016-->