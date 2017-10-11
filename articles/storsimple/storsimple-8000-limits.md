---
title: "Límites del sistema de la serie 8000 de StorSimple | Microsoft Docs"
description: "Describe los límites del sistema y los tamaños recomendados para conexiones y componentes de la serie StorSimple 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc3c0ad193af7625c8c4c1c2e82b6bdc8be33310
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>¿Cuáles son los límites del sistema de StorSimple serie 8000?

## <a name="overview"></a>Información general

StorSimple proporciona almacenamiento escalable y flexible para su centro de datos. Sin embargo, hay algunas limitaciones que debe tener en cuenta durante la planificación, implementación y utilización de su solución StorSimple. En la tabla siguiente se describen estos límites y se ofrecen algunas recomendaciones para que pueda obtener el máximo provecho a su solución StorSimple.

| Identificador de límites | Límite | Comentarios |
| --- | --- | --- |
| Número máximo de credenciales de la cuenta de almacenamiento |64 | |
| Número máximo de contenedores de volúmenes |64 | |
| Número máximo de volúmenes |255 | |
| Número máximo de volúmenes anclados localmente |32 | |
| Número máximo de programaciones por plantilla de ancho de banda |168 |Una programación para cada hora y cada día de la semana (24*7). |
| Tamaño máximo de un volumen en capas en dispositivos físicos |64 TB para 8100 y 8600 |8100 y 8600 son dispositivos físicos. |
| Tamaño máximo de un volumen en capas en dispositivos virtuales de Azure |30 TB para 8010  <br></br> 64 TB para 8020 |8010 y 8020 son dispositivos virtuales de Azure que utilizan el almacenamiento estándar y premium respectivamente. |
| Tamaño máximo de un volumen anclado localmente en dispositivos físicos |8,5 TB para 8100 <br></br> 22,5 TB para 8600 |8100 y 8600 son dispositivos físicos. |
| Número máximo de conexiones iSCSI |512 | |
| Número máximo de conexiones de iSCSI de iniciadores |512 | |
| Número máximo de registros de control de acceso por dispositivo |64 | |
| Número máximo de volúmenes por directiva de copia de seguridad |20 | | |
| Número máximo de copias de seguridad retenidas por programación (en directiva de copia de seguridad) |64 | |
| Número máximo de programaciones por directiva de copia de seguridad |10 | |
| Número máximo de instantáneas de cualquier tipo que se pueden retener por volumen |256 |Este número incluye las instantáneas locales y en la nube. |
| Número máximo de instantáneas que pueden estar presentes en cualquier dispositivo |10.000 | |
| Número máximo de volúmenes que se pueden procesar en paralelo para copia de seguridad, restauración o clonación |16 |<ul><li>Si hay más de 16 volúmenes, se procesarán secuencialmente a medida que las ranuras de procesamiento estén disponibles.</li><li>No se pueden realizar copias de seguridad nuevas de un volumen clonado o de un volumen en capas restaurado hasta que no haya finalizado la operación. Sin embargo, para un volumen local, se permiten las copias de seguridad después de que el volumen está en línea.</li></ul> |
| Tiempo de recuperación de la restauración y la clonación |< 2 minutos |<ul><li>El volumen estará disponible en dos minutos de operación de restauración o clonación, independientemente del tamaño del volumen.</li><li>Es posible que el rendimiento del volumen resulte más lento de lo normal inicialmente, ya que la mayoría de los datos y metadatos todavía residen en la nube. Es posible que el rendimiento aumente a medida que los datos fluyan de la nube al dispositivo StorSimple.</li><li>El tiempo total para descargar los metadatos depende del tamaño del volumen asignado. Los metadatos se traen automáticamente al dispositivo en segundo plano a una velocidad de 5 minutos por cada TB de datos de volumen asignado. Es posible que esta velocidad se vea afectada por un ancho de banda de Internet a la nube.</li><li>La operación de restauración o clonación se completará cuando todos los metadatos estén en el dispositivo.</li><li>No se pueden realizar operaciones de copia de seguridad hasta que se haya completado totalmente la operación de restauración o clonación. |
| Restauración del tiempo de recuperación de volúmenes anclados localmente |< 2 minutos |<ul><li>El volumen estará disponible en dos minutos de operación de restauración, independientemente del tamaño del volumen.</li><li>Es posible que el rendimiento del volumen resulte más lento de lo normal inicialmente, ya que la mayoría de los datos y metadatos todavía residen en la nube. Es posible que el rendimiento aumente a medida que los datos fluyan de la nube al dispositivo StorSimple.</li><li>El tiempo total para descargar los metadatos depende del tamaño del volumen asignado. Los metadatos se traen automáticamente al dispositivo en segundo plano a una velocidad de 5 minutos por cada TB de datos de volumen asignado. Es posible que esta velocidad se vea afectada por un ancho de banda de Internet a la nube.</li><li>A diferencia de los volúmenes en capas, en el caso de los volúmenes anclados localmente, también se descargan los datos del volumen localmente en el dispositivo. La operación de restauración finaliza cuando todos los datos de volúmenes se transfieren al dispositivo.</li><li>Las operaciones de restauración pueden durar bastante tiempo. El tiempo total para completar la restauración depende del tamaño del volumen local aprovisionado, el ancho de banda de Internet y los datos existentes en el dispositivo. Se permiten las operaciones de copia de seguridad en el volumen anclado localmente mientras la operación de restauración está en curso. |
| Velocidad de procesamiento de instantáneas en la nube |15 minutos/TB. |<ul><li>Tiempo mínimo para que la instantánea en la nube esté lista para cargarse (por TB de datos del volumen asociado en la copia de seguridad). </li><li> El tiempo total de la instantánea en la nube se calcula mediante la suma de este tiempo al de carga de la instantánea, que se ve afectado por el ancho de banda de Internet en la nube. |
| Rendimiento máximo de lectura y escritura del cliente (cuando se obtiene desde la capa SSD)* |920/720 MB/s con una sola interfaz de red de 10 GbE |Hasta 2x con MPIO y dos interfaces de red. |
| Rendimiento máximo de lectura y escritura del cliente (cuando se obtiene desde la capa HDD)* |120/250 MB/s | |
| Rendimiento máximo de lectura y escritura de cliente (cuando se obtiene desde la capa de la nube) * para Update 3 y posterior** |40/60 MB/s para volúmenes en capas<br><br>80/60 MB/s para volúmenes en capas con una opción de archivado seleccionada durante la creación de volumen |El rendimiento de lectura depende de que los clientes generen y mantengan una profundidad de cola de E/S suficiente. <br><br>La velocidad conseguida depende de la velocidad de la cuenta de almacenamiento subyacente utilizada. |

&#42; Se midió el rendimiento máximo por tipo de E/S con escenarios de escritura y de lectura del 100%. Es posible que el rendimiento real sea inferior y dependa de las condiciones de la red y de la mezcla de E/S.

&#42; &#42; Los números de rendimiento antes de Update 3 pueden ser inferiores.

## <a name="next-steps"></a>Pasos siguientes
Revise los [Requisitos del sistema StorSimple](storsimple-8000-system-requirements.md).

