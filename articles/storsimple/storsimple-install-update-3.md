---
title: "Instalación de Update 3 en el dispositivo StorSimple | Microsoft Docs"
description: "Explica cómo instalar Update 3 de la serie StorSimple 8000 en un dispositivo de la serie StorSimple 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b99b9cd52dd28f7f62b5d8d5ffe32339a67f82a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>Instalación de Update 3 en el dispositivo StorSimple serie 8000

> [!NOTE]
> El portal clásico para StorSimple está en desuso. Los administradores de dispositivos StorSimple realizarán la transición automáticamente al nuevo Azure Portal según la programación de puesta en desuso. Recibirá un correo electrónico y una notificación del portal en los que se avisa de este paso. Este documento también se retirará pronto. Si tiene alguna pregunta sobre este paso, consulte las [preguntas frecuentes de la migración a Azure Portal](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Información general

En este tutorial se explica cómo instalar Update 3 en un dispositivo StorSimple ejecutando una versión anterior del software mediante el Portal de Azure clásico y usando el método de revisión. El método de revisión se utiliza cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo StorSimple y está intentando actualizar desde una versión del software anterior a Update 1.

Update 3 incluye actualizaciones de software de dispositivo, controlador LSI y firmware, Storport y Spaceport. Si actualiza desde Update 2 o una versión anterior, también debe aplicar iSCSI, WMI y, en algunos casos, las actualizaciones de firmware de disco. El software del dispositivo, WMI, iSCSI, el controlador LSI y las correcciones de Storport y Spaceport son actualizaciones que no provocan interrupciones. Estas actualizaciones se pueden aplicar mediante el Portal de Azure clásico. Las actualizaciones del firmware del disco son actualizaciones perturbadoras y solo pueden aplicarse mediante la interfaz de Windows PowerShell del dispositivo.

> [!IMPORTANT]
> * En esta actualización se incluye un conjunto de comprobaciones previas que se hace antes de la instalación para determinar el estado del dispositivo en cuanto a la conectividad de red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico.
> * Se recomienda instalar las actualizaciones de software y de los controladores mediante el Portal de Azure clásico. Vaya a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) únicamente si se produce un error en el portal en las comprobaciones de la puerta de enlace anteriores a la actualización. Dependiendo de la versión de origen, las actualizaciones pueden tardar 1,5 y 2,5 horas en instalarse. Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento generan interrupciones, se produce un tiempo de inactividad en el dispositivo.
> * Si ejecuta la opción de StorSimple Snapshot Manager, antes de actualizar el dispositivo, asegúrese de haber actualizado la versión de Snapshot Manager a Update 2.

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Instalación de Update 3 mediante el Portal de Azure clásico
Realice los pasos siguientes para actualizar el dispositivo a [Update 3](storsimple-update3-release-notes.md).

> [!NOTE]
> Si va a aplicar Update 2 o una versión posterior (incluida Update 2.1), Microsoft podrá extraer información de diagnóstico adicional del dispositivo. Estos datos ayudan a identificar los dispositivos de StorSimple que experimentan problemas y a diagnosticarlos. Al aceptar Update 2 o una versión posterior, nos permite ofrecer este soporte técnico proactivo.


