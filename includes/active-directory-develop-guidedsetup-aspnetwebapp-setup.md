
## <a name="set-up-your-project"></a>Configurar su proyecto

En esta sección se muestran los pasos necesarios para instalar y configurar la canalización de autenticación a través del middleware OWIN en un proyecto de ASP.NET con OpenID Connect. 

> ¿Prefiere descargar este proyecto de Visual Studio de ejemplo en su lugar? [Descargue un proyecto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) y vaya al [paso de configuración](#create-an-application-express) para configurar el código de ejemplo antes de ejecutarlo.

<!--start-collapse-->
> ### <a name="create-your-aspnet-project"></a>Creación del proyecto de ASP.NET

> 1. En Visual Studio: `File` > `New` > `Project`<br/>
> 2. En *Visual C#\Web*, seleccione `ASP.NET Web Application (.NET Framework)`.
> 3. Asigne un nombre a la aplicación y haga clic en *Aceptar*.
> 4. Seleccione `Empty` y active la casilla de verificación para agregar referencias de `MVC`.
<!--end-collapse-->

## <a name="add-authentication-components"></a>Adición de componentes de autenticación

1. En Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Agregue los *paquetes de NuGet de middleware de OWIN* escribiendo lo siguiente en la ventana de la Consola del Administrador de paquetes:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Acerca de estas bibliotecas

>Las bibliotecas anteriores habilitan el inicio de sesión único (SSO) utilizando OpenID Connect a través de la autenticación basada en cookies. Una vez que se completa la autenticación y se envía el token que representa al usuario a la aplicación, el middleware de OWIN crea una cookie de sesión. El explorador utiliza a continuación esta cookie en solicitudes posteriores para que el usuario no tenga que volver a escribir su contraseña, y no es necesaria ninguna comprobación adicional.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configuración de la canalización de autenticación
Los pasos siguientes se utilizan para crear una clase de inicio del middleware de OWIN para configurar la autenticación de OpenID Connect. Esta clase se ejecutará automáticamente cuando se inicie el proceso de IIS.

> Si el proyecto no tiene un archivo `Startup.cs` en la carpeta raíz:<br/>
> 1. Haga clic con el botón secundario en la carpeta raíz del proyecto:  >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Asígnele el nombre `Startup.cs`.

> Asegúrese de que la clase seleccionada es una clase de inicio de OWIN y no una clase estándar de C#. Para confirmarlo, compruebe si ve `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` encima del espacio de nombres.


1. Agregue las referencias *OWIN* y *Microsoft.IdentityModel* a `Startup.cs`:

```csharp
using Microsoft.Owin;
using Owin;
using Microsoft.IdentityModel.Protocols;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using Microsoft.Owin.Security.Notifications;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Reemplace la clase de inicio por el código siguiente:
</li>
</ol>

```csharp
public class Startup
{        
    // The Client ID is used by the application to uniquely identify itself to Azure AD.
    string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

    // RedirectUri is the URL where the user will be redirected to after they sign in.
    string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

    // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
    static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

    // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
    string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

    /// <summary>
    /// Configure OWIN to use OpenIdConnect 
    /// </summary>
    /// <param name="app"></param>
    public void Configuration(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Sets the ClientId, authority, RedirectUri as obtained from web.config
                ClientId = clientId,
                Authority = authority,
                RedirectUri = redirectUri,
                // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                PostLogoutRedirectUri = redirectUri,
                Scope = OpenIdConnectScopes.OpenIdProfile,
                // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                ResponseType = OpenIdConnectResponseTypes.IdToken,
                // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters() { ValidateIssuer = false },
                // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    AuthenticationFailed = OnAuthenticationFailed
                }
            }
        );
    }

    /// <summary>
    /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
    /// </summary>
    /// <param name="context"></param>
    /// <returns></returns>
    private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
    {
        context.HandleResponse();
        context.Response.Redirect("/?errormessage=" + context.Exception.Message);
        return Task.FromResult(0);
    }
}

```
<!--start-collapse-->
> ### <a name="more-information"></a>Más información

> Los parámetros que se proporcionan en *OpenIDConnectAuthenticationOptions* sirven de coordenadas para que la aplicación se comunique con Azure AD. Dado el que middleware de OpenID Connect usa cookies en segundo plano, también debe configurar la autenticación con cookies como muestra el código siguiente. El valor *ValidateIssuer* indica a OpenIdConnect que no restrinja el acceso a una organización específica.
<!--end-collapse-->

