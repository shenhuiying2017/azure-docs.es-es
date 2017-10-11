---
title: "Introducción a iOS en Azure AD v2: prueba | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones de iOS (Swift) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Prueba de consultas de la API de Microsoft Graph desde la aplicación iOS

Presione `Command` + `R` para ejecutar el código en el simulador.

![Captura de pantalla de ejemplo](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

Cuando esté listo para ejecutar la prueba, pulse *"Call Microsoft Graph API"* (Llamar a la API de Microsoft Graph) y se le pedirá que escriba su nombre de usuario y contraseña.

### <a name="consent"></a>Consentimiento
La primera vez que inicie sesión en la aplicación, verá una pantalla de consentimiento similar a la siguiente, donde debe aceptar explícitamente:

![Pantalla de consentimiento](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Resultados esperados
Debería ver la información del perfil de usuario devuelta por la llamada a la API de Microsoft Graph en la sección *Registro*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

La API de Microsoft Graph requiere el ámbito `user.read` para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en nuestro portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Por ejemplo, para Microsoft Graph, se requiere el ámbito `Calendars.Read` para enumerar los calendarios del usuario. Para tener acceso al calendario del usuario en el contexto de una aplicación, debe agregar el permiso delegado `Calendars.Read` a la información del registro de la aplicación y, a continuación, agregar el ámbito `Calendars.Read` a la llamada a `acquireTokenSilent`. Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

<!--end-collapse-->



