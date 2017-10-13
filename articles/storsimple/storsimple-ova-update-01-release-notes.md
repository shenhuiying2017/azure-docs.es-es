---
title: "Notas de la versión de las actualizaciones de la matriz virtual de StorSimple | Microsoft Docs"
description: "Se describen problemas críticos pendientes y soluciones para la matriz virtual de StorSimple que ejecuta Update 0.2 y Update 0.1."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de la versión de la matriz virtual de StorSimple Update 0.2 y 0.1
## <a name="overview"></a>Información general
En las notas de la versión siguientes se identifican los problemas críticos pendientes y los problemas resueltos de las actualizaciones de la matriz virtual de Microsoft Azure StorSimple. (La matriz virtual de Microsoft Azure StorSimple también se conoce como dispositivo virtual StorSimple local o dispositivo virtual StorSimple). 

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar el dispositivo virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

Update 0.2 se corresponde a la versión de software **10.0.10280.0** y Update 0.1 es la versión **10.0.10279.0**. Las secciones siguientes muestran los cambios en cada actualización. 

> [!NOTE]
> Las actualizaciones causan interrupciones y reiniciarán el dispositivo. Si hay procesos de E/S en curso, el dispositivo incurrirá en tiempo de inactividad.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemas corregidos en Update 0.2
Update 0.2 incluye todos los cambios de Update 0.1 y, además, la revisión que se describe en la tabla siguiente:

| Característica | Problema |
| --- | --- |
| Actualizaciones |En la última versión, las actualizaciones se detectaban automáticamente en el Portal de Azure clásico, así que tenía que usar la interfaz de usuario web local para instalarlas. Este problema está corregido en esta versión. Después de instalar Update 0.2, puede instalar las actualizaciones futuras mediante el Portal de Azure clásico. |

## <a name="whats-new-in-the-update-01"></a>Novedades de la actualización 0.1
La actualización 0.1 contiene las siguientes correcciones de errores y mejoras. 

* **Resistencia mejorada a las interrupciones de la nube**: esta versión contiene varias correcciones de errores en torno a la recuperación ante desastres, la copia de seguridad, la restauración y la organización en niveles en caso de que se produzca una interrupción de la conectividad de la nube. 
* **Mejor rendimiento de la restauración**: esta versión contiene correcciones de errores que han reducido considerablemente el tiempo de finalización de los trabajos de restauración.
* **Optimización de la recuperación de espacio automatizada:**cuando se eliminan datos en volúmenes con aprovisionamiento fino, se deben recuperar los bloques de almacenamiento no utilizados. Con esta versión, se ha mejorado el proceso de recuperación de espacio de la nube, con lo que el espacio no utilizado que se encontrará disponible antes que en versiones anteriores.
* **Nuevas imágenes de disco virtual**: nuevos VHD, VHDX y VMDK disponibles mediante el Portal de Azure clásico. Puede descargar estas imágenes para aprovisionar nuevos dispositivos con la actualización 0.1.
* **Mejora de la precisión del estado de los trabajos en el portal**: en la versión anterior del software, el estado del trabajo que se notificada en el portal no estaba muy detallado. Este problema se ha corregido en esta versión.
* **Experiencias de unión a dominios**: correcciones de errores relacionados con la unión a un dominio y el cambio de nombre del dispositivo.

## <a name="issues-fixed-in-the-update-01"></a>Problemas corregidos en la actualización 0.1
En la tabla siguiente se proporciona un resumen de los problemas corregidos en esta versión.

| No. | Característica | Problema |
| --- | --- | --- |
| 1 |VMDK |En algunas versiones de VMware, el disco del sistema operativo se ve como disperso, provocando alertas e interrumpiendo el funcionamiento normal. Esto se ha corregido en esta versión. |
| 2 |Servidor iSCSI |En la última versión, era necesario que el usuario especificara una puerta de enlace para cada interfaz de red habilitada del dispositivo virtual StorSimple. Este comportamiento ha cambiado en esta versión para que el usuario tenga configurar al menos una puerta de enlace para todas las interfaces de red habilitadas. |
| 3 |Paquete de soporte |En la versión anterior del software, la colección de paquetes de compatibilidad daba error cuando el tamaño de los paquetes era superior a 1 GB. Este problema está corregido en esta versión. |
| 4 |Acceso a la nube |En la última versión, si la matriz virtual de StorSimple no tenía conectividad de red y se reiniciaba, la interfaz de usuario local tendría problemas de conectividad. Este problema se ha corregido en esta versión. |
| 5 |Gráficos de supervisión |En la versión anterior, tras una conmutación por error del dispositivo, los gráficos de uso de la capacidad de la nube mostraban valores incorrectos en el Portal de Azure clásico. Esto se ha corregido en la versión actual. |

