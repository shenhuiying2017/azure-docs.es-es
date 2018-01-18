---
title: "Visualización y administración de alertas para dispositivos de la serie StorSimple 8000 | Microsoft Docs"
description: "Describe las condiciones de alerta y gravedad de StorSimple, cómo configurar notificaciones de alerta y cómo usar el servicio StorSimple Device Manager para administrar las alertas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: e86b6af562208e51e36b4679fd088ea399ce70b8
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Uso del servicio StorSimple Device Manager para ver y administrar alertas de StorSimple

## <a name="overview"></a>Información general

La hoja **Alertas** del servicio StorSimple Device Manager proporciona una forma de revisar y borrar alertas relacionadas con el dispositivo StorSimple en tiempo real. Desde esta hoja, puede supervisar de forma centralizada los problemas de estado de los dispositivos StorSimple y la solución general Microsoft Azure StorSimple.

En este tutorial se describen las condiciones de alerta comunes, los niveles de gravedad de alerta y cómo configurar las notificaciones de alerta. Además, incluye tablas de referencia rápida de alertas, que le permiten localizar una alerta específica rápidamente y responder de forma adecuada.

![Página de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condiciones de alerta comunes

El dispositivo StorSimple genera alertas en respuesta a una serie de condiciones. A continuación se detallan los tipos más comunes de condiciones de alerta:

* **Problemas de hardware** – Estas alertas le informan sobre el estado del hardware. Le permiten saber si son necesarias actualizaciones de firmware, si una interfaz de red tiene problemas o si existe un problema con alguno de sus discos de datos.
* **Problemas de conectividad** : estas alertas se producen cuando existen dificultades para transferir datos. Los problemas de comunicación pueden ocurrir durante la transferencia de datos a y de la cuenta de almacenamiento de Azure o debido a la falta de conectividad entre los dispositivos y el servicio StorSimple Device Manager. Los problemas de comunicación están entre los más difíciles de solucionar porque existen numerosos puntos de error. En primer lugar, siempre debe verificar que estén disponibles la conectividad de la red y el acceso a Internet antes de pasar a soluciones de problemas más avanzados. Si necesita ayuda para la solución de problemas, vaya a [Solución de problemas con el cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemas de rendimiento** : estas alertas se producen cuando el sistema no tiene un rendimiento óptimo, por ejemplo, cuando está sobrecargado.

Además, podría ver alertas relacionadas con seguridad, actualizaciones o errores en el trabajo.

## <a name="alert-severity-levels"></a>Niveles de gravedad de alerta

Las alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación de alerta y la necesidad de dar respuesta a la misma. Los niveles de gravedad son:

* **Crítica** – Esta alerta es en respuesta a una condición que está afectando el rendimiento correcto del sistema. Se requiere una acción para asegurar que el servicio de StorSimple no se vea interrumpido.
* **Advertencia** : esta condición puede convertirse en crítica si no se resuelve. Debe investigar la situación y tomar las acciones necesarias para resolver el problema.
* **Información** : esta alerta contiene información que puede ser útil para el seguimiento y la administración del sistema.

## <a name="configure-alert-settings"></a>Configurar alertas

Puede elegir si desea recibir una notificación por correo electrónico de las condiciones de alerta para cada uno de los dispositivos StorSimple. Además, puede identificar otros destinatarios de notificaciones de alerta mediante la especificación de sus direcciones de correo electrónico en el cuadro **Otros destinatarios de correo electrónico** , separadas por punto y coma.

> [!NOTE]
> Puede escribir un máximo de 20 direcciones de correo electrónico por dispositivo.

Después de habilitar la notificación por correo electrónico para un dispositivo, los miembros de la lista de notificación recibirán un mensaje de correo electrónico cada vez que se produzca una alerta crítica. Los mensajes se enviarán desde *storsimple-alerts-noreply@mail.windowsazure.com* y describirán la condición de alerta. Los destinatarios pueden hacer clic en **Eliminar suscripción** para quitarse de la lista de notificación por correo electrónico.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para habilitar la notificación de alertas por correo electrónico de un dispositivo
1. Vaya al servicio StorSimple Device Manager. En la lista de dispositivos, seleccione y haga clic en el dispositivo que desea configurar.
2. Vaya a **Configuración** > **General** para seleccionar el dispositivo.

   ![Hoja Alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. En la hoja **Configuración general**, vaya a **Configuración de alertas** y establezca lo siguiente:
   
   1. En el campo **Enviar notificación por correo electrónico**, seleccione **SÍ**.
   2. En el campo **Administradores del servicio de correo electrónico**, seleccione **SÍ** para que el administrador de servicios y todos los coadministradores reciban las notificaciones de alerta.
   3. En el campo **Otros destinatarios de correo electrónico** , escriba las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres con el formato *someone@somewhere.com*. Utilice punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo. 
      
3. Para enviar una notificación de correo electrónico de prueba, haga clic en **Enviar correo electrónico de prueba**. El servicio StorSimple Device Manager mostrará mensajes de estado mientras envía la notificación de prueba.

    ![Configuración de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Verá una notificación cuando se envíe el correo electrónico de prueba. 
   
    ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Si el mensaje de notificación de prueba no puede enviarse, el servicio StorSimple Device Manager mostrará un mensaje de error adecuado. Espere algunos minutos y, después, intente volver a enviar el mensaje de notificación de prueba. 

5. Cuando se haya completado la configuración, haga clic en **Guardar**. Cuando se le pida confirmación, haga clic en **Sí**.

     ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Ver y realizar un seguimiento de las alertas

La hoja de resumen del servicio StorSimple Device Manager le permite ver de un vistazo el número de alertas en sus dispositivos virtuales, organizadas por nivel de gravedad.

![Panel de alertas](./media/storsimple-8000-manage-alerts/device-summary4.png)

Al hacer clic en el nivel de gravedad, se abre la pestaña **Alertas**. Los resultados solo incluyen las alertas que coinciden con ese nivel de gravedad.

Al hacer clic en la lista, se muestran detalles adicionales de la alerta, incluyendo la última vez que se emitió la alerta, el número de repeticiones de la alerta en el dispositivo y la acción recomendada para resolver la alerta. Si se trata de una alerta de hardware, también identificará el componente de hardware.

![Ejemplo de alerta de hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Si necesita enviar la información al soporte técnico de Microsoft, puede copiar los detalles de las alertas en un archivo de texto. Después de haber seguido las recomendación y resuelto la condición de alerta en el entorno local, debe borrar la alerta del dispositivo; para ello, selecciónela en la hoja **Alertas** y haga clic en **Borrar**. Para borrar varias alertas, seleccione cada alerta, haga clic en cualquier columna excepto en la columna **Alerta** y, a continuación, haga clic en **Borrar** después de haber seleccionado todas las alertas que vaya a borrar. Tenga en cuenta que algunas alertas se eliminan automáticamente cuando se resuelve el problema o cuando el sistema actualiza la alerta con nueva información.

Al hacer clic en **Borrar**, tendrá la posibilidad de proporcionar comentarios sobre la alerta y los pasos llevados a cabo para resolver el problema. El sistema borrará algunos eventos si se desencadena otro evento con información nueva. En ese caso, verá el siguiente mensaje.

![Borrar mensaje de alerta](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Clasificar y revisar alertas

Quizá le resulte más eficiente ejecutar informes de alertas que le permitan revisar y borrar las mismas en grupos. Además, la hoja **Alertas** puede mostrar hasta 250 alertas. Si se ha superado ese número de alertas, no todas las alertas se mostrarán en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

* **Estado**: puede mostrar alertas **Activas** o **Desactivadas**. Las alertas activas se siguen desencadenando en el sistema, mientras que las alertas desactivadas han sido borradas de forma manual por un administrador o se han borrado de forma programada porque el sistema actualizó la condición de alerta con nueva información.
* **Gravedad** – Puede mostrar las alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo las de determinada gravedad, como las alertas críticas únicamente.
* **Origen** – Puede mostrar las alertas de todos los orígenes, o limitar las alertas procedentes del servicio, o de uno o todos los dispositivos.
* **Intervalo de tiempo**: al especificar las fechas **Desde** y **Hasta** y las marcas de tiempo, puede ver las alertas durante el período de tiempo que le interesa.

![Lista de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Referencia rápida de alertas

Las siguientes tablas enumeran algunas de las alertas de Microsoft Azure StorSimple que pueden encontrarse, así como información adicional y recomendaciones donde estén disponibles. Las alertas del dispositivo StorSimple se clasifican en alguna de las siguientes categorías:

* [Alertas de conectividad de la nube](#cloud-connectivity-alerts)
* [Alertas de clúster](#cluster-alerts)
* [Alertas de recuperación ante desastres](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de errores de trabajo](#job-failure-alerts)
* [Alertas de volumen anclado localmente](#locally-pinned-volume-alerts)
* [Alertas de red](#networking-alerts)
* [Alertas de rendimiento](#performance-alerts)
* [Alertas de seguridad](#security-alerts)
* [Alertas de paquetes de soporte](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividad de la nube

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| No se puede establecer la conectividad a <*nombre de credencial de nube*>. |No es posible conectarse a la cuenta de almacenamiento. |Parece que podría existir un problema de conectividad con su dispositivo. Ejecute el cmdlet `Test-HcsmConnection` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y corregir el problema. Si la configuración es correcta, puede que el problema sea con las credenciales de la cuenta de almacenamiento por la que se desencadenó la alarma. En este caso, use el cmdlet `Test-HcsStorageAccountCredential` para determinar si hay problemas que pueda resolver.<ul><li>Compruebe la configuración de red.</li><li>Compruebe las credenciales de la cuenta de almacenamiento.</li></ul> |
| No hemos recibido ningún latido de su dispositivo durante los últimos <*número*> minutos. |No es posible conectarse al dispositivo. |Parece que existe un problema de conectividad con su dispositivo. Use el cmdlet `Test-HcsmConnection` de la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y corregir el problema, o póngase en contacto con su administrador de red. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamiento de StorSimple cuando se produce un error de conectividad de la nube

¿Qué sucede si se produce un error de conectividad de la nube para mi dispositivo StorSimple que se ejecuta en producción?

Si se produce un error en la conectividad de la nube en el dispositivo de producción de StorSimple, según el estado del dispositivo, puede ocurrir lo siguiente:

* **Para los datos locales en el dispositivo**: durante algún tiempo, no se producirá ninguna interrupción y las lecturas se seguirán atendiendo. Sin embargo, cuando el número de operaciones de E/S pendientes aumenta y excede el límite, las lecturas pueden comenzar a fallar.

    Según la cantidad de datos en el dispositivo, las escrituras seguirán también produciéndose en las primeras horas después de la interrupción de la conectividad de la nube. Las escrituras se ralentizarán y finalmente comenzarán a fallar si se interrumpe la conectividad de la nube durante varias horas. (Hay almacenamiento temporal en el dispositivo para los datos que se van a insertar en la nube. Este área se vacía cuando se envían los datos. Si se produce un error en la conectividad, los datos de este área de almacenamiento no se insertarán en la nube y se producirá un error de E/S).
* **Para los datos en la nube**: en la mayoría de los errores de conectividad de la nube, se devuelve un error. Una vez restaurada la conectividad, se reanudarán las operaciones de E/S sin que el usuario tiene que incorporar el volumen en línea. En raras ocasiones, podría ser necesaria la intervención del usuario para recuperar el volumen en línea desde Azure Portal.
* **Para las instantáneas de nube en curso**: la operación se reintenta varias veces en 4 o 5 horas y, si no se restaura la conectividad, se produce un error en las instantáneas de nube.

### <a name="cluster-alerts"></a>Alertas de clúster

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| El dispositivo conmutó por error a <*nombre del dispositivo*>. |El dispositivo está en modo de mantenimiento. |Conmutación por error de dispositivo debido a entrada o salida del modo de mantenimiento. Esto es normal y no se requiere ninguna acción. Después de confirmar esta alerta, bórrela de la página de alertas. |
| El dispositivo conmutó por error a <*nombre del dispositivo*>. |Se actualizó recientemente el firmware o software del dispositivo. |Se produjo una conmutación por error de clúster debido a una actualización. Esto es normal y no se requiere ninguna acción. Después de confirmar esta alerta, bórrela de la página de alertas. |
| El dispositivo conmutó por error a <*nombre del dispositivo*>. |Se apagó o reinició el controlador. |Conmutación por error de dispositivo debido al apagado o reinicio del controlador por parte de un administrador. No se requiere ninguna acción. Después de confirmar esta alerta, bórrela de la página de alertas. |
| El dispositivo conmutó por error a <*nombre del dispositivo*>. |Conmutación por error planeada. |Verifique que se trató de una conmutación por error planeada. Después de realizar la acción apropiada, borre esta alerta de la página de alertas. |
| El dispositivo conmutó por error a <*nombre del dispositivo*>. |Conmutación por error no planeada. |StorSimple está diseñado para recuperarse automáticamente de las conmutaciones por error no planeadas. Si ve un gran número de estas alertas, póngase en contacto con el soporte técnico de Microsoft. |
| El dispositivo conmutó por error a <*nombre del dispositivo*>. |Otras causas/causas desconocidas. |Si ve un gran número de estas alertas, póngase en contacto con el soporte técnico de Microsoft. Después de resolver el problema, borre esta alerta de la página de alertas. |
| Un servicio de dispositivo crítico indica un estado error. |Error del servicio de ruta de datos. |Para recibir asistencia, póngase en contacto con el servicio de soporte técnico de Microsoft. |
| La dirección IP virtual de la interfaz de red <*DATA #*> indica un estado de error. |Otras causas/causas desconocidas. |A veces las condiciones temporales pueden causar estas alertas. Si es el caso, esta alerta se borrará automáticamente después de un tiempo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. |
| La dirección IP virtual de la interfaz de red <*DATA #*> indica un estado de error. |Nombre de la interfaz: <*DATA #*> La dirección IP <IP address> no se puede poner en línea porque se ha detectado una dirección IP duplicada en la red. |Asegúrese de que la dirección IP duplicada se quita de la red o vuelva a configurar la interfaz con una dirección IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperación ante desastres

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Las operaciones de recuperación no lograron restaurar todos los valores de configuración para este servicio. Los datos de configuración de dispositivo están en un estado incoherente para algunos dispositivos. |Incoherencia de datos detectada después de la recuperación ante desastres. |Los datos cifrados en el servicio no están sincronizados con los del dispositivo. Autorice el dispositivo <*nombre del dispositivo*> desde StorSimple Device Manager para iniciar el proceso de sincronización. Use la interfaz de Windows PowerShell para StorSimple para ejecutar el cmdlet `Restore-HcsmEncryptedServiceData` en el dispositivo <*nombre del dispositivo*>, y proporcione la contraseña anterior como entrada a este cmdlet para restaurar el perfil de seguridad. Después, ejecute el cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` para actualizar la clave de cifrado de datos de servicio. Después de realizar la acción apropiada, borre esta alerta de la página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| El componente de hardware <*id. de componente*> notifica el estado como <*estado*>. | |A veces las condiciones temporales pueden causar estas alertas. Si es el caso, esta alerta se borrará automáticamente después de un tiempo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. |
| Funcionamiento incorrecto del controlador pasivo. |El controlador pasivo (secundario) no funciona. |El dispositivo está operativo, pero uno de los controladores no funciona. Intente reiniciar ese controlador. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft. |

### <a name="job-failure-alerts"></a>Alertas de errores de trabajo

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Error de copia de seguridad de <*id. de grupo de volúmenes de origen*>. |Error de trabajo de copia de seguridad. |Podrían existir problemas de conectividad que impiden que la operación de copia de seguridad se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el número máximo de copias de seguridad. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Después de realizar la acción apropiada, borre esta alerta de la página de alertas. |
| Error de clonación de <*id. de elemento de copia de seguridad de origen*> a <*números de serie de volúmenes de destino*>. |Error de trabajo de clonación. |Actualice la lista de copias de seguridad para verificar que la copia de seguridad siga siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de clonación se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el límite de almacenamiento. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Después de realizar la acción apropiada para resolver el problema, borre esta alerta de la página de alertas. |
| Error de restauración de <*id. de elemento de copia de seguridad de origen*>. |Error de trabajo de restauración. |Actualice la lista de copias de seguridad para verificar que la copia de seguridad siga siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de restauración se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el límite de almacenamiento. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Después de realizar la acción apropiada para resolver el problema, borre esta alerta de la página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volumen anclado localmente

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Error de creación de un volumen local <*nombre de volumen*>. |Error del trabajo de creación de volumen. <*Mensaje de error correspondiente al código de error*&gt;. |Puede que existan problemas de conectividad que impidan que la operación de creación de espacio se complete correctamente. Los volúmenes anclados localmente están aprovisionados densamente y el proceso de creación de espacio supone escribir los volúmenes en capas en la nube. Si no hay ningún problema de conectividad, puede que haya agotado el espacio local en el dispositivo. Determine si hay espacio en el dispositivo antes de volver a intentar esta operación. |
| Error de expansión del volumen local <*nombre de volumen*>. |Error del trabajo de modificación del volumen debido a <*mensaje de error correspondiente al código de error de incorrecto*>. |Puede que existan problemas de conectividad que impidan que la operación de expansión del volumen se realice correctamente. Los volúmenes anclados localmente están aprovisionados densamente y el proceso de ampliar el espacio existente supone escribir los volúmenes en capas en la nube. Si no hay ningún problema de conectividad, puede que haya agotado el espacio local en el dispositivo. Determine si hay espacio en el dispositivo antes de volver a intentar esta operación. |
| Error de conversión de volumen <*nombre de volumen*>. |El trabajo de conversión de volumen para convertir el tipo de volumen anclado localmente en un volumen en capas ha dado error. |La conversión del volumen anclado localmente en un volumen en capas no se ha podido realizar. Asegúrese de que no haya ningún problema de conectividad que impida que la operación se realice correctamente. Para solucionar problemas de conectividad, vaya a [Solución de problemas con el cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>El volumen original anclado localmente ahora se ha marcado como un volumen en capas, dado que algunos de sus datos se han escrito en la nube durante la conversión. El volumen en capas resultante todavía ocupa espacio local en el dispositivo que no se puede reclamar para futuros volúmenes locales.<br>Solucione los problemas de conectividad, borre la alerta y vuelva a convertir este volumen en anclado localmente para garantizar que todos los datos vuelven a estar disponibles localmente. |
| Error de conversión de volumen <*nombre de volumen*>. |El trabajo de conversión de volumen para convertir el tipo de volumen en capas en un volumen anclado localmente ha dado error. |La conversión del volumen en capas a un volumen anclado localmente no se ha podido realizar. Asegúrese de que no haya ningún problema de conectividad que impida que la operación se realice correctamente. Para solucionar problemas de conectividad, vaya a [Solución de problemas con el cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>El volumen en capas original se marca ahora como volumen anclado localmente y, como parte del proceso de conversión, continúa teniendo datos que residen en la nube, mientras que el espacio con aprovisionamiento denso en el dispositivo para este volumen ya no se puede reclamar para futuros volúmenes locales.<br>Solucione los problemas de conectividad, borre la alerta y vuelva a convertir este volumen en el tipo de volumen en capas original para asegurarse de que se pueda reclamar el espacio local densamente aprovisionado en el dispositivo. |
| A punto de consumir el espacio local para instantáneas locales de <*nombre del grupo de volúmenes*> |Las instantáneas locales de la directiva de copia de seguridad podrían quedarse pronto sin espacio e invalidarse para evitar errores de escritura del host. |La existencia de instantáneas locales frecuentes junto con una alta actividad de datos en los volúmenes asociados a este grupo de directiva de copia de seguridad están haciendo que el espacio local en el dispositivo se consuma rápidamente. Elimine las instantáneas locales que no sean necesarias. Asimismo, actualice las programaciones de instantáneas locales de esta directiva de copia de seguridad para que se tomen las instantáneas locales menos frecuentes, y asegúrese de que las instantáneas de nube se toman con regularidad. Si no se realizan estas acciones, el espacio local de estas instantáneas puede agotarse pronto y el sistema las eliminará automáticamente para asegurarse de que las escrituras del host se siguen procesando correctamente. |
| Las instantáneas locales para <*nombre del grupo de volúmenes*> se han invalidado. |Las instantáneas locales para <*nombre del grupo de volúmenes*> se han invalidado y, luego, se han eliminado porque superaban el espacio local en el dispositivo. |Para asegurarse de que esto no vuelva a pasar en un futuro, revise las programaciones de instantáneas locales de esta directiva de copia de seguridad y elimine las instantáneas locales que ya no sean necesarias. La existencia de instantáneas locales frecuentes junto con una alta actividad de datos en los volúmenes asociados a este grupo de directiva de copia de seguridad pueden hacer que el espacio local en el dispositivo se consuma rápidamente. |
| Error de restauración de <*id. de elemento de copia de seguridad de origen*>. |Error del trabajo de restauración. |Si tiene volúmenes anclados localmente o una mezcla de volúmenes anclados localmente y volúmenes en capas en esta directiva de copia de seguridad, actualice la lista de copias de seguridad para comprobar que la copia de seguridad sigue siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de restauración se complete correctamente. Los volúmenes anclados localmente que se estaban restaurando como parte de este grupo de instantáneas no han descargados todos sus datos en el dispositivo y, si tiene una mezcla de volúmenes anclados localmente y en capas en este grupo de instantáneas, no se sincronizarán entre sí. Para completar correctamente la operación de restauración, desconecte los volúmenes de este grupo en el host y vuelva a intentar la operación de restauración. Tenga en cuenta que las modificaciones realizadas en los datos del volumen durante el proceso de restauración se perderán. |

### <a name="networking-alerts"></a>Alertas de red

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| No se han podido iniciar los servicios de StorSimple. |Error de ruta de datos |Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |
| Se ha detectado una dirección IP duplicada para 'Data0'. | |El sistema ha detectado un conflicto para la dirección IP '10.0.0.1'. El recurso de red 'Data0' en el dispositivo *<device1>* está sin conexión. Asegúrese de que ninguna otra entidad de esta red utilice esta dirección IP. Para solucionar los problemas de red, vaya a [Solución de problemas con el cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Para resolver este problema, póngase en contacto con el administrador de red. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |
| La dirección IPv4 (o IPv6) para 'Data0' está sin conexión. | |El recurso de red 'Data0' con la dirección IP '10.0.0.1'. El prefijo de longitud '22' en el dispositivo *<device1>* está sin conexión. Asegúrese de que los puertos de conmutador a los que está conectada esta interfaz están operativos. Para solucionar los problemas de red, vaya a [Solución de problemas con el cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| No se pudo conectar al servicio de autenticación. |Error de ruta de datos |La dirección URL que se usa para autenticar no está accesible. Asegúrese de que las reglas de firewall incluyen los patrones de dirección URL especificados para el dispositivo StorSimple. Para obtener más información sobre los patrones de dirección URL en Azure Portal, vaya a https://aka.ms/ss-8000-network-reqs. Si usa Azure Government Cloud, vaya a los modelos de dirección URL de https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alertas de rendimiento

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| La carga del dispositivo ha superado el <*umbral*>. |Tiempos de respuesta más lentos que lo esperado. |El dispositivo indica su utilización con una carga elevada de entrada/salida. Esto podría provocar que el dispositivo no funcione tan bien como debería. Revise las cargas de trabajo que ha conectado al dispositivo y determine si hay alguna que se pueda mover a otro dispositivo o que ya no sea necesaria.| No se han podido iniciar los servicios de StorSimple. |Error de ruta de datos |Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |Para conocer el estado actual, vaya a [Uso del servicio StorSimple Device Manager para supervisar el dispositivo StorSimple](storsimple-8000-monitor-device.md) |

### <a name="security-alerts"></a>Alertas de seguridad

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Se ha iniciado una sesión de soporte técnico de Microsoft Support. |Un tercero ha accedido a la sesión de soporte técnico. |Confirme que este acceso está autorizado. Después de realizar la acción apropiada, borre esta alerta de la página de alertas. |
| La contraseña de <*elemento*> caducará en <*período de tiempo*>. |La caducidad está a punto de caducar. |Cambiar la contraseña antes de que caduque. |
| Falta información de la configuración de seguridad para <*id. de elemento*>. | |Los volúmenes asociados con este contenedor de volúmenes no pueden utilizarse para replicar la configuración de StorSimple. Para asegurarse de que sus datos se almacenan de forma segura, recomendamos eliminar el contenedor de volúmenes y los volúmenes asociados con el contenedor de volúmenes. Después de realizar la acción apropiada, borre esta alerta de la página de alertas. |
| <*número*&gt; intentos de inicio de sesión incorrectos para &lt;*id. de elemento*&gt;. |Múltiples intentos de inicio de sesión erróneos. |Su dispositivo puede estar bajo ataque o un usuario autorizado está intentando conectarse con una contraseña incorrecta.<ul><li>Póngase en contacto con los usuarios autorizados y verifique que estos intentos proceden de un origen legítimo. Si continúa viendo un gran número de intentos de inicio de sesión erróneos, considere la posibilidad de deshabilitar la administración remota y de ponerse en contacto con su administrador de red. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.</li><li>Compruebe que las instancias de Snapshot Manager estén configuradas con la contraseña correcta. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.</li></ul>Para más información, vaya a [Cambio de una contraseña de dispositivo caducada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Se ha producido uno o más errores al cambiar la clave de cifrado de datos del servicio. | |Se encontraron errores al cambiar la clave de cifrado de datos del servicio. Después de solucionar las condiciones de error, ejecute el cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para actualizar el servicio. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. Cuando haya solucionado el problema, borre esta alerta de la página de alertas. |

### <a name="support-package-alerts"></a>Alertas de paquetes de soporte

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Error de creación de paquete de soporte técnico. |StorSimple no pudo generar el paquete. |Vuelva a intentarlo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. Cuando haya solucionado el problema, borre esta alerta de la página de alertas. |

## <a name="next-steps"></a>pasos siguientes

Aprenda más sobre [los errores de StorSimple y la solución de problemas al implementar un dispositivo](storsimple-8000-troubleshoot-deployment.md).

