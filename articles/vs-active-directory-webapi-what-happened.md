<properties 
	pageTitle="" 
	description="Describe los cambios que se han producido en el proyecto de Visual Studio después de ejecutar el asistente para Azure Active Directory." 
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

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-active-directory-webapi-getting-started/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-active-directory-webapi-what-happened/)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

Se han agregado referencias. 

#####Referencias de paquetes de NuGet 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Referencias de .NET 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####Se han agregado archivos de código a su proyecto 

Se ha agregado una clase de inicio de autenticación, **App_Start/Startup.Auth.cs**, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD. 

#####Se ha agregado código de inicio a su proyecto. 

Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada `ConfigureAuth(app)` que se agregó a ese método. Si no era así, se ha agregado una clase de inicio a su proyecto. 


#####Su archivo app.config o web.config tiene nuevos valores de configuración.

Se han agregado las siguientes entradas de configuración. 
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###Se ha creado una aplicación de Azure AD. 

Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.


[Más información acerca de Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
