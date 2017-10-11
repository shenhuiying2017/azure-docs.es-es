---
title: "Instalación de Update 4 en el dispositivo StorSimple | Microsoft Docs"
description: "Explica cómo instalar Update 4 de la serie StorSimple 8000 en un dispositivo de la serie StorSimple 8000."
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
ms.date: 05/30/2017
ms.author: alkohli
ms.openlocfilehash: 7dc2f29db64218b9db17eaf555c43a7ad282ea07
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalación de Update 4 en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo instalar Update 4 en un dispositivo StorSimple ejecutando una versión anterior del software mediante el Portal de Azure clásico y usando el método de revisión. El método de revisión se utiliza cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo StorSimple y está intentando actualizar desde una versión del software anterior a Update 1.

Update 4 incluye actualizaciones de software de dispositivo, firmware de USM, firmware y controlador LSI, Storport y Spaceport, seguridad de SO y varias otras actualizaciones de SO.  Las actualizaciones de software de dispositivo, firmware de USM, Spaceport, Storport y otras actualizaciones de SO no provocan interrupciones. Las actualizaciones que no provocan interrupciones o habituales se pueden aplicar a través del Portal de Azure clásico o mediante el método de revisión. Las actualizaciones del firmware del disco son actualizaciones que provocan interrupciones y solo pueden aplicarse mediante el método de revisión a través de la interfaz de Windows PowerShell del dispositivo. 

> [!IMPORTANT]
> * En esta actualización se incluye un conjunto de comprobaciones previas que se hace antes de la instalación para determinar el estado del dispositivo en cuanto a la conectividad de red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico.
> * Se recomienda instalar las actualizaciones de software y otras actualizaciones habituales mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. En función de la versión de origen, las actualizaciones pueden demorar 4 horas (o más) en instalarse. Las actualizaciones en modo de mantenimiento también deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento son perturbadoras, generarán un tiempo de inactividad para el dispositivo.
> * Si ejecuta la opción de StorSimple Snapshot Manager, antes de actualizar el dispositivo, asegúrese de haber actualizado la versión de Snapshot Manager a Update 4.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-classic-portal"></a>Instalación de Update 4 mediante el Portal de Azure clásico
Realice los pasos siguientes para actualizar el dispositivo a [Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Si va a aplicar Update 2 o una versión posterior (incluida Update 2.1), Microsoft podrá extraer información de diagnóstico adicional del dispositivo. Como consecuencia, cuando nuestro equipo de operaciones identifica dispositivos que están teniendo problemas, estamos mejor equipados para recopilar información del dispositivo y diagnosticar problemas. Al aceptar Update 2 o una versión posterior, nos permite ofrecer este soporte técnico proactivo. 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. También se debe modificar **Fecha de última actualización:** . 

* Ahora también verá que hay disponibles actualizaciones en modo de mantenimiento (este mensaje podría seguir apareciendo hasta 24 horas después de instalar las actualizaciones). Las actualizaciones del modo de mantenimiento provocan interrupciones con tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo.
 
* Descargue las actualizaciones en modo de mantenimiento mediante los pasos enumerados en [Descargar revisiones](#to-download-hotfixes) para buscar y descargar KB4011837, que instala las actualizaciones de firmware del disco (el resto de actualizaciones ya deben estar instaladas). Siga los pasos enumerados en [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones del modo de mantenimiento. 

## <a name="install-update-4-as-a-hotfix"></a>Instalación de Update 4 como una revisión
El método que se recomienda para instalar Update 4 es mediante el Portal de Azure clásico.

Debe usar este procedimiento si la comprobación de la puerta de enlace produce un error al intentar instalar las actualizaciones a través del Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1.

Las versiones de software que se pueden actualizar mediante el método de revisión son las siguientes:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1 


El método de revisión implica los tres pasos siguientes:

1. Descargar las revisiones desde el catálogo de Microsoft Update.
2. Instalar y comprobar las revisiones de modo normal.
3. Instalar y comprobar la revisión del modo de mantenimiento.

#### <a name="download-updates-for-your-device"></a>Descargar las actualizaciones para el dispositivo

Debe descargar e instalar las revisiones siguientes en el orden indicado y las carpetas sugeridas:

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |Carpeta de instalación|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 <br> (2 archivos) |Actualización de software del dispositivo <br> Actualización del agente CiS/MDS |Regular  <br></br>Sin interrupciones |~ 25 min |FirstOrderUpdate <br> _Instale la actualización de software del dispositivo antes que la actualización del agente CiS/MDS._|
| 2A. |KB4011841 <br> KB4011842 |Actualizaciones de firmware y controlador LSI <br> Actualización de firmware de USM (versión 3.38) |Regular  <br></br>Sin interrupciones |~ 3 horas <br> (incluye 2A. + 2B. + 2C).|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3192392  <br> KB3153704, KB3174644 <br> KB3139914  |Paquete de actualizaciones de seguridad de SO |Regular  <br></br>Sin interrupciones |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Paquete de actualizaciones de SO |Regular  <br></br>Sin interrupciones |- |SecondOrderUpdate|

Es posible que también necesite instalar actualizaciones de firmware de disco en la parte superior de todas las actualizaciones que se muestran en las tablas anteriores. Puede comprobar si las necesita actualizaciones de firmware de disco ejecutando el cmdlet `Get-HcsFirmwareVersion` . Si ejecuta estas versiones de firmware: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, no necesita instalar estas actualizaciones.

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación | Carpeta de instalación|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4011837 |Firmware del disco |Mantenimiento  <br></br>Perjudicial |~30 min | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Este procedimiento debe realizarse una sola vez para aplicar Update 4. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.
> * Si actualiza desde Update 3 o 3.1, el tiempo total de instalación es de 4 horas aproximadamente.
> * Antes de usar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores de dispositivo estén en línea y todos los componentes de hardware estén en buen estado.

Realice los siguientes pasos para descargar e instalar las revisiones.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el [lanzamiento de Update 4](storsimple-update4-release-notes.md).

