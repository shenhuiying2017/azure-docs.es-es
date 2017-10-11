---
title: Hoja de resumen de dispositivos de StorSimple Virtual Array | Microsoft Docs
description: "Describe la hoja de resumen de dispositivos para StorSimple Device Manager y explica cómo usarla para supervisar el estado de StorSimple Virtual Array."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Uso de la hoja de resumen de dispositivos para StorSimple Device Manager conectado a StorSimple Virtual Array

## <a name="overview"></a>Información general

La hoja de dispositivos de StorSimple Device Manager proporciona una vista de resumen de una instancia de StorSimple Virtual Array que se registra con un determinado StorSimple Device Manager, y resalta los problemas en los dispositivos que necesitan la atención del administrador del sistema. Este tutorial presenta la hoja de resumen de dispositivos, explica su contenido y su función, y describe las tareas que puede realizar desde esta hoja.

La hoja de resumen de dispositivos muestra la siguiente información:

![Panel del dispositivo](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Administración

En la hoja de dispositivos de StorSimple, verá las opciones para administrar el dispositivo de StorSimple. Verá los comandos de administración en la parte superior de la hoja y en el lado izquierdo. Utilice estas opciones para agregar recursos compartidos o volúmenes, o bien actualizar o conmutar por error la matriz virtual.

El área de información esencial captura algunas de las propiedades importantes, como el estado, el modelo y la versión del software, así como un vínculo a la **interfaz de usuario web** de la matriz. Si se encuentra en una red interna, puede iniciar directamente la [IU web local](storsimple-ova-web-ui-admin.md) para administrar la matriz virtual.

![Información esencial de dispositivos](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Resumen de dispositivos de StorSimple

* El icono **Alertas** proporciona una instantánea de todas las alertas activas para la matriz virtual, agrupadas por gravedad. Haga clic en el icono para abrir la hoja **Alertas** y, a continuación, en una alerta concreta para ver detalles adicionales acerca de ella, incluidas las acciones recomendadas. También puede desactivar la alerta si el problema se ha resuelto.

* El icono **Capacidad** muestra el almacenamiento principal que está aprovisionado y el almacenamiento restante que queda en el dispositivo virtual, en relación con el almacenamiento total disponible para él. **Aprovisionado** hace referencia a la cantidad de almacenamiento que está preparada y asignada para su uso. **Restante** hace referencia a la capacidad restante que se puede aprovisionar en este dispositivo. La capacidad **restante en capas** es la capacidad disponible que se puede aprovisionar, incluida la capacidad en la nube, mientras que la capacidad **restante en local** es la capacidad restante en los discos conectados a esta matriz virtual.

* En el gráfico **Uso**, puede ver el almacenamiento principal que se usa en su matriz virtual, así como el almacenamiento en la nube consumido durante los últimos 7 días, el período predeterminado. Use la opción **Editar** en la esquina superior derecha del gráfico para elegir una escala de tiempo diferente.

* El icono **Recursos compartidos** o **Volúmenes** proporciona un resumen del número de recursos compartidos o de volúmenes en el dispositivo, agrupados por estado. Haga clic en el icono para abrir la hoja de lista **Recursos compartidos** o **Volúmenes** y, a continuación, en un recurso compartido o un volumen concreto para ver o modificar sus propiedades. Para más información, consulte cómo [administrar recursos compartidos](storsimple-virtual-array-manage-shares.md) o cómo [administrar volúmenes](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo:
- [Administrar recursos compartidos en una instancia de StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)
    
- [Administrar volúmenes en una instancia de StorSimple Virtual Array](storsimple-virtual-array-manage-volumes.md)

