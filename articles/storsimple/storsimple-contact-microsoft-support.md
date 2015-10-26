<properties 
   pageTitle="Contactar al soporte técnico de Microsoft | Microsoft Azure"
   description="Aprenda a crear una solicitud de soporte técnico y a iniciar una sesión de soporte técnico en su dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/06/2015"
   ms.author="alkohli" />

# Contactar al soporte técnico de Microsoft

Si tiene algún problema con la solución de Microsoft Azure StorSimple, puede crear una solicitud de servicio de soporte técnico. En una sesión en línea con su ingeniero de soporte técnico, también deberá iniciar una sesión de soporte técnico en su dispositivo StorSimple. Este artículo le enseñará a:

- Crear una solicitud de soporte.
- Iniciar una sesión de soporte en la interfaz de Windows PowerShell del dispositivo de StorSimple.

Revise la sección [Información y contratos de nivel de servicio de soporte de la serie StorSimple 8000](https://msdn.microsoft.com/library/mt433077.aspx) antes de crear una solicitud de soporte técnico.

## Crear una solicitud de soporte

Lleve a cabo los siguientes pasos para crear una solicitud de soporte.

#### Para crear una solicitud de soporte

1. Las solicitudes de soporte técnico pueden crearse a través del [Portal de administración](http://manage.windowsazure.com/). En el [Portal de administración](http://manage.windowsazure.com/), haga clic en su **Nombre de cuenta** y después haga clic en **Póngase en contacto con el Soporte técnico de Microsoft**.

	![Contactar al soporte técnico de MS a través del Portal de administración](./media/storsimple-contact-microsoft-support/IC777286.png)

2. En el cuadro de diálogo **Póngase en contacto con el Soporte técnico de Microsoft**:

	1. En la lista desplegable, seleccione la **Suscripción** de destino asociada con su servicio StorSimple Manager. En **Tipo de soporte técnico** especifique **Técnico**. Para habilitar el soporte técnico, debe contar con un plan de soporte técnico de pago.

	2. Haga clic en el icono ![Icono de marca de verificación](./media/storsimple-contact-microsoft-support/IC740895.png) para ejecutar la opción **Crear incidencia**.

3. En la ventana **Servicio de soporte técnico de Microsoft**, en la lista desplegable **Producto**, elija **StorSimple**.

	![Contactar al soporte técnico de Microsoft - Producto](./media/storsimple-contact-microsoft-support/IC777288.png)

4. Siga las instrucciones en pantalla para clasificar la solicitud correctamente y proporcionar una descripción clara y específica de su problema.

Una vez que ha enviado la solicitud, un ingeniero de soporte se pondrá en contacto con usted tan pronto como sea posible para procesar su solicitud.

## Iniciar una sesión de soporte en Windows PowerShell para StorSimple

Para solucionar los problemas que pudiera experimentar con el dispositivo StorSimple, deberá ponerse en contacto con el equipo de soporte técnico de Microsoft. Es posible que el soporte técnico de Microsoft necesite utilizar una sesión de soporte para iniciar sesión en su dispositivo.

Lleve a cabo los siguientes pasos para iniciar una sesión de soporte.

#### Para iniciar una sesión de soporte

1. Acceda al dispositivo directamente desde la consola serie o a través de una sesión de telnet desde un equipo remoto. Para ello, siga los pasos descritos en [Usar PuTTY para conectarse a la consola serie del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. En la sesión que se abre, presione la tecla **Intro** para obtener un símbolo del sistema.

3. En el menú de la consola serie, seleccione la opción 1, **iniciar sesión con acceso completo**.

4. En el símbolo del sistema, escriba la siguiente contraseña:

	`Password1`

5. En el símbolo del sistema, escriba el siguiente comando:

	`Enable-HcsSupportAccess`

6. Aparecerá una cadena cifrada. Copie esta cadena en un editor de texto como el Bloc de notas.

7. Guarde esta cadena y enviéla por correo electrónico al soporte técnico de Microsoft.

> [AZURE.IMPORTANT]Puede deshabilitar el acceso al soporte técnico ejecutando `Disable-HcsSupportAccess`. El dispositivo StorSimple también intenta deshabilitar el acceso al soporte técnico 8 horas después de iniciada la sesión. Es un procedimiento recomendado cambiar las credenciales de su dispositivo StorSimple después de una sesión de soporte técnico.

<!---HONumber=Oct15_HO3-->