<properties 
   pageTitle="Contactar al soporte técnico de Microsoft | Microsoft Azure"
   description="Aprenda a crear una solicitud de soporte técnico y a iniciar una sesión de soporte técnico en su dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Contactar al soporte técnico de Microsoft

Si tiene problemas con la solución de Microsoft Azure StorSimple, puede crear una solicitud de servicio de soporte técnico. Durante una sesión en línea con un ingeniero de soporte, podría tener que iniciar una sesión de soporte en su dispositivo StorSimple. Este artículo le guiará a través del proceso de creación de una solicitud de soporte y también le explicará cómo iniciar una sesión de apoyo en la interfaz de Windows PowerShell del dispositivo StorSimple.

## Crear una solicitud de soporte

Lleve a cabo los siguientes pasos para crear una solicitud de soporte.

#### Para crear una solicitud de soporte

1. Las solicitudes de soporte pueden crearse a través del [Portal de administración](http://manage.windowsazure.com/). En el [Portal de administración](http://manage.windowsazure.com/), haga clic en su **Nombre de cuenta** y, a continuación, haga clic en **Contactar al soporte técnico de Microsoft**.

	![Contactar al soporte técnico de MS a través del Portal de administración](./media/storsimple-contact-microsoft-support/IC777286.png)

1. En el cuadro de diálogo **Contactar al soporte técnico de Microsoft**:


	- En la lista desplegable, seleccione la **Suscripción** de destino asociada con su servicio de Administrador de StorSimple. En **Tipo de soporte** especifique **Técnico**. Para habilitar el soporte técnico, debe contar con un plan de soporte pago.

	1. Haga clic en el icono ![Icono de marca de verificación](./media/storsimple-contact-microsoft-support/IC740895.png) para ejecutar la opción **Crear incidencia**.

1. En la ventana **Soporte técnico de Microsoft**, de la lista desplegable **Producto**, elija **StorSimple**.

	![Contactar al soporte técnico de Microsoft - Producto](./media/storsimple-contact-microsoft-support/IC777288.png)

1. Siga las instrucciones en pantalla para clasificar la solicitud correctamente y proporcionar una descripción clara y específica de su problema.

Una vez que ha enviado la solicitud, un ingeniero de soporte se pondrá en contacto con usted tan pronto como sea posible para procesar su solicitud.

## Iniciar una sesión de soporte en Windows PowerShell para StorSimple

Para solucionar los problemas que pudiera experimentar con el dispositivo StorSimple, deberá ponerse en contacto con el equipo de soporte técnico de Microsoft. Es posible que el soporte técnico de Microsoft necesite utilizar una sesión de soporte para iniciar sesión en su dispositivo.

Lleve a cabo los siguientes pasos para iniciar una sesión de soporte.

#### Para iniciar una sesión de soporte

1. Acceda al dispositivo directamente desde la consola serie o a través de una sesión de telnet desde un equipo remoto. Para ello, siga los pasos descritos en [Uso de PuTTy para conectarse a la consola serie del dispositivo](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

1. En la sesión que se abre, presione **Entrar** para obtener un símbolo del sistema.

1. En el menú de la consola serie, seleccione la opción 1, **iniciar sesión con acceso completo**.

1. En el símbolo del sistema, escriba la siguiente contraseña:

	`Password1`

1. En el símbolo del sistema, escriba el siguiente comando:

	`Enable-HcsSupportAccess`

1. Aparecerá una cadena cifrada. Copie esta cadena en un editor de texto como el Bloc de notas.

1. Guarde esta cadena y enviéla por correo electrónico al soporte técnico de Microsoft.

> [AZURE.IMPORTANT]Puede deshabilitar el acceso al soporte técnico ejecutando`Disable-HcsSupportAccess`. El dispositivo StorSimple también intenta deshabilitar el acceso al soporte técnico 8 horas después de iniciada la sesión. Es un procedimiento recomendado cambiar las credenciales de su dispositivo StorSimple después de una sesión de soporte técnico.

<!---HONumber=August15_HO6-->