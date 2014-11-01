<properties title="Introducci&oacute;n a la autenticaci&oacute;n de Active Directory - &iquest;Qu&eacute; ha ocurrido?" pageTitle="Introducci&oacute;n a la autenticaci&oacute;n de Active Directory - &iquest;Qu&eacute; ha ocurrido?" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

### <span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

Se han agregado referencias.

##### Referencias de paquetes de NuGet

-   `Microsoft.IdentityModel.Protocol.Extensions`
-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.Cookies`
-   `Microsoft.Owin.Security.OpenIdConnect`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Referencias de .NET

-   `Microsoft.IdentityModel.Protocol.Extensions`
-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.Cookies`
-   `Microsoft.Owin.Security.OpenIdConnect`
-   `Owin`
-   `System`
-   `System.Data`
-   `System.Drawing`
-   `System.IdentityModel`
-   `System.IdentityModel.Tokens.Jwt`
-   `System.Runtime.Serialization`

##### Se han agregado archivos de código a su proyecto

Se ha agregado una clase de inicio de autenticación `App_Start/Startup.Auth.cs` a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD. Además, se ha agregado una clase de controlador (Controllers/AccountController.cs) que contiene los métodos `SignIn()` y `SignOut()`. Por último, se ha agregado una vista parcial (`Views/Shared/_LoginPartial.cshtml`) que contiene un vínculo de acción para SignIn/SignOut.

##### Se ha agregado código de inicio a su proyecto

Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a `ConfigureAuth(app)`. Si no era así, se ha agregado una clase de inicio a su proyecto.

##### Su archivo app.config o web.config tiene nuevos valores de configuración

Se han agregado las siguientes entradas de configuración.

     <appSettings>         <add key="ida:ClientId" value="ClientId from the new Azure AD App" />          <add key="ida:Tenant" value="Your selected Azure AD Tenant" />          <add key="ida:AADInstance" value="https://login.windows.net/{0}" />          <add key="Ida:PostLogoutRedirectURI" value="Your project start page" />      </appSettings> 

</p>
##### Se ha creado una aplicación de Azure Active Directory (AD)

Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

[Más información acerca de Azure Active Directory][Más información acerca de Azure Active Directory]

  [Tareas iniciales]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Más información acerca de Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
