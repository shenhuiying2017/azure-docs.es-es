---
title: "Notas de la versión de la serie StorSimple 8000 Update 4 | Microsoft Docs"
description: "Describe las nuevas características, problemas y soluciones alternativas de la serie StorSimple 8000 Update 4."
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
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 23f1bbb066c5b6481988ee841ad8979d78abf084
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Notas de la versión de la serie StorSimple 8000 Update 4

## <a name="overview"></a>Información general

Las siguientes notas de la versión describen las características nuevas e identifican los problemas críticos abiertos de la serie StorSimple 8000 Update 4. También contienen una lista de las actualizaciones de software de StorSimple incluidas en esta versión. 

Update 4 puede aplicarse a cualquier dispositivo de StorSimple que ejecute software de versión (GA) o las actualizaciones de la 0.1 a la 3.1. La versión de dispositivo asociada a Update 4 es 6.3.9600.17820.

Revise la información contenida en las notas de la versión antes de implementar la actualización de la solución de StorSimple.

> [!IMPORTANT]
> * Update 4 tiene actualizaciones de software de dispositivo, firmware de USM, firmware y controlador LSI, firmware de disco, Storport y Spaceport, seguridad y otras actualizaciones de SO. Para instalar esta actualización, se necesitan en torno a unas cuatro horas. La actualización del firmware de disco es una actualización perjudicial y provoca un tiempo de inactividad en el dispositivo. Se recomienda aplicar la actualización 4 para mantener actualizado el dispositivo. 
> * Para las nuevas versiones, no podrán ver las actualizaciones de inmediato porque hacemos una implementación por fases de las actualizaciones. Espere unos días y luego busque actualizaciones de nuevo ya que estas estarán disponibles pronto.

## <a name="whats-new-in-update-4"></a>Novedades de la actualización 4

Se realizaron las siguientes mejoras clave y correcciones de errores en Update 4.

* **Algoritmos de recuperación de espacio automatizados más inteligentes**. En Update 4, los algoritmos de recuperación de espacio automatizados se mejoraron para ajustarlos a los ciclos de recuperación de espacio en virtud del espacio que se espera recuperar disponible en la nube. 

* **Mejoras de rendimiento en volúmenes anclados localmente**. En Update 4 se mejoró el rendimiento de los volúmenes anclados localmente en escenarios con una alta ingesta de datos (los datos comparados con el tamaño del volumen).

