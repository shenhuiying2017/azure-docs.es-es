---
title: Cambios realizados en un proyecto de WebApi al conectarse a Azure AD | Microsoft Docs
description: Se describe lo que sucede al proyecto de WebApi al conectarse a Azure AD mediante Visual Studio.
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 086e5a9622cad681cd282345d97e0c28ee7de2fa
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>¿Qué ha ocurrido a mi proyecto de WebApi (servicio conectado de Visual Studio Azure Active Directory)
> [!div class="op_single_selector"]
> * [Introducción](vs-active-directory-webapi-getting-started.md)
> * [¿Qué ha ocurrido?](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Se han agregado referencias
### <a name="nuget-package-references"></a>Referencias de paquetes de NuGet
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>Referencias de .NET
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Cambios de código
### <a name="code-files-were-added-to-your-project"></a>Se han agregado archivos de código a su proyecto
Se ha agregado una clase de inicio de autenticación, **App_Start/Startup.Auth.cs**, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD.

### <a name="startup-code-was-added-to-your-project"></a>Se ha agregado código de inicio a su proyecto.
Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a `ConfigureAuth(app)`. Si no era así, se ha agregado una clase de inicio a su proyecto.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Su archivo app.config o web.config tiene nuevos valores de configuración.
Se han agregado las siguientes entradas de configuración.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Se ha creado una aplicación de Azure AD.
Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Si he activado la *deshabilitación de la autenticación de cuentas de usuario individuales*, ¿qué otros cambios se realizaron en mi proyecto?
Se han quitado las referencias al paquete NuGet y se han quitado los archivos y se ha realizado una copia de seguridad de los mismos. Según el estado del proyecto, tendrá que quitar referencias o archivos adicionales manualmente o modificar el código, según corresponda.

### <a name="nuget-package-references-removed-for-those-present"></a>Referencias al paquete NuGet quitadas (si existen)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Se ha realizado una copia de seguridad de los archivos y se han eliminado (si existen)
Se ha realizado una copia de seguridad de cada uno de los siguientes archivos y se han eliminado del proyecto. Los archivos de copia de seguridad se encuentran en una carpeta 'Backup' en la raíz del directorio del proyecto.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Copia de seguridad de los archivos de código (si existen)
Se realizó una copia de seguridad de cada uno de los siguientes archivos antes de ser reemplazados. Los archivos de copia de seguridad se encuentran en una carpeta 'Backup' en la raíz del directorio del proyecto.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Si activé *Leer datos de directorio*, ¿qué otros cambios se realizaron en mi proyecto?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Se realizaron cambios adicionales en app.config o web.config
Se han agregado las siguientes entradas de configuración adicionales.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Se ha actualizado la aplicación Azure Active Directory
La aplicación Azure Active Directory se actualizó para incluir el permiso *Leer datos de directorio* y se creó una clave adicional que luego se usó como *ida:Password* en el archivo `web.config`.

## <a name="next-steps"></a>Pasos siguientes
- [Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

