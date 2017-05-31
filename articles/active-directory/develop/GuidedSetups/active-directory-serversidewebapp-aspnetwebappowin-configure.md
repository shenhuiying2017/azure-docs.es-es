---
title: "Introducción al servidor web de ASP.NET de Azure AD v2: configuración | Microsoft Docs"
description: "Implementación de inicio de sesión de Microsoft en una solución ASP.NET con una aplicación basada en un explorador web tradicional mediante el estándar OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6f50ced708cb79a39e190657e4720c515c09990f
ms.contentlocale: es-es


---

## <a name="create-an-application-express"></a>Creación de una aplicación (proceso rápido)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.    Escriba el nombre de la aplicación y su correo electrónico.
3.    Asegúrese de que está activada la opción de configuración paso a paso.
4.    Siga las instrucciones para agregar una dirección URL de redireccionamiento a la aplicación.

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adición de la información de registro de la aplicación a la solución (avanzado)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3.    Asegúrese de que está desactivada la opción de configuración paso a paso.
4.    Haga clic en `Add Platforms` y luego en `Web`.
5.    Vuelva a Visual Studio y, en el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana de propiedades (si no ve una ventana de propiedades, presione F4).
6.    Cambie SSL habilitado a `True`:<br/><br/>![Propiedades del proyecto](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />

7.    Copie la dirección URL de SSL y agregue esta dirección URL a la lista de direcciones URL de redireccionamiento en la lista del Portal de registro de direcciones URL de redireccionamiento.
8.    Agregue lo siguiente en `web.config`, ubicado en la carpeta raíz en la sección `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
9. Reemplace `ClientId` por el identificador de aplicación que acaba de registrar.
10. Reemplace `redirectUri` por la dirección URL de SSL del proyecto. 
<!-- End Docs -->

> Nota:
> ### <a name="restricting-users-from-only-one-organization-to-sign-in-to-your-application"></a>Establecimiento de una limitación para que los usuarios de una organización sean los únicos que pueden iniciar sesión en la aplicación
> De forma predeterminada, las cuentas personales (como outlook.com, live.com u otras), así como las cuentas profesionales y educativas de cualquier empresa u organización que se haya integrado con Azure Active Directory, pueden iniciar sesión en la aplicación. Si quiere que la aplicación solo acepte inicios de sesión de una organización, reemplace el parámetro `Tenant` en `web.config` de `Common` al nombre de inquilino de la organización; por ejemplo, `contoso.onmicrosoft.com`. A continuación, cambie el argumento *ValidateIssuer* de la clase de inicio de OWIN a `true`.
Para permitir usuarios de solo una lista de organizaciones específicas, establezca `ValidateIssuer` en `true` y use el parámetro `ValidIssuers` para especificar una lista de las organizaciones.
Otra opción consiste en implementar un método personalizado para validar a los emisores mediante `IssuerValidator parameter`. Para más información sobre `TokenValidationParameters`, consulte [este](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters(v=vs.114).aspx) artículo de MSDN.


