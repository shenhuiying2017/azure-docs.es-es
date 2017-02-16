---
title: "Experiencia de inicio de sesión de Azure MFA con la verificación en dos pasos | Microsoft Docs"
description: "Esta página proporciona instrucciones sobre dónde debe ir para ver los distintos métodos de inicio de sesión disponibles con Azure MFA."
keywords: "autenticación de usuario, experiencia de inicio de sesión, inicio de sesión con el teléfono móvil, inicio de sesión con el teléfono del trabajo"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 63951136d4f7bc2eb81c24ddfd58e696fc31cf37


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>La experiencia de inicio de sesión con Azure Multi-Factor Authentication 
> [!NOTE]
> La siguiente documentación incluida en esta página muestra una experiencia típica de inicio de sesión.  Para obtener ayuda con el inicio de sesión, vea [Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)
>
>

## <a name="what-will-your-sign-in-experience-be"></a>¿Cómo se presenta la experiencia de inicio de sesión?
Dependiendo de cómo inicie sesión y use la autenticación multifactor, su experiencia será distinta.  En esta sección se proporcionará información sobre qué esperar al iniciar sesión.  Elija el caso que mejor describa lo que hace:

| ¿Qué hace normalmente? | Description |
|:--- |:--- |
| [Inicio de sesión con el teléfono de la oficina o con un móvil](#signing-in-with-mobile-or-office-phone) |Esto es lo que puede esperar del inicio de sesión si usa el teléfono de la oficina o un móvil. |
| [Inicio de sesión en la aplicación Microsoft Authenticator mediante una notificación](#signing-in-with-the-microsoft-authenticator-app-using-notification) |Este es el funcionamiento que puede esperar a la hora de usar la aplicación Microsoft Authenticator con notificaciones. |
| [Inicio de sesión en la aplicación Microsoft Authenticator mediante un código de verificación](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |Este es el funcionamiento que puede esperar a la hora de usar la aplicación Microsoft Authenticator con un código de verificación. |
| [Inicio de sesión con un método alternativo](#signing-in-with-an-alternate-method) |Esto le mostrará qué esperar si desea usar un método alternativo. |

## <a name="signing-in-with-mobile-or-office-phone"></a>Inicio de sesión con el teléfono de la oficina o con un móvil
La siguiente información describe la experiencia de uso de la autenticación multifactor con el teléfono de su oficina o con el móvil.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Inicio de sesión con una llamada a su oficina o al móvil
* Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.  
* Microsoft le llamará.  
* Responda al teléfono y pulse la tecla #.  
* Con esto debe haber iniciado sesión.  

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Inicio de sesión en la aplicación Microsoft Authenticator mediante una notificación
La siguiente información describe la experiencia de uso de la autenticación multifactor con la aplicación Microsoft Authenticator cuando se le envía una notificación.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Para iniciar sesión con una notificación enviada a aplicación Authenticator de Microsoft
* Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
* Microsoft le enviará una notificación.

![Microsoft envía una notificación](./media/multi-factor-authentication-end-user-signin/notify.png)

* Responda al teléfono y pulse la tecla Comprobar.  Si su compañía le requiere un PIN, tendrá que escribirlo aquí.

![Verify](./media/multi-factor-authentication-end-user-signin/phone2.png)

![Configuración](./media/multi-factor-authentication-end-user-first-time/scan3.png)

* Con esto debe haber iniciado sesión.

## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Inicio de sesión en la aplicación Microsoft Authenticator mediante un código de verificación
La siguiente información describe la experiencia de uso de la autenticación multifactor con la aplicación Microsoft Authenticator cuando la usa con un código de verificación.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sesión en la aplicación Microsoft Authenticator con un código de verificación
* Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
* Microsoft le pedirá un código de verificación.

![Escribir el código de comprobación](./media/multi-factor-authentication-end-user-signin/verify3.png)

* Abra la aplicación Microsoft Authenticator en el teléfono y escriba el código en el cuadro donde está iniciando sesión.

![Obtener código](./media/multi-factor-authentication-end-user-signin/phone3.png)

* Con esto debe haber iniciado sesión.

## <a name="signing-in-with-an-alternate-method"></a>Inicio de sesión con un método alternativo
En la siguiente sección se mostrará cómo iniciar sesión con un método alternativo cuando el método principal no esté disponible.

### <a name="to-sign-in-with-an-alternate-method"></a>Para iniciar sesión con un método alternativo
* Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
* Seleccione el uso de una opción de comprobación distinta.  Se le presentarán con una serie de opciones diferentes. El número de opciones se basará en la selección que haya realizado en la configuración.

![Usar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

* Elija un método alternativo e inicie sesión.



<!--HONumber=Dec16_HO4-->


