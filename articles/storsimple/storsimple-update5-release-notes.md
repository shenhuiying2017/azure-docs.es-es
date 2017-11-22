---
title: "Notas de la versión de la serie StorSimple 8000 Update 5 | Microsoft Docs"
description: "Describe las nuevas características, problemas y soluciones alternativas de la serie StorSimple 8000 Update 5."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de la versión de la serie StorSimple 8000 Update 5

## <a name="overview"></a>Información general

Las siguientes notas de la versión describen las características nuevas e identifican los problemas críticos abiertos de la serie StorSimple 8000 Update 5. También contienen una lista de las actualizaciones de software de StorSimple incluidas en esta versión.

Update 5 puede aplicarse a cualquier dispositivo de StorSimple que ejecute las actualizaciones de la 0.1 a la 4. La versión de dispositivo asociada a Update 5 es 6.3.9600.17845.

Revise la información contenida en las notas de la versión antes de implementar la actualización de la solución de StorSimple.

> [!IMPORTANT]
> * Update 5 es una actualización obligatoria y debe instalarse inmediatamente. Para más información, consulte cómo [aplicar Update 5](storsimple-8000-install-update-5.md).
> * Update 5 tiene software de dispositivo, firmware del disco, seguridad del sistema operativo y otras actualizaciones del sistema operativo. Para instalar esta actualización, se necesitan en torno a unas cuatro horas. La actualización del firmware de disco es una actualización perjudicial y provoca un tiempo de inactividad en el dispositivo. Se recomienda aplicar Update 5 para mantener actualizado el dispositivo.
> * Para las nuevas versiones, no podrán ver las actualizaciones de inmediato porque hacemos una implementación por fases de las actualizaciones. Espere unos días y luego busque actualizaciones de nuevo, ya que estas estarán disponibles pronto.

## <a name="whats-new-in-update-5"></a>Novedades de Update 5

Se realizaron las siguientes mejoras clave y correcciones de errores en Update 5.

* **Uso de Azure Active Directory (AAD) para autenticarse con el servicio StorSimple Device Manager**: desde Update 5 y versiones posteriores, Azure Active Directory se utiliza para autenticarse con el servicio StorSimple Device Manager. El mecanismo de autenticación antigua dejará de utilizarse en diciembre de 2017. Todos los usuarios deben incluir las nuevas direcciones URL de autenticación en sus reglas de firewall. Para obtener más información, consulte las [direcciones URL de autenticación enumeradas en los requisitos de red para su dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Si la dirección URL de autenticación no se incluye en las reglas de firewall, los usuarios verán una alerta crítica sobre que su dispositivo de StorSimple no se pudo autenticar con el servicio. Si los usuarios ven esta alerta, deben incluir la nueva dirección URL de autenticación. Para más información, vaya a [Alertas de red de StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nueva versión de StorSimple Snapshot Manager**: se ha publicado una nueva versión de StorSimple Snapshot Manager que es compatible con Update 5 y con todos los dispositivos StorSimple que ejecutan Update 4 o versiones posteriores. Se recomienda que actualice a esta versión. La versión anterior de StorSimple Snapshot Manager se utiliza con dispositivos StorSimple que ejecutan Update 3 o ediciones anteriores. [Descargue la versión adecuada de StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) y consulte información sobre la [implementación de StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas corregidos en Update 5

En las tablas siguientes se proporciona un resumen de los problemas corregidos en Update 5.

| No | Característica | Problema | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Comunicación remota de Windows PowerShell |En la versión anterior, un usuario recibiría un error al intentar establecer una conexión remota al dispositivo StorSimple Cloud Appliance mediante Windows PowerShell. La causa raíz de este problema se corrigió en esta versión. |No |Sí |
| 2 |Plantillas de ancho de banda |En las versiones anteriores, hubo un problema con las plantillas de ancho de banda que daba como resultado un ancho de banda inferior para el que se había configurado el dispositivo. Este problema se ha corregido en esta versión. |Sí |Sí |
| 3 |Conmutación por error |En la versión anterior, cuando un dispositivo con un gran número de volúmenes conmutaba por error a otro dispositivo que ejecutaba Update 4, el proceso produciría un error si se intentaban aplicar los registros de control de acceso. Este problema está corregido en esta versión. |Sí |Sí |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas conocidos en Update 5 de las versiones anteriores

No hay ningún problema conocido en Update 5. Si desea ver una lista de los problemas existentes en Update 5 que provienen de versiones anteriores, consulte las [notas de la versión de Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Controlador SCSI conectado en serie (SAS) y actualizaciones de firmware en Update 5

Esta versión tiene actualizaciones de firmware y controlador LSI y controlador SAS. Para más información sobre cómo instalar estas actualizaciones, consulte cómo [instalar Update 5](storsimple-8000-install-update-5.md) en el dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Actualizaciones de StorSimple Cloud Appliance en Update 5

Esta actualización no se puede aplicar a StorSimple Cloud Appliance (también conocido como el dispositivo virtual). Los dispositivos en la nube nuevos deben crearse con la imagen de Update 5. Para más información sobre cómo crear una instancia de StorSimple Cloud Appliance, vaya a [Implementación y administración de una instancia de StorSimple Cloud Appliance en Azure (Update 3 y versiones posteriores)](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Paso siguiente

Obtenga información sobre cómo [instalar Update 5](storsimple-8000-install-update-5.md) en el dispositivo StorSimple.

