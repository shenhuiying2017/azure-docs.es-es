<properties
	pageTitle="Aplicación web .NET del modelo de aplicación v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación web de .NET MVC con la que los usuarios pueden iniciar sesión utilizando tanto la cuenta personal de Microsoft como sus cuentas profesionales o educativas."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Vista previa del modelo de aplicaciones v2.0: agregar inicio de sesión a una aplicación web .NET MVC

Con el modelo de aplicaciones v2.0 puede agregar rápidamente la autenticación para sus aplicaciones web compatibles tanto con las cuentas personales de Microsoft como con las cuentas profesionales o educativas. En las aplicaciones web ASP.NET puede realizar esto con el OWIN middleware de Microsoft incluido en .NET Framework 4.5.

  >[AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio de Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

 Aquí usaremos OWIN para: - Iniciar la sesión del usuario en la aplicación con Azure AD y el modelo de aplicaciones v2.0. -Mostrar alguna información sobre el usuario. - Cerrar la sesión del usuario en la aplicación.

Para ello, deberá hacer lo siguiente:

1. Registrar una aplicación.
2. Configurar la aplicación para usar la canalización de autenticación OWIN.
3. Use OWIN para emitir solicitudes de inicio y cierre de sesión a Azure AD.
4. Imprima datos sobre el usuario.

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet). Para poder continuar, puede [descargar el esqueleto de la aplicación como .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Web** platform for your app.
- Enter the correct **Redirect URI**. The redirect uri indicates to Azure AD where authentication responses should be directed - the default for this tutorial is `https://localhost:44326/`.

## 2. Set up your app to use the OWIN authentication pipeline
Here, we'll configure the OWIN middleware to use the OpenID Connect authentication protocol.  OWIN will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `web.config` file in the root of the project, and enter your app's configuration values in the `<appSettings>` section.
    -	The `ida:ClientId` is the **Application Id** assigned to your app in the registration portal.
    -	The `ida:RedirectUri` is the **Redirect Uri** you entered in the portal.

-	Next, add the OWIN middleware NuGet packages to the project using the Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect PM> Install-Package Microsoft.Owin.Security.Cookies PM> Install-Package Microsoft.Owin.Host.SystemWeb ```

-	Agregue una "Clase de inicio OWIN" al proyecto denominado `Startup.cs`. Haga clic con el botón derecho en el proyecto **Agregar** --> **Nuevo elemento** --> Busque "OWIN". El middleware OWIN invocará el método `Configuration(...)` al iniciarse la aplicación.
-	Cambie la declaración de clase a `public partial class Startup` (ya hemos implementado parte de esta clase para usted en otro archivo). En el método `Configuration(...)`, realice una llamada a ConfigureAuth(...) para configurar la autenticación para su aplicación web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Los parámetros que proporciona en `OpenIdConnectAuthenticationOptions` servirán como coordenadas para que su aplicación se comunique con Azure AD. También tendrá que configurar la autenticación con cookies (el middleware OpenID Connect usa cookies debajo de las portadas).

```C#
public void ConfigureAuth(IAppBuilder app)
			 {
					 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

					 app.UseCookieAuthentication(new CookieAuthenticationOptions());

					 app.UseOpenIdConnectAuthentication(
							 new OpenIdConnectAuthenticationOptions
							 {
									 // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
									 // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
									 // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

									 ClientId = clientId,
									 Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
									 RedirectUri = redirectUri,
									 Scope = "openid",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## 3\. Use OWIN para emitir solicitudes de inicio y cierre de sesión a Azure AD
Ahora la aplicación está correctamente configurada para comunicarse con el extremo v2.0 mediante el protocolo de autenticación de OpenID Connect. OWIN se ha ocupado de todos los detalles feos de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Lo único que queda consiste en ofrecer a sus usuarios una forma de iniciar y cerrar sesión.

- Puede usar etiquetas Autorizar en sus controladores para solicitar que el usuario inicie sesión antes de tener acceso a una página determinada. Abra `Controllers\HomeController.cs` y agregue la etiqueta `[Authorize]` al controlador Acerca de.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	También puede usar OWIN para emitir directamente solicitudes de autenticación desde dentro de su código. Abra `Controllers\AccountController.cs`. En las acciones SignIn() y SignOut(), emita un concurso de OpenID Connect y solicitudes de cierre de sesión, respectivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	Ahora, abra `Views\Shared\_LoginPartial.cshtml`. Aquí mostrará al usuario los vínculos de inicio y cierre de sesión de su aplicación e imprimirá el nombre del usuario en una vista.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## 4\. Mostrar información de usuario
Al autenticar usuarios con OpenID Connect, el extremo v2.0 devuelve un id\_token a la aplicación que contiene [notificaciones](active-directory-v2-tokens.md#id_tokens) o aserciones sobre el usuario. Puede usar estas notificaciones para personalizar su aplicación:

- Abra el archivo `Controllers\HomeController.cs`. Puede tener acceso a las solicitudes del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current`.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

Por último, compile y ejecute su aplicación. Inicie sesión con una cuenta personal de Microsoft o con una cuenta profesional o educativa y observe cómo se refleja la identidad del usuario en la barra de navegación superior. Ahora dispone de una aplicación web protegida mediante protocolos estándar del sector que pueden autenticar a los usuarios tanto con sus cuentas personales como con sus cuentas profesionales/educativas.

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona como .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip); también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## Pasos siguientes

Ahora puede pasar a temas más avanzados. También puede probar lo siguiente:

[Proteger una API web con el modelo de aplicaciones v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obtener recursos adicionales, consulte: - [la vista previa del modelo de aplicaciones v2.0 >>](active-directory-appmodel-v2-overview.md) - [la etiqueta "azure-active-directory" StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=Sept15_HO3-->