---
title: "Notas de la versión de StorSimple Virtual Array Update 0.4 | Microsoft Docs"
description: "Describe los problemas críticos por resolver y las soluciones de StorSimple Virtual Array que ejecuta Update 0.4."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Notas de la versión de Update 0.4 de la matriz virtual de StorSimple

## <a name="overview"></a>Información general

En las notas de la versión siguientes se identifican los problemas críticos pendientes y los problemas resueltos de las actualizaciones de la matriz virtual de Microsoft Azure StorSimple.

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar la matriz virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

Update 0.4 se corresponde con la versión del software **10.0.10289.0**.

> [!NOTE]
> Las actualizaciones causan interrupciones y reinician el dispositivo. Si hay procesos de E/S en curso, el dispositivo incurrirá en tiempo de inactividad.


## <a name="whats-new-in-the-update-04"></a>Novedades de Update 0.4
Update 0.4 es principalmente una compilación de corrección de errores, junto con algunas mejoras. En esta versión, se han solucionado varios errores que provocan errores de copia de seguridad en la versión anterior. Estas son las principales mejoras y correcciones de errores:

- **Mejoras en el rendimiento de copia de seguridad**: en esta versión se han realizado varias mejoras esenciales destinadas a mejorar el rendimiento de copia de seguridad. Como consecuencia, las copias de seguridad que implican a un gran número de archivos tardan mucho menos en completarse, tanto las completas como las incrementales.

- **Mejora en el rendimiento de la restauración**: esta versión contiene importantes mejoras en el rendimiento de la restauración cuando se usan muchos archivos. Si se utilizan entre 2 y 4 millones de archivos, es aconsejable aprovisionar una matriz virtual con 16 GB de RAM para ver las mejoras. Cuando se usan menos de 2 millones de archivos, el requisito mínimo para la máquina virtual siguen siendo 8 GB de RAM.

- **Mejoras en el paquete de soporte**: las mejoras incluyen el registro de las estadísticas del disco, la CPU, la memoria, la red y la nube en el paquete de soporte, lo que mejora el proceso de diagnóstico y depuración de los problemas de los dispositivos.

- **Limitación de los volúmenes iSCSI anclados localmente a 200 GB**: en el caso de los volúmenes anclados localmente, es aconsejable limitar a un volumen iSCSI de 200 GB de StorSimple Virtual Array. La reserva local para volúmenes en capas sigue siendo el 10 % del tamaño del volumen aprovisionado, pero está limitada a 200 GB. 

- **Correcciones de errores relacionados con la copia de seguridad**: en las versiones anteriores del software, aparecieron problemas relacionados con las copias de seguridad que podían provocar errores. En esta versión se han solucionado estos errores.


## <a name="issues-fixed-in-the-update-04"></a>Problemas corregidos en la actualización 0.4

En la tabla siguiente se proporciona un resumen de los problemas corregidos en esta versión.

| No. | Característica | Problema |
| --- | --- | --- |
| 1 |Rendimiento de copia de seguridad|En las versiones anteriores, las copias de seguridad que implicaban a gran número de archivos podían tardar mucho tiempo en completarse (en el orden de días). Sin embargo, en esta versión, el tiempo que tardan en completarse tanto las copias de seguridad completas como las incrementales se reduce ostensiblemente. |
| 2 |Paquete de soporte|Las estadísticas de disco, CPU, memoria, red y nube ahora se incluyen en los registros de soporte técnico, lo que hace que los paquetes de soporte sean muy eficaces para solucionar cualquier problema del dispositivo.|
| 3 |Copia de seguridad |En las versiones anteriores, las copias de seguridad que tardaban mucho tiempo en completarse podían generar un problema de espacio en el dispositivo, lo que provocaría errores de copia de seguridad. Este error se soluciona en esta versión, ya que no se permite poner en cola más de cinco copias de seguridad al mismo tiempo.|
| 4 |iSCSI | En las versiones anteriores, la reserva local de los volúmenes anclados localmente o en niveles era el 10 % del tamaño del volumen aprovisionado. En esta versión, la reserva local de todos los volúmenes iSCSI (anclados localmente o en niveles) está limitada a un 10 %, con un máximo de 200 GB (para volúmenes en niveles de más de 2 TB), con lo que se libera más espacio en el disco local. En esta versión, se recomienda que los volúmenes anclados localmente tengan la limitación de 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Problemas conocidos de la actualización 0.4

En la tabla siguiente se muestra un resumen de los problemas conocidos de la matriz virtual de StorSimple y se incluyen los problemas notificados en la versiones anteriores. 

