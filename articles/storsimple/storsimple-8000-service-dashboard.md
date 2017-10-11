---
title: Uso del resumen del dispositivo de la serie StorSimple 8000 | Microsoft Docs
description: "Describe la hoja del resumen del servicio de StorSimple y explica cómo se usa para supervisar el estado de la solución de StorSimple."
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Uso de la hoja de resumen del servicio del dispositivo de la serie StorSimple 8000

## <a name="overview"></a>Información general

La hoja del resumen del servicio StorSimple Device Manager proporciona una vista resumida de todos los dispositivos conectados al servicio StorSimple Device Manager y resalta los que requieren la atención del administrador del sistema. En este tutorial se presenta la hoja de resumen del servicio, se explica el contenido y la función del panel y se describen las tareas que se pueden realizar desde esta página.

![Resumen del servicio](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Comandos de administración

En la hoja de resumen del servicio de StorSimple se ven las opciones para administrar tanto el servicio StorSimple Device Manager como los dispositivos de la serie StorSimple 8000 registrados en dicho servicio. Verá los comandos de administración en la parte superior de la hoja y en el lado izquierdo.

![Barra de comandos](./media/storsimple-8000-service-dashboard/service-summary2.png)

Estas opciones se pueden usar para realizar diversas operaciones tales como agregar recursos compartidos o volúmenes, o supervisar los distintos trabajos que se ejecutan en los dispositivos de StorSimple.


## <a name="essentials"></a>Essentials

El área de información esencial captura algunas de las propiedades importantes, como el grupo de recursos, la ubicación y la suscripción en la que se creó StorSimple Device Manager.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Resumen del servicio StorSimple Device Manager

* El icono **Alertas** (Alertas) proporciona una instantánea de todas las alertas activas en todos los dispositivos virtuales, agrupadas por gravedad.

    ![Icono Alerts (Alertas)](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Si hace clic en el icono, se abrirá la hoja **Alertas**, en la que puede hacer clic en una alerta individual para ver detalles adicionales acerca de ella, incluidas las acciones recomendadas. También puede desactivar la alerta si el problema se ha resuelto.

    ![Hacer clic en el icono de alertas](./media/storsimple-8000-service-dashboard/service-summary8.png)

* El icono **Capacidad** muestra tanto el almacenamiento principal que se aprovisiona como el restante que queda en todos los dispositivos, en comparación con el almacenamiento total disponible en todos los dispositivos. **Aprovisionado** hace referencia a la cantidad de almacenamiento que está preparada y asignada para su uso. **Restante** hace referencia a la capacidad restante que se puede aprovisionar en todos los dispositivos.

    ![Icono Capacity (Capacidad)](./media/storsimple-8000-service-dashboard/service-summary6.png)

    La capacidad **en capas restante** es la capacidad disponible que se puede aprovisionar incluyendo la nube, mientras que la capacidad **local restante** es la capacidad que queda en los discos conectados a los dispositivos de la serie StorSimple 8000.


* En el gráfico **Uso**, puede ver las métricas pertinentes para los dispositivos. Puede ver el almacenamiento principal que se usa en todos los dispositivos, así como el almacenamiento en la nube que han consumido los dispositivos durante los últimos 7 días, el período de tiempo predeterminado. 

    ![Icono de Uso](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Para elegir otra escala de tiempo, ese la opción **Editar** de la esquina superior derecha del gráfico.

     ![Hacer clic en el icono de uso](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportar datos de un gráfico](./media/storsimple-8000-service-dashboard/service-summary11.png)

* El icono **Devices** (Dispositivos) proporciona un resumen del número de dispositivos de la serie StorSimple 8000 en StorSimple Device Manager agrupados por el estado del dispositivo. 

    ![Icono Devices (Dispositivos)](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Haga clic en este icono para abrir la hoja de la lista **Dispositivos** y, a continuación, haga clic en un dispositivo individual para profundizar sobre el resumen específico del dispositivo. También puede realizar acciones específicas del dispositivo desde una hoja de resumen de un dispositivo determinado. Para más información acerca de la hoja de resumen del dispositivo, vaya a [Hoja de resumen de dispositivo](storsimple-8000-device-dashboard.md).

    ![Hacer clic en icono de dispositivos](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Visualizar los registros de actividad

Para ver las distintas operaciones que se llevan a cabo en StorSimple Device Manager, haga clic en el vínculo **Registros de actividad** en el lado izquierdo de la hoja de resumen del servicio StorSimple. Esto le llevará a la hoja **Registros de actividad**, donde puede ver un resumen de las operaciones recientes que se llevan a cabo.

![Registros de actividad](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información acerca de cómo [usar el servicio StorSimple Device Manager para administrar cualquier dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

