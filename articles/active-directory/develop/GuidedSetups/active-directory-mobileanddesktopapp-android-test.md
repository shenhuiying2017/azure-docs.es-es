---
title: "Introducción a Android en Azure AD v2: prueba | Microsoft Docs"
description: "Cómo puede una aplicación de Android obtener un token de acceso y llamar a las API Graph que requieren tokens de acceso desde el punto de conexión de Azure Active Directory v2"
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
ms.openlocfilehash: 85f9b475a77af9865882f4193c602756e345cc02
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
## <a name="test-your-code"></a>Prueba del código

1. Implemente el código en el dispositivo/emulador.
2. Cuando esté listo para realizar la prueba, use una cuenta de Microsoft Azure Active Directory (cuenta profesional) o una cuenta Microsoft (live.com, outlook.com) para iniciar sesión. 

![Captura de pantalla de ejemplo](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Inicio de sesión](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Consentimiento
La primera vez que un usuario inicie sesión en la aplicación, verá una pantalla de consentimiento similar a la siguiente, donde debe aceptar explícitamente: 

![Consentimiento](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Resultados esperados
Debería ver los resultados de una llamada al punto de conexión "me" de la API Graph de Microsoft utilizado para obtener el perfil de usuario: https://graph.microsoft.com/v1.0/me. Para ver una lista de los puntos de conexión comunes de Microsoft Graph, consulte este [artículo](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados
API Graph requiere el ámbito `user.read` para leer el perfil del usuario. Este ámbito se agrega de forma predeterminada a cada aplicación que se registra a través del portal de registro. Otras API Graph, así como las API personalizadas para el servidor back-end, requieren ámbitos adicionales. Por ejemplo, para Graph, `Calendars.Read` es necesario para elaborar un listado de los calendarios del usuario. Para tener acceso al calendario del usuario en un contexto de una aplicación, debe agregar esta información de registro de la aplicación delegada y, a continuación, agregar `Calendars.Read` a la llamada `AcquireTokenAsync`. Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

Si una API de back-end no requiere un ámbito (no recomendado), puede usar `ClientId` como el ámbito en la llamada de `AcquireTokenAsync`.
<!--end-collapse-->

