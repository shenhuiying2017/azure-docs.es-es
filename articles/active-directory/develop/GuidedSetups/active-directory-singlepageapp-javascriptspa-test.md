---
title: "Configuración de SPA de JS en Azure AD v2: prueba | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones SPA de JavaScript a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
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
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
## <a name="test-your-code"></a>Prueba del código

> ### <a name="testing-with-visual-studio"></a>Pruebas con Visual Studio
> Si usa Visual Studio, presione `F5` para ejecutar el proyecto: el explorador se abre y le lleva a *http://localhost:{puerto}*, donde puede ver el botón *Llamar a API de Microsoft Graph*.

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Pruebas con Python u otro servidor web
> Si no se utiliza Visual Studio, asegúrese de que se inicia el servidor web y de que está configurado para escuchar a un puerto TCP en función de la carpeta con el archivo *index.html*. En el caso de Python, puede empezar a escuchar el puerto mediante su ejecución en el terminal o símbolo del sistema, en la carpeta de la aplicación:
> 
> ```bash
> python -m http.server 8080
> ```
>  A continuación, abra el explorador y escriba *http://localhost:8080* o *http://localhost:{puerto}*: donde el *puerto* corresponde al puerto que está escuchando el servidor web. Debería ver el contenido de su página index.html con el botón *Llamar a API de Microsoft Graph*.

## <a name="test-your-application"></a>Prueba de la aplicación

Una vez que el explorador cargue su página *index.html*, haga clic en el botón *Llamar a API de Microsoft Graph*. Si se trata de la primera vez, el explorador le redirige al punto de conexión v2 de Microsoft Azure Active Directory, donde se le pide que inicie sesión.
 
![Captura de pantalla de ejemplo](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentimiento
La primera vez que inicie sesión en la aplicación, verá una pantalla de consentimiento similar a la siguiente, donde debe aceptar:

 ![Pantalla de consentimiento](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Resultados esperados
Debería ver la información del perfil de usuario devuelta por la respuesta a la llamada a la API de Microsoft Graph.
 
 ![Results](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

También verá información básica sobre el token adquirido en los cuadros *Token de acceso* e *ID Token Claims* (Notificaciones de token de identificador).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

La API de Microsoft Graph requiere el ámbito `user.read` para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en nuestro portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Por ejemplo, para Microsoft Graph, se requiere el ámbito `Calendars.Read` para enumerar los calendarios del usuario. Para tener acceso al calendario del usuario en el contexto de una aplicación, debe agregar el permiso delegado `Calendars.Read` a la información del registro de la aplicación y, a continuación, agregar el ámbito `Calendars.Read` a la llamada a `acquireTokenSilent`. Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

Si una API de back-end no requiere un ámbito (no se recomienda), puede usar el valor de `clientId` como ámbito en las llamadas a `acquireTokenSilent` o `acquireTokenRedirect`.

<!--end-collapse-->
