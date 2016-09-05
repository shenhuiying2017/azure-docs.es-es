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

## Adición de una cuenta a la aplicación Microsoft Authenticator

## Transición a la nueva aplicación Microsoft Authenticator

Con el lanzamiento de la nueva aplicación Microsoft Authenticator, se sustituye la antigua aplicación Azure Authenticator. La aplicación Azure Authenticator seguirá funcionando pero si decide dar el paso a la nueva aplicación Microsoft Authenticator, este artículo le servirá de ayuda.


## Transición a la nueva aplicación Microsoft Authenticator 

**Paso 1:** Instalación de Microsoft Authenticator.

![Nube](./media/multi-factor-authentication-azure-authenticator/home.png)

**Paso 2:** activar sus cuentas con la nueva aplicación

Asegúrese en primer lugar, de que tiene a mano el código QR o el código y la dirección URL para la cuenta que desea agregar a la aplicación.

> [AZURE.NOTE] ¿No está seguro de cómo obtener el código QR? Para obtener ayuda, póngase en contacto con el Servicio de asistencia.
> 
> ¿No puede activar su cuenta con la nueva aplicación? Póngase en contacto con el Servicio de asistencia.
>


Una vez que tenga el código QR delante, inicie la aplicación. Haga clic en +.


![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

Con esto se iniciará la cámara para examinar el código QR. Si no puede examinar el código QR, siempre tiene la opción de entrada manual.

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

- Abra ahora la aplicación Microsoft Authenticator; se le dirigirá a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una cuenta nueva, haga clic en el signo +. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Digitalice el código de barras.

![Add account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Espere mientras se activa la cuenta.

- Eso es todo. Debería ver ahora la nueva cuenta en la página de cuentas.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Cómo agregar una cuenta de Azure manualmente

Si quiere agregar una cuenta manualmente, puede hacerlo de la siguiente manera:

- En primer lugar, vaya a la página de configuración de comprobación de seguridad. Para obtener información sobre cómo ir a esta página, vea [Cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

- Haga clic en el botón Configurar.
 
![Add account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Aparecerá una pantalla con un código de barras. Tenga en cuenta el código y la URL que se encuentra debajo del código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Abra ahora la aplicación Microsoft Authenticator; se le dirigirá a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una cuenta nueva, haga clic en el signo +. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Haga clic en el botón para especificar manualmente de la parte inferior.

![Add account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Add account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Espere mientras se activa la cuenta.

- Eso es todo. Debería ver ahora la nueva cuenta en la página de cuentas.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## Cómo agregar una cuenta que no sea de Azure manualmente

Si quiere agregar una cuenta que no sea de Azure manualmente, por ejemplo, su cuenta de Microsoft, puede hacerlo de la siguiente manera:


- Agregar manualmente una cuenta que no sea de Azure puede realizarse mediante el análisis del código QR o escribiendo la clave secreta.
- Si va a especificar manualmente la clave secreta, obtenga la clave secreta en el sitio con el que está asociada la cuenta. Por ejemplo, en Outlook.com, vaya a la configuración de la cuenta, la configuración de seguridad y seleccione la opción para configurar una aplicación de autenticación. Tiene que seleccionar que indica que no puede escanear el código de barras para obtener la clave secreta.
- 

![Add account](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Abra la aplicación Microsoft Authenticator; se le dirigirá a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una cuenta nueva, haga clic en el signo +. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Examine el código QR o haga clic en el botón para especificar manualmente de la parte inferior. Si examina el código QR, omita el paso siguiente, debido a que la activación comenzará inmediatamente.

![Add account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Si va a introducir la clave secreta manualmente, escriba el nombre de la cuenta y la clave secreta que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Add account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Espere mientras se activa la cuenta. Ahora debería ver la nueva cuenta agregada.

![Add account](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- Para completar el proceso, especifique el código que se encuentra debajo de su cuenta (en este caso es 875 756) y escríbalo en el cuadro de la página de la que recibió la clave secreta y haga clic en Siguiente.

![Add account](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Cómo agregar una cuenta con TouchID
La aplicación móvil Microsoft Authenticator en iOS es compatible con Touch ID. Azure Multi-Factor Authentication permite a las organizaciones requerir un PIN además de disponer de su dispositivo registrado. Con esta nueva característica, los usuarios de iOS con dispositivos habilitados con Touch ID ya no necesitan volver a escribir el PIN. Cuando se configure, los usuarios solo digitalizarán su huella digital en lugar de escribir el PIN y pulsar Aprobar.

La configuración de Touch ID con Microsoft Authenticator es realmente sencilla. Solo tiene que completar un desafío de comprobación normal con PIN, y si su dispositivo admite Touch ID, lo configuraremos automáticamente para usted.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

A partir de ese momento, cuando se requiera que compruebe el inicio de sesión, pulsará en la notificación de inserción recibida y digitalizará su huella dactilar en lugar de escribir su PIN.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Eliminación de una cuenta

Para quitar cuentas individuales de la aplicación Microsoft Authenticator basta con tocar la cuenta. Obtendrá una opción para "Borrar".

![Quitar cuenta](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0824_2016-->