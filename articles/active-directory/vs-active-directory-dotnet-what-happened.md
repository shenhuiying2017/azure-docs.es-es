<properties 
	pageTitle="Introducción a la autenticación de Active Directory - ¿Qué ha ocurrido?" 
	description="Describe lo que le ha ocurrido al proyecto de Azure Active Directory en Visual Studio" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# ¿Qué le ha ocurrido a mi proyecto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>
 
Se han agregado referencias.

#####Referencias de paquetes de NuGet

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Referencias de .NET

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####Se han agregado archivos de código a su proyecto 

Se ha agregado una clase de inicio de autenticación, `App_Start/Startup.Auth.cs`, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD. Además, se ha agregado una clase de controlador, Controllers/AccountController.cs, que contiene los métodos `SignIn()` y `SignOut()`. Por último, se ha agregado una vista parcial, `Views/Shared/_LoginPartial.cshtml`, que contiene un vínculo de acción para iniciar y cerrar sesión.

#####Se ha agregado código de inicio a su proyecto.
 
Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a `ConfigureAuth(app)`. Si no era así, se ha agregado una clase de inicio a su proyecto.

#####Su archivo app.config o web.config tiene nuevos valores de configuración 

Se han agregado las siguientes entradas de configuración. <pre> `<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/" /> 
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Se ha creado una aplicación de Azure Active Directory (AD) 
Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

###¿Qué otros cambios se realizaron en el proyecto porque activé *Leer datos de directorio*?
Se han agregado referencias adicionales.

#####Referencias de paquetes de NuGet adicionales

- `EntityFramework`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `System.Spatial`

#####Referencias de .NET adicionales

- `EntityFramework`
- `EntityFramework.SqlServer`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms`
- `System.Spatial`

#####Se han agregado archivos de código adicionales a su proyecto 

Se han agregado dos archivos para admitir el almacenamiento en caché de los tokens: `Models\ADALTokenCache.cs` y `Models\ApplicationDbContext.cs`. Se han agregado un controlador y una vista adicionales para ilustrar el acceso a la información de perfil de usuario mediante las API Graph de Azure. Estos archivos son `Controllers\UserProfileController.cs` y `Views\UserProfile\Index.cshtml`.

#####Se ha agregado código de inicio adicional al proyecto.
 
En el archivo `startup.auth.cs`, se agregó un nuevo objeto `OpenIdConnectAuthenticationNotifications` al miembro `Notifications` de `OpenIdConnectAuthenticationOptions`. Esto permite recibir el código de OAuth y cambiarlo por un token de acceso.

#####Se realizaron cambios adicionales en app.config o web.config

Se han agregado las siguientes entradas de configuración adicionales. <pre> `<appSettings>
	    <add key="Ida:ClientSecret" value="Your Azure AD App's new client secret" /> 
	</appSettings>` </pre>

Se han agregado las siguientes secciones de configuración y la cadena de conexión. <pre> `<configSections>
	    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
	    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
	</configSections>
	<connectionStrings>
	    <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
	</connectionStrings>
	<entityFramework>
	    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
	      <parameters>
	        <parameter value="mssqllocaldb" />
	      </parameters>
	    </defaultConnectionFactory>
	    <providers>
	      <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
	    </providers>
	</entityFramework>`</pre>


#####Se ha actualizado la aplicación Azure Active Directory
La aplicación Azure Active Directory se actualizó para incluir el permiso *Leer datos de directorio* y se creó una clave adicional que se utilizó posteriormente como *ClientSecret* en el archivo `web.config`.

[Más información acerca de Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=62-->