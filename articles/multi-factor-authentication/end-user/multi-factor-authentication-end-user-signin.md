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
ms.date: 02/02/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c0af680c83a72a9f2a415999141f9913c8b724a4
ms.openlocfilehash: 85e0212ed9d7bde001f9b5cd4776d34ad1f01816


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>La experiencia de inicio de sesión con Azure Multi-Factor Authentication
> [!NOTE]
> El propósito de este artículo es recorrer una experiencia de inicio de sesión típica. Para obtener ayuda sobre el inicio de sesión o resolver problemas, consulte [Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>¿Cómo se presenta la experiencia de inicio de sesión?
Dependiendo de cómo inicie sesión y use la autenticación multifactor, su experiencia será distinta. Elija la opción que mejor describa lo que hace:

| ¿Cómo se puede iniciar sesión? | 
| --- |
| [Con el teléfono móvil o de la oficina](#signing-in-with-mobile-or-office-phone) |
| [Con notificaciones de la aplicación Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Con códigos de verificación de la aplicación Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Con un método alternativo, ya que ahora no puedo usar mi método preferido](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-mobile-or-office-phone"></a>Inicio de sesión con el teléfono de la oficina o con un móvil
La siguiente información describe la experiencia de uso de la autenticación multifactor con el teléfono de su oficina o con el móvil.

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.  
2. Microsoft le llamará.  
3. Responda al teléfono y pulse la tecla #.  
4. Con esto debe haber iniciado sesión.  

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Inicio de sesión con la aplicación Microsoft Authenticator 
La siguiente información describe la experiencia de uso de la aplicación Microsoft Authenticator para las verificaciones en dos pasos. Hay dos maneras diferentes de utilizar la aplicación. O bien puede recibir notificaciones push en el dispositivo o puede abrir la aplicación para obtener un código de verificación.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para iniciar sesión con una notificación de la aplicación Microsoft Authenticator
1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Microsoft envía una notificación a la aplicación Microsoft Authenticator de su dispositivo.

  ![Microsoft envía una notificación](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra la notificación en el teléfono y seleccione la clave de **verificación**. Si su empresa requiere un PIN, escríbalo aquí.
4. Con esto debe haber iniciado sesión.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sesión en la aplicación Microsoft Authenticator con un código de verificación

Si usa la aplicación Microsoft Authenticator para obtener códigos de verificación, al abrir la aplicación, podrá ver un número debajo de su nombre de cuenta. Este número cambia cada treinta segundos para que no use el mismo número dos veces. Cuando se le solicite un código de verificación, abra la aplicación y use cualquier número que se muestre. 

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Microsoft le pedirá un código de verificación.

  ![Escribir el código de comprobación](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra la aplicación Microsoft Authenticator en el teléfono y escriba el código en el cuadro donde está iniciando sesión.
4. Con esto debe haber iniciado sesión.

## <a name="signing-in-with-an-alternate-method"></a>Inicio de sesión con un método alternativo
En ocasiones, no tiene el teléfono o el dispositivo que configuró como método de verificación preferida. Esta situación es el motivo por el que se recomienda configurar métodos de copia de seguridad para la cuenta. En la siguiente sección se mostrará cómo iniciar sesión con un método alternativo cuando el método principal no esté disponible.

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Seleccione **Usar otra opción de comprobación**. Verá distintas opciones de verificación en función del número que haya instalado.

  ![Usar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

3. Elija un método alternativo e inicie sesión.

## <a name="next-steps"></a>Pasos siguientes

Si tiene problemas para iniciar sesión con la verificación en dos pasos, obtenga más información en [Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Aprenda a [administrar la configuración de la verificación en dos pasos](multi-factor-authentication-end-user-manage-settings.md).

Descubra cómo [empezar a trabajar con la aplicación Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para que pueda utilizar las notificaciones para iniciar sesión, en lugar de textos y llamadas de teléfono. 


<!--HONumber=Feb17_HO1-->