[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 3 (6.3.9600.17759)**. También se debe modificar la **fecha de última actualización**. 
   - Si va a actualizar desde una versión anterior a Update 2, verá que están disponibles las actualizaciones en modo de mantenimiento. Este mensaje podría seguir mostrándose hasta 24 horas después de instalar las actualizaciones.
     Las actualizaciones en modo de mantenimiento generan interrupciones que provocan un tiempo de inactividad en el dispositivo. Estas actualizaciones solo se pueden aplicar a través de la interfaz de Windows PowerShell del dispositivo. En algunos casos, cuando se ejecuta Update 1.2, es posible que el firmware del disco ya esté actualizado y que no sea necesario instalar las actualizaciones en modo de mantenimiento.
   - Si va a actualizar desde Update 2 o posterior, el dispositivo debería estar ahora actualizado. Puede avanzar el paso siguiente.

Descargue las actualizaciones en modo de mantenimiento mediante los pasos enumerados en [Descargar revisiones](#to-download-hotfixes) para buscar y descargar KB3121899, que instala las actualizaciones de firmware del disco (el resto de actualizaciones ya deben estar instaladas). Siga los pasos enumerados en [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones del modo de mantenimiento. 

## <a name="install-update-3-as-a-hotfix"></a>Instalar Update 3 como una revisión
Debe usar este procedimiento si la comprobación de la puerta de enlace produce un error al intentar instalar las actualizaciones a través del Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1.

Las versiones de software que se pueden actualizar mediante el método de revisión son las siguientes:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2 

> [!IMPORTANT]
> * Si el dispositivo está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para que le ayude con la actualización.
> 
> 

El método de revisión implica los tres pasos siguientes:

1. Descargar las revisiones desde el catálogo de Microsoft Update.
2. Instalar y comprobar las revisiones de modo normal.
3. Instalar y comprobar la revisión del modo de mantenimiento (solo cuando se actualiza desde versiones de software anteriores a Update 2).

#### <a name="download-updates-for-your-device"></a>Descargar las actualizaciones para el dispositivo
**Si el dispositivo ejecuta Update 2.1 o 2.2**, debe descargar e instalar las siguientes revisiones en el orden indicado:

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |Actualización de software &#42; |Regular  <br></br>Sin interrupciones |~45 min |
| 2. |KB3186859 |Controlador LSI y firmware |Regular  <br></br>Sin interrupciones |~20 min |
| 3. |KB3121261 |Revisión de Storport y Spaceport  </br> Windows Server 2012 R2 |Regular  <br></br>Sin interrupciones |~20 min |

&#42; *Tenga en cuenta que la actualización de software consta de dos archivos binarios: actualización de software de dispositivo precedido por `all-hcsmdssoftwareupdate` y el agente de Cis y Mds precedido por `all-cismdsagentupdatebundle`. La actualización de software de dispositivo debe instalarse antes que el agente de Cis y Mds. También debe reiniciar el controlador activo mediante el cmdlet `Restart-HcsController` después de aplicar la actualización del agente de Cis y Mds (y antes de aplicar el resto de actualizaciones).* 

**Si el dispositivo está ejecutando Update 0.1, 0.2, 0.3, 1.0, 1.1, 1.2 o 2.0**, debe descargar e instalar las revisiones siguientes, además de las actualizaciones de software, controlador LSI y firmware (se muestra en la tabla anterior), en el orden indicado:

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |Paquete iSCSI |Regular  <br></br>Sin interrupciones |~20 min |
| 5. |KB3103616 |Paquete WMI |Regular  <br></br>Sin interrupciones |~12 min |

<br></br>

**Si el dispositivo está ejecutando las versiones 0.2, 0.3, 1.0, 1.1 y 1.2**, es posible que también necesite instalar actualizaciones de firmware de disco en la parte superior de todas las actualizaciones que se muestran en las tablas anteriores. Puede comprobar si las necesita actualizaciones de firmware de disco ejecutando el cmdlet `Get-HcsFirmwareVersion` . Si ejecuta estas versiones de firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, no necesita instalar estas actualizaciones.

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |Firmware del disco |Mantenimiento  <br></br>Perjudicial |~30 min |

<br></br>

> [!IMPORTANT]
> * Este procedimiento debe realizarse una sola vez para aplicar Update 3. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.
> * Si actualiza desde Update 2.2, el tiempo total de instalación es de 1,1 horas aproximadamente.
> * Antes de usar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores de dispositivo estén en línea y todos los componentes de hardware estén en buen estado.
> 
> 

Realice los siguientes pasos para descargar e instalar las revisiones.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el [lanzamiento de Update 3](storsimple-update3-release-notes.md).

