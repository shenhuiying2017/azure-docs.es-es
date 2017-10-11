---
title: "Introducción al escritorio de Windows en Azure AD v2: prueba | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones .NET de escritorio de Windows (XAML) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
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
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
## <a name="test-your-code"></a>Prueba del código

Para probar la aplicación, presione `F5` para ejecutar el proyecto en Visual Studio. Aparecerá la ventana principal:

![Captura de pantalla de ejemplo](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Cuando esté listo para realizar la prueba, haga clic en *Call Microsoft Graph API* (Llamar a la API Graph de Microsoft) y use una cuenta de Microsoft Azure Active Directory (cuenta profesional) o una cuenta Microsoft (live.com, outlook.com) para iniciar sesión. Si es la primera vez, verá una ventana que solicita al usuario que inicie sesión:

![Inicio de sesión](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Consentimiento
La primera vez que inicie sesión en la aplicación, verá una pantalla de consentimiento similar a la siguiente, donde debe aceptar explícitamente:

![Pantalla de consentimiento](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Resultados esperados
Debería ver la información de perfil de usuario devuelta por la llamada de API Graph de Microsoft en la pantalla de resultados de la llamada de API.

También debería aparecer información básica sobre el token obtenido a través de `AcquireTokenAsync` o `AcquireTokenSilentAsync` en el cuadro de información de token:

|Propiedad  |Formato  |Descripción |
|---------|---------|---------|
|Nombre | {Nombre completo del usuario} |Nombre y apellido del usuario|
|Nombre de usuario |<span>user@domain.com</span> |El nombre de usuario utilizado para identificar al usuario|
|Token Expires |{FechaHora}         |La hora en que expira el token. MSAL ampliará la fecha de expiración para que renueve el token cuando sea necesario|
|Access token |{Cadena}         |La cadena de token enviada que se hará llegar a las solicitudes HTTP que requieran un encabezado de autorización|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados
API Graph requiere el ámbito `user.read` para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en nuestro portal de registro. Otras API Graph, así como las API personalizadas para el servidor back-end, requieren ámbitos adicionales. Por ejemplo, para Graph, `Calendars.Read` es necesario para elaborar un listado de los calendarios del usuario. Para tener acceso al calendario del usuario en un contexto de una aplicación, debe agregar la información de registro de la aplicación delegada de `Calendars.Read` y, a continuación, agregar `Calendars.Read` a la llamada `AcquireTokenAsync`. Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

<!--end-collapse-->



