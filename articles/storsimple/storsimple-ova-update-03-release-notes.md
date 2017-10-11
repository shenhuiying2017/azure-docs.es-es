---
title: "Notas de la versión de las actualizaciones de la matriz virtual de StorSimple | Microsoft Docs"
description: "Describe los problemas críticos por resolver y las soluciones de la matriz virtual de StorSimple que ejecuta la actualización 0.3."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notas de la versión de Update 0.3 de la matriz virtual de StorSimple
## <a name="overview"></a>Información general
En las notas de la versión siguientes se identifican los problemas críticos pendientes y los problemas resueltos de las actualizaciones de la matriz virtual de Microsoft Azure StorSimple.

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar la matriz virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

Update 0.3 se corresponde con la versión de software **10.0.10288.0**.

> [!NOTE]
> Las actualizaciones causan interrupciones y reinician el dispositivo. Si hay procesos de E/S en curso, el dispositivo incurrirá en tiempo de inactividad.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Novedades de Update 0.3
Update 0.3 es principalmente una compilación de corrección de errores. En esta versión, se han solucionado varios errores que provocan errores de copia de seguridad en la versión anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas corregidos en Update 0.3
En la tabla siguiente se proporciona un resumen de los problemas corregidos en esta versión.

| No. | Característica | Problema |
| --- | --- | --- |
| 1 |Copias de seguridad |Se ha detectado un problema en la versión anterior en el que las copias de seguridad no podían completarse. Si se produjo este problema, el trabajo de copia de seguridad no se realizaría correctamente y se generó una alerta crítica en el servicio StorSimple Manager para notificar al usuario. Este problema no afectó a los datos en los recursos compartidos o el acceso a los datos. La causa raíz se identificó y corrigió en esta versión. <br></br> La corrección no se aplica con carácter retroactivo a recursos compartidos que ya están experimentando este problema. Los clientes que detecten este problema deben aplicar primero Update 0.3 y después póngase en contacto con el soporte técnico de Microsoft para realizar una copia de seguridad completa del sistema para corregir el problema. En lugar de ponerse en contacto con el soporte técnico de Microsoft, los clientes también pueden restaurar a un nuevo recurso compartido desde una copia de seguridad correcta para los recursos compartidos afectados. |
| 2 |iSCSI |Se ha detectado un problema en la versión anterior, donde los volúmenes desaparecían cuando se copiaban datos en un volumen en la matriz virtual de StorSimple. Este problema se ha corregido en esta versión. <br></br> Las revisiones surten efecto solo en volúmenes creados recientemente. Las revisiones no se aplican con carácter retroactivo a volúmenes que ya están experimentando este problema. Se recomienda a los clientes poner los volúmenes afectados en línea mediante el Portal de Azure clásico, realizar una copia de seguridad de estos volúmenes y luego restaurar estos volúmenes a nuevos volúmenes. |

## <a name="known-issues-in-the-update-03"></a>Problemas conocidos de Update 0.3
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

## <a name="next-step"></a>Paso siguiente
[Instale la actualización 0.3](storsimple-ova-install-update-01.md) en la matriz virtual de StorSimple.

## <a name="references"></a>Referencias
¿Busca una nota de la versión anterior? Vaya a: 

* [Notas de la versión de la matriz virtual de StorSimple Update 0.1 y 0.2](storsimple-ova-update-01-release-notes.md)
* [Notas de la versión de disponibilidad general de la matriz Virtual de StorSimple](storsimple-ova-pp-release-notes.md)

