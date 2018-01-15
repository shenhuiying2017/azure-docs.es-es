---
title: "Visualización y administración de alertas de Microsoft Azure StorSimple Virtual Array | Microsoft Docs"
description: "Describe las condiciones de alerta y gravedad de la matriz virtual de StorSimple y cómo usar el servicio Administrador de StorSimple para administrar las alertas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d4d680e3460fbeff73c2f334c6461da7967374d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Use de StorSimple Device Manager para administrar alertas de la matriz virtual de StorSimple

## <a name="overview"></a>Información general

La característica de alertas del servicio StorSimple Device Manager proporciona una forma de revisar y borrar alertas relacionadas con StorSimple Virtual Array en tiempo real. Puede usar las alertas en la hoja **Resumen del servicio** para supervisar de forma centralizada los problemas de estado de sus instancias de StorSimple Virtual Array y la solución general Microsoft Azure StorSimple.

En este tutorial se describe cómo configurar notificaciones de alerta, condiciones de alerta comunes, niveles de gravedad de alerta y cómo ver y realizar un seguimiento de las alertas. Además, incluye tablas de referencia rápida de alertas, que le permiten localizar una alerta específica rápidamente y responder de forma adecuada.

![Página de alertas](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar alertas

Puede elegir si desea recibir una notificación por correo electrónico sobre las condiciones de alerta para cada instancia de StorSimple Virtual Array. Además, puede identificar otros destinatarios de las notificaciones de alerta mediante la especificación de sus direcciones de correo electrónico en el cuadro **Destinatarios de correo electrónico adicionales**, separadas por punto y coma.

> [!NOTE]
> Puede escribir un máximo de 20 direcciones de correo electrónico por matriz virtual.


Después de habilitar la notificación por correo electrónico para una matriz virtual, los miembros de la lista de notificación recibirán un mensaje de correo electrónico cada vez que se produzca una alerta crítica. Los mensajes se enviarán desde *storsimple-alerts-noreply@mail.windowsazure.com* y describirán la condición de alerta. Los destinatarios pueden hacer clic en **Eliminar suscripción** para quitarse de la lista de notificación por correo electrónico.

#### <a name="to-enable-email-notification-for-alerts"></a>Para habilitar la notificación de alertas por correo electrónico de alertas

1. Vaya al servicio StorSimple Device Manager y en la sección **Administración**, haga clic en **Dispositivos**. En la lista de dispositivos que se muestra, haga clic en el dispositivo.
   
    ![Configuración de alertas](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Se abrirá la hoja **Configuración**. En la sección **Configuración del dispositivo**, seleccione **General**. Se abrirá la hoja **Configuración general**.
   
    ![configuración de notificaciones de alerta](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. En la hoja **Configuración general**, vaya a la sección **Configuración de alertas** y establezca lo siguiente:
   
   1. En el campo **Habilitar notificación por correo electrónico**, seleccione **SÍ**.
   2. En el campo **Administradores del servicio de correo electrónico**, seleccione **SÍ** si desea que el administrador de servicios y todos los coadministradores reciban las notificaciones de alerta.
   3. En el campo **Destinatarios de correo electrónico adicionales**, escriba las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres con el formato *someone@somewhere.com*. Utilice punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo virtual.
      
       ![configuración de notificaciones de alerta](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Para enviar una notificación de correo electrónico de prueba, haga clic en **Enviar correo electrónico de prueba**. El servicio StorSimple Device Manager mostrará mensajes de estado mientras envía la notificación de prueba.
      
       ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Si el mensaje de notificación de prueba no puede enviarse, el servicio StorSimple Device Manager mostrará un mensaje adecuado. Haga clic en **Aceptar**y, después de algunos minutos, intente volver a enviar el mensaje de notificación de prueba.
      > 
      > 
   5. Haga clic en **Guardar** en la parte inferior de la página para guardar la configuración. Cuando se le pida confirmación, haga clic en **Sí**.
      
      ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Condiciones de alerta comunes

La matriz virtual de StorSimple genera alertas en respuesta a una serie de condiciones. A continuación se detallan los tipos más comunes de condiciones de alerta:

* **Problemas de conectividad** : estas alertas se producen cuando existen dificultades para transferir datos. Los problemas de comunicación pueden ocurrir durante la transferencia de datos a y de la cuenta de Azure Storage o debido a la falta de conectividad entre los dispositivos virtuales y el servicio StorSimple Device Manager. Los problemas de comunicación están entre los más difíciles de solucionar porque existen numerosos puntos de error. En primer lugar, siempre debe verificar que estén disponibles la conectividad de la red y el acceso a Internet antes de pasar a soluciones de problemas más avanzados. Para obtener información sobre la configuración del firewall y los puertos, vaya a [Requisitos del sistema de la matriz virtual de StorSimple](storsimple-ova-system-requirements.md). Si necesita ayuda para la solución de problemas, vaya a [Solución de problemas con el cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de rendimiento** : estas alertas se producen cuando el sistema no tiene un rendimiento óptimo, por ejemplo, cuando está sobrecargado.

Además, podría ver alertas relacionadas con seguridad, actualizaciones o errores en el trabajo.

## <a name="alert-severity-levels"></a>Niveles de gravedad de alerta

Las alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación de alerta y la necesidad de dar respuesta a la misma. Los niveles de gravedad son:

* **Crítica** – Esta alerta es en respuesta a una condición que está afectando el rendimiento correcto del sistema. Se requiere una acción para asegurar que el servicio de StorSimple no se vea interrumpido.
* **Advertencia** : esta condición puede convertirse en crítica si no se resuelve. Debe investigar la situación y tomar las acciones necesarias para resolver el problema.
* **Información** : esta alerta contiene información que puede ser útil para el seguimiento y la administración del sistema.

## <a name="view-and-track-alerts"></a>Ver y realizar un seguimiento de las alertas

La hoja de resumen del servicio StorSimple Device Manager le permite ver de un vistazo el número de alertas en sus dispositivos virtuales, organizadas por nivel de gravedad.

![Panel de alertas](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Haga clic en el nivel de gravedad para abrir la hoja **Alertas**. Los resultados solo incluyen las alertas que coinciden con ese nivel de gravedad.

![Informe de alertas enfocado al tipo de alerta](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Haga clic en una alerta de la lista para ver detalles adicionales de la alerta, incluida la última vez que se emitió la alerta, el número de repeticiones de la alerta en el dispositivo y la acción recomendada para resolver la alerta.

![Lista de alertas y detalles](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Si necesita enviar la información al soporte técnico de Microsoft, puede copiar los detalles de las alertas en un archivo de texto. Después de haber seguido las recomendación y resuelto la condición de alerta en el entorno local, debe borrar la alerta seleccionándola en la lista. Seleccione la alerta en la lista y haga clic en **Borrar**. Para borrar varias alertas, seleccione cada alerta, haga clic en cualquier columna excepto en la columna **Alerta** y, a continuación, haga clic en **Borrar** después de haber seleccionado todas las alertas que vaya a borrar.

Al hacer clic en **Borrar**, tendrá la posibilidad de proporcionar comentarios sobre la alerta y los pasos llevados a cabo para resolver el problema. 

![comentarios de alertas](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

El sistema borrará algunos eventos si se desencadena otro evento con información nueva. 

## <a name="sort-and-review-alerts"></a>Clasificar y revisar alertas

En la hoja **Alertas** se pueden mostrar hasta 250 alertas. Si se ha superado ese número de alertas, no todas las alertas se mostrarán en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

* **Estado**: puede mostrar alertas **Activas** o **Desactivadas**. Las alertas activas se siguen desencadenando en el sistema, mientras que las alertas desactivadas han sido borradas de forma manual por un administrador o se han borrado de forma programada porque el sistema actualizó la condición de alerta con nueva información.
* **Gravedad** – Puede mostrar las alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo las de determinada gravedad, como las alertas críticas únicamente.
* **Origen** – Puede mostrar las alertas de todos los orígenes, o limitar las alertas procedentes del servicio, o de uno o todos los dispositivos virtuales.
* **Intervalo de tiempo**: al especificar las fechas **Desde** y **Hasta** y las marcas de tiempo, puede ver las alertas durante el período de tiempo que le interesa.

## <a name="alerts-quick-reference"></a>Referencia rápida de alertas

Las siguientes tablas enumeran algunas de las alertas de StorSimple que pueden encontrarse, así como información adicional y recomendaciones donde estén disponibles. Las alertas de StorSimple Virtual Array se clasifican en alguna de las siguientes categorías:

* [Alertas de conectividad de la nube](#cloud-connectivity-alerts)
* [Alertas de configuración](#configuration-alerts)
* [Alertas de errores de trabajo](#job-failure-alerts)
* [Alertas de rendimiento](#performance-alerts)
* [Alertas de seguridad](#security-alerts)

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
| La copia de seguridad de <*nombre de dispositivo*> no se pudo completar. |Error de trabajo de copia de seguridad. |No se pudo crear una copia de seguridad. Tenga en cuenta alguna de las siguientes opciones:<ul><li>Podrían existir problemas de conectividad que impiden que la operación de copia de seguridad se complete correctamente. Asegúrese de que no hay ningún problema de conectividad. Para obtener más información sobre cómo solucionar problemas de conectividad, vaya a la [interfaz de usuario web local](storsimple-ova-web-ui-admin.md) del dispositivo virtual.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul> Resuelva los problemas, borre la alerta y vuelva a intentar la operación. |
| No se puedo completar la clonación de  <*nombre de dispositivo*>. |Error de trabajo de clonación. |No se pudo crear una clonación. Tenga en cuenta alguna de las siguientes opciones:<ul><li>La lista de copia de seguridad puede no ser válida. Actualice la lista para comprobar que sigue siendo válida</li><li>Los problemas de conectividad pueden evitar que la operación de clonación se complete correctamente. Asegúrese de que no hay ningún problema de conectividad.</li><li>Ha alcanzado el límite de almacenamiento disponible. Para liberar espacio, considere la posibilidad de eliminar las copias de seguridad que ya no son necesarias.</li></ul>Resuelva los problemas, borre la alerta y vuelva a intentar la operación. |

### <a name="networking-alerts"></a>Alertas de red
| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| No se pudo conectar al servicio de autenticación. |Error de ruta de datos |La dirección URL que se usa para autenticar no está accesible. Asegúrese de que las reglas de firewall incluyen los patrones de dirección URL especificados para el dispositivo StorSimple. Para más información sobre los patrones de dirección URL en Azure Portal, vea los [requisitos de red de StorSimple Virtual Array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alertas de rendimiento

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| Observa retrasos inesperados en la transferencia de datos. |Transferencias de datos lenta. |Los errores de limitación se producen cuando supera los objetivos de escalabilidad de un servicio de almacenamiento. El servicio de almacenamiento hace esto para asegurarse de que ningún cliente o inquilino pueda usar el servicio a expensas de otros. Para obtener más información sobre la solución de problemas de la cuenta de Azure Storage, vaya a [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Se está agotando el espacio de reserva en disco local en <*nombre de dispositivo*>. |Tiempo de respuesta lento. |El 10 % del tamaño total aprovisionado para <*nombre de dispositivo*> está reservado en el dispositivo local y ahora se está agotando el espacio reservado. La carga de trabajo <*nombre de dispositivo*> está generando una mayor tasa de renovación o puede que haya migrado recientemente una gran cantidad de datos. Esto puede producir un rendimiento inferior. Considere una de las siguientes acciones para resolver este problema:<ul><li>Aumente el ancho de banda de la nube para este dispositivo.</li><li>Reduzca o mueva las cargas de trabajo a otro volumen o recurso compartido.</li></ul> |

### <a name="security-alerts"></a>Alertas de seguridad

| Texto de la alerta | Evento | Más información / acciones recomendadas |
|:--- |:--- |:--- |
| La contraseña de <*nombre de dispositivo*> expirará en <*número*> días. |Advertencia de contraseña. |Su contraseña expirará en < número> días. Considere la posibilidad de cambiar la contraseña. Para obtener más información, consulte [Cambiar la contraseña del administrador de dispositivos de la matriz virtual de StorSimple](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>pasos siguientes

* [Obtenga más información sobre la matriz virtual de StorSimple](storsimple-ova-overview.md)

