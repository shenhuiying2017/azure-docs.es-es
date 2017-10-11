---
title: Cambios realizados en un proyecto de MVC al conectarse a Azure AD | Microsoft Docs
description: Describe lo que sucede a su proyecto de MVC cuando se conecta a Azure AD mediante los servicios conectados de Visual Studio.
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 095411a7fc854f4dce11921adb0f57c5389a8e13
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>¿Qué ha ocurrido a mi proyecto MVC (servicio conectado a Azure Active Directory de Visual Studio)?
> [!div class="op_single_selector"]
> * [Introducción](vs-active-directory-dotnet-getting-started.md)
> * [¿Qué ha ocurrido?](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Se han agregado referencias
### <a name="nuget-package-references"></a>Referencias de paquetes de NuGet
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Referencias de .NET
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Se ha agregado el código
### <a name="code-files-were-added-to-your-project"></a>Se han agregado archivos de código a su proyecto
Se ha agregado una clase de inicio de autenticación, **App_Start/Startup.Auth.cs**, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD. Además, se ha agregado una clase de controlador (Controllers/AccountController.cs) que contiene los métodos **SignIn()** y **SignOut()**. Por último, se ha agregado una vista parcial **Views/Shared/_LoginPartial.cshtml** que contiene un vínculo de acción para SignIn/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Se ha agregado código de inicio a su proyecto.
Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a **ConfigureAuth(app)**. Si no era así, se ha agregado una clase de inicio a su proyecto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Su archivo app.config o web.config tiene nuevos valores de configuración
Se han agregado las siguientes entradas de configuración.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Se ha creado una aplicación de Azure Active Directory (AD)
Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Si he activado la *deshabilitación de la autenticación de cuentas de usuario individuales*, ¿qué otros cambios se realizaron en mi proyecto?
Se han quitado las referencias al paquete NuGet y se han quitado los archivos y se ha realizado una copia de seguridad de los mismos. Según el estado del proyecto, tendrá que quitar referencias o archivos adicionales manualmente o modificar el código, según corresponda.

### <a name="nuget-package-references-removed-for-those-present"></a>Referencias al paquete NuGet quitadas (si existen)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Se ha realizado una copia de seguridad de los archivos y se han eliminado (si existen)
Se ha realizado una copia de seguridad de cada uno de los siguientes archivos y se han eliminado del proyecto. Los archivos de copia de seguridad se encuentran en una carpeta 'Backup' en la raíz del directorio del proyecto.

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Copia de seguridad de los archivos de código (si existen)
Se realizó una copia de seguridad de cada uno de los siguientes archivos antes de ser reemplazados. Los archivos de copia de seguridad se encuentran en una carpeta 'Backup' en la raíz del directorio del proyecto.

* **Startup.cs**
* **App_Start\Startup.Auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Si activé *Leer datos de directorio*, ¿qué otros cambios se realizaron en mi proyecto?
Se han agregado referencias adicionales.

### <a name="additional-nuget-package-references"></a>Referencias de paquetes de NuGet adicionales
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>Referencias de .NET adicionales
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>Se han agregado archivos de código adicionales a su proyecto
Se agregaron dos archivos para admitir el almacenamiento en caché de tokens: **Models\ADALTokenCache.cs** y **Models\ApplicationDbContext.cs**.  Se han agregado un controlador y una vista adicionales para ilustrar el acceso a la información de perfil de usuario mediante las API Graph de Azure.  Estos archivos son **Controllers\UserProfileController.cs** y **Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>Se ha agregado código de inicio adicional al proyecto.
En el archivo **startup.auth.cs**, se agregó un nuevo objeto **OpenIdConnectAuthenticationNotifications** al miembro **Notifications** de **OpenIdConnectAuthenticationOptions**.  Esto permite recibir el código de OAuth y cambiarlo por un token de acceso.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Se realizaron cambios adicionales en app.config o web.config
Se han agregado las siguientes entradas de configuración adicionales.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Se han agregado las siguientes secciones de configuración y la cadena de conexión.

    <configSections>
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
    </entityFramework>


### <a name="your-azure-active-directory-app-was-updated"></a>Se ha actualizado la aplicación Azure Active Directory
La aplicación Azure Active Directory se actualizó para incluir el permiso *Leer datos de directorio* y se creó una clave adicional que luego se usó como *ida:ClientSecret* en el archivo **web.config**.

## <a name="next-steps"></a>Pasos siguientes
- [Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

