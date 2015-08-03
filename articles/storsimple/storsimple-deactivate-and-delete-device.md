<properties 
   pageTitle="Desactivación y eliminación de un dispositivo de StorSimple | Microsoft Azure"
   description="Describe cómo desactivar y eliminar el dispositivo de StorSimple para quitarlo del servicio."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Desactivación y eliminación de un dispositivo de StorSimple

Este tutorial explica cómo quitar un dispositivo de StorSimple del servicio desactivándolo y eliminándolo después.

>[AZURE.NOTE]Debe desactivar un dispositivo antes de poder eliminarlo.

## Desactivación de un dispositivo

Puede que quiera quitar un dispositivo del servicio. En este caso, el dispositivo deberá desactivarse. La desactivación interrumpe la conexión entre el dispositivo y el servicio StorSimple Manager correspondiente.

>[AZURE.WARNING]La desactivación es una operación permanente y no se puede deshacer. Un dispositivo desactivado no se puede registrar en el servicio StorSimple Manager a menos que se realice un restablecimiento de fábrica.

Al desactivar un dispositivo, ya no se podrá acceder a los datos que se almacenan localmente en el mismo. Solo se pueden recuperar los datos asociados con el dispositivo que se almacenaron en la nube. Una vez desactivado, se debe realizar un restablecimiento de fábrica del dispositivo para poder reutilizarlo con un servicio nuevo o existente. El restablecimiento de fábrica elimina todos los datos almacenados localmente en el dispositivo. Por lo tanto, es esencial que realice una instantánea en la nube de todos los datos antes de desactivar un dispositivo. Esto le permitirá recuperar todos los datos más adelante.

Para un dispositivo virtual de StorSimple, la desactivación elimina la máquina virtual y los recursos que se crearon al aprovisionarla. Después de desactivar el dispositivo virtual, no se puede restaurar a su estado anterior. Antes de desactivar un dispositivo virtual de StorSimple, asegúrese de detener o eliminar los clientes y hosts que dependen de ese dispositivo virtual.

### Desactivación y eliminación de datos

Si está interesado en eliminar completamente el dispositivo y no desea conservar los datos del mismo, haga lo siguiente:

1. Antes de desactivar un dispositivo, debe eliminar todos los contenedores de volúmenes (y los volúmenes) asociados con el dispositivo. Solo puede eliminar los contenedores de volúmenes después de eliminar las copias de seguridad asociadas.

2. Desactive el dispositivo. Vaya a [Pasos para la desactivación](#steps-to-deactivate) para obtener instrucciones.

3. Tras la desactivación, puede eliminar completamente el dispositivo. Vaya a [Eliminación de un dispositivo](#delete-a-device) para obtener instrucciones.

### Desactivación y conservación de los datos

Si está interesado en eliminar el dispositivo pero desea conservar los datos del mismo, haga lo siguiente:

1. Desactive el dispositivo. Todos los contenedores de volúmenes y las instantáneas del dispositivo se conservarán. Vaya a [Pasos para la desactivación](#steps-to-deactivate) para obtener instrucciones.

2. Ahora puede conmutar los contenedores de volúmenes y las instantáneas asociadas. Para ver los procedimientos, vaya a [Conmutación por error y recuperación ante desastres para el dispositivo de StorSimple](storsimple-device-failover-disaster-recovery.md)

3. Después de la desactivación y la conmutación por error, puede eliminar completamente el dispositivo. Vaya a [Eliminación de un dispositivo](#delete-a-device) para obtener instrucciones.

### Pasos para la desactivación

Use el procedimiento siguiente para desactivar un dispositivo para preparar su eliminación.

#### Para desactivar un dispositivo

1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione el dispositivo que desea desactivar y, en la parte inferior de la página, haga clic en **Desactivar**.

2. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para continuar. El proceso de desactivación se iniciará y tardará unos minutos en completarse.

    En un dispositivo virtual de StorSimple, la desactivación genera las siguientes acciones:

      - Se quita el dispositivo virtual de StorSimple.

      - Se quitan los OSDisk y los discos de datos creados para el dispositivo virtual StorSimple.

      - Se mantienen el servicio hospedado y la red virtual creados durante el aprovisionamiento. Si no usa estas entidades, debe eliminarlas manualmente.

      - Se conservan las instantáneas en la nube creadas por el dispositivo virtual de StorSimple.

<!--After the device is deactivated, you will need to perform a failover before you can delete it completely. For failover instructions, go to [Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery.md).-->
## Eliminación de un dispositivo

Solo puede eliminar los dispositivos que se han desactivado. Al eliminar un dispositivo, se quita de la lista de dispositivos conectados al servicio. El servicio ya no podrá administrar el dispositivo eliminado.

#### Para eliminar un dispositivo

1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione un dispositivo desactivado que desea eliminar.

2. En la parte inferior de la página, haga clic en **Eliminar**.

3. Se le pedirá confirmación. Haga clic en **Sí** para continuar.

Es posible que el servicio tarde unos minutos en eliminarse.

## Pasos siguientes
Para restaurar los valores de fábrica del dispositivo desactivado, vaya a [Restablecer el dispositivo a los valores de fábrica](https://msdn.microsoft.com/library/dn772373.aspx).

Para obtener asistencia técnica, [póngase en contacto con el soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

<!---HONumber=July15_HO4-->