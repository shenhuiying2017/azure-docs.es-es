---
title: "Introducción a Android en Azure AD v2: configuración | Microsoft Docs"
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
ms.openlocfilehash: c09937582118ebcc5b8cbc1f43a0a2019f2f7a89
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adición de información de registro de la aplicación a su aplicación

En este paso, debe agregar el identificador de cliente a su proyecto.

1.  Abra `MainActivity` (en `app` > `java` > *`{host}.{namespace}`*)
2.  Reemplace la línea que empieza con `final static String CLIENT_ID` por:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Abra `app` > `manifests` > `AndroidManifest.xml`
4. Agregue la siguiente actividad al nodo `manifest\application`. De este modo se registra un `BrowserTabActivity` para permitir que el sistema operativo reanude la aplicación después de completar la autenticación:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

### <a name="what-is-next"></a>Pasos siguientes

[Probar y validar](active-directory-mobileanddesktopapp-android-test.md)
