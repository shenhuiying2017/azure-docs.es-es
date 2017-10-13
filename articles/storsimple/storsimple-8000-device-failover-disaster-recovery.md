---
title: "Conmutación por error y recuperación ante desastres de StorSimple para dispositivos de la serie 8000| Microsoft Docs"
description: "Obtenga información sobre cómo conmutar por error el dispositivo de StorSimple a sí mismo, a otro dispositivo físico o a una aplicación de nube."
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
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Conmutación por error y recuperación ante desastres para dispositivos de StorSimple de la serie 8000

## <a name="overview"></a>Información general

En este artículo se describe la característica de conmutación por error de dispositivo para los dispositivos de la serie StorSimple 8000 y cómo se puede usar esta característica para recuperar dispositivos de StorSimple si se produce un desastre. StorSimple usa la conmutación por error de dispositivo para migrar los datos desde un dispositivo de origen en el centro de datos a otro dispositivo de destino. Las instrucciones de este artículo se aplican a los dispositivos físicos y aplicaciones de nube de la serie StorSimple 8000 que ejecutan las versiones de software Update 3 y posteriores.

StorSimple usa la hoja **Dispositivos** para iniciar la característica de conmutación por error de dispositivo en caso de desastre. En esta hoja se enumeran todos los dispositivos de StorSimple conectados al servicio Administrador de dispositivos de StorSimple Device.

