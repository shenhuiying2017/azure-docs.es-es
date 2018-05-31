---
title: Introducción a Azure AD Android | Microsoft Docs
description: Cómo crear una aplicación Android que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth2.0.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155701"
---
# <a name="azure-ad-android-getting-started"></a>Introducción a Azure AD Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Si está desarrollando una aplicación para Android, Microsoft hace que los usuarios puedan iniciar sesión en Azure Active Directory (Azure AD) de forma fácil y directa. Azure AD permite que la aplicación acceda a los datos de usuario a través de Microsoft Graph o de su propia API web protegida. 

La biblioteca para Android, Biblioteca de Autenticación de Azure AD (ADAL), permite que la aplicación pueda empezar a usar la [API de Microsoft Graph](https://graph.microsoft.io) de [Microsoft Azure Cloud](https://cloud.microsoft.com) &  al ser compatible con [cuentas de Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) con OAuth2 y OpenID Connect estándar del sector. En este ejemplo se muestran todos los ciclos de vida normales que debería experimentar la aplicación, como:

* Obtener un token de Microsoft Graph
* Actualizar un token
* Llamar a Microsoft Graph
* Cerrar la sesión del usuario

Para comenzar, necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Escenario: Realice el inicio de sesión de usuarios y llame a Microsoft Graph

![Topología](./media/active-directory-android-topology.png)

Esta aplicación se puede usar para todas las cuentas de Azure AD. Es compatible con escenarios únicos y multiorganizativos (que se describen en pasos). Muestra cómo un desarrollador puede compilar aplicaciones para conectarse con usuarios empresariales y acceder a sus datos de Azure + O365 a través de Microsoft Graph. Durante el flujo de autenticación, los usuarios finales deberán iniciar sesión y aceptar los permisos de la aplicación y, en algunos casos, es posible que un administrador tenga que dar su consentimiento a la aplicación. La mayoría de la lógica de este ejemplo muestra cómo autenticar un usuario final y realizar una llamada básica a Microsoft Graph.

## <a name="example-code"></a>Ejemplo de código

El código de ejemplo completo se puede encontrar [en GitHub](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Pasos que se deben seguir

### <a name="register-and-configure-your-app"></a>Registrar y configurar la aplicación 
Debe haber registrado una aplicación cliente nativa con Microsoft mediante [Azure Portal](https://portal.azure.com). 

1. Acceder al registro de la aplicación
    - Acceda a [Azure Portal](https://aad.portal.azure.com). 
    - Seleccione ***Azure Active Directory*** > ***Registros de aplicaciones***. 

2. Creación de la aplicación
    - Seleccione **Nuevo registro de aplicaciones**. 
    - Escriba un nombre de aplicación en el campo **Nombre**. 
    - En **Tipo de aplicación**, seleccione: **Nativa**. 
    - En **URI de redirección**, escriba `http://localhost`. 

3. Configurar Microsoft Graph
    - Seleccione **Configuración > Permisos necesarios**.
    - Seleccione **agregar** y, en **Seleccionar una API**, seleccione ***Microsoft Graph***. 
    - Seleccione el permiso **Iniciar sesión y leer el perfil de usuario** y, luego, presione **Selecciona** para guardar. 
        - Este permiso se asigna al ámbito `User.Read`. 
    - Opcional: en **Permisos necesarios > Windows Azure Active Directory**, quite el permiso seleccionado **Iniciar sesión y leer el perfil de usuario**. Esto evita que la página de consentimiento de usuario muestre el permiso dos veces. 

4. ¡Enhorabuena! La aplicación se ha configurado correctamente. En la siguiente sección, necesitará:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Obtención del código de ejemplo

1. Clonar el código.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Abra el ejemplo en Android Studio.
    - Seleccione **Abrir un proyecto existente de Android Studio**.

### <a name="configure-your-code"></a>Configurar el código

Puede encontrar toda la configuración de este ejemplo de código en el archivo ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***. 

1. Reemplace la constante `CLIENT_ID` con el valor `ApplicationID`.
2. Reemplace la constante `REDIRECT URI` con el valor `Redirect URI` que configuró anteriormente (`http://localhost`). 

### <a name="run-the-sample"></a>Ejecución del ejemplo

1. Seleccione **Compilar > Limpiar el proyecto**. 
2. Seleccione **Ejecutar > ejecutar aplicación**. 
3. La aplicación debe compilarse y mostrar la experiencia del usuario básica. Al hacer clic en el botón `Call Graph API`, se solicita un inicio de sesión y después, en modo silencioso, se llama a Microsoft Graph API con el nuevo token. 

## <a name="important-info"></a>Información importante

1. Consulte la [wiki de ADAL para Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) para obtener más información sobre los mecanismos de biblioteca y cómo configurar nuevos escenarios y funcionalidades. 
2. En escenarios nativo, la aplicación usará un elemento WebView incrustado y no saldrá de la aplicación. El valor `Redirect URI` puede ser arbitrario. 
3. ¿Encontró algún problema o tiene preguntas? Puede crear un problema o una entrada en Stackoverflow con la etiqueta `azure-active-directory`. 

### <a name="cross-app-sso"></a>SSO entre aplicaciones
Obtenga información sobre la [Habilitación de SSO entre aplicaciones en Android mediante ADAL](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Telemetría de autenticación
la biblioteca ADAL expone telemetría de autenticación para ayudar a los desarrolladores de aplicaciones a comprender cómo se comportan sus aplicaciones para poder crear mejores experiencias. Esto le permite obtener un inicio de sesión correcto, usuarios activos y varias otras conclusiones interesantes. Para usar telemetría de autenticación, los desarrolladores de aplicaciones necesitan establecer un servicio de telemetría para agregar y almacenar eventos.

Para obtener más información sobre telemetría de autenticación, consulte [Telemetría de autenticación de ADAL para Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
