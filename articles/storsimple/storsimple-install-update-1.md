---
title: "Instalación de Update 1.2 en el dispositivo StorSimple | Microsoft Docs"
description: "Explica cómo instalar la actualización 1.2 de la serie StorSimple 8000 en un dispositivo de la serie StorSimple 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 7a513923-eb77-4078-b0ab-f8e90183796a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 56af9046fff24229298997b169aaf329e1692cc2
ms.openlocfilehash: 80ff35cc47dfc38089f4c392ef4c90baf9ccc03e
ms.lasthandoff: 02/28/2017


---
# <a name="install-update-12-on-your-storsimple-8000-series-device"></a>Instalación de Update 1.2 en el dispositivo StorSimple serie 8000
## <a name="overview"></a>Información general
En este tutorial se explica cómo instalar la actualización 1.2 en un dispositivo StorSimple con una versión de software anterior a la actualización 1. El tutorial también trata los pasos adicionales necesarios para la actualización cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo StorSimple.

La actualización 1.2 incluye actualizaciones de software del dispositivo, actualizaciones de controladores LSI y actualizaciones del firmware del disco. Las actualizaciones del software y de los controladores LSI son actualizaciones que no provocan interrupciones y se pueden aplicar mediante el Portal de Azure clásico. Las actualizaciones del firmware del disco son actualizaciones perturbadoras y solo pueden aplicarse mediante la interfaz de Windows PowerShell del dispositivo.

En función de la versión que se está ejecutando en el dispositivo, puede determinar si se aplicará la actualización 1.2. Para comprobar la versión del software del dispositivo, desplácese hasta la sección **Vista rápida** del **Panel** de su dispositivo.

</br>

