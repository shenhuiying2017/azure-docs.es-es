---
title: Diagnóstico de errores con el servicio conectado de Azure Active Directory
description: El servicio conectado de Active Directory detectó un tipo de autenticación no compatible.
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31784894"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnóstico de errores con el servicio conectado de Azure Active Directory

Al detectar el código de autenticación anterior, el servicio conectado de Azure Active Directory detectó un tipo de autenticación no compatible.

Para detectar correctamente el código de autenticación anterior de un proyecto, este debe estar compilado.  Si se produjo este error y no tiene un código de autenticación anterior en el proyecto, vuelva a compilarlo e inténtelo de nuevo.

## <a name="project-types"></a>Tipos de proyecto

El servicio conectado comprueba el tipo de proyecto que esté desarrollando, así puede insertar la lógica de autenticación correcta en el proyecto. Si no hay ningún controlador que derive de `ApiController` en el proyecto, el proyecto se considerará como un proyecto WebAPI. Si solo hay controladores que derivan de `MVC.Controller` en el proyecto, el proyecto se considerará como proyecto MVC. El servicio conectado no admite ningún otro tipo de proyecto.

## <a name="compatible-authentication-code"></a>Código de autenticación compatible

El servicio conectado también comprueba la configuración de autenticación que se ha configurado previamente con el servicio o que es compatible con él. Si todos los valores de configuración están presentes, se considera un caso reentrante y el servicio conectado abre y muestra la configuración.  Si solo algunos valores de configuración están presentes, se considera un caso de error.

En un proyecto MVC, el servicio conectado comprueba cualquiera de los siguientes valores de configuración, que se originan por un uso anterior del servicio:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Además, el servicio conectado comprueba los siguientes valores de configuración en el proyecto de Web API, que se originan por el uso anterior del servicio:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Código de autenticación no compatible

Finalmente, el servicio conectado trata de detectar versiones de código de autenticación que se hayan configurado con versiones anteriores de Visual Studio. Si recibió este error, significa el proyecto contiene un tipo de autenticación incompatible. El servicio conectado detecta los siguientes tipos de autenticación de las versiones anteriores de Visual Studio:

* Autenticación de Windows
* Cuentas de usuario individuales
* Cuentas organizativas

Para detectar la autenticación de Windows en un proyecto de MVC, el servicio conectado busca el elemento `authentication` en su archivo `web.config`.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Para detectar la autenticación de Windows en un proyecto de Web API, el servicio conectado busca el elemento `IISExpressWindowsAuthentication` en el archivo `.csproj` del proyecto:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Para detectar la autenticación de cuentas de usuario individuales, el servicio conectado busca el elemento de paquete en el archivo `packages.config`.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Para detectar una forma anterior de autenticación de cuentas de organización, el servicio conectado busca el siguiente elemento en `web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Para cambiar el tipo de autenticación, quite el tipo de autenticación no compatible e intente volver a agregar el servicio conectado.

Para obtener más información, consulte [Escenarios de autenticación en Azure AD](active-directory-authentication-scenarios.md).