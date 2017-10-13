---
title: "Desactivación y eliminación de un dispositivo de la serie StorSimple 8000 | Microsoft Docs"
description: "Describe cómo desactivar y eliminar en primer lugar el dispositivo de StorSimple para quitarlo del servicio."
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Desactivación y eliminación de un dispositivo de StorSimple

## <a name="overview"></a>Información general

En este artículo se describe cómo desactivar y eliminar un dispositivo de StorSimple conectado a un servicio StorSimple Device Manager. Las instrucciones de este artículo se aplican solo a dispositivos de la serie StorSimple 8000, incluidas las instancias de StorSimple Cloud Appliance. Si utiliza una matriz virtual de StorSimple, vaya a [Desactivación y eliminación de una matriz virtual de StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

La desactivación interrumpe la conexión entre el dispositivo y el servicio StorSimple Device Manager correspondiente. Puede que desee eliminar un dispositivo de StorSimple (por ejemplo, si va a reemplazar o actualizar el dispositivo o si ya no usa StorSimple). En ese caso, es preciso desactivarlo antes de eliminarlo.

Al desactivar un dispositivo, no se puede volver a acceder a los datos que se almacenaron localmente en el mismo. Solo se pueden recuperar los datos asociados con el dispositivo que se almacenaron en la nube.

> [!WARNING]
> La desactivación es una operación permanente y no se puede deshacer. Un dispositivo desactivado no se puede registrar en el servicio StorSimple Device Manager, salvo que se restablezca a la configuración predeterminada de fábrica.
>
> El restablecimiento de fábrica elimina todos los datos almacenados localmente en el dispositivo. Por consiguiente, debe realizar una instantánea en la nube de todos los datos antes de desactivar un dispositivo. Dicha instantánea le permitirá recuperar todos los datos más adelante.

Después de leer este tutorial, podrá:

* Desactivar un dispositivo y eliminar los datos.
* Desactivar un dispositivo y conservar los datos.

> [!NOTE]
> Antes de desactivar un dispositivo virtual o aplicación de nube de StorSimple, detenga o elimine los clientes y hosts que dependen de dicho dispositivo.


## <a name="deactivate-and-delete-data"></a>Desactivación y eliminación de datos

Si está interesado en eliminar completamente el dispositivo y no desea conservar los datos del mismo, siga estos pasos.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desactivar un dispositivo y eliminar los datos

1. Antes de desactivar un dispositivo, debe eliminar todos los contenedores de volúmenes (y los volúmenes) asociados con el dispositivo. Puede eliminar los contenedores de volúmenes solo después de eliminar las copias de seguridad asociadas.

    > [!NOTE]
    > Antes de desactivar un dispositivo físico StorSimple o StorSimple Cloud Appliance, asegúrese de que se eliminan realmente del dispositivo los datos del contenedor de volúmenes. Puede supervisar los gráficos de consumo en la nube y, cuando vea que el uso en la nube disminuye debido a las copias de seguridad eliminadas, podrá continuar para desactivar el dispositivo. Si desactiva el dispositivo antes de que esto ocurra, los datos se deshabilitarán en la cuenta de almacenamiento y se acumularán cargos.

2. Desactive el dispositivo como sigue:
   
   1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**. En la hoja **Dispositivos**, seleccione el dispositivo que desea desactivar, haga clic con el botón derecho y, después, haga clic en **Desactivar**.

        ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. En la hoja **Desactivar**, escriba el nombre del dispositivo y haga clic en **Desactivar**. El proceso de desactivación se inicia y tarda pocos minutos en completarse.

        ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Tras la desactivación, puede eliminar completamente el dispositivo. Al eliminar un dispositivo, se quita de la lista de dispositivos conectados al servicio. El servicio ya no podrá administrar el dispositivo eliminado. Siga estos pasos para eliminar el dispositivo:
   
   1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**. En la hoja **Dispositivos**, seleccione el dispositivo desactivado que desea eliminar, haga clic con el botón derecho y haga clic en **Eliminar**.

        ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. En la hoja **Eliminar**, escriba el nombre del dispositivo y haga clic en **Eliminar**. La eliminación tarda unos minutos en completarse.

        ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Una vez que la eliminación se completa correctamente, recibirá una notificación. La lista de dispositivos también se actualiza para reflejar la eliminación.

## <a name="deactivate-and-retain-data"></a>Desactivación y conservación de los datos

Si está interesado en eliminar el dispositivo, pero desea conservar sus datos, siga estos pasos:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desactivar un dispositivo y conservar los datos
1. Desactive el dispositivo. Todos los contenedores de volúmenes y las instantáneas del dispositivo se conservan.
   
   1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**. En la hoja **Dispositivos**, seleccione el dispositivo que desea desactivar, haga clic con el botón derecho y, después, haga clic en **Desactivar**.

         ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. En la hoja **Desactivar**, escriba el nombre del dispositivo y haga clic en **Desactivar**. El proceso de desactivación se inicia y tarda pocos minutos en completarse.

         ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Ahora puede conmutar los contenedores de volúmenes y las instantáneas asociadas. Para ver los procedimientos, vaya a [Conmutación por error y recuperación ante desastres para el dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md)
3. Después de la desactivación y la conmutación por error, puede eliminar completamente el dispositivo. Al eliminar un dispositivo, se quita de la lista de dispositivos conectados al servicio. El servicio ya no podrá administrar el dispositivo eliminado. Para eliminar el dispositivo, siga estos pasos:
   
   1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**. En la hoja **Dispositivos**, seleccione el dispositivo desactivado que desea eliminar, haga clic con el botón derecho y haga clic en **Eliminar**.

       ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. En la hoja **Eliminar**, escriba el nombre del dispositivo y haga clic en **Eliminar**. La eliminación tarda unos minutos en completarse.

       ![Desactivar dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Una vez que la eliminación se completa correctamente, recibirá una notificación. La lista de dispositivos también se actualiza para reflejar la eliminación.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Desactivación y eliminación de una aplicación en la nube

En StorSimple Cloud Appliance, la desactivación desde el portal desasigna y elimina la máquina virtual y los recursos creados en el aprovisionamiento. Después de desactivar el dispositivo de nube, no se puede restaurar a su estado anterior.

![Desactivación de StorSimple Cloud Appliance](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Los resultados de la desactivación de las siguientes acciones:

* StorSimple Cloud Appliance se quitan del servicio.
* Se elimina la máquina virtual de StorSimple Cloud Appliance.
* Se quitan los discos de datos y del sistema operativo creados para StorSimple Cloud Appliance.
* Se conservan el servicio hospedado y la red virtual que se crearon durante el aprovisionamiento. Si no usa estas entidades, debe eliminarlas manualmente.
* Se conservan las instantáneas en la nube creadas por StorSimple Cloud Appliance.

Después de que desactiva el dispositivo en la nube, puede eliminarlo de la lista de dispositivos. Seleccione el dispositivo desactivado, haga clic con el botón derecho y haga clic en **Eliminar**. StorSimple le envía una notificación cuando se elimina el dispositivo y se actualiza la lista de dispositivos.

## <a name="next-steps"></a>Pasos siguientes

* Para restaurar los valores de fábrica del dispositivo desactivado, vaya a [Restablecer el dispositivo a los valores de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para obtener asistencia técnica, [póngase en contacto con el soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).
* Para obtener información acerca de cómo usar el servicio StorSimple Device Manager, vaya a ///[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Uso del servicio StorSimple Device Manager para administrar un dispositivo de StorSimple).

