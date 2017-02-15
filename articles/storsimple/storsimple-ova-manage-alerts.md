---
title: "Visualización y administración de alertas de la matriz virtual de StorSimple | Microsoft Docs"
description: "Describe las condiciones de alerta y gravedad de la matriz virtual de StorSimple y cómo usar el servicio Administrador de StorSimple para administrar las alertas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4f227ea1-6cda-41d8-af06-b8a4e38118d9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 19a8634e8f97e7875ae37e0de2200757fdaba50e


---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Uso del servicio de Administrador de StorSimple para ver y administrar alertas para la matriz virtual de StorSimple
## <a name="overview"></a>Información general
La pestaña **Alertas** del servicio de Administrador de StorSimple proporciona una forma de revisar y borrar alertas relacionadas con la matriz virtual de StorSimple en tiempo real. Desde esta pestaña, puede supervisar de forma centralizada los problemas de estado de las matrices virtuales de StorSimple (también conocidas como dispositivos virtuales locales de StorSimple) y la solución general Microsoft Azure StorSimple.

En este tutorial se describe cómo configurar notificaciones de alerta, condiciones de alerta comunes, niveles de gravedad de alerta y cómo ver y realizar un seguimiento de las alertas. Además, incluye tablas de referencia rápida de alertas, que le permiten localizar una alerta específica rápidamente y responder de forma adecuada.

