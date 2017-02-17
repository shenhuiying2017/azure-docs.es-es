---
title: 'Azure Active Directory B2C: limitaciones y restricciones | Microsoft Docs'
description: Lista de limitaciones y restricciones de Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 04ec3310-98bb-4bb1-856d-ddc66913b390
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 351149296a6d7dfa801b295ec21fc04215c7b051
ms.openlocfilehash: 0f0805c8363226b6fab6463c668d750e8e7c9265


---
# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory B2C: limitaciones y restricciones
Hay varias características y funcionalidades de Azure Active Directory (Azure AD) B2C que no se admiten aún. Muchos de estos problemas conocidos y limitaciones se resolverán más adelante, pero se recomienda tenerlos en cuenta si está compilando aplicaciones para consumidor con Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemas durante la creación de inquilinos de Azure AD B2C
Si surgen problemas durante la [creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started.md), consulte [Creación de un inquilino de Azure AD o de Azure AD B2C: problemas y soluciones](active-directory-b2c-support-create-directory.md) para saber qué hacer.

Tenga en cuenta que existen problemas conocidos al eliminar un inquilino de B2C existente y volver a crearlo con el mismo nombre de dominio. Es necesario crear un inquilino de B2C con otro nombre de dominio.

## <a name="note-about-b2c-tenant-quotas"></a>Nota sobre las cuotas de inquilinos B2C
De forma predeterminada, el número de usuarios de un inquilino B2C está limitado a 50 000 usuarios. Si necesita aumentar la cuota del inquilino B2C, póngase en contacto con el soporte técnico.

## <a name="branding-issues-on-verification-email"></a>Problemas de personalización de marca en el mensaje de confirmación
El correo electrónico de comprobación predeterminado contiene información de personalización de marca de Microsoft. Lo quitaremos en el futuro. Por ahora, puede quitarla mediante la [característica de personalización de marca corporativa](../active-directory/active-directory-add-company-branding.md).

## <a name="branding-issues-on-local-account-sign-in-page-in-a-sign-in-policy"></a>Problemas de personalización de marca en la página de inicio de sesión de la cuenta local en una directiva de inicio de sesión
En una directiva de inicio de sesión, la página de inicio de sesión de la cuenta local se puede personalizar mediante la característica de [personalización de marca de la empresa](../active-directory/active-directory-add-company-branding.md), no mediante la característica de personalización de la interfaz de usuario de la página que se describe [aquí](active-directory-b2c-reference-ui-customization.md). Además, no hay etiquetas ni marcadores de posición disponibles en los campos de nombre de usuario y contraseña. Como solución alternativa se recomienda usar la "directiva de suscripción o de inicio de sesión" totalmente personalizable. Si está interesado en personalizar completamente la página de inicio de sesión de la cuenta local en una directiva de inicio de sesión, vote por la característica [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13062033-b2c-fully-customizable-sign-in-page).

## <a name="restrictions-on-applications"></a>Restricciones en las aplicaciones
Los siguientes tipos de aplicaciones no se admiten actualmente en Azure AD B2C. Para ver una descripción de los tipos de aplicaciones compatibles, consulte [Azure AD B2C: tipos de aplicaciones](active-directory-b2c-apps.md).

### <a name="daemons--server-side-applications"></a>Aplicaciones de servidor o de demonio 
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del consumidor) mediante el [flujo de credenciales de cliente de OAuth 2.0](active-directory-b2c-reference-protocols.md). Este flujo aún no está disponible en Azure AD B2C; así pues, por ahora, las aplicaciones solo pueden obtener tokens después de que se produzca un flujo de inicio de sesión de consumidor interactivo.

