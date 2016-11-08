---
title: 'Azure Active Directory B2C: configuración de tokens, sesión e inicio de sesión único | Microsoft Docs'
description: Configuración de token, sesión e inicio de sesión único en Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2016
ms.author: swkrish

---
# Azure Active Directory B2C: configuración de tokens, sesión e inicio de sesión único
Esta característica ofrece un control más preciso, [por directivas](active-directory-b2c-reference-policies.md), de:

1. La vigencia de los tokens de seguridad emitidos por Azure Active Directory (Azure AD) B2C.
2. La duración de las sesiones de aplicación web administradas por Azure AD B2C.
3. El comportamiento de inicio de sesión único (SSO) entre varias aplicaciones y directivas en el inquilino B2C.

Puede utilizar esta característica en el inquilino B2C como sigue:

1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) del Portal de Azure.
2. Haga clic en **Directivas de inicio de sesión**. *Nota: Esta característica se puede usar en cualquier tipo de directiva, no solo en **directivas de inicio de sesión***.
3. Abra una directiva haciendo clic en ella. Por ejemplo, haga clic en **B2C\_1\_SiIn**.
4. Haga clic en **Editar** en la parte superior de la hoja.
5. Haga clic en **Token, session & single sign-on config** (Configuración de tokens, sesión e inicio de sesión único).
6. Realice los cambios deseados. Obtenga información acerca de las propiedades disponibles en las secciones siguientes.
7. Haga clic en **Aceptar**.
8. Haga clic en **Guardar** en la parte superior de la hoja.

![Captura de pantalla de configuración de token, sesión e inicio de sesión único](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## Configuración de la vigencia de los tokens
Azure AD B2C admite el [protocolo de autorización de OAuth 2.0](active-directory-b2c-reference-protocols.md) para habilitar un acceso seguro a los recursos protegidos. Para implementar esta compatibilidad, Azure AD B2C emite varios [tokens de seguridad](active-directory-b2c-reference-tokens.md). Estas son las propiedades que puede utilizar para administrar la vigencia de los tokens de seguridad emitidos por Azure AD B2C:

* **Vigencia (en minutos) de token de acceso y de identificador**: la vigencia del token de portador de OAuth 2.0 que se utiliza para obtener acceso a un recurso protegido. Azure AD B2C solo emite tokens de identificador en este momento. Este valor se aplicará también a los tokens de acceso cuando se agregue compatibilidad para ellos.
  * Valor predeterminado: 60 minutos.
  * Mínimo (incluido) = 5 minutos.
  * Máximo (incluido) = 1440 minutos.
* **Vigencia del token de actualización (en días)**: el período máximo en que un token de actualización se puede utilizar para adquirir un nuevo token de acceso o de identificador (y opcionalmente, un nuevo token de actualización, si se hubiera concedido el ámbito `offline_access` a la aplicación).
  * Valor predeterminado = 14 días.
  * Mínimo (incluido) = 1 día.
  * Máximo (incluido) = 90 días.
* **Vigencia (en días) de la ventana deslizante del token de actualización**: después de transcurrido este período de tiempo, el usuario está obligado a volver a autenticarse, independientemente de cuál sea el período de validez del último token de actualización obtenido por la aplicación. Solo se pueden proporcionar si el conmutador se establece en **Bounded** (Enlazado). Es preciso que sea mayor o igual que el valor de **Vigencia del token de actualización (en días)**. Si el conmutador se establece en **Unbounded** (Sin enlazar), no se puede proporcionar un valor específico.
  * Valor predeterminado = 90 días.
  * Mínimo (incluido) = 1 día.
  * Máximo (incluido) = 365 días.

Aquí puede ver un par de casos de uso que puede habilitar mediante el uso de estas propiedades:

* Permiso para que un usuario pueda permanecer conectado en una aplicación móvil indefinidamente, siempre que esté continuamente activo en la misma. Para ello, establezca el conmutador **Vigencia (en días) de la ventana deslizante del token de actualización** en **Unbounded** (Sin enlazar) en la directiva de inicio de sesión.
* Cumpla los requisitos de cumplimiento normativo y seguridad de la industria mediante el establecimiento de la vigencia adecuada del token de acceso.

## Configuración de sesión
Azure AD B2C admite el [protocolo de autenticación OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar el inicio de sesión seguro en las aplicaciones web. Estas son las propiedades que puede usar para administrar sesiones de la aplicación web:

* **Vigencia (en minutos) de la sesión de la aplicación web**: la vigencia de la cookie de sesión de Azure AD B2C almacenada en el explorador del usuario tras una autenticación correcta.
  * Valor predeterminado: 1440 minutos.
  * Mínimo (incluido) = 15 minutos.
  * Máximo (incluido) = 1440 minutos.
* **Tiempo de espera de la sesión de la aplicación web**: si este conmutador se establece en **Absolute** (Absoluto), el usuario se ve obligado a volver a autenticarse una vez transcurrido el período especificado en **Vigencia (en minutos) de la sesión de la aplicación web**. Si este conmutador se establece en **Rolling** (Gradual) (el valor predeterminado), el usuario permanecerá conectado siempre que esté activo en su aplicación web.

Aquí puede ver un par de casos de uso que puede habilitar mediante el uso de estas propiedades:

* Conformidad con los requisitos de cumplimiento normativo y seguridad de la industria mediante el establecimiento de la duración adecuada de la sesión de la aplicación web.
* Obligación de volver a autenticar después de un período de tiempo establecido durante la interacción del usuario con una zona de alta seguridad de la aplicación web.

## Configuración de inicio de sesión único (SSO)
Si tiene varias aplicaciones y directivas en el inquilino de B2C, puede administrar las interacciones del usuario a través de ellas con la propiedad **Configuración de inicio de sesión único**. Puede establecer la propiedad en uno de los siguientes valores:

* **Inquilino**: es la configuración predeterminada. Esta configuración permite que varias aplicaciones y directivas en el inquilino B2C compartan la misma sesión de usuario. Por ejemplo, una vez que un usuario inicia sesión en una aplicación llamada Contoso Shopping, puede también iniciar sesión perfectamente en otra llamada Contoso Pharmacy simplemente con acceder a ella.
* **Aplicación**: permite mantener una sesión de usuario exclusivamente para una aplicación, independientemente de otras aplicaciones. Por ejemplo, si desea que el usuario inicie sesión en Contoso Pharmacy (con las mismas credenciales), aunque ya haya iniciado sesión en Contoso Shopping, otra aplicación en el mismo inquilino B2C.
* **Directiva**: permite mantener una sesión de usuario exclusivamente para una directiva, independientemente de las aplicaciones que la utilicen. Por ejemplo, si el usuario ya ha iniciado sesión y completado un paso de autenticación multifactor (MFA), puede tener acceso a zonas de una mayor seguridad de varias aplicaciones mientras no expire la sesión asociada a la directiva.
* **Deshabilitado**: obliga al usuario a realizar todo el proceso cada vez que se ejecuta la directiva. Por ejemplo, esto le permitirá que varios usuarios inicien sesión en la aplicación (en un escenario de escritorio compartido), incluso si un único usuario permanece conectado durante todo el tiempo.

<!---HONumber=AcomDC_0727_2016-->