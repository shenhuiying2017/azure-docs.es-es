---
title: "Cambio de la contraseña a través de StorSimple Device Manager | Microsoft Docs"
description: "Describe cómo usar el servicio StorSimple Manager para cambiar sus contraseñas de administrador de dispositivos y de StorSimple Snapshot Manager."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: d890b59595628ca3eeff1df258847c2bb54d29ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Use el servicio de Administrador de StorSimple para cambiar las contraseñas de StorSimple
## <a name="overview"></a>Información general
La página **Configurar** del Portal de Azure clásico contiene todos los parámetros de dispositivo que puede volver a configurar en un dispositivo de StorSimple administrado mediante un servicio de Administrador de StorSimple. En este tutorial se explica cómo puede usar la página **Configurar** para cambiar el administrador del dispositivo o la contraseña de StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Cambio de la contraseña del administrador de dispositivos
Cuando se usa la interfaz de Windows PowerShell para tener acceso al dispositivo StorSimple, se le pedirá que escriba una contraseña de administrador de dispositivos. Cuando el primer dispositivo StorSimple esté registrado con un servicio, la contraseña predeterminada para esta interfaz será *Password1*. Por seguridad de sus datos, deberá cambiar esta contraseña al final del proceso de registro. No se puede salir del proceso de registro sin cambiar esta contraseña. Para más información, consulte [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

La contraseña que se estableció en primer lugar a través de la interfaz de Windows PowerShell durante el registro puede cambiarse a continuación a través del Portal de Azure clásico. Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos.

#### <a name="to-change-the-device-administrator-password"></a>Para cambiar la contraseña del administrador de dispositivos
1. En el Portal clásico, haga clic en **Dispositivos** > **Configurar** para el dispositivo.
2. Desplácese hacia abajo a la sección **Contraseña de administrador de dispositivos** . Proporcione una contraseña de administrador que tenga entre 8 y 15 caracteres. La contraseña debe ser una combinación de tres o más caracteres en mayúsculas, minúsculas, números y caracteres especiales.
3. Confirme la contraseña.
4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora debe actualizarse la contraseña del administrador de dispositivos. Puede usar esta contraseña modificada para tener acceso a la interfaz de Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Cambio de la contraseña de StorSimple Snapshot Manager
El software StorSimple Snapshot Manager reside en el host de Windows y permite a los administradores administrar copias de seguridad del dispositivo StorSimple en forma de instantáneas locales y en la nube.

Al configurar un dispositivo en StorSimple Snapshot Manager, se le pedirá que proporcione la dirección IP y la contraseña del dispositivo para autenticar el dispositivo de almacenamiento. Esta contraseña primero se configura a través de la interfaz de Windows PowerShell. Para más información, consulte [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

La contraseña que se estableció en primer lugar a través de la interfaz de Windows PowerShell durante el registro puede cambiarse a continuación a través del Portal clásico. Realice los pasos siguientes para cambiar la contraseña de StorSimple Snapshot Manager.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Para cambiar la contraseña de StorSimple Snapshot Manager
1. En el Portal clásico, haga clic en **Dispositivos** > **Configurar** para el dispositivo.
2. Desplácese hacia abajo hasta la sección de **Administrador de instantáneas StorSimple** . Escriba una contraseña que tenga 14 o 15 caracteres. Asegúrese de que la contraseña contiene una combinación de tres o más caracteres en mayúsculas, minúsculas, números y caracteres especiales.
3. Confirme la contraseña.
4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora se debe actualizar la contraseña de StorSimple Snapshot Manager.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [seguridad de StorSimple](storsimple-security.md).
* Obtenga más información sobre la [modificación de la configuración del dispositivo](storsimple-modify-device-config.md).
* Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