![Hoja Dispositivos](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperación ante desastres y conmutación por error del dispositivo

En un escenario de recuperación ante desastres, el dispositivo principal deja de funcionar. StorSimple usa el dispositivo primario como _origen_ y mueve los datos en la nube asociados a otro dispositivo de _destino_. Este proceso se conoce como *conmutación por error*. En el siguiente gráfico se ilustra el proceso de conmutación por error.

![¿Qué ocurre en la conmutación por error de dispositivo?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

El dispositivo de destino para una conmutación por error podría ser un dispositivo físico o incluso una aplicación de nube. El dispositivo de destino puede estar en la misma ubicación geográfica que el dispositivo de origen o en otra diferente.

Durante la conmutación por error, puede seleccionar contenedores de volúmenes para la migración. Después, StorSimple cambia la propiedad de estos contenedores de volúmenes del dispositivo de origen al dispositivo de destino. Una vez que los contenedores de volúmenes cambian la propiedad, StorSimple los elimina del dispositivo de origen. Una vez completada la eliminación, se puede volver a realizar la conmutación por recuperación al dispositivo de destino. La _conmutación por recuperación_ transfiere la propiedad de nuevo al dispositivo de origen original.

### <a name="cloud-snapshot-used-during-device-failover"></a>Instantánea de nube usada durante la conmutación por error de dispositivo

Tras una recuperación ante desastres, se usa la copia de seguridad en la nube más reciente para restaurar los datos al dispositivo de destino. Para más información sobre las instantáneas de nube, vea [Usar el servicio Administrador de dispositivos de StorSimple para realizar una copia de seguridad manual](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

En la serie StorSimple 8000, las directivas de copia de seguridad están asociadas a las copias de seguridad. Si hay varias directivas de copia de seguridad para el mismo volumen, StorSimple selecciona la directiva de copia de seguridad con el mayor número de volúmenes. Después, StorSimple usa la copia de seguridad más reciente de la directiva de copia de seguridad seleccionada para restaurar los datos en el dispositivo de destino.

Supongamos que hay dos directivas de copia de seguridad, *defaultPol* y *customPol*:

* *defaultPol*: Un volumen, *vol1*; se ejecuta diariamente a las 22:30.
* *customPol*: Cuatro volúmenes, *vol1*, *vol2*, *vol3* y *vol4*, se ejecuta diariamente a las 22:00.

En este caso, StorSimple establece la prioridad para la coherencia de bloqueos y usa *customPol*, ya que tiene más volúmenes. La copia de seguridad más reciente de esta directiva se usa para restaurar los datos. Para más información sobre cómo crear y administrar directivas de copia de seguridad, vaya a [Use the StorSimple Device Manager service to manage backup policies](storsimple-8000-manage-backup-policies-u2.md) (Usar el servicio Administrador de dispositivos de StorSimple para administrar las directivas de copia de seguridad).

## <a name="common-considerations-for-device-failover"></a>Consideraciones comunes para la conmutación por error de dispositivo

Antes de conmutar por error a un dispositivo, revise la información siguiente:

* Antes de que se inicie una conmutación por error de dispositivo, todos los volúmenes en los contenedores de volúmenes deben estar sin conexión. En una conmutación por error no planeada, los volúmenes de StotSimple se desconectan automáticamente. Pero si va a realizar una conmutación por error planeada (para probar la recuperación ante desastres), debe desconectar todos los volúmenes.
* Solo los contenedores de volúmenes que tienen asociada una instantánea de nube se enumeran para la recuperación ante desastres. Debe haber al menos un contenedor de volúmenes con una instantánea de nube asociada para recuperar los datos.
* Si hay instantáneas de que se distribuyen entre varios contenedores de volúmenes, StorSimple conmuta por error a estos contenedores de volúmenes como un conjunto. En el caso poco frecuente de que haya instantáneas locales que se distribuyen entre varios contenedores de volúmenes pero las instantáneas de nube asociadas no lo hacen, StorSimple conmuta por error las instantáneas locales y los datos locales se pierden después de la recuperación ante desastres.
* Los dispositivos de destino disponibles para la recuperación ante desastres son dispositivos con espacio suficiente para alojar los contenedores de volúmenes seleccionados. Los dispositivos que no tienen suficiente espacio no se muestran como dispositivos de destino.
* Tras una recuperación ante desastres, durante un tiempo limitado, el rendimiento del acceso a datos puede verse afectado de forma significativa, ya que el dispositivo tiene que obtener acceso a los datos de la nube y almacenarlos localmente.

#### <a name="device-failover-across-software-versions"></a>Conmutación por error del dispositivo a través de las versiones de software

Un servicio Administrador de dispositivos de StorSimple en una implementación puede tener varios dispositivos, tanto virtuales como en la nube, y todos ellos pueden ejecutar versiones de software diferentes.

Use la tabla siguiente para determinar si puede conmutar por error o por recuperación a otro dispositivo que ejecuta una versión diferente de software y el comportamiento de los tipos de volumen durante la recuperación ante desastres.

#### <a name="failover-and-failback-across-software-versions"></a>Conmutación por error y conmutación por recuperación entre versiones de software

| Conmutación por error / Conmutación por recuperación | Dispositivo físico | Dispositivo de nube |
| --- | --- | --- |
| De Update 3 a Update 4 |Los volúmenes en capas conmutan por error como capas. <br></br>Los volúmenes anclados localmente conmutan por error como anclados localmente. <br></br> Al tomar una instantánea en el dispositivo con Update 4 después de realizar la conmutación por error, se inicia el [seguimiento basado en mapa térmico](storsimple-update4-release-notes.md#whats-new-in-update-4). |Los volúmenes anclados localmente conmutan por error como capas. |
| De Update 4 a Update 3 |Los volúmenes en capas conmutan por error como capas. <br></br>Los volúmenes anclados localmente conmutan por error como anclados localmente. <br></br> Las copias de seguridad usadas para la restauración conservan los metadatos de mapa térmico. <br></br>El seguimiento basado en mapa térmico no está disponible en Update 3 después de una conmutación por recuperación. |Los volúmenes anclados localmente conmutan por error como capas. |


## <a name="device-failover-scenarios"></a>Escenarios de conmutación por error de dispositivo

En caso de desastre, puede elegir conmutar por error el dispositivo de StorSimple:

* [A un dispositivo físico](storsimple-8000-device-failover-physical-device.md).
* [A sí mismo](storsimple-8000-device-failover-same-device.md).
* [A una aplicación de nube](storsimple-8000-device-failover-cloud-appliance.md).

En los artículos anteriores se proporcionan pasos detallados para cada uno de los casos de conmutación por error anteriores.


## <a name="failback"></a>Conmutación por recuperación

Para Update 3 y versiones posteriores, StorSimple también admite la conmutación por recuperación. La conmutación por recuperación es simplemente lo contrario que la conmutación por error, el destino se convierte en el origen y el dispositivo de origen original durante la conmutación por error ahora se convierte en el dispositivo de destino. 

Durante la conmutación por recuperación, StorSimple vuelve a sincronizar los datos a la ubicación principal, detiene la E/S y la actividad de la aplicación, y realiza la transición de nuevo a la ubicación original.

Una vez completada la conmutación por error, StorSimple realiza las siguientes acciones:

* StorSimple borra del dispositivo de origen los contenedores de volúmenes que se conmutaron por error.
* StorSimple inicia un trabajo en segundo plano por contenedor de volúmenes (conmutación por error) en el dispositivo de origen. Si trata de realizar una conmutación por recuperación mientras el trabajo está en curso, recibirá una notificación al respecto. Espere hasta que se complete el trabajo para iniciar la conmutación por recuperación.
* El tiempo que se tarda en completar la eliminación de los contenedores de volúmenes depende de varios factores, como la cantidad y la antigüedad de los datos, el número de copias de seguridad y el ancho de banda de red disponible para la operación.

Si piensa probar la conmutación por error o la conmutación por recuperación, se recomienda que pruebe contenedores de volúmenes con menos datos (GB). Por lo general, puede iniciar la conmutación por recuperación 24 horas después de que finalice la conmutación por error.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

P: **¿Qué ocurre si se produce un error de la recuperación ante desastres o se completa correctamente solo de forma parcial?**

A. Si se produce un error en la recuperación ante desastres, se recomienda volver a intentarlo. El trabajo de conmutación por error del segundo dispositivo es compatible con el progreso del primer trabajo y se inicia desde ese punto en adelante.

P: **¿Puedo eliminar un dispositivo mientras se realiza la conmutación por error del dispositivo?**

a. No se puede eliminar un dispositivo mientras se realiza una recuperación ante desastres. Solo puede eliminar el dispositivo una vez completada la recuperación ante desastres. Puede supervisar el progreso del trabajo de conmutación por error de dispositivo en la hoja **Trabajos**.

P: **¿Cuándo se inicia la recolección de elementos no utilizados en el dispositivo de origen para que se eliminen los datos locales en el dispositivo de origen?**

A. La recolección de elementos no utilizados solo se habilita en el dispositivo de origen después de que el dispositivo se limpie completamente. La limpieza incluye limpiar los objetos que se han conmutado por error desde dispositivo de origen, como volúmenes, objetos de copia de seguridad (no datos), contenedores de volúmenes y directivas.

P: **¿Qué ocurre si se produce un error en el trabajo de eliminación asociado con los contenedores de volúmenes del dispositivo de origen?**

A.  Si se produce un error en el trabajo de eliminación, puede eliminar manualmente los contenedores de volúmenes. En la hoja **Dispositivos**, seleccione el dispositivo de origen y haga clic en **Contenedores de volúmenes**. Seleccione los contenedores de volúmenes que se conmutaron por error y, en la parte inferior de la hoja, haga clic en **Eliminar**. Una vez que haya eliminado todos los contenedores de volúmenes conmutados por error en el dispositivo de origen, puede iniciar la conmutación por recuperación. Para más información, vaya a [Eliminar un contenedor de volúmenes](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperación ante desastres y continuidad empresarial (BCDR)

Un escenario de recuperación ante desastres y continuidad empresarial (BCDR) se produce cuando todo el centro de datos de Azure deja de funcionar. Este escenario puede afectar al servicio Administrador de dispositivos de StorSimple y a los dispositivos de StorSimple asociados.

Si un dispositivo de StorSimple se registró justo antes de que ocurriera un desastre, es posible que este dispositivo deba restablecerse a los valores de fábrica. Tras el desastre, el dispositivo de StorSimple se muestra en Azure Portal como sin conexión. Este dispositivo debe eliminarse desde el portal. Restablezca el dispositivo a los valores predeterminados de fábrica y vuelva a registrarlo con el servicio.

## <a name="next-steps"></a>Pasos siguientes

Si está listo para realizar una conmutación por error de dispositivo, elija uno de los escenarios siguientes para obtener instrucciones detalladas:

- [Conmutar por error a otro dispositivo físico](storsimple-8000-device-failover-physical-device.md)
- [Conmutar por error al mismo dispositivo](storsimple-8000-device-failover-same-device.md)
- [Conmutar por error a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Si ha conmutado por error el dispositivo, elija una de las opciones siguientes:

* [Desactivar o eliminar el dispositivo de StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Usar el servicio Administrador de dispositivos de StorSimple para administrar el dispositivo de StorSimple](storsimple-8000-manager-service-administration.md).

