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
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
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

La API de Microsoft Graph requiere el ámbito `user.read` para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en nuestro portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Por ejemplo, para Microsoft Graph, se requiere el ámbito `Calendars.Read` para enumerar los calendarios del usuario. Para tener acceso al calendario del usuario en el contexto de una aplicación, debe agregar el permiso delegado `Calendars.Read` a la información del registro de la aplicación y, a continuación, agregar el ámbito `Calendars.Read` a la llamada a `acquireTokenSilentAsync`. Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

<!--end-collapse-->
