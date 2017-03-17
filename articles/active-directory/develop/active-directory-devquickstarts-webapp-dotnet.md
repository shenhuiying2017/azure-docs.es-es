---
title: "Introducción a la aplicación web de .NET de Azure AD | Microsoft Docs"
description: "Creación de una aplicación web de .NET MVC que se integra con Azure AD para el inicio de sesión."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 4094759caba015b9d609b616d5099a6e109bf1d4
ms.openlocfilehash: 6ac0c3b2893b96f93bf2aeadd61b263654957477
ms.lasthandoff: 02/17/2017


---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Inicio y cierre de sesión de la aplicación web de ASP.NET con Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) facilita la externalización de la administración de identidad de las aplicaciones web al proporcionar un inicio y cierre de sesión únicos con unas cuantas líneas de código. Puede iniciar y cerrar la sesión de los usuarios en las aplicaciones web de ASP.NET mediante la implementación de Microsoft del middleware Open Web Interface for .NET (OWIN). El middleware OWIN controlado por la comunidad se incluye en .NET Framework 4.5. En este artículo se muestra cómo usar OWIN para:

* Iniciar la sesión de los usuarios en aplicaciones web mediante Azure AD como proveedor de identidades.
* Mostrar información del usuario.
* Cerrar la sesión de los usuarios en las aplicaciones.

## <a name="before-you-get-started"></a>Antes de comenzar
* Descargue el [esqueleto de la aplicación](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o el [ejemplo completado](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* También necesita a un inquilino de Azure AD en el que se va a registrar la aplicación. Si aún no tiene uno, [descubra cómo conseguirlo](active-directory-howto-tenant.md).

Cuando esté listo, siga los procedimientos descritos en las cuatro secciones siguientes.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Paso 1: Registro de la nueva aplicación en Azure AD
Para configurar la aplicación para autenticar a los usuarios, primero regístrela mediante estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en el nombre de su cuenta. En la lista **Directorio** lista, seleccione el inquilino de Active Directory donde quiere registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y seleccione **Agregar**.
5. Siga las indicaciones para crear una nueva **aplicación web o WebAPI**.
  * **Nombre**: describe la aplicación a los usuarios.
  * La **dirección URL de inicio de sesión** es la dirección URL base de su aplicación. La dirección URL predeterminada del esqueleto es https://localhost:44320/.
  * El **URI de id. de aplicación** es un identificador único para la aplicación. La convención de nomenclatura es `https://<tenant-domain>/<app-name>` (por ejemplo, `https://contoso.onmicrosoft.com/my-first-aad-app`).
6. Después de haber completado el registro, Azure AD le asigna a la aplicación un id. de aplicación único. Copie el valor de la página de aplicación para usarlo en las secciones siguientes.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Paso 2: Configuración de la aplicación para que use la canalización de autenticación OWIN
En este paso, configuraremos el middleware OWIN para usar el protocolo de autenticación OpenID Connect. Usará OWIN para emitir solicitudes de inicio y cierre de sesión, administrar sesiones de usuario, obtener información del usuario, etc.

1. Para comenzar, agregue al proyecto los paquetes NuGet del middleware OWIN mediante la Consola del Administrador de paquetes.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Para agregar una clase de inicio de OWIN al proyecto denominado `Startup.cs`, haga clic con el botón derecho en el proyecto, seleccione **Agregar**, **Nuevo elemento** y luego busque **OWIN**. El middleware de OWIN invoca el método **Configuration(...)** cuando se inicia la aplicación.
3. Cambie la declaración de clase a `public partial class Startup`. Ya hemos implementado parte de esta clase en otro archivo. En el método **Configuration(...)**, realice una llamada a **ConfgureAuth(...)** para configurar la autenticación de la aplicación.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Abra el archivo App_Start\Startup.Auth.cs y luego implemente el método **ConfigureAuth(...)**. Los parámetros que se proporcionan en *OpenIDConnectAuthenticationOptions* sirven de coordenadas de la aplicación para comunicarse con Azure AD. También debe configurar la autenticación con cookies, ya que el middleware OpenID Connect usa cookies en segundo plano.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
             });
     }
     ```

5. Abra el archivo web.config en la raíz del proyecto y luego especifique los valores de configuración en la sección `<appSettings>`.
  * `ida:ClientId`: el GUID que copió de Azure Portal en "Paso 1: Registro de la nueva aplicación en Azure AD".
  * `ida:Tenant`: el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `ida:PostLogoutRedirectUri`: el indicador que señala a Azure AD dónde se debe redirigir a un usuario después de completarse correctamente una solicitud de cierre de sesión.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Paso 3: Uso de OWIN para emitir solicitudes de inicio y cierre de sesión a Azure AD
Ahora la aplicación está correctamente configurada para comunicarse con Azure AD mediante el protocolo de autenticación OpenID Connect. OWIN se ha ocupado de todos los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de sesiones de usuario. Lo único que queda consiste en ofrecer a sus usuarios una forma de iniciar y cerrar sesión.

1. Puede usar etiquetas de autorización en los controladores para exigir que los usuarios inicien sesión para poder acceder a determinadas páginas. Para ello, abra Controllers\HomeController.cs y luego agregue la etiqueta `[Authorize]` al controlador About.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. También puede usar OWIN para emitir directamente solicitudes de autenticación desde dentro de su código. Para ello, abra Controllers\AccountController.cs. A continuación, en las acciones SignIn() y SignOut(), emita las solicitudes de desafío y cierre de sesión de OpenID Connect.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Abra Views\Shared\_LoginPartial.cshtml para mostrar al usuario los vínculos de inicio y cierre de sesión de la aplicación y para imprimir el nombre del usuario en una vista.

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
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

## <a name="step-4-display-user-information"></a>Paso 4: Visualización de la información del usuario
Cuando los usuarios se autentican con OpenID Connect, Azure AD devuelve un id_token a la aplicación que contiene "notificaciones" o aserciones sobre el usuario. Puede usar estas notificaciones para personalizar la aplicación de la manera siguiente:

1. Abra el archivo Controllers\HomeController.cs. Puede tener acceso a las solicitudes del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current` .

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Compile y ejecute la aplicación. Si aún no ha creado un nuevo usuario en el inquilino con un dominio onmicrosoft.com, ahora es el momento de hacerlo. Este es el procedimiento:

  a. Inicie sesión con ese usuario y observe cómo se refleja la identidad del usuario en la barra superior.

  b. Cierre la sesión y vuelva a iniciarla con otro usuario en su inquilino.

  c. Si se siente especialmente ambicioso, registre y ejecute otra instancia de esta aplicación (con su propio clientId) y vea el inicio de sesión único en acción.

## <a name="next-steps"></a>Pasos siguientes
Como referencia, consulte el [ejemplo completado](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (sin sus valores de configuración).

Ahora puede pasar a temas más avanzados. Por ejemplo, intente [proteger una API web con Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

