---
title: "Conmutación por error y recuperación ante desastres de StorSimple | Microsoft Docs"
description: "Aprenda cómo conmutar por error el dispositivo StorSimple a sí mismo, a otro dispositivo físico o a un dispositivo virtual."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5751598e-49c8-42b3-8121-fea5857a7d83
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: alkohli
ms.openlocfilehash: bf92ffdb16b86c4033cc96ae2abb060d90f9505e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-device"></a>Conmutación por error y recuperación ante desastres para el dispositivo StorSimple
## <a name="overview"></a>Información general
En este tutorial se describen los pasos necesarios para conmutar por error un dispositivo StorSimple en caso de desastre. La conmutación por error permite migrar los datos desde un dispositivo de origen en el centro de datos a otro dispositivo físico o incluso virtual situado en la misma ubicación geográfica o en otra diferente. 

La recuperación ante desastres (DR) se coordina a través de la función de recuperación ante desastres del dispositivo y se inicia desde la página **Dispositivos** . Esta página recoge en formato de tabla todos los dispositivos de StorSimple conectados al servicio de Administrador de StorSimple. Para cada dispositivo se muestran el nombre descriptivo, el estado, la capacidad aprovisionada y máxima, el tipo y el modelo.

![Página de dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

Las instrucciones de este tutorial se aplican a dispositivos físicos y virtuales de StorSimple en todas las versiones de software.

## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperación ante desastres y conmutación por error del dispositivo
En un escenario de recuperación ante desastres, el dispositivo principal deja de funcionar. En esta situación, puede mover los datos en la nube asociados al dispositivo con error a otro dispositivo usando el dispositivo primario como *origen* y especificando otro dispositivo como *destino*. Puede seleccionar uno o varios contenedores de volúmenes para migrar al dispositivo de destino. Este proceso se conoce como *conmutación por error*. 

Durante la conmutación por error, los contenedores de volúmenes del dispositivo de origen cambian la propiedad y se transfieren al dispositivo de destino. Una vez que los contenedores de volumen cambian la propiedad, estos se eliminan del dispositivo de origen. Una vez completada la eliminación, el dispositivo de destino puede conmutarse por recuperación.

Normalmente, tras una recuperación ante desastres, se utiliza la copia de seguridad más reciente para restaurar los datos al dispositivo de destino. Sin embargo, si hay varias directivas de copia de seguridad para el mismo volumen, se detecta la directiva de copia de seguridad con el mayor número de volúmenes y la copia de seguridad más reciente de esa directiva se usa para restaurar los datos en el dispositivo de destino.

Por ejemplo, si hay dos directivas de copia de seguridad (una predeterminada y otra personalizada), *defaultPol* y *customPol*, con los detalles siguientes:

* *defaultPol*: un volumen, *vol1*; se ejecuta diariamente a las 22:30.
* *customPol*: cuatro volúmenes, *vol1*, *vol2*, *vol3* y *vol4*; se ejecuta diariamente a las 22:00.

En este caso, se usará *customPol* , ya que es la que tiene más volúmenes, y establecemos prioridad para la coherencia de bloqueos. La copia de seguridad más reciente de esta directiva se usa para restaurar los datos.

## <a name="considerations-for-device-failover"></a>Consideraciones para la conmutación por error del dispositivo
En caso de desastre, puede elegir conmutar por error el dispositivo StorSimple:

* A un dispositivo físico 
* A sí mismo
* A un dispositivo virtual

En todos los tipos de conmutación por error del dispositivo, tenga en cuenta lo siguiente:

* Los requisitos previos para la recuperación ante desastres son que todos los volúmenes incluidos en los contenedores de volúmenes estén desconectados y que los contenedores de volúmenes tengan asociada una instantánea en la nube. 
* Los dispositivos de destino disponibles para la recuperación ante desastres son dispositivos con espacio suficiente para alojar los contenedores de volúmenes seleccionados. 
* Los dispositivos que están conectados al servicio pero que no cumplen los criterios de espacio suficiente no estarán disponibles como dispositivos de destino.
* Tras una recuperación ante desastres, durante un tiempo limitado, el rendimiento del acceso a datos puede verse afectado de forma significativa, ya que el dispositivo deberá acceder a los datos de la nube y almacenarlos localmente.

#### <a name="device-failover-across-software-versions"></a>Conmutación por error del dispositivo a través de las versiones de software
Un servicio del administrador de StorSimple en una implementación puede tener varios dispositivos tanto virtuales como físicos, y todos ellos pueden ejecutar versiones de software diferentes. En función de la versión de software, los tipos de volúmenes de los dispositivos también pueden ser diferentes. Por ejemplo, un dispositivo que ejecuta Update 2 o posterior tendría volúmenes anclados localmente y en capas (de tal forma que los archivos serían un subconjunto de las capas). Por otro lado, un dispositivo con una versión anterior a Update 2 puede tener volúmenes en capas y de archivado. 

Utilice la tabla siguiente para determinar si puede conmutar por error a otro dispositivo que ejecuta una versión diferente de software y el comportamiento de los tipos de volumen durante la recuperación ante desastres.

| Conmutación por error desde | Permitida para dispositivo físico | Permitida para dispositivo virtual |
| --- | --- | --- |
| Update 2 a versión anterior a Update 1 (lanzamiento, 0.1, 0.2, 0.3) |No |No |
| Update 2 a Update 1 (1, 1.1, 1.2) |Sí  <br></br>Si utiliza volúmenes anclados localmente o en capas o una combinación de ambos, la conmutación por error de tales volúmenes siempre se realiza en capas. |Sí <br></br>Si utiliza volúmenes anclados localmente, su conmutación por error se realiza en capas. |
| Update 2 a Update 2 (versión posterior) |Sí <br></br>Si utiliza volúmenes anclados localmente o en capas o una combinación de ambos, la conmutación por error de tales volúmenes se realiza como el tipo de volumen de partida; los volúmenes en capas como en capas y los anclados localmente como anclados localmente. |Sí <br></br>Si utiliza volúmenes anclados localmente, su conmutación por error se realiza en capas. |

#### <a name="partial-failover-across-software-versions"></a>Conmutación por error parcial entre versiones de software
Siga esta guía si va a realizar una conmutación por error parcial de un dispositivo de origen StorSimple con una versión anterior a la actualización 1 en un destino en que se ejecuta la actualización 1 o una versión posterior. 

| Conmutación por error parcial desde | Permitida para dispositivo físico | Permitida para dispositivo virtual |
| --- | --- | --- |
| Versión anterior a la actualización 1 (lanzamiento, 0.1, 0.2, 0.3) en la versión de actualización 1 o posterior |Sí, consulte abajo la sugerencia de procedimiento recomendado. |Sí, consulte abajo la sugerencia de procedimiento recomendado. |

> [!TIP]
> Se produjo un cambio de formato de datos y metadatos de nube en la versión de actualización 1 y posteriores. Por lo tanto, no se recomienda realizar una conmutación por error parcial desde versiones anteriores a la actualización 1 en la versión de actualización 1 o posteriores. Si necesita realizar una conmutación por error parcial, se recomienda que aplique primero la actualización 1 o posterior en ambos dispositivos (origen y destino) y después lleve a cabo la conmutación por error. 
> 
> 

## <a name="fail-over-to-another-physical-device"></a>Conmutar por error a otro dispositivo físico
Siga estos pasos para restaurar el dispositivo a un dispositivo físico de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.
2. En la página **Dispositivos**, haga clic en la pestaña **Contenedores de volúmenes**.
3. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.
4. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.
5. En la página **Dispositivos**, haga clic en **Conmutación por error**.
6. En el asistente que se abre, en **Elegir el contenedor de volúmenes para la conmutación por error**:
   
   1. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error.
      **Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**
   2. En **Elegir un dispositivo de destino** para los volúmenes de los contenedores seleccionados, elija un dispositivo de destino en la lista desplegable de dispositivos disponibles. En la lista desplegable solo se muestran los dispositivos con capacidad disponible.
   3. Por último, revise toda la configuración de conmutación por error en **Confirmar conmutación por error**. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
7. Se crea un trabajo de conmutación por error que puede supervisarse mediante la página **Trabajos** . Si el contenedor de volúmenes que conmutó por error tiene volúmenes locales, verá los trabajos de restauración individuales de cada volumen local (no de volúmenes en niveles) en el contenedor. Es posible que esos trabajos de restauración tarden bastante tiempo en completarse. Asimismo, es probable que el trabajo de conmutación por error se complete antes. Tenga en cuenta que estos volúmenes solo tendrán garantías locales después de que los trabajos de restauración se completen. Una vez completada la conmutación por error, vaya a la página **Dispositivos** .                                            
   
   1. Seleccione el dispositivo que se usó como dispositivo de destino para el proceso de conmutación por error.
   2. Vaya a la página **Contenedores de volúmenes** . Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

## <a name="failover-using-a-single-device"></a>Conmutación por error usando un solo dispositivo
Siga estos pasos si solo dispone de un dispositivo y necesita realizar una conmutación por error.

1. Tome instantáneas en la nube de todos los volúmenes del dispositivo.
2. Restablezca el dispositivo a los valores predeterminados de fábrica. Siga las instrucciones detalladas de [Cómo restablecer un dispositivo StorSimple a los valores predeterminados de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Configure el dispositivo y vuelva a registrarlo con el servicio de Administrador de StorSimple.
4. En la página **Dispositivos**, el dispositivo antiguo debe aparecer como **Desconectado**. El dispositivo recién registrado debe aparecer como **Conectado**.
5. Complete primero la configuración mínima del dispositivo en el dispositivo nuevo. 
   
   > [!IMPORTANT]
   > **Si no se completa primero la configuración mínima, se producirá un error en la recuperación ante desastres como resultado de un error en la implementación actual. Este comportamiento se solucionará en una versión posterior.**
   > 
   > 
6. Seleccione el dispositivo antiguo (estado desconectado) y haga clic en **Conmutación por error**. En el asistente que aparece, conmute por error este dispositivo y especifique el dispositivo de destino como dispositivo recién registrado. Para obtener instrucciones detalladas, consulte [Conmutar por error a otro dispositivo físico](#fail-over-to-another-physical-device).
7. Se creará un trabajo de restauración de dispositivo que puede supervisar desde la página **Trabajos** .
8. Después de que el trabajo se haya completado correctamente, acceda al dispositivo nuevo y navegue hasta la página **Contenedores de volúmenes** . Ahora se deben migrar todos los contenedores de volúmenes del dispositivo antiguo al dispositivo nuevo.

## <a name="fail-over-to-a-storsimple-virtual-device"></a>Conmutar por error a un dispositivo virtual de StorSimple
Debe crear y configurar un dispositivo virtual de StorSimple antes de ejecutar este procedimiento. Si ejecuta Update 2, considere el uso de un dispositivo virtual 8020 para la recuperación ante desastres que tiene 64 TB y utiliza el almacenamiento premium. 

Siga estos pasos para restaurar el dispositivo a un dispositivo virtual de StorSimple de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.
2. En la página **Dispositivos**, haga clic en la pestaña **Contenedores de volúmenes**.
3. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.
4. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.
5. En la página **Dispositivos**, haga clic en **Conmutación por error**.
6. En el asistente que se abre, en **Elegir el contenedor de volúmenes para la conmutación por error**, siga estos pasos:
   
    a. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error.
   
    **Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**
   
    b. En **Elegir un dispositivo de destino para los volúmenes de los contenedores seleccionados**, seleccione el dispositivo virtual de StorSimple en la lista desplegable de dispositivos disponibles. **En la lista desplegable solo se muestran los dispositivos con capacidad suficiente.**  
7. Por último, revise toda la configuración de conmutación por error en **Confirmar conmutación por error**. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
8. Una vez completada la conmutación por error, vaya a la página **Dispositivos** .
   
    a. Seleccione el dispositivo virtual de StorSimple que se usó como dispositivo de destino para el proceso de conmutación por error.
   
    b. Vaya a la página **Contenedores de volúmenes** . Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

![Vídeo disponible](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Vídeo disponible**

Para ver un vídeo que muestra cómo se puede restaurar un dispositivo físico con conmutación por error en un dispositivo virtual en la nube, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## <a name="failback"></a>Conmutación por recuperación
Para Update 3 y versiones posteriores, StorSimple también admite la conmutación por recuperación. Cuando se complete la conmutación por error, se producen las siguientes acciones:

* Los contenedores de volumen que se conmutan por error se borran del dispositivo de origen.
* Se inicia un trabajo en segundo plano por contenedor de volúmenes (conmutación por error) en el dispositivo de origen. Si trata de realizar una conmutación por recuperación mientras el trabajo está en curso, recibirá una notificación. Debe esperar hasta que se complete para iniciar este proceso. 
  
    El tiempo que se tarda en eliminar los contenedores de volumen depende de varios factores, como la cantidad y la antigüedad de los datos, el número de copias de seguridad y el ancho de banda de red disponible para la operación. Si piensa probar la conmutación por error y la conmutación por recuperación, se recomienda que pruebe contenedores de volúmenes con menos datos (GB). En la mayoría de los casos, puede iniciar la conmutación por recuperación 24 horas después de que finalice la conmutación por error. 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
P: **¿Qué ocurre si se produce un error de la recuperación ante desastres o se completa correctamente solo de forma parcial?**

a. Si se produce un error en la recuperación ante desastres, recomendamos que vuelva a intentarlo. La segunda vez, la recuperación ante desastres sabe lo que todo se lleva a cabo y cuándo se detuvo el proceso la primera vez. El proceso de recuperación ante desastres se inicia desde ese punto hacia adelante. 

P: **¿Puedo eliminar un dispositivo mientras se realiza la conmutación por error del dispositivo?**

a. No se puede eliminar un dispositivo mientras se realiza una recuperación ante desastres. Solo puede eliminar el dispositivo una vez completada la recuperación ante desastres.

P:    **¿Cuándo se inicia la recolección de elementos no utilizados en el dispositivo de origen para que se eliminen los datos locales en el dispositivo de origen?**

a. La recopilación de elementos no utilizados se habilitará en el dispositivo de origen solo después de que el dispositivo se limpie completamente. La limpieza incluye limpiar los objetos que se han conmutado por error desde dispositivo de origen, como volúmenes, objetos de copia de seguridad (no datos), contenedores de volúmenes y directivas.

P: **¿Qué ocurre si se produce un error en el trabajo de eliminación asociado con los contenedores de volúmenes del dispositivo de origen?**

a.  Si se produce un error en el trabajo de eliminación, necesitará desencadenar manualmente la eliminación de los contenedores de volúmenes. En la página **Dispositivos**, seleccione el dispositivo de origen y haga clic en **Contenedores de volúmenes**. Seleccione los contenedores de volúmenes que se conmutaron por error y en la parte inferior de la página, haga clic en **Eliminar**. Una vez que haya eliminado todos los contenedores de volúmenes conmutados por error en el dispositivo de origen, puede iniciar la conmutación por recuperación.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperación ante desastres y continuidad empresarial (BCDR)
Un escenario de recuperación ante desastres y continuidad empresarial (BCDR) se produce cuando todo el centro de datos de Azure deja de funcionar. Esto puede afectar al servicio de Administrador de StorSimple y a los dispositivos StorSimple asociados.

Si hay dispositivos StorSimple que se registraron justo antes de que ocurra un desastre, es posible que estos dispositivos deban restablecerse a valores de fábrica. Después del desastre, el dispositivo StorSimple se mostrará como desconectado. El dispositivo StorSimple debe eliminarse del portal, restablecerse a valores de fábrica y volver a registrarse.

## <a name="next-steps"></a>Pasos siguientes
* Después de haber realizado una conmutación por error, puede que necesite [desactivar o eliminar el dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).
* Para obtener información sobre cómo usar el servicio del administrador de StorSimple, vaya a [Utilizar el servicio de Administrador de StorSimple para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

