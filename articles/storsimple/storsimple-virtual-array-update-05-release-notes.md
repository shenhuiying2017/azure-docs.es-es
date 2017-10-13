---
title: "Notas de la versión de StorSimple Virtual Array Update 0.5 | Microsoft Docs"
description: "Describe los problemas críticos por resolver y las soluciones de StorSimple Virtual Array que ejecuta Update 0.5."
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 4d020ff2b998da4cb52fe91e4d7d4b93544965a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Notas de la versión de StorSimple Virtual Array Update 0.5

## <a name="overview"></a>Información general

En las notas de la versión siguientes se identifican los problemas críticos pendientes y los problemas resueltos de las actualizaciones de la matriz virtual de Microsoft Azure StorSimple.

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar la matriz virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

Update 0.5 se corresponde con la versión del software **10.0.10290.0**.

> [!NOTE]
> Las actualizaciones causan interrupciones y reinician el dispositivo. Si hay procesos de E/S en curso, el dispositivo incurrirá en tiempo de inactividad. Para obtener instrucciones detalladas sobre cómo aplicar la actualización, vaya a [Instalación de Update 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Novedades de Update 0.5
Update 0.5 es principalmente una compilación de corrección de errores. Estas son las principales mejoras y correcciones de errores:

- **Mejoras en la resistencia de copia de seguridad**: esta versión tiene correcciones que mejoran la resistencia de copia de seguridad. En las versiones anteriores, las copias de seguridad se reintentaban solo para ciertas excepciones. Esta versión reintenta todas las excepciones de copia de seguridad y hace que las copias de seguridad sean más resistentes.

- **Actualizaciones de la supervisión de uso de almacenamiento**: a partir del 30 de junio de 2017, se retirará la supervisión de uso de almacenamiento de la serie de dispositivos virtuales de StorSimple. Esto se aplica a los gráficos de supervisión en todas las matrices virtuales que ejecutan Update 0.4 o versiones anteriores. Esta actualización contiene los cambios necesarios para que pueda continuar usando la supervisión de uso de almacenamiento en Azure Portal. Instale esta actualización crítica antes del 30 de junio de 2017 para seguir usando la característica de supervisión.


## <a name="issues-fixed-in-the-update-05"></a>Problemas corregidos en Update 0.5

En la tabla siguiente se proporciona un resumen de los problemas corregidos en esta versión.

| No. | Característica | Problema |
| --- | --- | --- |
| 1 |Resistencia de copia de seguridad| En las versiones anteriores, las copias de seguridad se reintentaban solo para ciertas excepciones. Esta versión contiene una corrección para que las copias de seguridad tengan más resistencia gracias al reintento de todas las excepciones de copia de seguridad.|
| 2 |Supervisión| La supervisión de uso de almacenamiento de la serie de dispositivos virtuales de StorSimple quedará en desuso a partir del 30 de junio de 2017. Esta acción afecta a los gráficos de supervisión del servicio del Administrador de dispositivos de StorSimple que se ejecuta en StorSimple Virtual Array (modelo 1200). Esta versión contiene actualizaciones que permiten al usuario continuar usando la supervisión de uso de almacenamiento en las matrices virtuales más allá del 30 de junio de 2017.|
| 3 |Servidor de archivos| En las versiones anteriores, un usuario podía copiar por error archivos cifrados en la matriz virtual. Esta versión contiene una corrección que no permitiría la operación de copia de archivos cifrados en la matriz virtual. Si el dispositivo tiene archivos cifrados existentes antes de la actualización, las copias de seguridad continuarán generando un error hasta que se eliminen todos los archivos cifrados del sistema. |


## <a name="known-issues-in-the-update-05"></a>Problemas conocidos de Update 0.5

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
| **8.** |Capacidad de recursos compartidos usada |Si no hay datos en el recurso compartido, es posible que vea cierto consumo del recurso compartido. Este consumo se produce porque la capacidad que se usa para los recursos compartidos incluye los metadatos. | |
| **9.** |Recuperación ante desastres |Solo puede realizar la recuperación ante desastres de un servidor de archivos en el mismo dominio que el del dispositivo de origen. Con esta versión no se puede realizar la recuperación ante desastres en el dispositivo de destino de otro dominio. |Esto se implementará en una versión posterior. Para obtener información sobre cómo realizar la conmutación por error, vaya a [Recuperación ante desastres y conmutación por error en StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md). |
| **10.** |Azure PowerShell |En esta versión no se pueden administrar los dispositivos virtuales de StorSimple a través de Azure PowerShell. |Toda la administración de los dispositivos virtuales debe realizarse mediante Azure Portal y la interfaz de usuario web local. |
| **11.** |Cambio de contraseña |La consola del dispositivo de matriz virtual solo acepta entradas con el formato de teclado en-us. | |
| **12.** |CHAP |Las credenciales CHAP no se pueden quitar una vez creadas. Además, si modifica las credenciales CHAP, deberá desconectar los volúmenes y volver a ponerlos en línea para que el cambio surta efecto. |Este problema se corregirá en una versión posterior. |
| **13.** |Servidor iSCSI |El "almacenamiento usado" que aparece para un volumen iSCSI puede ser diferente en el servicio del Administrador de dispositivos de StorSimple y en el host iSCSI. |El host iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo. |
| **14.** |Servidor de archivos |Si un archivo de una carpeta tiene una secuencia de datos alternativa (ADS) asociada, no se hará una copia de seguridad de dicha secuencia ni se restaurará a través de la recuperación ante desastres, la clonación o la recuperación a nivel de elemento. | |
| **15.** |Servidor de archivos |No se admiten los vínculos simbólicos. | |
| **16.** |Servidor de archivos |Los archivos protegidos por el Sistema de cifrado de archivos (EFS) de Windows al copiarse o almacenarse en el servidor de archivos de la matriz virtual de StorSimple desembocarán en una configuración no admitida.  | |

## <a name="next-step"></a>Paso siguiente
[Instalación de Update 0.5](storsimple-virtual-array-install-update-05.md) en StorSimple Virtual Array

## <a name="references"></a>Referencias
¿Busca una nota de la versión anterior? Vaya a:

* [Notas de la versión de StorSimple Virtual Array Update 0.4](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de la versión de StorSimple Virtual Array Update 0.3](storsimple-ova-update-03-release-notes.md)
* [Notas de la versión de la matriz virtual de StorSimple Update 0.1 y 0.2](storsimple-ova-update-01-release-notes.md)
* [Notas de la versión de disponibilidad general de la matriz Virtual de StorSimple](storsimple-ova-pp-release-notes.md)

