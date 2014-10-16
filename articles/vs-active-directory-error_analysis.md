<properties title="Error During Authentication Detection" pageTitle="Error During Authentication Detection" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

#### Error durante la detección de autenticación

Al detectar el código de autenticación anterior, el asistente detectó un tipo de autenticación incompatible.

###### ¿Qué se está comprobando?

El asistente trata de detectar versiones de código de autenticación que se hayan configurado con versiones anteriores de Visual Studio. Si recibió este error, significa que existe un tipo de autenticación incompatible. El asistente detecta los siguientes tipos de autenticación de las versiones anteriores de Visual Studio:

-   Autenticación de Windows
-   Cuentas de usuario individuales
-   Cuentas organizativas

Para obtener más información, consulte [Escenarios de autenticación en Azure AD][Escenarios de autenticación en Azure AD].

  [Escenarios de autenticación en Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
