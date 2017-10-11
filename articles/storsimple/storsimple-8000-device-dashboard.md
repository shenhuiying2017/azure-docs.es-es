---
title: Uso del resumen del dispositivo de la serie StorSimple 8000 | Microsoft Docs
description: "Describe el resumen del dispositivo del servicio StorSimple Device Manager y cómo usarlo para ver las métricas de almacenamiento y los iniciadores conectados y buscar el número de serie y el IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Uso del resumen del dispositivo del servicio StorSimple Device Manager

## <a name="overview"></a>Información general
La hoja de resumen del dispositivo StorSimple le ofrece una visión general sobre la información de un dispositivo StorSimple específico, a diferencia de la hoja de resumen del servicio, que le ofrece información sobre todos los dispositivos incluidos en la solución de Microsoft Azure StorSimple.

La hoja de resumen del dispositivo proporciona una vista de resumen de un dispositivo de la serie StorSimple 8000 que se registra con un determinado StorSimple Device Manager, y resalta los problemas en los dispositivos que necesitan la atención del administrador del sistema. Este tutorial presenta la hoja de resumen de dispositivos, explica su contenido y su función, y describe las tareas que puede realizar desde esta hoja.

La hoja de resumen de dispositivos muestra la siguiente información:

![Hoja de resumen del dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra de comandos de administración

En la hoja de dispositivos de StorSimple, verá las opciones para administrar el dispositivo de StorSimple. Verá los comandos de administración en la parte superior de la hoja y en el lado izquierdo. Utilice estas opciones para agregar recursos compartidos o volúmenes, o bien actualizar o conmutar por error el dispositivo.

![Barra de comandos de administración](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

El área de información esencial captura algunas de las propiedades importantes, como el estado, el modelo, el IQN de destino y la versión del software. 

![Información esencial de dispositivos](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Supervisión

* El icono **Alertas** proporciona una instantánea de todas las alertas activas para el dispositivo, agrupadas por gravedad.

    ![Icono Alertas](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Haga clic en el icono para abrir la hoja **Alertas** y, a continuación, en una alerta concreta para ver detalles adicionales acerca de ella, incluidas las acciones recomendadas. También puede desactivar la alerta si el problema se ha resuelto.

    ![Hacer clic en el icono Alertas](./media/storsimple-8000-device-dashboard/device-summary10.png)

* El icono **Estado y mantenimiento** proporciona información acerca del estado de los componentes de hardware de un dispositivo, incluido el estado del propio dispositivo. El estado del dispositivo puede ser sin conexión, en línea, desactivado o listo para configurar.

    ![Icono Estado y mantenimiento](./media/storsimple-8000-device-dashboard/device-summary5.png)

* El icono **Volúmenes** proporciona un resumen del número de volúmenes del dispositivo, agrupados por estado.

    ![Icono Volúmenes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Haga clic en el icono para abrir la hoja de lista **Volúmenes** y, a continuación, en un recurso compartido o un volumen concreto para ver o modificar sus propiedades.
    
    ![Hacer clic en el icono Volúmenes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Para más información, consulte cómo [administrar volúmenes](storsimple-8000-manage-volumes-u2.md).

* En el gráfico **Uso**, puede ver el almacenamiento principal que se usa en su dispositivo y el almacenamiento en la nube consumido durante los últimos 7 días, el período predeterminado.

     ![Icono de Uso](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Para elegir otra escala de tiempo, ese la opción **Editar** de la esquina superior derecha del gráfico.

     ![Editar gráfico de uso](./media/storsimple-8000-device-dashboard/device-summary12.png)

     En este gráfico, puede ver las métricas del almacenamiento principal total (la cantidad de datos escritos por hosts en su dispositivo) y el almacenamiento en nube total consumido por su dispositivo durante un período de tiempo.
  
     En este contexto, *almacenamiento principal* hace referencia a la cantidad total de los datos escritos por el host y se puede desglosar por tipo de volumen: el *almacenamiento principal en capas* incluye los datos almacenados localmente y en capas en la nube. El *almacenamiento principal anclado localmente* incluye solo los datos almacenados localmente. Por otro lado, *almacenamiento en la nube* es una medición de la cantidad total de datos almacenados en la nube. Este almacenamiento incluye las copias de seguridad y los datos en niveles. Los datos almacenados en la nube están desduplicados y comprimidos, mientras que el almacenamiento principal indica la cantidad de almacenamiento usado antes de que los datos estén desduplicados y comprimidos. (Puede comparar estos dos números para hacerse una idea de la tasa de compresión). Para el almacenamiento principal y en la nube, las cantidades mostradas se basan en la frecuencia de seguimiento que configure. Por ejemplo, si elige una frecuencia de una semana, el gráfico mostrará datos para cada día de la semana anterior.

     Para ver la cantidad de almacenamiento en la nube consumido en el tiempo, seleccione la opción **ALMACENAMIENTO EN LA NUBE USADO** . Para ver el almacenamiento total escrito por el host, seleccione las opciones **PRIMARY TIERED STORAGE USED** (Almacenamiento en capas principal usado) y **PRIMARY LOCALLY PINNED STORAGE USED** (Almacenamiento principal anclado localmente usado). 
     Para más información, consulte [Uso del servicio StorSimple Device Manager para supervisar su dispositivo StorSimple](storsimple-monitor-device.md).


* El icono **Capacidad** muestra el almacenamiento principal que está aprovisionado y el almacenamiento restante que queda en el dispositivo, en relación con el almacenamiento total disponible para él. **Aprovisionado** hace referencia a la cantidad de almacenamiento que está preparada y asignada para su uso. **Restante** hace referencia a la capacidad restante que se puede aprovisionar en este dispositivo. 

    ![Icono de Uso](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Haga clic en este icono para ver cómo se aprovisiona la capacidad a través de los volúmenes anclados localmente y en capas. La capacidad **restante en capas** es la capacidad disponible que se puede aprovisionar, incluida la capacidad en la nube, mientras que la capacidad **restante en local** es la capacidad restante en los discos conectados a este dispositivo.

    ![Hacer clic en el gráfico de uso](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la [hoja de resumen del servicio StorSimple](storsimple-8000-service-dashboard.md).
* Más información sobre el [uso del servicio StorSimple Device Manager para administrar su dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

