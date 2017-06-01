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
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: ad4160abfef748033eeb84a2f07a37124ce3b4b1
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
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Reemplace `ClientId` por el identificador de aplicación que acaba de registrar.
</li>
<li>
Reemplace `redirectUri` por la dirección URL de SSL del proyecto.
</li>
</ol>
<!-- End Docs -->

