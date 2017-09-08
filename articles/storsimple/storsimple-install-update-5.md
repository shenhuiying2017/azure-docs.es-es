---
title: "Instalación de Update 5 en un dispositivo de StorSimple 8000 Series en el portal clásico | Microsoft Docs"
description: "Explica cómo instalar StorSimple 8000 Series Update 5 en un dispositivo de StorSimple 8000 Series en el portal clásico."
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
ms.date: 08/25/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 51fdaae3359c4e341431477ec5079473c345c32d
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalación de Update 5 en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo instalar Update 5 en un dispositivo StorSimple que ejecuta una versión anterior del software mediante el Portal de Azure clásico y que usa el método de revisión. El método de revisión se utiliza cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo StorSimple y está intentando actualizar desde una versión del software anterior a Update 1.

Update 5 incluye actualizaciones de software de dispositivo, firmware de USM, firmware y controlador LSI, Storport y Spaceport, seguridad del sistema operativo y varias otras actualizaciones de SO.  Las actualizaciones de software de dispositivo, Spaceport, Storport, seguridad y otras actualizaciones del sistema operativo no provocan interrupciones. Las actualizaciones que no provocan interrupciones o habituales se pueden aplicar a través del Portal de Azure clásico o mediante el método de revisión. Las actualizaciones del firmware del disco son actualizaciones que provocan interrupciones y pueden aplicarse cuando el dispositivo está en modo de mantenimiento a través del método de revisión con la interfaz de Windows PowerShell del dispositivo.

> [!IMPORTANT]
> * En esta actualización se incluye un conjunto de comprobaciones previas que se hace antes de la instalación para determinar el estado del dispositivo en cuanto a la conectividad de red y el estado del hardware. Estas comprobaciones previas solo se realizan si aplica las actualizaciones desde Azure Portal.
> * Se recomienda instalar las actualizaciones de software y otras actualizaciones habituales mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. En función de la versión de origen, las actualizaciones pueden demorar 4 horas (o más) en instalarse. Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento generan interrupciones, provocan un tiempo de inactividad en el dispositivo.
> * Si ejecuta la opción de StorSimple Snapshot Manager, antes de actualizar el dispositivo, asegúrese de haber actualizado la versión de Snapshot Manager a Update 5.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-classic-portal"></a>Instalación de Update 5 desde el Portal de Azure clásico
Realice los pasos siguientes para actualizar el dispositivo a [Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft extrae información de diagnóstico adicional del dispositivo. Como consecuencia, cuando nuestro equipo de operaciones identifica dispositivos que están teniendo problemas, estamos mejor equipados para recopilar información del dispositivo y diagnosticar problemas.

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. También se debe modificar la **fecha de última actualización**.

* Ahora también verá que hay disponibles actualizaciones en modo de mantenimiento (este mensaje podría seguir apareciendo hasta 24 horas después de instalar las actualizaciones). Las actualizaciones del modo de mantenimiento provocan interrupciones con tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo.

* Descargue las actualizaciones en modo de mantenimiento mediante los pasos enumerados en [Descargar revisiones](#to-download-hotfixes) para buscar y descargar KB4011837, que instala las actualizaciones de firmware del disco (el resto de actualizaciones ya deben estar instaladas). Siga los pasos enumerados en [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones del modo de mantenimiento.

## <a name="install-update-5-as-a-hotfix"></a>Instalación de Update 5 como una revisión


Las versiones de software que se pueden actualizar mediante el método de revisión son las siguientes:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4

> [!NOTE]
> El método que se recomienda para instalar Update 5 es desde el Portal de Azure clásico. Debe usar este procedimiento si la comprobación de la puerta de enlace produce un error al intentar instalar las actualizaciones a través del Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software anterior a Update 1.

El método de revisión implica los tres pasos siguientes:

1. Descargar las revisiones desde el catálogo de Microsoft Update.
2. Instalar y comprobar las revisiones de modo normal.
3. Instalar y comprobar la revisión del modo de mantenimiento.

#### <a name="download-updates-for-your-device"></a>Descargar las actualizaciones para el dispositivo

Debe descargar e instalar las revisiones siguientes en el orden indicado y las carpetas sugeridas:

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |Carpeta de instalación|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Actualización de software<br> Descargar tanto _HcsSfotwareUpdate.exe_ como _CisMSDAgent.exe_ |Regular  <br></br>Sin interrupciones |~ 25 min |FirstOrderUpdate|

Si actualiza desde un dispositivo que ejecuta Update 4, basta con instalar las actualizaciones acumulativas del sistema operativo como actualizaciones de segundo orden.

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |Carpeta de instalación|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Paquete de actualizaciones acumulativas de SO <br> Descargar la versión R2 de Windows Server 2012 |Regular  <br></br>Sin interrupciones |- |SecondOrderUpdate|

Si la instalación se realiza desde un dispositivo que ejecuta Update 3 o versiones anteriores, instale los siguientes elementos además de las actualizaciones acumulativas.

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |Carpeta de instalación|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Actualizaciones de firmware y controlador LSI <br> Actualización de firmware de USM (versión 3.38) |Regular  <br></br>Sin interrupciones |~ 3 horas <br> (incluye 2A. + 2B. + 2C).|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Paquete de actualizaciones de seguridad de SO <br> Descargar la versión R2 de Windows Server 2012 |Regular  <br></br>Sin interrupciones |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Paquete de actualizaciones de SO <br> Descargar la versión R2 de Windows Server 2012 |Regular  <br></br>Sin interrupciones |- |SecondOrderUpdate|


Es posible que también necesite instalar actualizaciones de firmware de disco en la parte superior de todas las actualizaciones que se muestran en las tablas anteriores. Puede comprobar si las necesita actualizaciones de firmware de disco ejecutando el cmdlet `Get-HcsFirmwareVersion` . Si ejecuta estas versiones de firmware: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, no necesita instalar estas actualizaciones.

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación | Carpeta de instalación|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware del disco |Mantenimiento  <br></br>Perjudicial |~30 min | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Si actualiza desde Update 4, el tiempo total de instalación es de casi cuatro horas.
> * Antes de usar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores de dispositivo estén en línea y todos los componentes de hardware estén en buen estado.

Realice los siguientes pasos para descargar e instalar las revisiones.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Pasos siguientes
Más información sobre el [lanzamiento de Update 5](storsimple-update5-release-notes.md).


