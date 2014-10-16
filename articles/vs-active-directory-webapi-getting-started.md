<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### ¿Qué ha ocurrido?

Se han agregado referencias a su proyecto

###### Referencias de paquetes de NuGet

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Referencias de .NET

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Se han agregado archivos de código a su proyecto

Se ha agregado una clase de inicio de autenticación (App\_Start/Startup.Auth.cs) a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD.

###### Se ha agregado código de inicio a su proyecto

Si ya tenía una clase de inicio en su proyecto, el método Configuration() se ha actualizado para incluir una llamada ConfigureAuth(app) que se agregó a ese método. Si no era así, se ha agregado una clase de inicio a su proyecto.

###### Su archivo app.config o web.config tiene nuevos valores de configuración.

Se han agregado las siguientes entradas de configuración.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Se ha creado una aplicación de Azure Active Directory (AD)

Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

### Introducción a Azure Active Directory (AD)

Esto es lo que puede hacer con el código que se ha agregado.

###### Requerimiento de autenticación para obtener acceso a los controladores

Todos los controladores de su proyecto cuentan ahora con el atributo Authorize. Este atributo requerirá que el usuario se autentique antes de tener acceso a las API definidas por estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.

