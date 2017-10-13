---
title: "Conmutación por error y recuperación ante desastres de StorSimple en un dispositivo físico de la serie StorSimple 8000 | Microsoft Docs"
description: "Aprenda a conmutar por error el dispositivo físico de la serie StorSimple 8000 a otro dispositivo físico."
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Conmutación por error a un dispositivo físico de la serie StorSimple 8000

## <a name="overview"></a>Información general

En este tutorial se describen los pasos necesarios para conmutar por error un dispositivo físico de la serie StorSimple 8000 a un dispositivo StorSimple Cloud Appliance en caso de desastre. StorSimple usa la característica de conmutación por error de dispositivos para migrar los datos desde un dispositivo físico de origen en el centro de datos a otro dispositivo físico. Las instrucciones de este tutorial se aplican a los dispositivos físicos de la serie StorSimple 8000 que ejecutan las versiones de software Update 3 y posteriores.

Para aprender más sobre la conmutación por error de dispositivos y cómo se usa para recuperarse de un desastre, vaya a [Conmutación por error y recuperación ante desastres para dispositivos de la serie StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para conmutar por error un dispositivo físico StorSimple a un dispositivo StorSimple Cloud Appliance, vaya a [Fail over to a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md) (Conmutación por error a un dispositivo StorSimple Cloud Appliance). Para conmutar por error un dispositivo físico a sí mismo, vaya a [Fail over to the same StorSimple physical device](storsimple-8000-device-failover-same-device.md) (Conmutación por error al mismo dispositivo físico StorSimple).


## <a name="prerequisites"></a>Requisitos previos

- Asegúrese de haber revisado las consideraciones sobre la conmutación por error de dispositivos. Para más información, vaya a [Consideraciones comunes para la conmutación por error de dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Debe disponer de un dispositivo físico de la serie StorSimple 8000 implementado en el centro de datos. El dispositivo debe ejecutar la versión Update 3 del software u otra posterior. Para obtener más información, vaya a [Implementación del dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Pasos para conmutar por error a un dispositivo físico

Siga estos pasos para restaurar el dispositivo a un dispositivo físico de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube. Para más información, vaya a [Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md) (Uso del servicio StorSimple Device Manager para crear copias de seguridad).
2. Vaya a StorSimple Device Manager y, después, haga clic en **Dispositivos**. En la hoja **Dispositivos**, vaya a la lista de dispositivos conectados con su servicio.
    ![Seleccionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Seleccione su dispositivo de origen y haga clic en él. El dispositivo de origen tiene los contenedores de volúmenes que desea conmutar por error. Vaya a **Configuración > Contenedores de volúmenes**.
4. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen, haga clic en él con el botón derecho y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.
5. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.
6. Vuelva a la hoja **Dispositivos**. En la barra de comandos, haga clic en **Conmutar por error**.
    Haga clic en ![Conmutación por error](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png).
    
7. En la hoja **Conmutar por error**, siga estos pasos:
   
   1. Haga clic en **Origen**. Se muestran los contenedores de volúmenes con volúmenes asociados con instantáneas de nube. Solo los contenedores que aparecen en la lista son aptos para la conmutación por error. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error. **Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**

       ![Seleccionar origen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Haga clic en **Destino**. Para los contenedores de volúmenes seleccionados en el paso anterior, seleccione un dispositivo de destino en la lista desplegable de dispositivos disponibles. Solo se muestran en la lista los dispositivos que tienen capacidad suficiente para alojar los contenedores de volúmenes de origen.

        ![Seleccionar destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Por último, revise toda la configuración de conmutación por error en **Resumen**. Una vez que haya revisado la configuración, seleccione la casilla que indica que los volúmenes de los contenedores de volumen seleccionados están sin conexión. Haga clic en **Aceptar**.

       ![Revisar la configuración de conmutación por error](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple crea un trabajo de conmutación por error. Para supervisar el trabajo de conmutación por error, haga clic en la notificación de trabajo en la hoja **Trabajos**.

    Si el contenedor de volúmenes que conmutó por error tiene volúmenes locales, verá los trabajos de restauración individuales de cada volumen local (no de volúmenes en niveles) en el contenedor. Es posible que esos trabajos de restauración tarden bastante tiempo en completarse. Asimismo, es probable que el trabajo de conmutación por error se complete antes. Estos volúmenes solo tendrán garantías locales después de que los trabajos de restauración se completen.

    ![Supervisión del trabajo de conmutación por error](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Una vez completada la conmutación por error, vaya a la hoja **Dispositivos**.
   
   1. Seleccione el dispositivo que se usó como dispositivo de destino para el proceso de conmutación por error.

       ![Selección del dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Vaya a la hoja **Contenedores de volúmenes**. Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

       ![Ver contenedores de volúmenes de destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Pasos siguientes

* Después de haber realizado una conmutación por error, puede que necesite [desactivar o eliminar el dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para más información sobre cómo usar el servicio StorSimple Device Manager, vaya a [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Uso del servicio StorSimple Device Manager para administrar el dispositivo StorSimple).

