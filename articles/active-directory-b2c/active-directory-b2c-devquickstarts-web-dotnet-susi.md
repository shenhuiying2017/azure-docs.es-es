---
title: Azure Active Directory B2C | Microsoft Docs
description: Cómo crear una aplicación web que tiene procesos de registro, inicio de sesión y restablecimiento de contraseña mediante Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure AD B2C: suscripción e inicio de sesión en aplicaciones web ASP.NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede agregar eficaces características de administración de identidades de autoservicio a su aplicación web en unos cuantos pasos. En este artículo se describe cómo crear una aplicación web de ASP.NET que incluya el registro de usuario, el inicio de sesión y el restablecimiento de contraseña. La aplicación incluirá compatibilidad para el registro y el inicio de sesión mediante un nombre de usuario o correo electrónico, y mediante el uso de cuentas sociales como Facebook y Google.

[Nuestro otro tutorial web de .NET](active-directory-b2c-devquickstarts-web-dotnet.md) es distinto a este, ya que utiliza una directiva de [registro o inicio de sesión](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy) para proporcionar estos procesos con un solo botón en lugar de dos (uno para el registro y otro para el inicio de sesión). En un resumen, una directiva de registro o inicio de sesión permite a los usuarios iniciar sesión con una cuenta que ya tengan (en caso de que dispongan de una), o bien crear una nuevo si es la primera vez que utilizan la aplicación.

## Obtener un directorio de Azure AD B2C
Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación
A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir una **aplicación web o una API web** en la aplicación.
* Escriba `https://localhost:44316/` como **URI de redireccionamiento**. Es la dirección URL predeterminada para este ejemplo de código.
* Escribir el **Id. de aplicación** asignado a la aplicación. Lo necesitará más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas
En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Este ejemplo de muestra contiene dos experiencias de identidad: **registro e inicio de sesión** y **restablecimiento de contraseña**. Es preciso que cree una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Cuando cree las dos directivas, asegúrese de realizar estos pasos:

* Elegir **User ID sign-up** (Registro de id. de usuario) o **Email sign-up** (Registro de correo electrónico) en la hoja de proveedores de identidades.
* Elegir el **nombre para mostrar** y los atributos de registro restantes en la directiva de registro e inicio de sesión.
* Elegir la notificación de **Nombre para mostrar** como una notificación de aplicación en cada directiva. Puede elegir también otras notificaciones.
* Copiar el **nombre** de cada directiva después de crearla. Necesitará esos nombres de directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las dos directivas, ya podrá compilar la aplicación.