* **Restauración basada en mapa térmico**. En las versiones anteriores, después de una recuperación ante desastres, los datos se restauraban desde la nube en función de los patrones de acceso, lo que hacía que el rendimiento fuese lento. 

    En Update 4, se implementa una característica nueva que hace seguimiento de los datos a los que se obtiene acceso con menos frecuencia con el fin de crear un mapa término cuando el dispositivo está en uso antes de la recuperación ante desastres (los fragmentos de datos más usados muestran mayor calor, mientras que los fragmentos menos usados se ven más fríos). Después de la recuperación ante desastres, StorSimple usa el mapa térmico para restaurar y rehidratar los datos desde la nube. 

    Todas las restauraciones ahora son restauraciones basadas en mapas térmicos. Para más información sobre cómo consultar y cancelar los trabajos de restauración y rehidratación basados en mapas térmicos, vaya a [Referencia de cmdlets de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **Herramienta de diagnóstico de StorSimple**. En Update 4, se lanzó una herramienta de diagnóstico de StorSimple para diagnosticar y solucionar rápidamente problemas relacionados con el sistema, la red, el rendimiento y el estado de los componentes de hardware. Esta herramienta se ejecuta a través de Windows PowerShell para StorSimple. Para obtener más información, lea el artículo sobre cómo [solucionar problemas con la herramienta de diagnóstico de StorSimple](storsimple-8000-diagnostics.md).

* **Herramienta de migración de StorSimple basada en IU**. Antes de esta versión, la migración de datos de las series 5000 a 7000 necesitaba que los usuarios ejecutarán una parte del flujo de trabajo de migración a través de la interfaz de Azure PowerShell. En esta versión, hay disponible una herramienta de migración de StorSimple basada en IU fácil de usar para soporte técnico a fin de facilitar el mismo flujo de trabajo de migración. Esta herramienta también permitiría la consolidación de los depósitos de recuperación. 

* **Cambios relacionados con FIPS**. Desde esta versión, FIPS está habilitado de manera predeterminada en todos los dispositivos de la serie StorSimple 8000 tanto para cuentas de Microsoft Azure Government como para cuentas de la nube pública de Azure.

* **Actualización de cambios**: en esta versión, se han corregido los errores relacionados con la actualización.

* **Alerta de errores en el disco**. En esta versión, se agregó una alerta nueva que advierte al usuario cuando existen errores latentes en el disco. Si se produce esta alerta, póngase en contacto con el soporte técnico de Microsoft para enviar un disco de sustitución. Para obtener más información, lea el tema sobre [alertas de hardware del dispositivo StorSimple](storsimple-manage-alerts.md#hardware-alerts).

* **Cambios en el reemplazo del controlador**. En esta versión, se agregó un cmdlet que permite que el usuario consulte el estado del proceso de reemplazo del controlador. Para más información, vaya al [cmdlet para consultar el estado del reemplazo del controlador](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemas corregidos en Update 4

En las tablas siguientes se proporciona un resumen de los problemas corregidos en Update 4.    

| No | Característica | Problema | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Conmutación por error |En la versión anterior, después de la conmutación por error, existía un error relacionado con la limpieza que se observaba en el sitio del cliente. Este problema está corregido en esta versión. |Sí |Sí |
| 2 |Volúmenes anclados localmente |En la versión anterior, se producía un error relacionado con la creación de los volúmenes anclados localmente que podía generar errores al crear los volúmenes. La causa raíz de este problema se corrigió en esta versión. |Sí |No |
| 3 |Paquete de soporte |En la versión anterior, existían problemas relacionados con el paquete de soporte que podían generar una excepción System.OutOfMemory u otros errores que podían impedir la creación del paquete de soporte. Estos errores se corrigieron en esta versión. |Sí |Sí |
| 4 |Supervisión |En la versión anterior, existía un problema relacionado con la supervisión de gráficos para los volúmenes anclados localmente, donde el consumo se mostraba en EB. Este error se solucionó en esta versión. |Sí |Sí |
| 5 |Migración |En la versión anterior, había varios problemas relacionados con la confiabilidad de la migración desde dispositivos de las series 5000 a 7000 a los dispositivos de la serie 8000. Estos problemas se solucionaron en esta versión. |Sí |Sí |
| 6 |Actualizar |En versiones anteriores, si se produjo un error de actualización, los controladores entraban en modo de recuperación y, por tanto, el usuario no podía continuar con la actualización y tenía que ponerse en contacto con el Servicio de soporte técnico de Microsoft. <br> Este comportamiento se cambió en esta versión. Si el usuario sufre un error de actualización una vez que ambos controladores ejecutan la misma versión (actualización 4), los controladores no entran en modo de recuperación. Si el usuario encuentra este error, se recomienda que espere un poco y, a continuación, vuelva a intentar la actualización. Este reintento podría ser correcto. Si el reintento es incorrecto, debe ponerse en contacto con el Servicio de soporte técnico de Microsoft. |Sí |Sí |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemas conocidos en Update 4 de las versiones anteriores

No hay ningún problema conocido en Update 4. Si desea ver una lista de los problemas existentes en Update 4 que provienen de versiones anteriores, consulte las [notas de la versión de Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Controlador SCSI conectado en serie (SAS) y actualizaciones de firmware en Update 4

Esta versión tiene actualizaciones de firmware y controlador LSI y controlador SAS. Para más información sobre cómo instalar estas actualizaciones, consulte cómo [instalar Update 4](storsimple-install-update-4.md) en el dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Actualizaciones de dispositivos virtuales en Update 4

Esta actualización no se puede aplicar a StorSimple Cloud Appliance (también conocido como el dispositivo virtual). Deben crearse nuevos dispositivos virtuales. 

## <a name="next-step"></a>Paso siguiente

Obtenga información sobre cómo [instalar Update 4](storsimple-install-update-4.md) en el dispositivo StorSimple.

