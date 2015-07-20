<properties 
	pageTitle="Uso de una aplicación móvil como método de contacto con Azure MFA" 
	description="Esta página mostrará a los usuarios cómo utilizar la aplicación móvil como método de contacto principal para Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# Uso de una aplicación móvil como método de contacto con Azure Multi-Factor Authentication

Este artículo puede serle útil si desea usar la aplicación móvil como método de contacto principal. La información que aquí se ofrece le guiará a través de la configuración de Multi-Factor Authentication para usar la aplicación móvil como método de contacto principal.

La aplicación Azure Authenticator está disponible para [Windows Phone](http://www.windowsphone.com/es-es/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Para usar una aplicación móvil como método de contacto
<ol>
<li>Seleccione Aplicación móvil en la lista desplegable.</li>

<center>! [Setup] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>Seleccione Notificación o Contraseña de un solo uso y haga clic en Configurar</li>
<li>En el teléfono que tenga instalada la aplicación Azure Authenticator, inicie la aplicación y haga clic en Digitalizar código de barras.</li>


<center>! [Setup] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>Digitalice la imagen de código de barras que aparece en la pantalla para configurar la aplicación móvil. Haga clic en Listo para cerrar la pantalla del códigos de barras. Si no puede digitalizar el código de barras puede escribir la información manualmente.</li>

<center>! [Setup] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>Comenzará la activación en el teléfono, una vez que se haya completado, haga clic en la opción para contactar. Con esto se enviará una notificación o un código de verificación a su teléfono. Haga clic en Comprobar.</li>

<center>! [Setup] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>Haga clic en Cerrar. A estas alturas, la verificación debería haberse realizado correctamente.</li>
<li>Ahora se recomienda que escriba su número de teléfono móvil para usar en el caso de que pierda el acceso a la aplicación móvil. <li>Especifique el país en la lista desplegable y escriba su número de teléfono móvil en la casilla junto al país. Haga clic en Siguiente.</li> <li>Ahora ya ha configurado el método de contacto y es el momento de hacer lo mismo con las contraseñas de aplicación para las aplicaciones sin explorador como Outlook 2010 o anterior. Si no usa estas aplicaciones, haga clic en **Listo**. De lo contrario, continúe con el paso siguiente.</li>

<center>! [Setup] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>Si está usando estas aplicaciones copie la contraseña de aplicación proporcionada y péguela en la aplicación sin explorador. Para obtener información acerca de aplicaciones específicas como Outlook y Lync vea cómo cambiar la contraseña en su correo electrónico a la contraseña de aplicación y cómo cambiar la contraseña en su aplicación a la contraseña de aplicación.</li>
<li>Haga clic en Done (Listo).</li>

<!---HONumber=July15_HO2-->