<properties 
	pageTitle="Uso de una aplicación móvil como método de contacto con Azure MFA" 
	description="Esta página mostrará a los usuarios cómo utilizar la aplicación móvil como método de contacto principal para Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Uso de una aplicación móvil como método de contacto con Azure Multi-Factor Authentication

Este artículo puede serle útil si desea usar la aplicación Microsoft Authenticator como método de contacto principal. La información que aquí se ofrece le guiará a través de la configuración de Multi-Factor Authentication para usar la aplicación móvil como método de contacto principal.

La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Procedimiento para usar Microsoft Authenticator como método de contacto


- En la pantalla Comprobación de seguridad adicional, seleccione Aplicación móvil en la lista desplegable.


![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Seleccione Notificación o Contraseña de un solo uso y haga clic en Configurar.
- En el teléfono que tenga instalada la aplicación Microsoft Authenticator, inicie la aplicación y haga clic en + para agregar una nueva cuenta. Después, especifique que desea agregar una cuenta profesional o educativa, que iniciarán el escáner de códigos QR. Si la cámara no funciona correctamente, puede seleccionar escribir la información de su compañía manualmente. [Agregar una cuenta manualmente](#adding-an-account-manually)

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Digitalice la imagen de código QR que aparece en la pantalla para configurar la aplicación móvil. Haga clic en Listo para cerrar la pantalla de códigos QR.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Si no puede digitalizar el código QR, puede escribir la información manualmente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Comenzará la activación en el teléfono, una vez que se haya completado, haga clic en la opción para contactar. Con esto se enviará una notificación o un código de verificación a su teléfono. Haga clic en Comprobar.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Algunas compañías pueden requerir un PIN durante la fase de comprobación.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- Cuando finalice, puede hacer clic en Cerrar. A estas alturas, la verificación debería haberse realizado correctamente.
- Ahora se recomienda que escriba su número de teléfono móvil para usar en el caso de que pierda el acceso a la aplicación móvil.
- Especifique el país en la lista desplegable y escriba su número de teléfono móvil en la casilla junto al país. Haga clic en Siguiente.
- Ahora ya ha configurado el método de contacto y es el momento de hacer lo mismo con las contraseñas de aplicación para las aplicaciones sin explorador como Outlook 2010 o anterior. Si no usa estas aplicaciones, haga clic en **Listo**. De lo contrario, continúe con el paso siguiente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Si está usando estas aplicaciones copie la contraseña de aplicación proporcionada y péguela en la aplicación sin explorador. Para obtener información acerca de aplicaciones específicas como Outlook y Lync vea cómo cambiar la contraseña en su correo electrónico a la contraseña de aplicación y cómo cambiar la contraseña en su aplicación a la contraseña de aplicación.
- Haga clic en Done (Listo).


## Agregar una cuenta manualmente
Si desea agregar una cuenta manualmente, seleccione el botón introducir cuenta manualmente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Ahora si tiene una cuenta que ya tenga MFA de Azure, especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Cuando se haya completado esta haga clic en Contacto. Con esto se enviará una notificación o un código de verificación a su teléfono. Haga clic en Comprobar.



 

<!---HONumber=AcomDC_0831_2016-->