## <a name="known-issues-in-the-update-01"></a>Problemas conocidos de la actualización 0.1
En la tabla siguiente se muestra un resumen de los problemas conocidos de la matriz virtual de StorSimple y se incluyen los problemas notificados en la versiones anteriores. **Los problemas notificados en esta versión están marcados con un asterisco. Casi todos los problemas de esta lista proceden de la versión de disponibilidad general de la matriz virtual de StorSimple.**

| No. | Característica | Problema | Soluciones alternativas o comentarios |
| --- | --- | --- | --- |
| **1.** |Actualizaciones |Los dispositivos virtuales que se crean en la versión preliminar no se pueden actualizar a una versión que sea compatible con la versión de disponibilidad general. |Debe conmutar por error estos dispositivos virtuales a la versión de disponibilidad general mediante un flujo de trabajo de recuperación ante desastres (DR). |
| **2.** |Disco de datos aprovisionado |Una vez se haya aprovisionado un disco de datos de un determinado tamaño y cree el correspondiente dispositivo virtual de StorSimple, no debe expandir o reducir el disco de datos. Si intentar hacer esto, es posible que pierda todos los datos de las capas locales del dispositivo. | |
| **3.** |Directiva de grupo |Cuando un dispositivo está unido al dominio, aplicar una directiva de grupo puede afectar negativamente al funcionamiento de dispositivo. |Asegúrese de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO). |
| **4.** |Interfaz de usuario web local. |Si tiene las características de seguridad mejorada habilitadas en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario web local, tales como Solución de problemas o Mantenimiento, no funcionen correctamente. Asimismo, cabe la posibilidad de que los botones de estas páginas tampoco funcionen. |Desactive las características de seguridad mejorada de Internet Explorer. |
| **5.** |Interfaz de usuario web local. |En una máquina virtual de Hyper-V, las interfaces de red que se encuentran en la interfaz de usuario web se muestran como interfaces de 10 Gbps. |Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra los adaptadores de red virtual a 10 Gbps. |
| **6.** |Volúmenes o recursos compartidos en niveles |El bloqueo del intervalo de bytes de las aplicaciones que funcionan con volúmenes de StorSimple no se admite. Si tiene habilitado un bloqueo de intervalo de bytes, la organización en niveles de StorSimple no funcionará. |Entre las medidas recomendadas se incluyen:  <br></br>Desactive el bloqueo del intervalo de bytes en la lógica de la aplicación.<br></br>Elija esta opción colocar los datos de esta aplicación en los volúmenes anclados localmente en lugar en volúmenes en capas.<br></br>*Advertencia*: Si usa volúmenes anclados localmente y tiene el bloqueo del intervalo de bytes habilitado, el volumen anclado localmente puede estar en línea incluso antes de que se complete la restauración. En tal caso, si hay una restauración en curso, debe esperar a que esta se complete. |
| **7.** |Recursos compartidos organizados en niveles |Si trabaja con archivos de gran tamaño, estos podrían ocasionar que la organización en niveles se desarrolle lentamente. |Cuando trabaje con esta clase de archivos, es recomendable que el archivo de mayor tamaño no ocupe más del 3 % del tamaño recurso compartido. |
| **8.** |Capacidad de recursos compartidos usada |Si no hay datos en el recurso compartido, es posible que vea cierto consumo del recurso compartido. Esto ocurre porque la capacidad que se usa para los recursos compartidos incluye los metadatos. | |
| **9.** |Recuperación ante desastres |Solo puede realizar la recuperación ante desastres de un servidor de archivos en el mismo dominio que el del dispositivo de origen. Con esta versión no se puede realizar la recuperación ante desastres en el dispositivo de destino de otro dominio. |Esta característica se implementará en una versión posterior. |
| **10.** |Azure PowerShell |En esta versión no se pueden administrar los dispositivos virtuales de StorSimple a través de Azure PowerShell. |Toda la administración de los dispositivos virtuales debe realizarse mediante el portal de Azure clásico y la interfaz de usuario web local. |
| **11.** |Cambio de contraseña |La consola del dispositivo de matriz virtual solo acepta entradas con el formato de teclado en-US. | |
| **12.** |CHAP |Las credenciales CHAP no se pueden quitar una vez creadas. Además, si modifica las credenciales CHAP, deberá desconectar los volúmenes y volver a ponerlos en línea para que el cambio surta efecto. |Esta situación se abordará en una versión posterior. |
| **13.** |Servidor iSCSI |El "almacenamiento usado" que aparece para un volumen iSCSI puede ser diferente en el servicio StorSimple Manager y en el host iSCSI. |El host iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo. |
| **14.** |Servidor de archivos* |Si un archivo de una carpeta tiene una secuencia de datos alternativa (ADS) asociada, no se hará una copia de seguridad de dicha secuencia ni se restaurará a través de la recuperación ante desastres, la clonación o la recuperación a nivel de elemento. | |

## <a name="next-step"></a>Paso siguiente
[Instale actualizaciones](storsimple-ova-install-update-01.md) en la matriz virtual de StorSimple.

