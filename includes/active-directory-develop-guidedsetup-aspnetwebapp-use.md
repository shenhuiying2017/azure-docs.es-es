
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Agregar un controlador para controlar las solicitudes de inicio de sesión y cierre de sesión

En este paso se muestra cómo crear un controlador para exponer los métodos de inicio de sesión y cierre de sesión.

1.  Haga clic con el botón derecho en la carpeta `Controllers` y seleccione `Add` > `Controller`.
2.  Seleccione `MVC (.NET version) Controller – Empty`.
3.  Haga clic en *Agregar*.
4.  Asígnele el nombre `HomeController` y haga clic en *Aceptar*.
5.  Agregue referencias de *OWIN* a la clase:

```csharp
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
```
<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Agregue los dos métodos siguientes para controlar el inicio de sesión y el cierre de sesión en el controlador iniciando un desafío de autenticación a través de código:
</li>
</ol>

```csharp
/// <summary>
/// Send an OpenID Connect sign-in request.
/// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
/// </summary>
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
    HttpContext.GetOwinContext().Authentication.SignOut(
            OpenIdConnectAuthenticationDefaults.AuthenticationType,
            CookieAuthenticationDefaults.AuthenticationType);
}
```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Creación de la página principal de la aplicación para que los usuarios inicien sesión mediante un botón de inicio de sesión

En Visual Studio, cree otra vista para agregarle el botón de inicio de sesión y mostrar información del usuario tras la autenticación:

1.  Haga clic con el botón derecho en la carpeta `Views\Home` y seleccione `Add View`.
2.  Asígnele el nombre `Index`.
3.  Agregue el código HTML siguiente, que incluye el botón de inicio de sesión, al archivo:

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Guide</title>
</head>
<body>
@if (!Request.IsAuthenticated)
{
    <!-- If the user is not authenticated, display the sign-in button -->
    <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
        <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
        <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
        <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
        <rect x="150" y="129" width="122" height="122" fill="#F35325" />
        <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
        <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
        <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
        <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
        </svg>
    </a>
}
else
{
    <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
    <br /><br />
    @Html.ActionLink("See Your Claims", "Index", "Claims")
    <br /><br />
    @Html.ActionLink("Sign out", "SignOut", "Home")
}
@if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
{
    <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
}
</body>
</html>
```
<!--start-collapse-->
### <a name="more-information"></a>Más información
> Esta página agrega un botón de inicio de sesión en formato SVG con un fondo negro:<br/>![Iniciar sesión en Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Para ver más botones de inicio de sesión, vaya a [esta página](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Directrices de personalización de marca").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Agregar un controlador para mostrar las notificaciones del usuario
Este controlador muestra los usos del atributo `[Authorize]` para proteger un controlador. Este atributo restringe el acceso al controlador permitiendo únicamente usuarios autenticados. El siguiente código utiliza el atributo para mostrar notificaciones de usuario que se han recuperado como parte del inicio de sesión.

1.  Haga clic con el botón derecho en la carpeta `Controllers`: `Add` > `Controller`.
2.  Seleccione `MVC {version} Controller – Empty`.
3.  Haga clic en *Agregar*.
4.  Asígnele el nombre `ClaimsController`.
5.  Reemplace el código de la clase de controlador por el código siguiente; el atributo `[Authorize]` se agrega a la clase:

```csharp
[Authorize]
public class ClaimsController : Controller
{
    /// <summary>
    /// Add user's claims to viewbag
    /// </summary>
    /// <returns></returns>
    public ActionResult Index()
    {
        var claimsPrincipalCurrent = System.Security.Claims.ClaimsPrincipal.Current;
        //You get the user’s first and last name below:
        ViewBag.Name = claimsPrincipalCurrent.FindFirst("name").Value;

        // The 'preferred_username' claim can be used for showing the username
        ViewBag.Username = claimsPrincipalCurrent.FindFirst("preferred_username").Value;

        // The subject claim can be used to uniquely identify the user across the web
        ViewBag.Subject = claimsPrincipalCurrent.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier).Value;

        // TenantId is the unique Tenant Id - which represents an organization in Azure AD
        ViewBag.TenantId = claimsPrincipalCurrent.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;

        return View();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Más información
> Debido al uso del atributo `[Authorize]`, todos los métodos de este controlador solo pueden ejecutarse si el usuario está autenticado. Si el usuario no se ha autenticado e intenta acceder al controlador, OWIN iniciará un desafío de autenticación y obligará al usuario a autenticarse. El código anterior busca en la colección de notificaciones de la instancia de `ClaimsPrincipal.Current` los atributos de usuario específicos incluidos en el token del usuario. Estos atributos incluyen el nombre completo del usuario y el nombre de usuario, así como el firmante del identificador de usuario global. También contienen el *Id. del inquilino*, que representa el identificador de la organización del usuario. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Creación de una vista que muestre las notificaciones del usuario

En Visual Studio, cree otra vista para mostrar las notificaciones del usuario en una página web:

1.  Haga clic con el botón derecho en la carpeta `Views\Claims` y en: `Add View`.
2.  Asígnele el nombre `Index`.
3.  Agregue el código HTML siguiente al archivo:

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Sample</title>
    <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
</head>
<body style="padding:50px">
    <h3>Main Claims:</h3>
    <table class="table table-striped table-bordered table-hover">
        <tr><td>Name</td><td>@ViewBag.Name</td></tr>
        <tr><td>Username</td><td>@ViewBag.Username</td></tr>
        <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
        <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
    </table>
    <br />
    <h3>All Claims:</h3>
    <table class="table table-striped table-bordered table-hover table-condensed">
    @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
    {
        <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
    }
</table>
    <br />
    <br />
    @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
</body>
</html>
```
