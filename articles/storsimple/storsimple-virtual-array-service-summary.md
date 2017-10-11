---
title: Hoja de resumen de servicios de StorSimple Virtual Array | Microsoft Docs
description: "Describe la hoja de resumen del servicio StorSimple Device Manager y explica cómo usarlo para supervisar el estado de StorSimple Virtual Array."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Uso de la hoja de resumen del servicio StorSimple Device Manager conectado a StorSimple Virtual Array
## <a name="overview"></a>Información general
La hoja de resumen del servicio StorSimple Device Manager proporciona una vista resumida de las instancias de StorSimple Virtual Array (también conocidas como dispositivos virtuales o dispositivos virtuales locales de StorSimple) que están conectadas al servicio y resalta las que necesitan la atención de un administrador del sistema. Este tutorial presenta la hoja de resumen del servicio, explica el contenido y la función y describe las tareas que puede realizar desde esta hoja.

![Panel del servicio](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Elementos esenciales y comandos de administración
En la hoja de resumen de StorSimple, verá las opciones para administrar el servicio StorSimple Device Manager, así como las matrices virtuales registradas en este servicio. Verá los comandos de administración en la parte superior de la hoja y en el lado izquierdo.

Utilice estas opciones para realizar diversas operaciones tales como agregar recursos compartidos o volúmenes o supervisar los distintos trabajos en ejecución en las matrices virtuales.

El área de información esencial captura algunas de las propiedades importantes, como el grupo de recursos, la ubicación y la suscripción en la que se creó StorSimple Device Manager.

## <a name="storsimple-device-manager-service-summary"></a>Resumen del servicio StorSimple Device Manager
* El icono **Alertas** proporciona una instantánea de todas las alertas activas en todos los dispositivos virtuales, agrupadas por gravedad. Si hace clic en el icono, se abrirá la hoja **Alertas**, en la que puede hacer clic en una alerta individual para ver detalles adicionales acerca de ella, incluidas las acciones recomendadas. También puede desactivar la alerta si el problema se ha resuelto.
* El icono **Capacidad** muestra el almacenamiento principal que está aprovisionado y el almacenamiento restante que queda en todos los dispositivos virtuales en comparación con el almacenamiento total disponible en todos los dispositivos virtuales. **Aprovisionado** hace referencia a la cantidad de almacenamiento que está preparada y asignada para su uso. **Restante** hace referencia a la capacidad restante que se puede aprovisionar en todos los dispositivos virtuales. La capacidad **restante en capas** es la capacidad disponible que se puede aprovisionar, incluida la capacidad en la nube, mientras que la capacidad **restante en local** es la capacidad restante en los discos conectados a las matrices virtuales.
* En el gráfico **Uso**, puede ver las métricas pertinentes para los dispositivos virtuales. Puede ver el almacenamiento principal que se usa en todos los dispositivos virtuales, así como el almacenamiento en la nube que consumieron los dispositivos virtuales durante los últimos 7 días, el período de tiempo predeterminado. Use la opción **Editar** en la esquina superior derecha del gráfico para elegir una escala de tiempo diferente.
* El icono **Dispositivos** proporciona un resumen del número de matrices virtuales en StorSimple Device Manager agrupado por estado del dispositivo. Haga clic en este icono para abrir la hoja de la lista **Dispositivos** y, a continuación, haga clic en un dispositivo individual para profundizar sobre el resumen específico del dispositivo. También puede realizar acciones específicas de dispositivo desde una hoja de resumen de un dispositivo determinado. Para más información acerca de la hoja de resumen del dispositivo, vaya a [Hoja de resumen de dispositivo](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Visualizar los registros de actividad
Para ver las distintas operaciones que se llevan a cabo en StorSimple Device Manager, haga clic en el vínculo **Registros de actividad** en el lado izquierdo de la hoja de resumen del servicio StorSimple. Esto le llevará a la hoja **Registros de actividad**, donde puede ver un resumen de las operaciones recientes que se llevan a cabo.

![Registros de actividad](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Pasos siguientes
Obtener información sobre cómo [usar la interfaz de usuario web local para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

