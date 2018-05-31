---
title: Cambios realizados en un proyecto de WebAPI al conectarse a Azure AD
description: Se describe lo que sucede en el proyecto de WebAPI al conectarse a Azure AD mediante Visual Studio.
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: b8303ccf6fc96e8d0708c9e414fdf511e0cf2fdf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31785169"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>¿Qué le ha ocurrido a mi proyecto de WebAPI (servicio conectado de Visual Studio para Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Introducción](vs-active-directory-webapi-getting-started.md)
> - [¿Qué ha ocurrido?](vs-active-directory-webapi-what-happened.md)

En este artículo se identifican los cambios exactos realizados en proyectos de WebAPI de ASP.NET, proyectos de aplicación de una sola página de ASP.NET y proyectos de API de Azure para ASP.NET al agregar el [servicio conectado de Azure Active Directory mediante Visual Studio](vs-active-directory-add-connected-service.md). La información de este artículo también se aplica a los proyectos de ASP.NET Azure Mobile Service en Visual Studio 2015.

Para más información sobre cómo trabajar con el servicio conectado, consulte la [introducción](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Referencias agregadas

Afecta al archivo de proyecto (referencias de \*.NET) y `packages.config` (referencias de NuGet).

| Escriba | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Referencias adicionales si ha seleccionado la opción **Leer datos de directorio**:

| Escriba | Referencia |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (solo Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(solo Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

Se quitan las referencias siguientes (solo proyectos de ASP.NET 4, como en Visual Studio 2015):

| Escriba | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Cambios del archivo de proyecto

- Establezca la propiedad `IISExpressSSLPort` en un número distinto.
- Establezca la propiedad `WebProject_DirectoryAccessLevelKey` en 0 o 1 si ha seleccionado la opción **Leer datos de directorio**.
- Establezca la propiedad `IISUrl` en `https://localhost:<port>/`, donde `<port>` coincide con el valor `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>Cambios de web.config o app.config

- Se agregaron las siguientes entradas de configuración:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Solo Visual Studio 2017: también se agregó la siguiente entrada en `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Se agregaron los elementos `<dependentAssembly>` en el nodo `<runtime><assemblyBinding>` de `System.IdentityModel.Tokens.Jwt`.

- Si seleccionó la opción **Leer datos de directorio**, se agrega la siguiente entrada de configuración en `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Adiciones y cambios en el código

- Se agregó el atributo `[Authorize]` a `Controllers/ValueController.cs` y cualquier otro controlador existente.

- Se agregó o modificó, según el caso, una clase de inicio de autenticación, `App_Start/Startup.Auth.cs`, que contiene la lógica de inicio para la autenticación de Azure AD. Si seleccionó la opción **Leer datos de directorio**, este archivo también contiene código para recibir código de OAuth y cambiarlo por un token de acceso.

- (Solo Visual Studio 2015 con aplicaciones ASP.NET 4) Se quitó `App_Start/IdentityConfig.cs` y se agregaron `Controllers/AccountController.cs`, `Models/IdentityModel.cs` y `Providers/ApplicationAuthProvider.cs`.

- Se agregó `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) o `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), que contiene información que usa Visual Studio para realizar el seguimiento de la adición del servicio conectado.

### <a name="file-backup-visual-studio-2015"></a>Copia de seguridad de archivos (Visual Studio 2015)

Al agregar el servicio conectado, Visual Studio 2015 hace una copia de seguridad de los archivos modificados y eliminados. Todos los archivos afectados se guardan en la carpeta `Backup/AzureAD`. Visual Studio 2017 no crea copias de seguridad.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Cambios en Azure

- Se creó una aplicación de Azure AD en el dominio que seleccionó al agregar el servicio conectado.
- Se actualizó la aplicación para incluir el permiso **Leer datos de directorio** si se seleccionó esta opción.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Pasos siguientes

- [Escenarios de autenticación para Azure Active Directory](active-directory-authentication-scenarios.md)
- [Adición de inicio de sesión con Microsoft a una aplicación web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)