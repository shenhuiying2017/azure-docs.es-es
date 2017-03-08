---
title: "Administración de los registros de control de acceso en la matriz virtual de StorSimple | Microsoft Docs"
description: "Describe cómo administrar los registros de control de acceso (ACR) para determinar qué hosts pueden conectarse a un volumen en la matriz virtual de StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 11252938-5b97-4178-8c37-f58eaa3d00b1
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f45d8e2b848e930a790e7bf38c2b09fbdaaf2fab
ms.openlocfilehash: 30b6aedd9b4b7913f6862b34582f3ae96c9eae5f
ms.lasthandoff: 03/01/2017


---
# <a name="use-storsimple-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Uso del servicio StorSimple Manager para administrar los registros de control de acceso para la matriz virtual de StorSimple

## <a name="overview"></a>Información general
Los registros de control de acceso (ACR) le permiten especificar qué hosts pueden conectarse a un volumen de la matriz virtual de StorSimple (también conocido como dispositivo virtual local de StorSimple). Los ACR se establecen en un volumen específico y contienen los nombres calificados iSCSI (IQNs) de los hosts. Cuando un host intenta conectarse a un volumen, el dispositivo comprueba el ACR asociado a ese volumen para el nombre IQN y, a continuación, si hay una coincidencia, se establece la conexión. La sección de **registros de control de acceso** de la página **Configurar** muestra todos los registros de control de acceso con los IQN correspondientes de los hosts.

Este tutorial explica las siguientes tareas comunes relacionadas con los ACR comunes:

* Obtención del IQN
* Agregar un registro de control de acceso 
* Editar un registro de control de acceso 
* Eliminar un registro de control de acceso 

> [!IMPORTANT]
> * Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen. 
> * Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.
> 
> 

## <a name="get-the-iqn"></a>Obtención del IQN
Siga estos pasos para obtener el nombre completo del IQN de un host de Windows que ejecute Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adición de ACR
Use la página **Configuración** del servicio Administrador de StorSimple para agregar ACR. Normalmente, se asociará un ACR a un volumen.

Para más información sobre cómo asociar un ACR a un volumen, vaya a [Agregar un volumen](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen.
> 
> 

Realice los pasos siguientes para agregar un ACR.

#### <a name="to-add-an-acr"></a>Para agregar un ACR
1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configuración** .
   
    ![pestaña Configuración](./media/storsimple-ova-manage-acrs/acr1.png)
2. En la lista tabular de **Registros de control de acceso**, escriba un **Nombre** para el ACR.
3. Proporcione el nombre IQN del host de Windows en **Nombre del iniciador iSCSI**. 
4. Para guardar el ACR recién creado, haga clic en **Guardar** en la parte inferior de la página. Verá el siguiente mensaje de confirmación.
   
    ![mensaje de confirmación](./media/storsimple-ova-manage-acrs/acr2.png)
5. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-ova-manage-acrs/check-icon.png). La lista tabular se actualizará para reflejar esta adición.

## <a name="edit-an-acr"></a>Edición de un ACR
Use la página **Configuración** del Portal de Azure clásico para editar ACR. 

> [!NOTE]
> Debe modificar solo los ACR que no están actualmente en uso. Para editar un ACR asociado a un volumen que está actualmente en uso, el volumen debe estar desconectado.
> 
> 

Realice los pasos siguientes para editar un ACR.

#### <a name="to-edit-an-acr"></a>Para editar un ACR
1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configuración** .
2. En la lista tabular de los registros de control de acceso, pase el ratón sobre el ACR que desea modificar.
3. Proporcione un nombre nuevo y/o el IQN del ACR.
4. Haga clic en **Guardar** en la parte inferior de la página para guardar el ACR modificado. Aparecerá un mensaje de confirmación. 
5. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-ova-manage-acrs/check-icon.png). La lista tabular se actualizará para reflejar este cambio.

## <a name="delete-an-access-control-record"></a>Eliminar un registro de control de acceso
Utilice la página **Configuración** del Portal de Azure clásico para eliminar ACR. 

> [!NOTE]
> * Debe eliminar solo esos ACR que no están actualmente en uso. Para eliminar un ACR asociado a un volumen que está actualmente en uso, el volumen debe estar desconectado.
> * Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.
> 
> 

Realice los pasos siguientes para eliminar un registro de control de acceso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar un registro de control de acceso
1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configuración** .
2. En la lista tabular de los registros de control de acceso (ACR), pase el ratón sobre el ACR que desea eliminar.
3. Aparecerá un icono de eliminación (**x**) en la columna en el extremo derecho para el ACR que seleccione. Haga clic en el icono **x** para eliminar el ACR. Verá el siguiente mensaje de confirmación.
   
    ![mensaje de confirmación](./media/storsimple-ova-manage-acrs/acr3.png)
4. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-ova-manage-acrs/check-icon.png). La lista tabular se actualizará para reflejar la eliminación.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [adición de volúmenes y la configuración de ACR](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).


