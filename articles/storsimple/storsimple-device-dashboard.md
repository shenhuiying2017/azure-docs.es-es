---
title: Uso del panel del dispositivo StorSimple Manager | Microsoft Docs
description: "Describe el panel de dispositivos del servicio StorSimple Manager y cómo usarlo para ver las métricas de almacenamiento y los iniciadores conectados y buscar el número de serie y el IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d8035b9608ca3bac3d4822c7c755b81c96d481e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-device-dashboard-in-storsimple-manager-service"></a>Uso del panel de dispositivos del servicio StorSimple Manager  

## <a name="overview"></a>Información general
El panel del dispositivo StorSimple Manager ofrece una visión general de la información de un dispositivo StorSimple específico, a diferencia del panel de servicio, que ofrece información sobre todos los dispositivos incluidos en la solución de Microsoft Azure StorSimple.

![Página de Panel del dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

El panel contiene la información siguiente:

* **Área de gráfico** : puede ver las métricas de almacenamiento relevantes en el área del gráfico de la parte superior del panel. En este gráfico, puede ver las métricas del almacenamiento principal total (la cantidad de datos escritos por hosts en su dispositivo) y el almacenamiento en nube total consumido por su dispositivo durante un período de tiempo.
  
     En este contexto, *almacenamiento principal* hace referencia a la cantidad total de los datos escritos por el host y se puede desglosar por tipo de volumen: el *almacenamiento principal en capas* incluye los datos almacenados localmente y en capas en la nube; el *almacenamiento principal anclado localmente* incluye solo los datos almacenados localmente. Por otro lado, *almacenamiento en la nube* es una medición de la cantidad total de datos almacenados en la nube. Esto incluye las copias de seguridad y los datos en niveles. Tenga en cuenta que los datos almacenados en la nube está desduplicados y comprimidos, mientras que el almacenamiento principal indica la cantidad de almacenamiento usado antes de que los datos estén desduplicados y comprimidos. (Puede comparar estos dos números para hacerse una idea de la tasa de compresión). Para el almacenamiento principal y en la nube, las cantidades mostradas se basarán en la frecuencia de seguimiento que configure. Por ejemplo, si elige una frecuencia de una semana, el gráfico mostrará datos para cada día de la semana anterior.
  
     Puede configurar el gráfico de la manera siguiente:
  
  * Para ver la cantidad de almacenamiento en la nube consumido en el tiempo, seleccione la opción **ALMACENAMIENTO EN LA NUBE USADO** . Para ver el almacenamiento total escrito por el host, seleccione las opciones **PRIMARY TIERED STORAGE USED** (Almacenamiento en capas principal usado) y **PRIMARY LOCALLY PINNED STORAGE USED** (Almacenamiento principal anclado localmente usado). En la ilustración, ambas opciones están seleccionadas; por lo tanto, el gráfico muestra las cantidades de almacenamiento en la nube y principal. Tenga en cuenta que cualquier almacenamiento principal usado antes de instalar la actualización 2 está representado por la línea **PRIMARY TIERED STORAGE USED** (Almacenamiento en capas principal usado).
  * Use el menú desplegable de la esquina superior derecha del gráfico para especificar un período de tiempo de 1 semana, 1 mes, 3 meses o 1 año. Tenga en cuenta que el gráfico de nivel superior solo se actualiza una vez al día y, por lo tanto, reflejarán los totales del día anterior.
    
    Para obtener más información, consulte [Uso del servicio StorSimple Manager para supervisar su dispositivo StorSimple](storsimple-monitor-device.md).
* **Información general del uso**: en el área de **información general de uso**, puede ver la cantidad de almacenamiento principal usado, la cantidad de almacenamiento aprovisionado y la capacidad máxima de almacenamiento del dispositivo. Al comparar estos números de uso con la cantidad máxima de almacenamiento disponible, podrá ver a simple vista si necesita obtener almacenamiento adicional. Tenga en cuenta que esta información general se actualiza cada 15 minutos y, debido a la diferencia en la frecuencia de actualización, es posible que muestre números diferentes a los que se muestran en el área de gráfico anterior, que se actualiza diariamente. Para obtener más información, consulte [Uso del servicio StorSimple Manager para supervisar su dispositivo StorSimple](storsimple-monitor-device.md).
* **Alertas**: el área de **alertas** contiene información general sobre las alertas del dispositivo. Estas se agrupan por nivel de gravedad, y se proporciona un recuento del número de alertas de cada nivel de gravedad. Al hacer clic en la gravedad de la alerta se abre una vista concreta de la pestaña Alertas para mostrar solo las alertas de ese nivel de gravedad para este dispositivo.
* **Trabajos**: el área de **trabajos** muestra el resultado de la actividad reciente de los trabajos. Esto puede garantizarle que el sistema funciona según lo esperado, o puede informarle de que debe tomar medidas correctivas. Para obtener más información acerca de los trabajos completados recientemente, haga clic en **Trabajos correctos en las últimas 24 horas**.
* El área de **vista rápida** situada a la derecha del panel proporciona información útil, como el modelo del dispositivo, el número de serie, el estado, la descripción y el número de volúmenes.

También puede configurar la conmutación por error y ver iniciadores conectados desde el panel de dispositivos.

Las tareas comunes que se pueden realizar en esta página son:

* Ver iniciadores conectados
* Buscar el número de serie del dispositivo
* Encontrar el IQN de destino del dispositivo

## <a name="view-connected-initiators"></a>Ver iniciadores conectados
Puede ver los iniciadores iSCSI que están conectados al dispositivo haciendo clic en el vínculo **Ver iniciadores conectados** en el área de **vista rápida** del panel del dispositivo. Esta página proporciona una lista tabular de los iniciadores que se han conectado correctamente al dispositivo. Para cada iniciador, podrá ver:

* El nombre completo de iSCSI (IQN) del iniciador conectado.
* El nombre del registro de control de acceso (ACR) que permite este iniciador conectado.
* La dirección IP del iniciador conectado.
* Las interfaces de red a las que está conectado el iniciador en el dispositivo de almacenamiento. Estas pueden ir desde DATA 0 hasta DATA 5.
* Todos los volúmenes a los que puede tener acceso el iniciador conectado según la configuración actual de ACR.

Si ve iniciadores inesperados en esta lista o no ve los esperados, revise la configuración de ACR. El máximo de iniciadores que pueden conectarse a su dispositivo es de 512.

## <a name="find-the-device-serial-number"></a>Buscar el número de serie del dispositivo
Es posible que necesite el número de serie del dispositivo para configurar E/S de múltiples rutas de Microsoft (MPIO) en el dispositivo. Realice los pasos siguientes para buscar el número de serie del dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Para buscar el número de serie del dispositivo
1. Vaya a **Dispositivos** > **Panel**.
2. En el panel derecho del panel, busque el área **Vista rápida** .
3. Desplácese hacia abajo y busque el número de serie.

## <a name="find-the-device-target-iqn"></a>Encontrar el IQN de destino del dispositivo
Es posible que necesite el IQN de destino del dispositivo para configurar el Protocolo de autenticación por desafío mutuo (CHAP) en el dispositivo StorSimple. Realice los pasos siguientes para encontrar el IQN de destino del dispositivo.

### <a name="to-find-the-device-target-iqn"></a>Para encontrar el IQN de destino del dispositivo
1. Vaya a **Dispositivos** > **Panel**.
2. En el panel derecho del panel, busque el área **Vista rápida** .
3. Desplácese hacia abajo y busque el IQN de destino.

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga más información sobre el panel del servicio StorSimple Manager](storsimple-service-dashboard.md).
* Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

