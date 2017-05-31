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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b10c8bd6878f6891b2420ae7fb4b65b19144762c
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configuración de la aplicación web ASP.NET con la información de registro de la aplicación

En este paso, se configura el proyecto para usar SSL y, a continuación, se usa la dirección URL de SSL para configurar la información de registro de la aplicación. Después, se agrega la información de registro de la aplicación a la solución a través de *web.config*.

1.    En el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana `Properties` (si no ve una ventana de propiedades, presione F4).
2.    Cambie `SSL Enabled` a `True`:<br/>
![Propiedades del proyecto](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
3.    Copie el valor de `SSL URL` anterior y péguelo en el campo `Redirect URL` en la parte superior de esta página; a continuación, haga clic en *Actualizar*:
4.    Agregue lo siguiente en el archivo `web.config` ubicado en la carpeta raíz, en la sección `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

