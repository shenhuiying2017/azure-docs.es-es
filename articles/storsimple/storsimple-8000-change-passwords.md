---
title: "Cambio de sus contraseñas de StorSimple | Microsoft Docs"
description: "Describe cómo usar el servicio StorSimple Device Manager para cambiar sus contraseñas de administrador de dispositivos y de StorSimple Snapshot Manager."
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Uso del servicio StorSimple Device Manager para cambiar las contraseñas de StorSimple

## <a name="overview"></a>Información general
La opción **Configuración del dispositivo** de Azure Portal contiene todos los parámetros de dispositivo que puede volver a configurar en un dispositivo StorSimple administrado mediante un servicio StorSimple Device Manager. En este tutorial se explica cómo se puede usar la opción **Seguridad** de **Configuración del dispositivo** para cambiar la contraseña de administrador de dispositivos y de StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Cambio de la contraseña del administrador de dispositivos
Cuando se usa la interfaz de Windows PowerShell para tener acceso al dispositivo StorSimple, se le pedirá que escriba una contraseña de administrador de dispositivos. Cuando el primer dispositivo StorSimple esté registrado con un servicio, la contraseña predeterminada para esta interfaz será *Password1*. Por seguridad de sus datos, deberá cambiar esta contraseña al final del proceso de registro. No se puede salir del proceso de registro sin cambiar esta contraseña. Para más información, consulte [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

La primera contraseña que se estableció a través de la interfaz de Windows PowerShell durante el registro puede cambiarse después a través de Azure Portal. Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos.

#### <a name="to-change-the-device-administrator-password"></a>Para cambiar la contraseña del administrador de dispositivos
1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**.

2. En la lista tabular de dispositivos, seleccione el dispositivo cuya contraseña tiene intención de cambiar y haga clic en él.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. En la hoja **Configuración**, vaya a **Configuración del dispositivo > Seguridad**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. En la hoja **Configuración de seguridad**, haga clic en **Contraseña** para cambiar la contraseña de administrador de dispositivos.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. En la hoja **Contraseña**, especifique una contraseña de administrador que tenga entre 8 y 15 caracteres. La contraseña debe ser una combinación de tres o más caracteres en mayúsculas, minúsculas, números y caracteres especiales.

6. Confirme la contraseña.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Haga clic en **Guardar** y, cuando se le pida confirmación, haga clic en **Sí**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Ahora debe actualizarse la contraseña del administrador de dispositivos. Puede usar esta contraseña modificada para tener acceso a la interfaz de Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Establecimiento de la contraseña de StorSimple Snapshot Manager
El software StorSimple Snapshot Manager reside en el host de Windows y permite a los administradores administrar copias de seguridad del dispositivo StorSimple en forma de instantáneas locales y en la nube.

Al configurar un dispositivo en StorSimple Snapshot Manager, se le pedirá que proporcione la dirección IP y la contraseña del dispositivo para autenticar el dispositivo de almacenamiento.

La contraseña de StorSimple Snapshot Manager se puede establecer o cambiar a través de Azure Portal. Realice los pasos siguientes para establecer o cambiar la contraseña de StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Para establecer la contraseña de StorSimple Snapshot Manager
1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**.

2. En la lista tabular de dispositivos, seleccione el dispositivo cuya contraseña de StorSimple Snapshot Manager tiene intención de establecer o cambiar, y haga clic en él.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. En la hoja **Configuración**, vaya a **Configuración del dispositivo > Seguridad**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. En la hoja **Configuración de seguridad**, haga clic en **Contraseña** para establecer o cambiar la contraseña de StorSimple Snapshot Manage.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. En la hoja **Contraseña**, escriba una contraseña que tenga 14 o 15 caracteres. Asegúrese de que la contraseña contiene una combinación de tres o más caracteres en mayúsculas, minúsculas, números y caracteres especiales.

6. Confirme la contraseña.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Haga clic en **Guardar** y, cuando se le pida confirmación, haga clic en **Sí**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Ahora se debe actualizar la contraseña de StorSimple Snapshot Manager.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [seguridad de StorSimple](storsimple-8000-security.md).
* Obtenga más información sobre la [modificación de la configuración del dispositivo](storsimple-8000-modify-device-config.md).
* Más información sobre el [uso del servicio StorSimple Device Manager para administrar su dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

