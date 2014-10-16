<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### ¿Qué ha ocurrido?

Se han agregado referencias a su proyecto

###### Referencias de paquetes de NuGet

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### Referencias de .NET

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### Se han agregado archivos de código a su proyecto

Se ha agregado una clase de inicio de autenticación (App\_Start/Startup.Auth.cs) a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD. Además, se ha agregado una clase de controlador (Controllers/AccountController.cs) que contiene los métodos SignIn() y SignOut(). Por último, se ha agregado una vista parcial (Views/Shared/\_LoginPartial.cshtml) que contiene un vínculo de acción para SignIn/SignOut.

###### Se ha agregado código de inicio a su proyecto

Si ya tenía una clase de inicio en su proyecto, el método Configuration() se ha actualizado para incluir una llamada ConfigureAuth(app) que se agregó a ese método. Si no era así, se ha agregado una clase de inicio a su proyecto.

###### Su archivo app.config o web.config tiene nuevos valores de configuración

Se han agregado las siguientes entradas de configuración.

     <appSettings>         <add key="ida:ClientId" value="ClientId from the new Azure AD App" />          <add key="ida:Tenant" value="Your selected Azure AD Tenant" />          <add key="ida:AADInstance" value="https://login.windows.net/{0}" />          <add key="Ida:PostLogoutRedirectURI" value="Your project start page" />      </appSettings> 

</p>
###### Se ha creado una aplicación de Azure Active Directory (AD)

Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

## Introducción a Azure Active Directory (AD)

Esto es lo que puede hacer con el código que se ha agregado.

###### Requerimiento de autenticación para obtener acceso a los controladores

Todos los controladores de su proyecto cuentan ahora con el atributo [Authorize]. Este atributo requerirá que el usuario se autentique antes de tener acceso a estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador.

###### Incorporación de controles SignIn / SignOut

Para agregar controles SignIn/SignOut a su vista, puede utiliza la vista parcial \_LoginPartial.cshtml para agregar la funcionalidad a una de sus vistas. Aquí se presenta un ejemplo de la funcionalidad agregada a la vista estándar 

     <!DOCTYPE html>      <html>      <head>          <meta charset="utf-8" />          <meta name="viewport" content="width=device-width, initial-scale=1.0">          <title>@ViewBag.Title - My ASP.NET Application</title>          @Styles.Render("~/Content/css")          @Scripts.Render("~/bundles/modernizr")      </head>      <body>          <div class="navbar navbar-inverse navbar-fixed-top">              <div class="container">                  <div class="navbar-header">                      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">                          <span class="icon-bar"></span>                          <span class="icon-bar"></span>                          <span class="icon-bar"></span>                      </button>                      @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })                  </div>                  <div class="navbar-collapse collapse">                      <ul class="nav navbar-nav">                          <li>@Html.ActionLink("Home", "Index", "Home")</li>                          <li>@Html.ActionLink("About", "About", "Home")</li>                          <li>@Html.ActionLink("Contact", "Contact", "Home")</li>                      </ul>                      @Html.Partial("_LoginPartial")                  </div>              </div>          </div>          <div class="container body-content">              @RenderBody()              <hr />              <footer>                  <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>              </footer>          </div>          @Scripts.Render("~/bundles/jquery")          @Scripts.Render("~/bundles/bootstrap")          @RenderSection("scripts", required: false)      </body>      </html> 

</p>

