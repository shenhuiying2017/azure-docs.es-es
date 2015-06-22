<properties 
	pageTitle="Introducción a la autenticación de Active Directory - ¿Qué ha ocurrido?" 
	description="Describe lo que le ha ocurrido al proyecto de Azure Active Directory en Visual Studio" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# ¿Qué le ha ocurrido a mi proyecto?

> [AZURE.SELECTOR]
> - [Introducción](vs-active-directory-dotnet-getting-started.md)
> - [¿Qué ha ocurrido?](vs-active-directory-dotnet-what-happened.md)

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
- `System`
- `System.Data`
- `System.Drawing`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####Se han agregado archivos de código a su proyecto 

Se ha agregado una clase de inicio de autenticación, `App_Start/Startup.Auth.cs`, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD. Además, se ha agregado una clase de controlador, Controllers/AccountController.cs, que contiene los métodos  `SignIn()` y  `SignOut()`. Por último, se ha agregado una vista parcial, `Views/Shared/_LoginPartial.cshtml`, que contiene un vínculo de acción para iniciar y cerrar sesión. 

#####Se ha agregado código de inicio a su proyecto.
 
Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a  `ConfigureAuth(app)`, que se ha agregado a ese método. Si no era así, se ha agregado una clase de inicio a su proyecto. 

#####Su archivo app.config o web.config tiene nuevos valores de configuración 

Se han agregado las siguientes entradas de configuración. 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Se ha creado una aplicación de Azure Active Directory (AD) 
Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente. 

[Más información acerca de Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=49--> 