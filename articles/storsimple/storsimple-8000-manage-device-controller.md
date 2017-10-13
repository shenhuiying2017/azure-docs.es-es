---
title: "Administración de controladores de dispositivos de la serie StorSimple 8000 | Microsoft Docs"
description: "Aprenda cómo detener, reiniciar, apagar o restablecer los controladores de su dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>Administrar controladores de su dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se describen las distintas operaciones que pueden llevarse a cabo en los controladores de su dispositivo StorSimple. Los controladores de su dispositivo StorSimple son controladores redundantes (del mismo nivel) en una configuración activo-pasivo. En un momento dado, solo un controlador está activo y es el que procesa todas las operaciones de disco y red. El otro controlador está en el modo pasivo. Si se produce un error en el controlador activo, el controlador pasivo se activa automáticamente.

Este tutorial incluye instrucciones paso a paso para administrar los controladores del dispositivo mediante:

* Hoja **Controladores** del dispositivo en el servicio StorSimple Device Manager.
* Windows PowerShell para StorSimple

Le recomendamos que administre los controladores del dispositivo mediante el servicio StorSimple Device Manager. Si una acción solo puede realizarse mediante Windows PowerShell para StorSimple, el tutorial se refiere a ello en una nota.

Después de leer este tutorial, podrá:

* Reiniciar o apagar un controlador de dispositivo StorSimple
* Apagar un dispositivo StorSimple
* Restablecer el dispositivo StorSimple a los valores predeterminados de fábrica.

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar o apagar un solo controlador
No es necesario reiniciar o apagar un controlador como parte del funcionamiento normal del sistema. Las operaciones de apagado de un solo controlador de dispositivo son comunes solo en los casos en que un componente de hardware de dispositivo con error requiere reemplazo. También puede ser necesario reiniciar un controlador en situaciones en que el rendimiento se vea afectado por el uso excesivo de memoria o en la que un controlador no funcione correctamente. Asimismo, debe reiniciar un controlador después de la correcta sustitución del mismo, si desea habilitar y probar el controlador reemplazado.

El reinicio de un dispositivo no es problemático para los iniciadores conectados, siempre que el controlador pasivo esté disponible. Si un controlador pasivo no está disponible o está desactivado, el reinicio del controlador activo puede resultar en la interrupción del servicio y en tiempo de inactividad.

