<properties
	pageTitle="Aplicación Microsoft Authenticator para teléfonos móviles | Microsoft Azure"
	description="Aprenda a actualizar a la versión más reciente de Azure Authenticator."
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

La aplicación Microsoft Authenticator proporciona un nivel de seguridad adicional a la cuenta de Azure (por ejemplo, bsimon@contoso.onmicrosoft.com), la cuenta profesional local (por ejemplo, bsimon@contoso.com) o la cuenta de Microsoft (por ejemplo, bsimon@outlook.com).

La aplicación funciona de una de estas dos formas:

- **Notificación**. La aplicación puede ayudar a impedir el acceso no autorizado a las cuentas y detener las transacciones fraudulentas mediante el envío de una notificación al smartphone o a la tableta. Solo tiene que ver la notificación y, si es legítima, seleccionar **Comprobar**. De lo contrario, seleccione **Denegar**. Para más información sobre cómo denegar notificaciones, consulte How to use the Deny and Report Fraud Feature for Multi-Factor Authentication (Uso de la característica Denegar y notificar fraude para Multi-Factor Authentication).

- **Contraseña con código de comprobación**. La aplicación puede utilizarse como token de software para generar un código de comprobación de OAuth. Escriba el código que facilita la aplicación en la pantalla de inicio de sesión, junto con el nombre de usuario y la contraseña, cuando se le pida. El código de comprobación es una forma adicional de autenticación.

Con el lanzamiento de la aplicación Microsoft Authenticator, se sustituye la antigua aplicación Azure Authenticator. La aplicación Azure Authenticator seguirá funcionando, pero si decide dar el paso a la nueva aplicación Microsoft Authenticator, este artículo puede servirle de ayuda.

## Instalación de la aplicación

La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Incorporación de cuentas a la aplicación

Siga uno de los procedimientos siguientes para cada cuenta que desee agregar a la aplicación Microsoft Authenticator.

### Incorporación de una cuenta a la aplicación con el escáner de códigos QR

1. Vaya a la pantalla de configuración de comprobación de seguridad. Para información sobre cómo ir a esta pantalla, consulte la sección sobre el [cambio de la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

2. Seleccione **Configurar**.

	![Botón Configurar en la pantalla de configuración de la comprobación de seguridad](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	Aparecerá una pantalla con un código QR.

	![Pantalla que proporciona el código QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra la aplicación Microsoft Authenticator. En la pantalla **Cuentas**, seleccione **+** y especifique que desea agregar una cuenta profesional o de formación.

	![Pantalla Cuentas con el signo más](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![Pantalla para especificar una cuenta profesional o de formación](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Utilice la cámara para digitalizar el código QR y seleccione **Listo** para cerrar la pantalla de código QR.

	![Pantalla para digitalizar un código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	Si la cámara no funciona correctamente, puede escribir manualmente la dirección URL y el código QR. Para más información, consulte [Incorporación manual de una cuenta a la aplicación](#add-an-account-to-the-app-manually).

5. Espere mientras se activa la cuenta. Cuando termine la activación, seleccione **Contact me** (Contacto). Con esto se envía una notificación o un código de verificación al teléfono. Seleccione **Comprobar**.

	![Pantalla para seleccionar la comprobación de inicio de sesión](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Si su empresa requiere un PIN para aprobar la comprobación de inicio de sesión, escríbalo.

	![Cuadro para escribir un NIP](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Cuando haya terminado de escribir el PIN, seleccione **Cerrar**. A estas alturas, la verificación debería haberse realizado correctamente.
8. Lo recomendable es introducir el número de teléfono móvil por si perdiera el acceso a la aplicación. Especifique el país en la lista desplegable y escriba su número de teléfono móvil en la casilla junto al país. Seleccione **Siguiente**.
9. Llegados a este punto, ya ha configurado el método de contacto. Ahora es el momento de configurar contraseñas de aplicación para las aplicaciones sin explorador, como Outlook 2010 o anterior. Si no utiliza estas aplicaciones, seleccione **Listo**. De lo contrario, continúe con el paso siguiente.

	![Pantalla para crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Si está usando aplicaciones sin explorador, copie la contraseña de aplicación proporcionada y péguela en la aplicación sin explorador. Para información acerca de aplicaciones específicas como Outlook y Lync, consulte How to change the password in your email to the app password (Cómo cambiar la contraseña del correo electrónico por la de aplicación) y How to change the password in your application to the app password (Cómo cambiar la contraseña de la aplicación por la contraseña de aplicación).
11. Seleccione **Listo**.

Ahora debería ver la nueva cuenta en la pantalla **Cuentas**.

![Pantalla Cuentas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### Incorporación manual de una cuenta a la aplicación

1. Vaya a la pantalla de configuración de comprobación de seguridad. Para información sobre cómo ir a esta pantalla, consulte la sección sobre el [cambio de la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

2. Seleccione **Configurar**.

	![Botón Configurar en la pantalla de configuración de la comprobación de seguridad](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	Aparecerá una pantalla con un código QR. Anote la dirección URL y el código.

	![Pantalla que proporciona el código QR y la dirección URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra la aplicación Microsoft Authenticator. En la pantalla **Cuentas**, seleccione **+** y especifique que desea agregar una cuenta profesional o de formación.

	![Pantalla Cuentas con el signo más](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![Pantalla para especificar una cuenta profesional o de formación](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. En el digitalizador, elija **escribir el código manualmente**.

	![Pantalla para digitalizar un código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. En la aplicación, escriba el código y la dirección URL en los cuadros correspondientes.

	![Pantalla para escribir el código y la dirección URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

	![Pantalla para escribir el código y la dirección URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Espere mientras se activa la cuenta. Cuando termine la activación, seleccione **Contact me** (Contacto). Con esto se envía una notificación o un código de verificación al teléfono. Seleccione **Comprobar**.

Ahora debería ver la nueva cuenta en la pantalla **Cuentas**.

![Pantalla Cuentas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### Incorporación de una cuenta a la aplicación con Touch ID

La aplicación Microsoft Authenticator de iOS es compatible con Touch ID. Azure Multi-Factor Authentication permite a las organizaciones pedir un PIN para los dispositivos. Con Touch ID, los usuarios de iOS no tienen por qué escribir un PIN. En su lugar, pueden digitalizar su huella y seleccionar **Aprobar**.

Configurar Touch ID con Microsoft Authenticator es sencillo. Un PIN supone un reto de comprobación normal. Si el dispositivo es compatible con Touch ID, Microsoft Authenticator lo configura automáticamente para esa cuenta.

![Comprobación de la configuración de Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

A partir de ese momento, cuando se requiera que compruebe el inicio de sesión, seleccione la notificación push recibida y digitalice la huella dactilar en lugar de escribir su PIN.

![Notificación push](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Desinstalación de la aplicación de autenticación de Azure antigua

Cuando haya agregado todas las cuentas a la aplicación nueva, desinstale la aplicación antigua del teléfono.

## Eliminación de una cuenta

Para quitar una cuenta de la aplicación Microsoft Authenticator, selecciónela y elija **Eliminar**.

![Botón Eliminar](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0914_2016-->