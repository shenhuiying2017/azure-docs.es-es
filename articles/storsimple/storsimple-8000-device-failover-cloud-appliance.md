---
title: "Conmutación por error y recuperación ante desastres de StorSimple en un dispositivo StorSimple Cloud Appliance | Microsoft Docs"
description: "Aprenda a conmutar por error el dispositivo físico de la serie StorSimple 8000 a un dispositivo de nube."
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Conmutación por error a StorSimple Cloud Appliance

## <a name="overview"></a>Información general

En este tutorial se describen los pasos necesarios para conmutar por error un dispositivo físico de la serie StorSimple 8000 a un dispositivo StorSimple Cloud Appliance en caso de desastre. StorSimple usa la característica de conmutación por error de dispositivos para migrar los datos desde un dispositivo físico de origen en el centro de datos a un dispositivo de nube que se ejecuta en Azure. Las instrucciones de este tutorial se aplican a los dispositivos físicos y dispositivos de nube de la serie StorSimple 8000 que ejecutan las versiones de software Update 3 y posteriores.

Para aprender más sobre la conmutación por error de dispositivos y cómo se usa para recuperarse de un desastre, vaya a [Failover and disaster recovery for StorSimple 8000 series devices](storsimple-8000-device-failover-disaster-recovery.md) (Conmutación por error y recuperación ante desastres para dispositivos de la serie StorSimple 8000).

Para conmutar por error un dispositivo físico StorSimple a otro dispositivo físico, vaya a [Fail over to a StorSimple physical device](storsimple-8000-device-failover-physical-device.md) (Conmutación por error a un dispositivo físico StorSimple). Para conmutar por error un dispositivo a sí mismo, vaya a [Fail over to the same StorSimple physical device](storsimple-8000-device-failover-same-device.md) (Conmutación por error al mismo dispositivo físico StorSimple).

## <a name="prerequisites"></a>Requisitos previos

- Asegúrese de haber revisado las consideraciones sobre la conmutación por error de dispositivos. Para más información, vaya a [Common considerations for device failover](storsimple-8000-device-failover-disaster-recovery.md) (Consideraciones comunes sobre la conmutación por error de dispositivos).

- Antes de ejecutar este procedimiento, debe haber creado y configurado un dispositivo StorSimple Cloud Appliance. Si ejecuta la versión de software Update 3 o posterior, considere la posibilidad de usar un dispositivo de nube 8020 para el DR. El modelo 8020 tiene 64 TB y usa Premium Storage. Para más información, vaya a [Implementación y administración de una instancia de StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Pasos para la conmutación por error a un dispositivo de nube

Siga estos pasos para restaurar el dispositivo a un dispositivo StorSimple Cloud Appliance de destino.

1.  Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube. Para más información, vaya a [Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md) (Uso del servicio StorSimple Device Manager para crear copias de seguridad).
2. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**. En la hoja **Dispositivos**, vaya a la lista de dispositivos conectados con su servicio.
    ![Seleccionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Seleccione su dispositivo de origen y haga clic en él. El dispositivo de origen tiene los contenedores de volúmenes que desea conmutar por error. Vaya a **Configuración > Contenedores de volúmenes**.

    ![Selección del dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen, haga clic en él con el botón derecho y haga clic en **Desconectar** para desconectar el volumen.

    ![Selección del dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.

     ![Selección del dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.

7. Vuelva a la hoja **Dispositivos**. En la barra de comandos, haga clic en **Conmutar por error**.

    ![Acción de hacer clic en Conmutar por error](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. En la hoja **Conmutar por error**, siga estos pasos:
   
    1. Haga clic en **Origen**. Seleccione los contenedores de volúmenes para conmutar por error. **Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**
        ![Seleccionar origen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Haga clic en **Destino**. Seleccione un dispositivo de nube de destino en la lista desplegable de dispositivos disponibles. **Solo se muestran en la lista los dispositivos que tienen capacidad suficiente para alojar los contenedores de volúmenes de origen.**

        ![Selección del destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Revise la configuración de conmutación por error en **Resumen** y seleccione la casilla de verificación que indica que los volúmenes de los contenedores de volumen seleccionados están sin conexión. 

        ![Revisión de la configuración de conmutación por error](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Se crea un trabajo de conmutación por error. Para supervisar el trabajo de conmutación por error, haga clic en la notificación de trabajo.

    ![Supervisión del trabajo de conmutación por error](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Una vez completada la conmutación por error, vuelva a la hoja **Dispositivos**.

    1. Seleccione el dispositivo que se usó como destino para la conmutación por error.

       ![Selección del dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Haga clic en **Contenedores de volúmenes**. Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

       Si el contenedor de volúmenes que ha conmutado por error ha anclado volúmenes localmente, esos volúmenes se conmutan por error como volúmenes en capas. Los volúmenes anclados localmente no se admiten en un dispositivo StorSimple Cloud Appliance.

       ![Visualización de contenedores de volúmenes de destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Pasos siguientes

* Después de haber realizado una conmutación por error, puede que necesite [desactivar o eliminar el dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Para más información sobre cómo usar el servicio StorSimple Device Manager, vaya a [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Uso del servicio StorSimple Device Manager para administrar el dispositivo StorSimple).

