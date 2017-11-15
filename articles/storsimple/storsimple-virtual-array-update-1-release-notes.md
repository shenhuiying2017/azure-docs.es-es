---
title: "Notas de la versión de StorSimple Virtual Array Update 1.0 | Microsoft Docs"
description: "Se describen los problemas críticos por resolver y las soluciones de StorSimple Virtual Array que ejecuta Update 1.0."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Notas de la versión de StorSimple Virtual Array Update 1.0

## <a name="overview"></a>Información general

En las notas de la versión siguientes se identifican los problemas críticos pendientes y los problemas resueltos de las actualizaciones de la matriz virtual de Microsoft Azure StorSimple.

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar la matriz virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

Update 1.0 se corresponde con la versión del software **10.0.10296.0**.

> [!IMPORTANT]
> - Las actualizaciones causan interrupciones y reinician el dispositivo. Si hay procesos de E/S en curso, el dispositivo incurrirá en tiempo de inactividad. Para obtener instrucciones detalladas sobre cómo aplicar la actualización, vaya a [Instalación de Update 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Update 1 solo está disponible en Azure Portal si el dispositivo ejecuta Update 0.6.

## <a name="whats-new-in-update-10"></a>Novedades de Update 1.0

**Update 1.0 contiene cambios relacionados con la autenticación del servicio StorSimple Device Manager y deben implementarse lo antes posible.** Esta actualización contiene las siguientes mejoras y correcciones de errores:

 - **Uso de Azure Active Directory (AAD) para autenticarse con el servicio StorSimple Device Manager**: desde Update 1.0 y versiones posteriores, Azure Active Directory se utiliza para autenticarse con el servicio StorSimple Device Manager. El mecanismo de autenticación antigua dejará de utilizarse en diciembre de 2017. Todos los usuarios deben incluir las nuevas direcciones URL de autenticación en sus reglas de firewall. Para obtener más información, consulte las direcciones URL de autenticación enumeradas en los [requisitos de red de StorSimple Virtual Array](storsimple-ova-system-requirements.md).
 
    Si la dirección URL de autenticación no se incluye en las reglas de firewall, los usuarios verán una alerta crítica sobre que su dispositivo de StorSimple no se pudo autenticar con el servicio. Si los usuarios ven esta alerta, deben incluir la nueva dirección URL de autenticación. Para más información, vaya a [Alertas de red de StorSimple](storsimple-virtual-array-manage-alerts.md).

 - **Mejora del rendimiento**: se realizaron varias correcciones de errores para aumentar la velocidad de las lecturas en la nube, la reducción horizontal de nivel y la escalabilidad horizontal de nivel. Como resultado, se mejora el rendimiento de copia de seguridad y restauración de dispositivos de servidor de archivos y de iSCSI.

 - **Mejora de la recolección de elementos no utilizados**: esta versión tiene correcciones de errores que mejoran el rendimiento del ciclo de recolección de elementos no utilizados cuando la cuenta de almacenamiento y el dispositivo se encuentran en dos regiones lejanas.

 - **Mejora de registro**: esta versión contiene mejoras de registro relacionadas con la recolección de elementos no utilizados y la ruta de acceso de E/S.


## <a name="issues-fixed-in-update-10"></a>Problemas corregidos en Update 1.0

En la tabla siguiente se proporciona un resumen de los problemas corregidos en esta versión.

| No. | Característica | Problema |
| --- | --- | --- |
| 1 |Autenticación basada en AAD| Esta versión contiene cambios que permiten la autenticación de AAD con StorSimple Device Manager.|
| 2 |Recolección de elementos no utilizados| Este problema se notificó en un sitio de cliente donde las cuentas de dispositivo y de almacenamiento se encuentran en regiones distintas, y el cliente informó de errores de red intermitentes que repercuten en la facturación. Este problema se ha corregido en esta versión. |
| 3 |Rendimiento| Esta versión contiene cambios que derivan en una mejora del rendimiento de la reducción horizontal de nivel, la escalabilidad horizontal de nivel, las lecturas en la nube y la restauración.|
| 4 |Actualizar| Hubo un problema con la actualización en la versión anterior que provocó errores de copia de seguridad el sitio de un cliente. Este problema está corregido en esta versión.|

## <a name="known-issues-in-update-10"></a>Problemas conocidos de Update 1.0

En la tabla siguiente se muestra un resumen de los problemas conocidos de la matriz virtual de StorSimple y se incluyen los problemas notificados en la versiones anteriores.

| No. | Característica | Problema | Soluciones alternativas o comentarios |
| --- | --- | --- | --- |
| **1.** |Actualizaciones |Las matrices virtuales que se crean en la versión preliminar no se pueden actualizar a una versión que sea compatible con la versión de disponibilidad general. |Debe conmutar por error estas matrices virtuales a la versión de disponibilidad general mediante un flujo de trabajo de recuperación ante desastres (DR). |
| **2.** |Disco de datos aprovisionado |Una vez se haya aprovisionado un disco de datos de un determinado tamaño y cree la correspondiente instancia de StorSimple Virtual Array, no debe expandir o reducir el disco de datos. Si intenta hacer esto, se perderán todos los datos de las capas locales del dispositivo. | |
| **3.** |Directiva de grupo |Cuando un dispositivo está unido al dominio, aplicar una directiva de grupo puede afectar negativamente al funcionamiento de dispositivo. |Asegúrese de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO). |
| **4.** |Interfaz de usuario web local. |Si tiene las características de seguridad mejorada habilitadas en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario web local, tales como Solución de problemas o Mantenimiento, no funcionen correctamente. Asimismo, cabe la posibilidad de que los botones de estas páginas tampoco funcionen. |Desactive las características de seguridad mejorada de Internet Explorer. |
| **5.** |Interfaz de usuario web local. |En una máquina virtual de Hyper-V, las interfaces de red que se encuentran en la interfaz de usuario web se muestran como interfaces de 10 Gbps. |Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra los adaptadores de red virtual a 10 Gbps. |
| **6.** |Volúmenes o recursos compartidos en niveles |El bloqueo del intervalo de bytes de las aplicaciones que funcionan con volúmenes de StorSimple no se admite. Si tiene habilitado un bloqueo de intervalo de bytes, la organización en niveles de StorSimple no funciona. |Entre las medidas recomendadas se incluyen:  <br></br>Desactive el bloqueo del intervalo de bytes en la lógica de la aplicación.<br></br>Elija esta opción colocar los datos de esta aplicación en los volúmenes anclados localmente en lugar en volúmenes en capas.<br></br>*Advertencia*: Si usa volúmenes anclados localmente y tiene el bloqueo del intervalo de bytes habilitado, el volumen anclado localmente puede estar en línea incluso antes de que se complete la restauración. En tal caso, si hay una restauración en curso, debe esperar a que esta se complete. |
| **7.** |Recursos compartidos organizados en niveles |Si trabaja con archivos de gran tamaño, estos podrían ocasionar que la organización en niveles se desarrolle lentamente. |Cuando trabaje con esta clase de archivos, es recomendable que el archivo de mayor tamaño no ocupe más del 3 % del tamaño recurso compartido. |
| **8.** |Capacidad de recursos compartidos usada |Si no hay datos en el recurso compartido, es posible que vea cierto consumo del recurso compartido. Este consumo se produce porque la capacidad que se usa para los recursos compartidos incluye los metadatos. | |
| **9.** |Recuperación ante desastres |Solo puede realizar la recuperación ante desastres de un servidor de archivos en el mismo dominio que el del dispositivo de origen. Con esta versión no se puede realizar la recuperación ante desastres en el dispositivo de destino de otro dominio. |Esto se implementará en una versión posterior. Para obtener información sobre cómo realizar la conmutación por error, vaya a [Recuperación ante desastres y conmutación por error en StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md). |
| **10.** |Azure PowerShell |En esta versión no se pueden administrar las instancias de StorSimple Virtual Array a través de Azure PowerShell. |Toda la administración de los dispositivos virtuales debe realizarse mediante Azure Portal y la interfaz de usuario web local. |
| **11.** |Cambio de contraseña |La consola del dispositivo de matriz virtual solo acepta entradas con el formato de teclado en-us. | |
| **12.** |CHAP |Las credenciales CHAP no se pueden quitar una vez creadas. Además, si modifica las credenciales CHAP, deberá desconectar los volúmenes y volver a ponerlos en línea para que el cambio surta efecto. |Este problema se corregirá en una versión posterior. |
| **13.** |Servidor iSCSI |El "almacenamiento usado" que aparece para un volumen iSCSI puede ser diferente en el servicio del Administrador de dispositivos de StorSimple y en el host iSCSI. |El host iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo. |
| **14.** |Servidor de archivos |Si un archivo de una carpeta tiene una secuencia de datos alternativa (ADS) asociada, no se hará una copia de seguridad de dicha secuencia ni se restaurará a través de la recuperación ante desastres, la clonación o la recuperación a nivel de elemento. | |
| **15.** |Servidor de archivos |No se admiten los vínculos simbólicos. | |
| **16.** |Servidor de archivos |Los archivos protegidos por el Sistema de cifrado de archivos (EFS) de Windows al copiarse o almacenarse en el servidor de archivos de la matriz virtual de StorSimple desembocarán en una configuración no admitida.  | |
| **17.** |Actualizaciones |Si ve el Código de error: 2359302 (hex 0x240006) al intentar instalar una revisión mediante la interfaz de usuario local, esto significa que la revisión ya está instalada en el dispositivo.   | |
| **18.** |Actualizaciones |Si usa la interfaz de usuario web local para instalar Update 1 en la matriz virtual, debe asegurarse de que se ejecute Update 0.6. Si ejecuta una versión anterior a Update 0.6, debe instalar Update 0.6 para poder aplicar Update 1. Si instala directamente Update 1.0 a partir de una versión anterior a Update 0.6, perderá algunas actualizaciones y los gráficos de supervisión no funcionarán.   | |


## <a name="next-steps"></a>Pasos siguientes
[Instale Update 1.0](storsimple-virtual-array-install-update-1.md) en StorSimple Virtual Array.

## <a name="references"></a>Referencias
¿Busca una nota de la versión anterior? Vaya a:
*  [Notas de la versión de StorSimple Virtual Array Update 0.6](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de la versión de StorSimple Virtual Array Update 0.5](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de la versión de StorSimple Virtual Array Update 0.4](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de la versión de StorSimple Virtual Array Update 0.3](storsimple-ova-update-03-release-notes.md)
* [Notas de la versión de la matriz virtual de StorSimple Update 0.1 y 0.2](storsimple-ova-update-01-release-notes.md)
* [Notas de la versión de disponibilidad general de la matriz Virtual de StorSimple](storsimple-ova-pp-release-notes.md)
