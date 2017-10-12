---
title: Azure Active Directory B2C | Microsoft Docs
description: "Cómo crear una aplicación web que tiene procesos de registro e inicio de sesión, edición de perfil y restablecimiento de contraseña mediante Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: barbaraselden
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 3144ced01b524abb035dc1c6f0cdf764bec46804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Creación de una aplicación web de ASP.NET con procesos de registro e inicio de sesión, edición de perfil y restablecimiento de contraseña de Azure Active Directory B2C

En este tutorial se muestra cómo realizar las siguientes acciones:

> [!div class="checklist"]
> * Agregar características de identidad de Azure AD B2C a la aplicación web
> * Registrar la aplicación web en el directorio de Azure AD B2C
> * Crear una directiva de registro e inicio de sesión de usuario, edición de perfil y restablecimiento de contraseña para la aplicación web

## <a name="prerequisites"></a>Requisitos previos

- Debe conectar el inquilino B2C a una cuenta de Azure. Puede crear una cuenta de Azure gratuita [aquí](https://azure.microsoft.com/en-us/).
- Necesita [Microsoft Visual Studio](https://www.visualstudio.com/) o un programa similar para ver y modificar el código de ejemplo.

## <a name="create-an-azure-ad-b2c-directory"></a>Creación de un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor de todos los usuarios, aplicaciones, grupos, etc. Si aún no tiene uno, cree un directorio B2C antes de continuar con esta guía.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Debe conectar el inquilino B2C a su suscripción a Azure. Tras seleccionar **Crear**, seleccione la opción **Vincular un inquilino de Azure AD B2C existente a mi suscripción de Azure** y, a continuación, en el menú desplegable **Inquilino de Azure AD B2C**, seleccione el inquilino que desea asociar.

## <a name="create-and-register-an-application"></a>Creación y registro de una aplicación

A continuación, debe crear y registrar la aplicación en su directorio B2C. Esto proporciona información que Azure AD B2C necesita para comunicarse de forma segura con la aplicación. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

Cuando haya terminado, tendrá una API y una aplicación nativa en la configuración de la aplicación.

## <a name="create-policies-on-your-b2c-tenant"></a>Creación de directivas en el inquilino B2C

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: **registro e inicio de sesión**, **edición de perfil** y **restablecimiento de contraseña**.  Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Para cada directiva, asegúrese de seleccionar el atributo o notificación Nombre para mostrar y de copiar el nombre de su directiva para un uso posterior.

### <a name="add-your-identity-providers"></a>Incorporación de sus proveedores de identidades

En la configuración, seleccione **Proveedores de identidades** y elija Registro de nombre de usuario o Registro por correo electrónico.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Después de crear las directivas, ya podrá compilar la aplicación.

## <a name="download-the-sample-code"></a>Descarga del código de ejemplo

El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Puede clonar el ejemplo al ejecutar lo siguiente:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. El archivo de solución contiene dos proyectos: `TaskWebApp` y `TaskService`. `TaskWebApp` es la aplicación web MVC con la que el usuario interactúa. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario. En este artículo se describe únicamente la aplicación `TaskWebApp`. Para más información sobre cómo crear `TaskService` con Azure AD B2C, consulte [nuestro tutorial de aplicaciones web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Actualización del código para usar el inquilino y las directivas

Nuestro ejemplo está configurado para usar las directivas y el identificador de cliente de nuestro inquilino de demostración. Para conectarlo a su propio inquilino, debe abrir `web.config` en el proyecto `TaskWebApp` y reemplazar los valores siguientes:

* `ida:Tenant` por el nombre del inquilino
* `ida:ClientId` por el identificador de la aplicación de API web
* `ida:ClientSecret` por la clave secreta de aplicación web
* `ida:SignUpSignInPolicyId` por el nombre de directiva "Inicio de sesión o registro"
* `ida:EditProfilePolicyId` por el nombre de la directiva "Editar perfil"
* `ida:ResetPasswordPolicyId` por el nombre de la directiva "Restablecer contraseña"

## <a name="launch-the-app"></a>Inicio de la aplicación
Desde Visual Studio, inicie la aplicación. Vaya a la pestaña Lista de tareas pendientes y tenga en cuenta que la dirección URL es: https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*&client_id=*YourclientID*.....

Regístrese en la aplicación con su dirección de correo electrónico o nombre de usuario. Cierre sesión, vuelva a iniciarla, edite el perfil o restablezca la contraseña. Cierre la sesión y iníciela con otro usuario diferente. 

## <a name="add-social-idps"></a>Agregar IDP sociales

Actualmente, la aplicación solo admite procesos de registro e inicio de sesión de usuario mediante **cuentas locales**; cuentas almacenadas en su directorio B2C que usan el nombre y la contraseña de un usuario. Con Azure AD B2C, puede agregar compatibilidad con otros **proveedores de identidades** (IDP) sin cambiar el código.

Para agregar proveedores de identidades sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada proveedor de identidades que desee admitir, necesitará registrar una aplicación en ese sistema y obtener un identificador de cliente.

* [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md)

Después de agregar los proveedores de identidades a su directorio B2C, edite cada una de las tres directivas para incluir los nuevos proveedores de identidades, tal como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación.  Debería ver los proveedores de identidades nuevos agregados como opciones de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con las directivas y observar el efecto en la aplicación de ejemplo. Agregue o quite proveedores de identidades, manipule notificaciones de aplicación o cambie atributos de registro. Experimente para ver cómo se combinan las directivas, las solicitudes de autenticación y OWIN.

## <a name="sample-code-walkthrough"></a>Tutorial de código de ejemplo
En las secciones siguientes se muestra cómo está configurado el código de la aplicación de ejemplo. Puede usar dicho tutorial como guía en su futuro desarrollo de aplicaciones.

### <a name="add-authentication-support"></a>Incorporación de compatibilidad con la autenticación

Ahora puede configurar la aplicación para que use Azure AD B2C. La aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación OpenID Connect. Las solicitudes indican la experiencia del usuario que la aplicación desea ejecutar mediante la especificación de la directiva. Puede usar la biblioteca OWIN de Microsoft para enviar estas solicitudes, ejecutar directivas, administrar sesiones de usuario, etc.

#### <a name="install-owin"></a>Instalar OWIN

Para comenzar, agregue los paquetes de NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes de Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Agregar una clase de inicio de OWIN

Agregue una clase de inicio OWIN a la API llamada `Startup.cs`.  Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento**; después, busque OWIN. El middleware OWIN invocará el método `Configuration(…)` al iniciarse la aplicación.

En nuestro ejemplo, hemos cambiado la declaración de clase a `public partial class Startup` y hemos implementado la otra parte de la clase en `App_Start\Startup.Auth.cs`. En el método `Configuration`, se agrega una llamada a `ConfigureAuth`, que se define en `Startup.Auth.cs`. Después de las modificaciones, `Startup.cs` es similar a lo siguiente:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Configuración del middleware de autenticación

Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Los parámetros que proporciona en `OpenIdConnectAuthenticationOptions` sirven como coordenadas para que su aplicación se comunique con Azure AD B2C. Si no especifica ciertos parámetros, se usará el valor predeterminado. Por ejemplo, se especifica el valor `ResponseType` en el ejemplo, por lo que el valor predeterminado `code id_token` se usará en cada una de las solicitudes salientes a Azure AD B2C.

También debe configurar la autenticación de cookies. El middleware OpenID Connect usa cookies para mantener las sesiones de usuario, entre otras cosas.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

En el valor `OpenIdConnectAuthenticationOptions` anterior, definimos un conjunto de funciones de devolución de llamada para notificaciones específicas que recibe el middleware de OpenID Connect. Estos comportamientos se definen con un objeto `OpenIdConnectAuthenticationNotifications` y se almacenan en la variable `Notifications`. En el ejemplo, se definen tres devoluciones de llamada distintas en función del evento.

### <a name="using-different-policies"></a>Uso de distintas directivas

La notificación `RedirectToIdentityProvider` se desencadena cada vez que se realiza una solicitud a Azure AD B2C. En la función de devolución de llamada `OnRedirectToIdentityProvider`, protegemos la llamada saliente si deseamos usar otra directiva. Para hacer un restablecimiento de contraseña o editar un perfil, se debe usar la directiva correspondiente, como la directiva de restablecimiento de contraseña en lugar de la directiva "Registro o inicio de sesión" predeterminada.

En nuestro ejemplo, cuando un usuario desea restablecer la contraseña o editar el perfil, se agrega la directiva que se prefiere usar en el contexto de OWIN. Para ello, haga lo siguiente:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Además, puede implementar la función de devolución de llamada `OnRedirectToIdentityProvider` si hace lo siguiente:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Control de códigos de autorización

La notificación `AuthorizationCodeReceived` se desactiva cuando se recibe un código de autorización. El middleware de OpenID Connect no admite el intercambio de códigos por tokens de acceso. Puede intercambiar manualmente el código por el token en una función de devolución de llamada. Para más información, consulte la [documentación](active-directory-b2c-devquickstarts-web-api-dotnet.md) que explica cómo hacerlo.

### <a name="handling-errors"></a>Control de errores

La notificación `AuthenticationFailed` se desencadena si se produce un error de autenticación. En su método de devolución de llamada, puede controlar los errores como desee hacerlo. Sin embargo, debe agregar una comprobación del código de error `AADB2C90118`. Durante la ejecución de la directiva "Registro o inicio de sesión", el usuario tiene la oportunidad de seleccionar un vínculo **¿Ha olvidado la contraseña?**. En este evento, Azure AD B2C envía a la aplicación ese código de error indicando que la aplicación debe realizar una solicitud con la directiva de restablecimiento de contraseña en su lugar.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Enviar solicitudes de autenticación a Azure AD

Ahora la aplicación está correctamente configurada para comunicarse con Azure AD B2C mediante el protocolo de autenticación OpenID Connect. OWIN administra los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD B2C y mantenimiento de la sesión de usuario. Todo lo que queda es iniciar cada flujo de usuarios.

Cuando un usuario selecciona **Registrarse o Iniciar sesión**, **Editar perfil** o **Restablecer contraseña** en la aplicación web, se invoca la acción asociada en `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

También puede usar OWIN para cerrar la sesión del usuario de la aplicación. En `Controllers\AccountController.cs`, tenemos:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Además de invocar explícitamente una directiva, puede utilizar una etiqueta `[Authorize]` en los controladores que ejecuta una directiva si el usuario no ha iniciado sesión. Abra `Controllers\HomeController.cs` y agregue la etiqueta `[Authorize]` al controlador de notificaciones.  OWIN selecciona la última directiva configurada cuando se ejecuta la etiqueta `[Authorize]`.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Mostrar información de usuario

Cuando se autentican los usuarios mediante OpenID Connect, Azure AD B2Cdevuelve un token de identificador a la aplicación que contiene **notificaciones**. Se trata de aserciones sobre el usuario. Puede usar notificaciones para personalizar su aplicación.

Abra el archivo `Controllers\HomeController.cs` . Puede acceder a las notificaciones del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current` .

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Puede tener acceso a cualquier notificación que recibe su aplicación de la misma manera.  Tiene disponible una lista de todas las notificaciones que recibe la aplicación en la página **Reclamaciones** .