| Si ejecuta la versión del software... | ¿Qué sucede en el portal? |
| --- | --- |
| Lanzamiento - GA |Si está ejecutando la versión de lanzamiento (GA), no aplique esta actualización. [Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para actualizar el dispositivo. |
| Actualización 0.1 |El portal aplica la actualización 1.2. |
| Actualización 0.2 |El portal aplica la actualización 1.2. |
| Actualización 0.3 |El portal aplica la actualización 1.2. |
| Actualización 1 |Esta actualización no estará disponible. |
| Actualización 1.1 |Esta actualización no estará disponible. |

</br>

> [!IMPORTANT]
> * Es posible que no vea la actualización 1.2 de inmediato porque hacemos una implementación por fases de las actualizaciones. Busque actualizaciones de nuevo en unos días ya que estarán disponibles pronto.
> * Esta actualización incluye un conjunto de comprobaciones previas manuales y automáticas para determinar el estado del dispositivo en cuanto a la conectividad de la red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico.
> * Se recomienda instalar las actualizaciones de software y de los controladores mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. Las actualizaciones pueden tardar 5-10 horas en instalarse (incluidas las actualizaciones de Windows). Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento son perturbadoras, generarán un tiempo de inactividad para el dispositivo.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Instalación de la actualización 1.2 mediante el Portal de Azure clásico
Realice los pasos siguientes para actualizar el dispositivo a [Update 1.2](storsimple-update1-release-notes.md). Use este procedimiento solo si tiene una puerta de enlace configurada en la interfaz de red DATA 0 del dispositivo.

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**. También se debe modificar **Fecha de última actualización:** . También verá que hay disponibles actualizaciones en modo de mantenimiento (este mensaje podría seguir apareciendo hasta 24 horas después de instalar las actualizaciones).
   
   Las actualizaciones del modo de mantenimiento provocan interrupciones con tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo.
   
   ![Página de mantenimiento](./media/storsimple-install-update-1/InstallUpdate12_10M.png "Página de mantenimiento")
2. Descargue las actualizaciones en el modo de mantenimiento mediante los pasos enumerados en [Descargar revisiones](#to-download-hotfixes) para buscar y descargar KB3063416, que instala las actualizaciones de firmware del disco (el resto de actualizaciones ya deben estar instaladas).
3. Siga los pasos enumerados en [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones del modo de mantenimiento.
4. En el Portal de Azure clásico, desplácese hasta la página **Mantenimiento** y, en la parte inferior de la página, haga clic en **Buscar actualizaciones** para comprobar si hay actualizaciones de Windows; a continuación, haga clic en **Instalar actualizaciones**. Habrá terminado cuando se instalen correctamente todas las actualizaciones.

## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Instale la actualización 1.2 en un dispositivo que tenga una puerta de enlace configurada para una interfaz de red que no sea DATA 0
Debe usar este procedimiento solo si la comprobación de la puerta de enlace es incorrecta al intentar instalar las actualizaciones mediante el Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1. Si el dispositivo no tiene una puerta de enlace en una interfaz de red que no sea DATA 0, puede actualizar el dispositivo directamente desde el Portal de Azure clásico. Consulte [Instalación de la actualización 1.2 mediante el Portal de Azure clásico](#install-update-1.2-via-the-azure-classic-portal).

Las versiones de software que se pueden actualizar con este método son actualización 0.1, actualización 0.2 y actualización 0.3.

> [!IMPORTANT]
> * Si el dispositivo está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para que le ayude con la actualización.
> * Este procedimiento debe realizarse una sola vez para aplicar la actualización 1.2. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.
> 
> 

Si el dispositivo ejecuta el software previo a la actualización 1 y tiene una puerta de enlace establecida para una interfaz de red que no sea DATA 0, la actualización 1.2 se puede aplicar de las dos maneras siguientes:

* **Opción 1**: Descargar la actualización y aplicarla mediante el cmdlet `Start-HcsHotfix` desde la interfaz de Windows PowerShell del dispositivo. Éste es el método recomendado. **No use este método para aplicar la actualización 1.2 si el dispositivo ejecuta la actualización 1.0 o la actualización 1.1.**
* **Opción 2**: quitar la configuración de la puerta de enlace e instalar la actualización directamente desde el Portal de Azure clásico.

En las siguientes secciones, se proporcionan instrucciones detalladas de cada una de estas opciones.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Opción 1: Use Windows PowerShell para StorSimple para aplicar la actualización 1.2 como una revisión
Solo debe usar este procedimiento si ejecuta la actualización 0.1, 0.2 y 0.3 y si la comprobación de la puerta de enlace produce un error al intentar instalar actualizaciones desde el Portal de Azure clásico. Si está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para actualizar el dispositivo.

Para instalar Update 1.2 como una revisión, descargue e instale las revisiones siguientes:

| Orden | KB | Descripción | Tipo de actualización |
| --- | --- | --- | --- |
| 1 |KB3063418 |Actualización de software |Normal |
| 2 |KB3043005 |Actualización del controlador SAS LSI |Normal |
| 3 |KB3063416 |Firmware del disco |Mantenimiento |

Antes de utilizar este procedimiento para aplicar la actualización, asegúrese de que:

* Los dos controladores de dispositivo están en línea.

Realice los pasos siguientes para aplicar la actualización 1.2. **Las actualizaciones podrían tardar alrededor de 2 horas en completarse (aproximadamente 30 minutos para el software, 30 minutos para los controladores, 45 minutos para el firmware del disco).**

[!INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]

## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Opción 2: use el Portal de Azure clásico para aplicar la actualización 1.2 después de quitar la configuración de la puerta de enlace
Este procedimiento solo se aplica a los dispositivos StorSimple que ejecutan una versión del software anterior a la actualización 1 y tienen una puerta de enlace establecida en una interfaz de red que no sea DATA 0. Deberá borrar la configuración de la puerta de enlace antes de aplicar la actualización.

La actualización puede tardar unas horas en completarse. Si los hosts se encuentran en subredes diferentes, la eliminación de la configuración de la puerta de enlace de las interfaces de iSCSI podría provocar un tiempo de inactividad. Para reducir el tiempo de inactividad, se recomienda configurar DATA 0 para el tráfico iSCSI.

Realice los pasos siguientes para deshabilitar la interfaz de red con la puerta de enlace y, a continuación, aplique la actualización.

[!INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el [lanzamiento de la actualización 1.2](storsimple-update1-release-notes.md).