![Página de alertas](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar alertas
Puede elegir si desea recibir una notificación por correo electrónico de las condiciones de alerta para cada uno de los dispositivos virtuales de StorSimple. Además, puede identificar otros destinatarios de notificaciones de alerta mediante la especificación de sus direcciones de correo electrónico en el cuadro **Otros destinatarios de correo electrónico** , separadas por punto y coma.

> [!NOTE]
> Puede escribir un máximo de 20 direcciones de correo electrónico por dispositivo virtual.
> 
> 

Después de habilitar la notificación por correo electrónico para un dispositivo virtual, los miembros de la lista de notificación recibirán un mensaje de correo electrónico cada vez que se produzca una alerta crítica. Los mensajes se enviarán desde *storsimple-alerts-noreply@mail.windowsazure.com* y describirán la condición de alerta. Los destinatarios pueden hacer clic en **Eliminar suscripción** para quitarse de la lista de notificación por correo electrónico.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Para habilitar la notificación de alertas por correo electrónico de un dispositivo virtual
1. En el dispositivo virtual, vaya a **Dispositivos** > **Configuración**. Desplácese hasta la sección **Configuración de alertas**.
   
    ![Configuración de alertas](./media/storsimple-ova-manage-alerts/alerts2.png)
2. En **Configuración de alertas**, establezca lo siguiente:
   
   1. En el campo **Enviar notificación por correo electrónico**, seleccione **SÍ**.
   2. En el campo **Administradores del servicio de correo electrónico**, seleccione **SÍ** si desea que el administrador de servicios y todos los coadministradores reciban las notificaciones de alerta.
   3. En el campo **Otros destinatarios de correo electrónico** , escriba las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres con el formato *someone@somewhere.com*. Utilice punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo virtual. 
      
       ![configuración de notificaciones de alerta](./media/storsimple-ova-manage-alerts/alerts3.png)
3. Haga clic en **Guardar** en la parte inferior de la página para guardar la configuración.
4. Para enviar una notificación de prueba por correo electrónico, haga clic en el icono de flecha junto a **Enviar correo electrónico de prueba**. El servicio Administrador de StorSimple mostrará mensajes de estado mientras envía la notificación de prueba. 
5. Cuando aparece el siguiente mensaje, haga clic en **Aceptar**. 
   
    ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-ova-manage-alerts/alerts4.png)
   
   > [!NOTE]
   > Si el mensaje de notificación de prueba no puede enviarse, el servicio Administrador de StorSimple mostrará un mensaje adecuado. Haga clic en **Aceptar**y, después de algunos minutos, intente volver a enviar el mensaje de notificación de prueba. 
   > 
   > 
   
    El mensaje de notificación de prueba debe ser similar al siguiente.
   
    ![Ejemplo de correo electrónico de prueba de alertas](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Condiciones de alerta comunes
La matriz virtual de StorSimple genera alertas en respuesta a una serie de condiciones. A continuación se detallan los tipos más comunes de condiciones de alerta:

* **Problemas de conectividad** : estas alertas se producen cuando existen dificultades para transferir datos. Los problemas de comunicación pueden ocurrir durante la transferencia de datos a y de la cuenta de almacenamiento de Azure o debido a la falta de conectividad entre los dispositivos virtuales y el servicio de Administrador de StorSimple. Los problemas de comunicación están entre los más difíciles de solucionar porque existen numerosos puntos de error. En primer lugar, siempre debe verificar que estén disponibles la conectividad de la red y el acceso a Internet antes de pasar a soluciones de problemas más avanzados. Para obtener información sobre la configuración del firewall y los puertos, vaya a [Requisitos del sistema de la matriz virtual de StorSimple](storsimple-ova-system-requirements.md). Si necesita ayuda para la solución de problemas, vaya a [Solución de problemas con el cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de rendimiento** : estas alertas se producen cuando el sistema no tiene un rendimiento óptimo, por ejemplo, cuando está sobrecargado.

Además, podría ver alertas relacionadas con seguridad, actualizaciones o errores en el trabajo.

## <a name="alert-severity-levels"></a>Niveles de gravedad de alerta
Las alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación de alerta y la necesidad de dar respuesta a la misma. Los niveles de gravedad son:

* **Crítica** – Esta alerta es en respuesta a una condición que está afectando el rendimiento correcto del sistema. Se requiere una acción para asegurar que el servicio de StorSimple no se vea interrumpido.
* **Advertencia** : esta condición puede convertirse en crítica si no se resuelve. Debe investigar la situación y tomar las acciones necesarias para resolver el problema.
* **Información** : esta alerta contiene información que puede ser útil para el seguimiento y la administración del sistema.

## <a name="view-and-track-alerts"></a>Ver y realizar un seguimiento de las alertas
El panel del servicio StorSimple Manager le permite ver de un vistazo el número de alertas en sus dispositivos virtuales, organizadas por nivel de gravedad.

![Panel de alertas](./media/storsimple-ova-manage-alerts/alerts6.png)

Al hacer clic en el nivel de gravedad, se abre la pestaña **Alertas** . Los resultados solo incluyen las alertas que coinciden con ese nivel de gravedad.

![Informe de alertas enfocado al tipo de alerta](./media/storsimple-ova-manage-alerts/alerts7.png)

Al hacer clic en la lista, se muestran detalles adicionales de la alerta, incluyendo la última vez que se emitió la alerta, el número de repeticiones de la alerta en el dispositivo y la acción recomendada para resolver la alerta.

Si necesita enviar la información al soporte técnico de Microsoft, puede copiar los detalles de las alertas en un archivo de texto. Después de haber seguido las recomendación y resuelto la condición de alerta en el entorno local, debe borrar la alerta seleccionándola en la pestaña **Alertas** y haciendo clic en **Borrar**. Para borrar varias alertas, seleccione cada alerta, haga clic en cualquier columna excepto en la columna **Alerta** y, a continuación, haga clic en **Borrar** después de haber seleccionado todas las alertas que vaya a borrar. Tenga en cuenta que algunas alertas se eliminan automáticamente cuando se resuelve el problema o cuando el sistema actualiza la alerta con nueva información.

Al hacer clic en **Borrar**, tendrá la posibilidad de proporcionar comentarios sobre la alerta y los pasos llevados a cabo para resolver el problema. 

![comentarios de alertas](./media/storsimple-ova-manage-alerts/clear-alert.png)

Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-ova-manage-alerts/check-icon.png)  para guardar los comentarios.

El sistema borrará algunos eventos si se desencadena otro evento con información nueva. En ese caso, verá el siguiente mensaje.

![Borrar mensaje de alerta](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Clasificar y revisar alertas
La pestaña **Alertas** puede mostrar hasta 250 alertas. Si se ha superado ese número de alertas, no todas las alertas se mostrarán en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

* **Estado**: puede mostrar alertas **Activas** o **Desactivadas**. Las alertas activas se siguen desencadenando en el sistema, mientras que las alertas desactivadas han sido borradas de forma manual por un administrador o se han borrado de forma programada porque el sistema actualizó la condición de alerta con nueva información.
* **Gravedad** – Puede mostrar las alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo las de determinada gravedad, como las alertas críticas únicamente.
* **Origen** – Puede mostrar las alertas de todos los orígenes, o limitar las alertas procedentes del servicio, o de uno o todos los dispositivos virtuales.
* **Intervalo de tiempo**: al especificar las fechas **Desde** y **Hasta** y las marcas de tiempo, puede ver las alertas durante el período de tiempo que le interesa.

## <a name="alerts-quick-reference"></a>Referencia rápida de alertas
Las siguientes tablas enumeran algunas de las alertas de Microsoft Azure StorSimple que pueden encontrarse, así como información adicional y recomendaciones donde estén disponibles. Las alertas del dispositivo virtual de StorSimple se clasifican en alguna de las siguientes categorías:

* [Alertas de conectividad de la nube](#cloud-connectivity-alerts)
* [Alertas de configuración](#configuration-alerts)
* [Alertas de errores de trabajo](#job-failure-alerts)
* [Alertas de rendimiento](#performance-alerts)
* [Alertas de seguridad](#security-alerts)
* [Alertas de actualización](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividad de la nube
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| El dispositivo *<device name>* no está conectado a la nube. |No se puede conectar el dispositivo con nombre con la nube. |No se puede conectar con la nube. Esto puede deberse a uno de los siguientes motivos:<ul><li>Puede haber un problema con la configuración de red del dispositivo.</li><li>Puede haber un problema con las credenciales de la cuenta de almacenamiento.</li></ul>Para obtener más información sobre cómo solucionar problemas de conectividad, vaya a la [interfaz de usuario web local](storsimple-ova-web-ui-admin.md) del dispositivo. |

### <a name="configuration-alerts"></a>Alertas de configuración
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Configuración del dispositivo virtual local no admitida. |Rendimiento lento. |La configuración actual puede provocar una degradación del rendimiento. Asegúrese de que el servidor cumple los requisitos mínimos de configuración. Para más información, vea [Requisitos de la matriz virtual de StorSimple](storsimple-ova-system-requirements.md). |
| Se está quedando sin espacio aprovisionado en disco en <*nombre de dispositivo*>. |Advertencia de espacio en disco. |Se está agotando el espacio en disco de aprovisionamiento. Para liberar espacio, considere la posibilidad de mover cargas de trabajo a otro volumen o recurso compartido o eliminar datos. |

### <a name="job-failure-alerts"></a>Alertas de errores de trabajo
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| La copia de seguridad de <*nombre de dispositivo*> no se pudo completar. |Error de trabajo de copia de seguridad. |No se pudo crear una copia de seguridad. Tenga en cuenta alguna de las siguientes opciones:<ul><li>Podrían existir problemas de conectividad que impiden que la operación de copia de seguridad se complete correctamente. Asegúrese de que no hay ningún problema de conectividad. Para obtener más información sobre cómo solucionar problemas de conectividad, vaya a la [interfaz de usuario web local](storsimple-ova-web-ui-admin.md) del dispositivo virtual.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul>  Resuelva los problemas, borre la alerta y vuelva a intentar la operación. |
| No se puedo completar la restauración de <*nombre de dispositivo*>. |Error de trabajo de restauración. |No se pudo restaurar desde la copia de seguridad. Tenga en cuenta alguna de las siguientes opciones:<ul><li>La lista de copia de seguridad puede no ser válida. Actualice la lista para comprobar que sigue siendo válida</li><li>Los problemas de conectividad pueden evitar que la operación de restauración se complete correctamente. Asegúrese de que no hay ningún problema de conectividad.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul>Resuelva los problemas, borre la alerta y vuelva a intentar la operación de restauración. |
| No se puedo completar la clonación de <*nombre de dispositivo*>. |Error de trabajo de clonación. |No se pudo crear una clonación. Tenga en cuenta alguna de las siguientes opciones:<ul><li>La lista de copia de seguridad puede no ser válida. Actualice la lista para comprobar que sigue siendo válida</li><li>Los problemas de conectividad pueden evitar que la operación de clonación se complete correctamente. Asegúrese de que no hay ningún problema de conectividad.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul> Resuelva los problemas, borre la alerta y vuelva a intentar la operación. |

### <a name="performance-alerts"></a>Alertas de rendimiento
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Observa retrasos inesperados en la transferencia de datos. |Transferencias de datos lenta. |Los errores de limitación se producen cuando supera los objetivos de escalabilidad de un servicio de almacenamiento. El servicio de almacenamiento hace esto para asegurarse de que ningún cliente o inquilino pueda usar el servicio a expensas de otros. Para obtener más información sobre la solución de problemas de la cuenta de Azure Storage, vaya a [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](../storage/storage-monitoring-diagnosing-troubleshooting.md). |
| Se está agotando el espacio de reserva en disco local en <*nombre de dispositivo*>. |Tiempo de respuesta lento. |El 10 % del tamaño total aprovisionado para <*nombre de dispositivo*> está reservado en el dispositivo local y ahora se está agotando el espacio reservado. La carga de trabajo <*nombre de dispositivo*> está generando una mayor tasa de renovación o puede que haya migrado recientemente una gran cantidad de datos. Esto puede producir un rendimiento inferior. Considere una de las siguientes acciones para resolver este problema:<ul><li>Aumente el ancho de banda de la nube para este dispositivo.</li><li>Reduzca o mueva las cargas de trabajo a otro volumen o recurso compartido.</li></ul> |

### <a name="security-alerts"></a>Alertas de seguridad
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| La contraseña de <*nombre de dispositivo*> expirará en <*número*> días. |Advertencia de contraseña. |Su contraseña expirará en < número> días. Considere la posibilidad de cambiar la contraseña. Para obtener más información, consulte [Cambiar la contraseña del administrador de dispositivos de la matriz virtual de StorSimple](storsimple-ova-change-device-admin-password.md). |

### <a name="update-alerts"></a>Alertas de actualización
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Hay nuevas actualizaciones disponibles para el dispositivo. |Las actualizaciones de la matriz virtual de StorSimple están disponibles. |Puede instalar nuevas actualizaciones desde la página **Mantenimiento** . |
| No se pudieron detectar nuevas actualizaciones en <*nombre de dispositivo*>. |Error de actualización. |Se produjo un error al instalar nuevas actualizaciones. Puede instalar manualmente las actualizaciones. Si el problema persiste, póngase en contacto con el [Servicio técnico de Microsoft](storsimple-contact-microsoft-support.md). |

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga más información sobre la matriz virtual de StorSimple](storsimple-ova-overview.md)




<!--HONumber=Nov16_HO3-->