### <a name="standalone-web-apis"></a>API web independiente
En Azure AD B2C, tiene la posibilidad de [crear una API web que se protege mediante tokens OAuth 2.0](active-directory-b2c-apps.md#web-apis). Sin embargo, esa API web sólo podrá recibir tokens de un cliente que comparta el mismo id. de aplicación. No se admite la creación de una API web a la que se obtiene acceso desde varios clientes diferentes.

### <a name="web-api-chains-on-behalf-of"></a>Cadenas de la API web (en nombre de)
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante Azure AD B2C. Este escenario es común en los clientes nativos que tienen un back-end de API web que, a su vez, llama a un servicio de Microsoft Online, como la API Graph de Azure AD.

Este escenario de API web encadenadas puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no está implementado actualmente en Azure AD B2C.

## <a name="restrictions-on-reply-urls"></a>Restricciones en las direcciones URL de respuesta
Actualmente, las aplicaciones registradas en Azure AD B2C están restringidas a un conjunto limitado de valores de direcciones URL de respuesta. La dirección URL de respuesta de aplicaciones y servicios web debe comenzar por el esquema `https`, y todos los valores de dicha dirección deben compartir un único dominio DNS. Por ejemplo, no puede registrar una aplicación web con una de estas direcciones URL de respuesta:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

El sistema de registro compara el nombre DNS completo de la dirección URL de respuesta existente con el nombre DNS de la dirección URL de respuesta que va a agregar. La solicitud para agregar el nombre DNS presentará un error si se cumple alguna de las condiciones siguientes:

* Si el nombre DNS completo de la nueva dirección URL de respuesta no coincide con el nombre DNS de la dirección URL de respuesta existente.
* Si el nombre DNS completo de la nueva dirección URL de respuesta no es un subdominio de la dirección URL de respuesta existente.

Por ejemplo, si la aplicación tiene esta dirección URL de respuesta:

`https://login.contoso.com`

Puede agregarla del modo siguiente:

`https://login.contoso.com/new`

En este caso, el nombre DNS es una coincidencia exacta. O bien, puede hacer lo siguiente:

`https://new.login.contoso.com`

En este caso, hace referencia a un subdominio DNS de login.contoso.com. Si desea que una aplicación tenga login-east.contoso.com y login-west.contoso.com como direcciones URL de respuesta, debe agregar dichas direcciones en este orden:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Puede agregar las dos últimas porque son subdominios de la primera URL de respuesta, contoso.com. Esta limitación se eliminará en una próxima versión.

Para obtener información sobre cómo registrar una aplicación en Azure AD B2C, vea [Registro de la aplicación con Azure Active Directory B2C](active-directory-b2c-app-registration.md).

## <a name="restriction-on-libraries-and-sdks"></a>Restricción en bibliotecas y SDK
El conjunto de bibliotecas compatibles de Microsoft que funcionan con Azure AD B2C es muy limitado en este momento. Contamos con compatibilidad para aplicaciones web y servicios basados en .NET, así como aplicaciones web y servicios de NodeJS.  También tenemos una biblioteca de cliente .NET en versión preliminar conocida como MSAL que puede usarse con Azure AD B2C en Windows y otras aplicaciones .NET.

Actualmente ninguna biblioteca admite otros lenguajes o plataformas, como iOS y Android.  Si desea compilar en una plataforma diferente a las mencionadas anteriormente, se recomienda usar un SDK de código abierto y consultar nuestra [referencia de protocolos OAuth 2.0 y OpenID Connect](active-directory-b2c-reference-protocols.md) según sea necesario.  Azure AD B2C implementa OAuth y OpenID Connect, lo que permite usar una biblioteca OAuth u OpenID Connect genérica para la integración.

Nuestros tutoriales de inicio rápido de iOS y Android utilizan bibliotecas de código abierto que hemos probado para ofrecer compatibilidad con Azure AD B2C.  Todos nuestros tutoriales de inicio rápido están disponibles en nuestra sección [Introducción](active-directory-b2c-overview.md#get-started) .

## <a name="restriction-on-protocols"></a>Restricción en los protocolos
Azure AD B2C admite OpenID Connect y OAuth 2.0. Sin embargo, no se han implementado todas las características y capacidades de cada protocolo. Para comprender mejor el alcance de la funcionalidad del protocolo compatible con Azure AD B2C, lea nuestra [referencia de protocolos OAuth 2.0 y OpenID Connect](active-directory-b2c-reference-protocols.md). Ahora existe compatibilidad con los protocolos SAML y WS-Fed.

## <a name="restriction-on-tokens"></a>Restricción en los tokens
Muchos de los tokens emitidos por Azure AD B2C se implementan como JSON Web Token o JWT. Sin embargo, no toda la información incluida en JWT (conocida como "notificaciones") es como debería ser o falta. Un ejemplo es la notificación "preferred_username".  Como los valores, el formato o el significado de las notificaciones cambian con el tiempo, los tokens para las directivas existentes no se ven afectados, por lo que puede confiar en sus valores de las aplicaciones de producción.  Cuando valores cambian, se le dará la oportunidad de configurar esos cambios para cada una de las directivas.  Para comprender mejor los tokens emitidos actualmente por el servicio de Azure AD B2C, lea nuestra [referencia de token](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restricción en grupos anidados
No se admiten pertenencias a grupos anidados en inquilinos de Azure AD B2C. No está previsto agregar esta funcionalidad.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restricción de la característica de consulta diferencial en API de Azure AD Graph.
La [característica de consulta diferencial en la API Azure AD Graph](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) no es compatible con los inquilinos de Azure AD B2C. Es nuestro plan a largo plazo.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemas con Administración de usuarios en el Portal de Azure clásico
Se puede acceder a las características B2C desde el Portal de Azure. Sin embargo, puede usar el Portal de Azure clásico para obtener acceso a otras características de inquilino, como la administración de usuarios. Actualmente hay un par de problemas conocidos con la administración de usuarios (la pestaña **Usuarios** ) en el Portal de Azure clásico.

* Para un usuario de cuenta local (es decir, un consumidor que se registra con una dirección de correo electrónico y una contraseña o un nombre de usuario y una contraseña), el campo **Nombre de usuario** no se corresponde con el identificador de inicio de sesión (dirección de correo electrónico o nombre de usuario) usado durante el registro. El motivo es que el campo que se muestra en el Portal de Azure clásico es en realidad el nombre principal de usuario (UPN), que no se usa en escenarios B2C. Para ver el identificador de inicio de sesión de la cuenta local, busque el objeto de usuario en el [Explorador de gráficos](https://graphexplorer.cloudapp.net/). Encontrará el mismo problema con un usuario de cuenta social (es decir, un consumidor que se registre a Facebook, Google +, etc.), pero en ese caso, no hay ningún identificador de inicio de sesión del que hablar.
  
    ![Cuenta local: UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)
* Para un usuario de cuenta local, no podrá editar ninguno de los campos y guardará los cambios en la pestaña **Perfiles** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemas con el restablecimiento de contraseña iniciado por el administrador en el Portal de Azure clásico
Si restablece la contraseña para un consumidor basado en una cuenta local en Portal de Azure clásico (el comando **Restablecer contraseña** de la pestaña **Usuarios**), ese consumidor no podrá cambiar su contraseña en el siguiente inicio de sesión (si utiliza una directiva de registro o inicio de sesión), y será bloqueado en sus aplicaciones. Como alternativa, utilice la [API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para restablecer la contraseña del consumidor (sin caducidad de contraseña) o use una directiva de inicio de sesión en lugar de una de registro o inicio de sesión.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemas con la creación de un atributo personalizado
Un [atributo personalizado agregado en el Portal de Azure](active-directory-b2c-reference-custom-attr.md) no se crea inmediatamente en el inquilino B2C. Tendrá que utilizar el atributo personalizado en al menos una de las directivas antes de crearlo en el inquilino B2C y permitir que esté disponible a través de la API Graph.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemas con la comprobación de un dominio en el Portal de Azure clásico
Actualmente no se puede verificar un dominio correctamente en el [Portal de Azure clásico](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemas con el inicio de sesión con la directiva MFA en los exploradores Safari
Solicitudes de las directivas de inicio de sesión (con MFA activado) errores intermitentes en los exploradores Safari con errores de HTTP 400 (solicitud incorrecta). Esto se debe a los bajos límites de tamaño de cookies de Safari. Hay un par de soluciones para este problema:

* Utilizar la "directiva de registro o de inicio de sesión" en lugar de la "directiva de inicio de sesión".
* Reducir el número de **notificaciones de la aplicación** que se solicita en la directiva.

## <a name="issues-with-windows-desktop-wpf-apps-using-azure-ad-b2c"></a>Problemas con aplicaciones de WPF de escritorio de Windows que usan Azure AD B2C
Las solicitudes a Azure AD B2C desde una aplicación de WPF de escritorio de Windows a veces genera un error con el siguiente mensaje de error: "The browser based authentication dialog failed to complete. Reason: The protocol is not known and no pluggable protocols have been entered that match." (El cuadro de diálogo de autenticación basada en el explorador no ha podido completarse. Motivo: No se conoce el protocolo y no se especificó ningún protocolo conectable que coincida).

Esto se debe al tamaño de los códigos de autorización proporcionados por Azure AD B2C; el tamaño se correlaciona con el número de notificaciones solicitadas en un token. Una solución alternativa para este problema es reducir el número de notificaciones solicitadas en el token y consultar API Graph por separado para el resto de notificaciones.



<!--HONumber=Jan17_HO4-->