| No. | Característica | Problema | Soluciones alternativas o comentarios |
| --- | --- | --- | --- |
| **1.** |Actualizaciones |Los dispositivos virtuales que se crean en la versión preliminar no se pueden actualizar a una versión que sea compatible con la versión de disponibilidad general. |Debe conmutar por error estos dispositivos virtuales a la versión de disponibilidad general mediante un flujo de trabajo de recuperación ante desastres (DR). |
| **2.** |Disco de datos aprovisionado |Una vez se haya aprovisionado un disco de datos de un determinado tamaño y cree el correspondiente dispositivo virtual de StorSimple, no debe expandir o reducir el disco de datos. Si intenta hacer esto, se perderán todos los datos de las capas locales del dispositivo. | |
| **3.** |Directiva de grupo |Cuando un dispositivo está unido al dominio, aplicar una directiva de grupo puede afectar negativamente al funcionamiento de dispositivo. |Asegúrese de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO). |
| **4.** |Interfaz de usuario web local. |Si tiene las características de seguridad mejorada habilitadas en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario web local, tales como Solución de problemas o Mantenimiento, no funcionen correctamente. Asimismo, cabe la posibilidad de que los botones de estas páginas tampoco funcionen. |Desactive las características de seguridad mejorada de Internet Explorer. |
| **5.** |Interfaz de usuario web local. |En una máquina virtual de Hyper-V, las interfaces de red que se encuentran en la interfaz de usuario web se muestran como interfaces de 10 Gbps. |Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra los adaptadores de red virtual a 10 Gbps. |
| **6.** |Volúmenes o recursos compartidos en niveles |El bloqueo del intervalo de bytes de las aplicaciones que funcionan con volúmenes de StorSimple no se admite. Si tiene habilitado un bloqueo de intervalo de bytes, la organización en niveles de StorSimple no funciona. |Entre las medidas recomendadas se incluyen:  <br></br>Desactive el bloqueo del intervalo de bytes en la lógica de la aplicación.<br></br>Elija esta opción colocar los datos de esta aplicación en los volúmenes anclados localmente en lugar en volúmenes en capas.<br></br>*Advertencia*: Si usa volúmenes anclados localmente y tiene el bloqueo del intervalo de bytes habilitado, el volumen anclado localmente puede estar en línea incluso antes de que se complete la restauración. En tal caso, si hay una restauración en curso, debe esperar a que esta se complete. |
| **7.** |Recursos compartidos organizados en niveles |Si trabaja con archivos de gran tamaño, estos podrían ocasionar que la organización en niveles se desarrolle lentamente. |Cuando trabaje con esta clase de archivos, es recomendable que el archivo de mayor tamaño no ocupe más del 3 % del tamaño recurso compartido. |
| **8.** |Capacidad de recursos compartidos usada |Si no hay datos en el recurso compartido, es posible que vea cierto consumo del recurso compartido. Esto ocurre porque la capacidad que se usa para los recursos compartidos incluye los metadatos. | |
| **9.** |Recuperación ante desastres |Solo puede realizar la recuperación ante desastres de un servidor de archivos en el mismo dominio que el del dispositivo de origen. Con esta versión no se puede realizar la recuperación ante desastres en el dispositivo de destino de otro dominio. |Esto se implementará en una versión posterior. |
| **10.** |Azure PowerShell |En esta versión no se pueden administrar los dispositivos virtuales de StorSimple a través de Azure PowerShell. |Toda la administración de los dispositivos virtuales debe realizarse mediante el portal de Azure clásico y la interfaz de usuario web local. |
| **11.** |Cambio de contraseña |La consola del dispositivo de matriz virtual solo acepta entradas con el formato de teclado en-US. | |
| **12.** |CHAP |Las credenciales CHAP no se pueden quitar una vez creadas. Además, si modifica las credenciales CHAP, deberá desconectar los volúmenes y volver a ponerlos en línea para que el cambio surta efecto. |Este problema se corregirá en una versión posterior. |
| **13.** |Servidor iSCSI |El "almacenamiento usado" que aparece para un volumen iSCSI puede ser diferente en el servicio StorSimple Manager y en el host iSCSI. |El host iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo. |
| **14.** |Servidor de archivos |Si un archivo de una carpeta tiene una secuencia de datos alternativa (ADS) asociada, no se hará una copia de seguridad de dicha secuencia ni se restaurará a través de la recuperación ante desastres, la clonación o la recuperación a nivel de elemento. | |
| **15.** |Servidor de archivos |No se admiten los vínculos simbólicos. | |
| **16.** |Servidor de archivos |Los archivos protegidos por el Sistema de cifrado de archivos (EFS) de Windows al copiarse o almacenarse en el servidor de archivos de la matriz virtual de StorSimple desembocarán en una configuración no admitida.  | |

## <a name="next-step"></a>Paso siguiente
[Instale Update 0.4](storsimple-virtual-array-install-update-04.md) en StorSimple Virtual Array.

## <a name="references"></a>Referencias
¿Busca una nota de la versión anterior? Vaya a: 

* [Notas de la versión de StorSimple Virtual Array Update 0.3](storsimple-ova-update-03-release-notes.md)
* [Notas de la versión de la matriz virtual de StorSimple Update 0.1 y 0.2](storsimple-ova-update-01-release-notes.md)
* [Notas de la versión de disponibilidad general de la matriz Virtual de StorSimple](storsimple-ova-pp-release-notes.md)

