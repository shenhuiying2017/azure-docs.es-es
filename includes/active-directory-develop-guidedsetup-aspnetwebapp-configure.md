---
title: archivo de inclusión
description: archivo de inclusión
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c1971e1eb3abc653ad8bdc6af772c699f8549019
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application"></a>Registrar su aplicación
Tiene dos opciones para registrar la aplicación y agregar la información de registro de la misma a la solución:

### <a name="option-1-express-mode"></a>Opción 1: Modo rápido

Puede registrar rápidamente la aplicación mediante estos pasos:
1. Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de configuración paso a paso.
4.  Siga las instrucciones para agregar una dirección URL de redireccionamiento a la aplicación.

### <a name="option-2-advanced-mode"></a>Opción 2: Modo avanzado

Para registrar la aplicación y agregar la información de registro de aplicación a la solución, siga estos pasos:

1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3.  Asegúrese de que está desactivada la opción de configuración paso a paso.
4.  Haga clic en `Add Platform` y luego en `Web`.
5.  Vuelva a Visual Studio y, en el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana de propiedades (si no ve una ventana de propiedades, presione F4).
6.  Cambie SSL habilitado a `True`.
7.  Copie la dirección URL de SSL y agréguela a la lista de direcciones URL de redireccionamiento en la lista del Portal de registro de direcciones URL de redireccionamiento:<br/><br/>![Propiedades del proyecto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Agregue lo siguiente en `web.config`, ubicado en la carpeta raíz en la sección `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
    ```

9. Reemplace `ClientId` por el identificador de aplicación que acaba de registrar.
10. Reemplace `redirectUri` por la dirección URL de SSL del proyecto. 

