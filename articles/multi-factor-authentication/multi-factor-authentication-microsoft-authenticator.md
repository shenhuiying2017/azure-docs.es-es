<properties 
	pageTitle="Aplicación Microsoft Authenticator para teléfonos móviles" 
	description="Obtenga información acerca de cómo actualizar a la versión más reciente de Azure Authenticatior." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# Microsoft Authenticator

La aplicación Microsoft Authenticator proporciona un nivel de seguridad adicional que se puede usar con su cuenta de Azure (por ejemplo, bsimon@contoso.onmicrosoft.com), la cuenta profesional local (por ejemplo, bsimon@contoso.com) o la cuenta de Microsoft (por ejemplo, bsimon@outlook.com).

## Descarga de la aplicación Microsoft Authenticator

La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Funcionamiento de la aplicación Microsoft Authenticator
La aplicación lo que hace es insertar una notificación en la aplicación Microsoft Authenticator del smartphone o la tableta. Con solo tocar "Comprobar" en la aplicación puede autenticarse.

Como alternativa, la aplicación también se puede usar con un código de comprobación. Solo tiene que escribir el código que facilita la aplicación en la pantalla de inicio de sesión cuando se le pida.

Estos 2 modos diferentes son los siguientes:

**Notificación**: En este modo, la aplicación Microsoft Authenticator impide el acceso no autorizado a cuentas y detiene las transacciones fraudulentas. Esto se realiza mediante una notificación push en su teléfono o dispositivo registrado. Simplemente vea la notificación y si es legítima seleccione Autenticar. En caso contrario, puede elegir Denegar o denegar e informar sobre la notificación fraudulenta. Para más información sobre cómo informar de notificaciones fraudulentas, consulte How to use the Deny and Report Fraud Feature for Multi-Factor Authentication (Uso de la característica Denegar y notificar fraude para Multi-Factor Authentication).

**Contraseña de un solo uso**: en este modo, la aplicación Microsoft Authenticator puede utilizarse como un token de software para generar un código de comprobación de OATH. A continuación, este código de comprobación se puede escribir junto con el nombre de usuario y la contraseña a fin de proporcionar una segunda forma de autenticación.

## Adición de una cuenta a la aplicación Microsoft Authenticator con el escáner de códigos QR

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


## Adición manual de una cuenta a la aplicación Microsoft Authenticator
Si desea agregar una cuenta manualmente, seleccione el botón introducir cuenta manualmente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Ahora si tiene una cuenta que ya tenga MFA de Azure, especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Cuando se haya completado esta haga clic en Contacto. Con esto se enviará una notificación o un código de verificación a su teléfono. Haga clic en Comprobar.

## Transición a la nueva aplicación Microsoft Authenticator

Con el lanzamiento de la aplicación Microsoft Authenticator, se sustituye la antigua aplicación Azure Authenticator. La aplicación Azure Authenticator seguirá funcionando pero si decide dar el paso a la nueva aplicación Microsoft Authenticator, este artículo le servirá de ayuda.


### Transición a la nueva aplicación Microsoft Authenticator 

**Paso 1:** Instalación de Microsoft Authenticator.


**Paso 2:** activar sus cuentas con la nueva aplicación

Asegúrese en primer lugar, de que tiene a mano el código QR o el código y la dirección URL para la cuenta que desea agregar a la aplicación.

> [AZURE.NOTE] ¿No está seguro de cómo obtener el código QR? Para obtener ayuda, póngase en contacto con el Servicio de asistencia.
> 
> ¿No puede activar su cuenta con la nueva aplicación? Póngase en contacto con el Servicio de asistencia.
>


Una vez que tenga el código QR delante, inicie la aplicación. Haga clic en +.

Después, especifique que quiere agregar una cuenta profesional o educativa. Con esto se iniciará la cámara para examinar el código QR. Si no puede examinar el código QR, siempre tiene la opción de entrada manual.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

Para confirmar que la cuenta se ha activado correctamente, compruebe que la nueva cuenta aparece en las páginas de las cuentas.


Siga este paso para todas las cuentas que desee migrar a la nueva aplicación.



**Paso 3:** desinstalar la aplicación anterior Multi-Factor Authentication en el teléfono.

Cuando haya agregado todas las cuentas a la nueva aplicación, desinstale la aplicación antigua en el teléfono.



## Cómo agregar una cuenta mediante el escáner de códigos de barras



- En primer lugar, vaya a la página de configuración de comprobación de seguridad. Para obtener información sobre cómo ir a esta página, vea [Cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

- Haga clic en el botón Configurar.
 
![Add account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Aparecerá una pantalla con un código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Abra ahora la aplicación Microsoft Authenticator; se le dirigirá a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una nueva cuenta, haga clic en el signo + y, luego, especifique que desea agregar una cuenta profesional o educativa. Se abrirá el escáner.

- Digitalice el código de barras.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Espere mientras se activa la cuenta.

- Eso es todo. Debería ver ahora la nueva cuenta en la página de cuentas.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Add account](./media/multi-factor-authentication-azure-authenticator/accounts.png)

## Cómo agregar una cuenta de Azure manualmente

Si quiere agregar una cuenta manualmente, puede hacerlo de la siguiente manera:

- En primer lugar, vaya a la página de configuración de comprobación de seguridad. Para obtener información sobre cómo ir a esta página, vea [Cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

- Haga clic en el botón Configurar.
 
![Add account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Aparecerá una pantalla con un código de barras. Tenga en cuenta el código y la URL que se encuentra debajo del código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Abra ahora la aplicación Microsoft Authenticator; se le dirigirá a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una nueva cuenta, haga clic en el signo + y, luego, especifique que desea agregar una cuenta profesional o educativa. Se abrirá el escáner. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Haga clic en escribir el código manualmente en la parte inferior.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Add account](./media/multi-factor-authentication-azure-authenticator/manual.png)

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- Espere mientras se activa la cuenta.

- Eso es todo. Debería ver ahora la nueva cuenta en la página de cuentas.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Add account](./media/multi-factor-authentication-azure-authenticator/accounts.png)


## Cómo agregar una cuenta con TouchID
La aplicación móvil Microsoft Authenticator en iOS es compatible con Touch ID. Azure Multi-Factor Authentication permite a las organizaciones requerir un PIN además de disponer de su dispositivo registrado. Con esta nueva característica, los usuarios de iOS con dispositivos habilitados con Touch ID ya no necesitan volver a escribir el PIN. Cuando se configure, los usuarios solo digitalizarán su huella digital en lugar de escribir el PIN y pulsar Aprobar.

La configuración de Touch ID con Microsoft Authenticator es realmente sencilla. Solo tiene que completar un desafío de comprobación normal con PIN, y si su dispositivo admite Touch ID, lo configuraremos automáticamente para usted.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

A partir de ese momento, cuando se requiera que compruebe el inicio de sesión, pulsará en la notificación de inserción recibida y digitalizará su huella dactilar en lugar de escribir su PIN.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Eliminación de una cuenta

Para quitar cuentas individuales de la aplicación Microsoft Authenticator basta con tocar la cuenta. Obtendrá una opción para "Borrar".

![Quitar cuenta](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0831_2016-->