> [!IMPORTANT]
> * **Un controlador en ejecución nunca debe moverse físicamente ya que esto podría provocar una pérdida de redundancia y un mayor riesgo de tiempo de inactividad.**
> * El siguiente procedimiento se aplica solo al dispositivo de StorSimple físico. Para más información sobre cómo iniciar, detener y reiniciar el dispositivo StorSimple Cloud Appliance, consulte [Uso del dispositivo de nube](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Puede reiniciar o apagar un solo controlador de dispositivo mediante Azure Portal del servicio StorSimple Device Manager o Windows PowerShell para StorSimple.

Para administrar los controladores de su dispositivo desde Azure Portal, lleve a cabo los siguientes pasos.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Reinicio o apagado de un controlador en Azure Portal
1. En el servicio StorSimple Device Manager, vaya a **Dispositivos**. Seleccione el dispositivo de la lista de dispositivos. 

    ![Selección de un dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Vaya a **Configuración > Controladores**.
   
    ![Comprobar que los controladores del dispositivo StorSimple están en buen estado](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. En la hoja **Controladores**, compruebe que el estado de ambos controladores en el dispositivo sea **Correcto**. Seleccione un controlador, haga clic con el botón derecho y luego seleccione **Reiniciar** o **Apagar**.

    ![Selección de reinicio o apagado de los controladores del dispositivo StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Se crea un trabajo para reiniciar o apagar el controlador y se presentan las advertencias pertinentes, si las hay. Para supervisar el reinicio o apagado, vaya a **Servicio > Registros de actividad** y luego filtre por los parámetros específicos de su servicio. Si un controlador se ha apagado, deberá presionar el botón de encendido para activarlo.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar o apagar un controlador en Windows PowerShell para StorSimple
Lleve a cabo los siguientes pasos para apagar o reiniciar un único controlador de su dispositivo StorSimple desde Windows PowerShell para StorSimple.

1. Acceda al dispositivo desde la consola serie o mediante una sesión de telnet desde un equipo remoto. Para conectarse al Controlador 0 o al Controlador 1, siga los pasos que se describen en [Uso de PuTTY para conectarse a la consola serie del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**.
3. En el mensaje de pancarta que aparece, anote el controlador al que está conectado (Controlador 0 o Controlador 1) y si es el controlador activo o el pasivo (en espera).
   
   * Para apagar un solo controlador, en el símbolo del sistema, escriba:
     
       `Stop-HcsController`
     
       Se apaga el controlador al que está conectado. Si detiene el controlador activo, el dispositivo conmuta por error al controlador pasivo.

   * Para reiniciar un controlador, en el símbolo del sistema, escriba:
     
       `Restart-HcsController`
     
       Se reinicia el controlador al que está conectado. Si reinicia el controlador activo, conmutará por error al controlador pasivo antes del reinicio.

## <a name="shut-down-a-storsimple-device"></a>Apagar un dispositivo StorSimple

En esta sección se explica cómo apagar un dispositivo StorSimple en ejecución o con errores desde un equipo remoto. Un dispositivo se apaga después de que ambos controladores de dispositivo están apagados. Se realiza el apagado de un dispositivo cuando se mueve físicamente o se deja fuera de servicio.

> [!IMPORTANT]
> Antes de apagar el dispositivo, compruebe el estado de los componentes del dispositivo. Vaya al dispositivo y, a continuación, haga clic en **Configuración > Hardware health** (Mantenimiento de hardware). En la hoja **Estado y mantenimiento de hardware**, compruebe que el estado del LED de todos los componentes sea verde. Un dispositivo en buen estado tiene un estado verde. Si el dispositivo se apaga para sustituir un componente que no funciona correctamente, verá un estado de error (rojo) o de degradado (amarillo) para los componentes correspondientes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para apagar un dispositivo StorSimple

1. Use el procedimiento [reiniciar o apagar un controlador](#restart-or-shut-down-a-single-controller) para identificar y apagar el controlador pasivo del dispositivo. Puede realizar esta operación en Azure Portal o en Windows PowerShell para StorSimple.
2. Repita el paso anterior para apagar el controlador activo.
3. Ahora debe examinar el plano posterior del dispositivo. Una vez apagados por completo los dos controladores, los LED de estado de ambos controladores deben parpadear en rojo. Si necesita apagar el dispositivo por completo en este momento, cambie los interruptores de alimentación de los módulos de alimentación y refrigeración (PCM) a la posición de apagado. Esto debe apagar el dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Restablecer el dispositivo a los valores predeterminados de fábrica.

> [!IMPORTANT]
> Si necesita restablecer la configuración predeterminada de fábrica del dispositivo, póngase en contacto con Soporte técnico de Microsoft. El procedimiento descrito a continuación solo debe utilizarse junto con el Soporte técnico de Microsoft.

Este procedimiento describe cómo restablecer su dispositivo Microsoft Azure StorSimple a los valores predeterminados de fábrica mediante Windows PowerShell para StorSimple.
De forma predeterminada, el restablecimiento del dispositivo quita todos los datos y la configuración del clúster.

Lleve a cabo los siguientes pasos para restablecer su dispositivo Microsoft Azure StorSimple a los valores predeterminados de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para restablecer el dispositivo a los valores predeterminados de fábrica en Windows PowerShell para StorSimple
1. Acceda al dispositivo mediante su consola serie. Consulte el mensaje del banner que aparece para asegurarse de que está conectado al controlador **activo**.
2. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**.
3. En el símbolo del sistema, escriba el comando siguiente para restablecer todo el clúster, quitando toda la configuración de datos, metadatos y controlador:
   
    `Reset-HcsFactoryDefault`
   
    Para restablecer un único controlador, utilice el cmdlet [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) con el parámetro `-scope`).
   
    El sistema se reiniciará varias veces. Se le notificará cuando el restablecimiento  se haya completado correctamente. Según el modelo del sistema, un dispositivo 8100 puede tardar de 45 a 60 minutos y un 8600 de 60 a 90 minutos en finalizar este proceso.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Preguntas y respuestas sobre cómo administrar los controladores de dispositivo
En esta sección, hemos resumido algunas de las preguntas más frecuentes sobre la administración de los controladores de dispositivo de StorSimple.

**P.** ¿Qué ocurre si los dos controladores de mi equipo están activados y figuran como correctos y reinicio o apago el controlador activo?

**R.** Si los dos controladores del dispositivo están activados y figuran como correctos, se le solicitará confirmación. Puede elegir:

* **Reiniciar el controlador activo**: se le notifica que reiniciar un controlador activo provocará la conmutación por error del dispositivo al controlador pasivo. El controlador se reinicia.
* **Apagar un controlador activo**: se le notifica que apagar un controlador activo dará lugar a tiempo de inactividad. También deberá presionar el botón de encendido en el dispositivo para activar el controlador.

**P.** ¿Qué ocurre si el controlador pasivo del dispositivo no está disponible o está apagado y reinicio o apago el controlador activo?

**R.** Si el controlador pasivo del dispositivo no está disponible o está desactivado y decide:

* **Reiniciar el controlador activo**: se le notifica que continuar con la operación provocará una interrupción temporal del servicio y se le solicita confirmación.
* **Apagar un controlador activo**: se le notifica que continuar con la operación dará lugar a tiempo de inactividad. También debe presionar el botón de encendido en uno o ambos controladores para activar el dispositivo. Se le pedirá confirmación.

**P.** ¿Cuándo se produce un error en el reinicio o el apagado del controlador?

**R.** El reinicio o apagado de un controlador puede producir un error si:

* Ye está en curso una actualización del dispositivo.
* Ya está en curso un reinicio del controlador.
* Ya está en curso un apagado del controlador.

**P.** ¿Cómo se puede saber si se ha reiniciado o apagado un controlador?

**R.** Puede comprobar el estado del controlador en la hoja Controlador. El estado del controlador indicará si un controlador está en proceso de reiniciarse o de apagarse. Además, la hoja **Alertas** contendrá una alerta informativa si el controlador se reinicia o se apaga. También se registran las operaciones de reinicio y apagado del controlador en los registros de actividad. Para más información sobre los registros de actividad, vaya a [Visualizar los registros de actividad](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**P.** ¿Qué impacto tiene para las operaciones de E/S una conmutación por error del controlador?

**R.** Las conexiones TCP entre los iniciadores y el controlador activo se reiniciarán como resultado de la conmutación por error del controlador, pero se restablecerán cuando el controlador pasivo asuma la operación. Puede haber una pausa temporal (de menos de 30 segundos) en la actividad de E/S entre los iniciadores y el dispositivo en el transcurso de esta operación.

**P.** ¿Cómo puedo volver a poner en servicio mi controlador después de haberlo apagado y quitado?

**R.** Para que un controlador vuelva a funcionar, se debe insertar en el chasis tal como se describe en [Reemplazar un módulo de controlador en el dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Pasos siguientes
* Si tiene algún problema con los controladores de su dispositivo StorSimple que no pueda resolver mediante el uso de los procedimientos descritos en este tutorial, [póngase en contacto con el servicio técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).
* Para aprender más sobre el uso del servicio StorSimple Device Manager service, vaya a [Uso del servicio StorSimple Device Manager service para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

