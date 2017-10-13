---
title: "Cambio de la contraseña del administrador de dispositivos de StorSimple Virtual Array | Microsoft Docs"
description: "Describe cómo usar Azure Portal o la interfaz de usuario web de StorSimple Virtual Array para cambiar la contraseña del administrador de dispositivos."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Cambio de la contraseña del administrador de dispositivos de la matriz virtual de StorSimple mediante StorSimple Device Manager

## <a name="overview"></a>Información general

Cuando use la interfaz de Windows PowerShell para acceder a StorSimple Virtual Array, se le pedirá que escriba una contraseña de administrador de dispositivos. Cuando el dispositivo StorSimple se aprovisione e inicie por primera vez, la contraseña predeterminada es *Password1*. Para la seguridad de los datos, la contraseña predeterminada caduca la primera vez que inicia sesión y deberá cambiar esta contraseña.

También puede utilizar la interfaz de usuario web local o Azure Portal para cambiar la contraseña del administrador de dispositivos en cualquier momento después de que el dispositivo se implemente en un entorno de producción. En este artículo se describe cada uno de estos procedimientos.

 ![hoja de dispositivos](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Uso de Azure Portal para cambiar la contraseña

Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos a través de Azure Portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Para cambiar la contraseña del administrador de dispositivos a través de Azure Portal

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en su nombre y, en la sección **Administración**, haga clic en **Dispositivos**. Se abrirá la hoja **Dispositivos**, que enumera todos los dispositivos de StorSimple Virtual Array.

2. En la hoja **Dispositivos**, haga doble clic en el dispositivo que requiere un cambio de contraseña.

3. En la hoja **Configuración** del dispositivo, haga clic en **Seguridad**.

4. En la hoja **Configuración de seguridad**, realice las siguientes operaciones:
   
   1. Desplácese hacia abajo a la sección **Contraseña de administrador de dispositivos** . Proporcione una contraseña de administrador que tenga entre 8 y 15 caracteres.
   2. Confirme la contraseña.
   3. Haga clic en **Guardar** en la parte superior de la hoja.

La contraseña del administrador de dispositivos ya está actualizada. Puede usar esta contraseña modificada para tener acceso al dispositivo de forma local.

![Hoja Configuración de seguridad](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Uso de la interfaz de usuario web local para cambiar la contraseña

Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos a través de la interfaz de usuario web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para cambiar la contraseña del administrador de dispositivos a través de la interfaz de usuario web local

1. En la interfaz de usuario web local, haga clic en **Mantenimiento**  >  **Cambio de contraseña** para el dispositivo.
   
    ![cambiar password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Escriba la **Contraseña actual**.
3. Introduzca una **Nueva contraseña**. La contraseña debe tener al menos 8 caracteres. Debe contener 3 de 4 de los siguientes requisitos: caracteres en mayúsculas, minúsculas, números y caracteres especiales.
   
    Tenga en cuenta que la contraseña no puede ser la misma que ninguna de las 24 últimas contraseñas.
4. Vuelva a escribir la contraseña para confirmarla.
   
    ![cambiar password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. En la parte inferior de la página, haga clic en **Aplicar**. Se aplica la nueva contraseña. Si el cambio de contraseña no se realiza correctamente, aparece el siguiente error:
   
    ![error de contraseña](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Después de que la contraseña se haya actualizado correctamente, se le notifica. A continuación, puede usar esta contraseña modificada para tener acceso al dispositivo de forma local.


## <a name="next-steps"></a>Pasos siguientes
Obtenga más información para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