## Descargar el código y configurar la autenticación
El código de esta muestra [se conserva en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

La muestra completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar.

Su aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación HTTP que especifican la directiva que desea ejecutar como parte de la solicitud. En el caso de las aplicaciones web. NET, puede usar la biblioteca OWIN de Microsoft para enviar solicitudes de autenticación de OpenID Connect, ejecutar directivas, administrar sesiones de usuario, etc.

Para comenzar, agregue los paquetes de NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes de Visual Studio.

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

Luego, abra el archivo `web.config` en la raíz del proyecto y escriba los valores de configuración de su aplicación en la sección `<appSettings>` reemplazando los siguientes por los suyos propios. Puede dejar los valores `ida:RedirectUri` y `ida:AadInstance` sin modificar.

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[!INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ahora, agregue una clase de inicio de OWIN al proyecto llamado "`Startup.cs`". Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento**; después, busque "OWIN". Cambie la declaración de clase a `public partial class Startup`. Hemos implementado parte de esta clase automáticamente en otro archivo. El middleware OWIN invocará el método `Configuration(...)` al iniciarse la aplicación. En este método, realice una llamada a `ConfigureAuth(...)`, donde se configura la autenticación para la aplicación.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Los parámetros que proporciona en `OpenIdConnectAuthenticationOptions` sirven como coordenadas para que su aplicación se comunique con Azure AD. También debe configurar la autenticación de cookies. El middleware OpenID Connect usa cookies para mantener las sesiones de usuario, entre otras cosas.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                SecurityTokenValidated = OnSecurityTokenValidated,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
...
```

## Enviar solicitudes de autenticación a Azure AD
Ahora la aplicación está correctamente configurada para comunicarse con Azure AD B2C mediante el protocolo de autenticación OpenID Connect. OWIN se ha ocupado de todos los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Todo lo que queda es iniciar cada flujo de usuarios.

Cuando un usuario selecciona **Iniciar sesión** u **¿Olvidó su contraseña?** o en la aplicación web, se invoca la acción asociada en `Controllers\AccountController.cs`. En cada caso, puede usar métodos integrados de OWIN para desencadenar la directiva correcta:

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

Durante la ejecución del registro o del inicio de sesión en la directiva, el usuario tiene la oportunidad de hacer clic en un vínculo **¿Olvidó su contraseña?**. En este caso, Azure AD B2C enviará a la aplicación un mensaje de error específico que indica que debe ejecutar una directiva de restablecimiento de contraseña. Puede capturar este error en `Startup.Auth.cs` con la notificación `AuthenticationFailed`:

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


Además de invocar explícitamente una directiva, puede utilizar una etiqueta `[Authorize]` en los controladores que ejecutará una directiva si el usuario no ha iniciado sesión. Abra `Controllers\HomeController.cs` y agregue la etiqueta `[Authorize]` al controlador de notificaciones. OWIN seleccionará la última directiva configurada cuando se ejecuta la etiqueta `[Authorize]`.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

También puede usar OWIN para cerrar la sesión del usuario de la aplicación. En `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void Logout()
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

## Mostrar información de usuario
Cuando se autentican los usuarios mediante OpenID Connect, Azure AD devuelve un token de identificador a la aplicación que contiene **notificaciones**. Se trata de aserciones sobre el usuario. Puede usar notificaciones para personalizar su aplicación.

Abra el archivo `Controllers\HomeController.cs`. Puede acceder a las notificaciones del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current`.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Puede tener acceso a cualquier notificación que recibe su aplicación de la misma manera. Tiene disponible una lista de todas las notificaciones que recibe la aplicación en la página **Reclamaciones**.

## Ejecutar la aplicación de ejemplo
Por último, puede compilar y ejecutar la aplicación. Regístrese en la aplicación con una dirección de correo electrónico o un nombre de usuario. Cierre la sesión y vuelva a iniciarla como el mismo usuario. Edite el perfil de ese usuario. Cierre la sesión y regístrese como otro usuario. Observe que la información que se muestra en la pestaña **Reclamaciones** se corresponde con la información configurada en las directivas.

## Agregar IDP sociales
Actualmente, la aplicación admite solo registros e inicios de sesión de usuarios mediante **cuentas locales**. Se trata de cuentas almacenadas en el directorio B2C que utilizan un nombre de usuario y una contraseña. Con Azure AD B2C, puede agregar compatibilidad con otros **proveedores de identidades** (IDP) sin cambiar el código.

Para agregar proveedores de identidades sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada proveedor de identidades que desee admitir, necesitará registrar una aplicación en ese sistema y obtener un identificador de cliente.

* [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md)

Después de agregar los proveedores de identidades a su directorio B2C, tendrá que editar cada una de las tres directivas para incluir los nuevos proveedores de identidades, tal y como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación. Debería ver los proveedores de identidades nuevos agregados como opciones de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con las directivas y observar el efecto en la aplicación de ejemplo. Agregue o quite proveedores de identidades, manipule notificaciones de aplicación o cambie atributos de registro. Experimente para ver cómo se combinan las directivas, las solicitudes de autenticación y OWIN.

Como referencia, se proporciona el ejemplo completado (sin sus valores de configuración) [en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip). También puede clonarlo desde GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->