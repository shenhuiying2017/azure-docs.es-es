<properties 
	pageTitle="La experiencia de inicio de sesión de Azure MFA con Azure Multi-Factor Authentication " 
	description="Esta página proporciona instrucciones sobre dónde debe ir para ver los distintos métodos de inicio de sesión disponibles con Azure MFA."
	keywords="autenticación de usuario, experiencia de inicio de sesión, inicio de sesión con el teléfono móvil, inicio de sesión con el teléfono del trabajo" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# La experiencia de inicio de sesión con Azure Multi-Factor Authentication
> [AZURE.NOTE]  La siguiente documentación incluida en esta página muestra una experiencia típica de inicio de sesión. Para obtener ayuda con el inicio de sesión, vea [Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)



## ¿Cómo se presenta la experiencia de inicio de sesión?
Dependiendo de cómo inicie sesión y use la autenticación multifactor, su experiencia será distinta. En esta sección se proporcionará información sobre qué esperar al iniciar sesión. Elija el caso que mejor describa lo que hace:


¿Qué hace normalmente?|Description
:------------- | :------------- | 
[Inicio de sesión con el teléfono de la oficina o con un móvil](#signing-in-with-mobile-or-office-phone) | Esto es lo que puede esperar del inicio de sesión si usa el teléfono de la oficina o un móvil.
[Inicio de sesión en la aplicación Microsoft Authenticator mediante una notificación](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Este es el funcionamiento que puede esperar a la hora de usar la aplicación Microsoft Authenticator con notificaciones.
[Inicio de sesión en la aplicación Microsoft Authenticator mediante un código de verificación](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Este es el funcionamiento que puede esperar a la hora de usar la aplicación Microsoft Authenticator con un código de verificación.
[Inicio de sesión con un método alternativo](#signing-in-with-an-alternate-method)|Esto le mostrará qué esperar si desea usar un método alternativo.

## Inicio de sesión con el teléfono de la oficina o con un móvil

La siguiente información describe la experiencia de uso de la autenticación multifactor con el teléfono de su oficina o con el móvil.

### Inicio de sesión con una llamada a su oficina o al móvil

- Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
- Microsoft le llamará.

![Microsoft llama](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Responda al teléfono y pulse la tecla #.

![Respuesta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Con esto debe haber iniciado sesión.</li>

## Inicio de sesión en la aplicación Microsoft Authenticator mediante una notificación

La siguiente información describe la experiencia de uso de la autenticación multifactor con la aplicación Microsoft Authenticator cuando se le envía una notificación.

### Para iniciar sesión con una notificación enviada a aplicación Authenticator de Microsoft

- Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
- Microsoft le enviará una notificación.

![Microsoft envía una notificación](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Responda al teléfono y pulse la tecla Comprobar.

![Verify](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)


- Con esto debe haber iniciado sesión.


## Inicio de sesión en la aplicación Microsoft Authenticator mediante un código de verificación

La siguiente información describe la experiencia de uso de la autenticación multifactor con la aplicación Microsoft Authenticator cuando la usa con un código de verificación.

### Para iniciar sesión en la aplicación Microsoft Authenticator con un código de verificación

- Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
- Microsoft le pedirá un código de verificación.

![Escribir el código de comprobación](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Abra la aplicación Microsoft Authenticator en el teléfono y escriba el código en el cuadro donde está iniciando sesión.

![Obtener código](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)

- Con esto debe haber iniciado sesión.


## Inicio de sesión con un método alternativo


En la siguiente sección se mostrará cómo iniciar sesión con un método alternativo cuando el método principal no esté disponible.

### Para iniciar sesión con un método alternativo

- Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
- Seleccione el uso de una opción de comprobación distinta. Se le presentarán con una serie de opciones diferentes. El número de opciones se basará en la selección que haya realizado en la configuración.

![Usar método alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Elija un método alternativo e inicie sesión.

 

<!---HONumber=AcomDC_0824_2016-->