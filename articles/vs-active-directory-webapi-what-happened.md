<properties title="Introducci&oacute;n a la autenticaci&oacute;n de Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

### <span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

Se han agregado referencias.

##### Referencias de paquetes de NuGet

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Referencias de .NET

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Se han agregado archivos de código a su proyecto

Se ha agregado una clase de inicio de autenticación, **App\_Start/Startup.Auth.cs**, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD.

##### Se ha agregado código de inicio a su proyecto

Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a `ConfigureAuth(app)`. Si no era así, se ha agregado una clase de inicio a su proyecto.

##### Su archivo app.config o web.config tiene nuevos valores de configuración.

Se han agregado las siguientes entradas de configuración.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Se ha creado una aplicación de Azure AD

Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

[Más información acerca de Azure Active Directory][Más información acerca de Azure Active Directory]

  [Tareas iniciales]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Más información acerca de Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
