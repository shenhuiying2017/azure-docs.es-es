---
title: "Administración de los registros de control de acceso para StorSimple Virtual Array | Microsoft Docs"
description: "Describe cómo administrar los registros de control de acceso (ACR) para determinar qué hosts pueden conectarse a un volumen en la matriz virtual de StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Uso de StorSimple Device Manager para administrar los registros de control de acceso para StorSimple Virtual Array

## <a name="overview"></a>Información general

Los registros de control de acceso (ACR) le permiten especificar qué hosts pueden conectarse a un volumen de la matriz virtual de StorSimple (también conocido como dispositivo virtual local de StorSimple). Los ACR se establecen en un volumen específico y contienen los nombres calificados iSCSI (IQNs) de los hosts. Cuando un host intenta conectarse a un volumen, el dispositivo comprueba el ACR asociado a ese volumen para el nombre IQN y, a continuación, si hay una coincidencia, se establece la conexión. La hoja **Registros de control de acceso** de la sección **Configuración** del servicio Device Manager muestra todos los registros de control de acceso con los IQN correspondientes de los hosts.

![Administrar registros de control de acceso](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Este tutorial explica las siguientes tareas comunes relacionadas con los ACR comunes:

* Obtención del IQN
* Agregar un registro de control de acceso
* Editar un registro de control de acceso
* Eliminar un registro de control de acceso

> [!IMPORTANT]
> 
> * Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen.
> * Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.


## <a name="get-the-iqn"></a>Obtención del IQN

Siga estos pasos para obtener el nombre completo del IQN de un host de Windows que ejecute Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adición de ACR

La hoja **Registros de control de acceso** de la sección **Configuración** de su servicio StorSimple Device Manager se usa para agregar ACR. Normalmente, se asociará un ACR a un volumen.

Para más información sobre cómo asociar un ACR a un volumen, vaya al paso para [agregar un volumen](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen.


Realice los pasos siguientes para agregar un ACR.

#### <a name="to-add-an-acr"></a>Para agregar un ACR

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en su nombre y, en la sección **Configuración**, haga clic en **Registros de control de acceso**.
2. En la hoja **Registros de control de acceso**, haga clic en **Agregar**.
3. En la hoja **Agregar ACR**, haga lo siguiente:
   
    1. Proporcione un **Nombre** para el ACR.
    
    2. Proporcione el nombre IQN del host de Windows en **Nombre del iniciador iSCSI**. Para obtener el IQN del host de Windows Server, haga lo siguiente:
   
    3. Inicie el iniciador iSCSI de Microsoft en el host de Windows. En la ventana Propiedades de Iniciador iSCSI, en la pestaña **Configuración**, seleccione y copie la cadena desde el campo **Nombre de iniciador**.
    Pegue esta cadena en el campo **IQN** de la hoja **Agregar ACR**.
   
    6. Haga clic en **Agregar** para agregar el ACR.  
   
        ![Incorporación de registros de control de acceso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. La lista tabular se actualizará para reflejar esta adición.

## <a name="edit-an-acr"></a>Edición de un ACR

La hoja **Registros de control de acceso** de la sección **Configuración** de su servicio StorSimple Device Manager se usa en Azure Portal para editar los ACR.

> [!NOTE]
> No debe modificar un ACR que esté en uso. Para editar un ACR asociado a un volumen que está actualmente en uso, el volumen debe estar desconectado.


Realice los pasos siguientes para editar un ACR.

#### <a name="to-edit-an-acr"></a>Para editar un ACR

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en su nombre y, en la sección **Configuración**, en **Registros de control de acceso**.
2. En la hoja **Registros de control de acceso** de la lista tabular de los registros de control de acceso, haga doble clic en el ACR que desea modificar.
3. En la hoja **Editar registro de control de acceso**, haga lo siguiente:
   
    1. Proporcione el IQN para el ACR.
   
    2. Haga clic en **Guardar** en la parte superior de la hoja para guardar el ACR modificado. Verá el siguiente mensaje de confirmación:
   
        ![Edición de registros de control de acceso](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Eliminar un registro de control de acceso

Utilice la página **Configuración** de Azure Portal para eliminar los ACR.

> [!NOTE]
> 
> * No debe eliminar un ACR que esté en uso. Para eliminar un ACR asociado a un volumen que está actualmente en uso, el volumen debe estar desconectado.
> * Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.


Realice los pasos siguientes para eliminar un registro de control de acceso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar un registro de control de acceso

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en su nombre y, en la sección **Configuración**, en **Registros de control de acceso**.

2. En la hoja **Registros de control de acceso** de la lista tabular de los registros de control de acceso, haga doble clic en el ACR que desea eliminar.

3. En la hoja Editar registro de control de acceso, haga clic en **Eliminar**.
   
    ![Eliminación de ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Cuando se le pida confirmación, haga clic en **Eliminar** para continuar con la eliminación. La lista tabular se actualizará para reflejar la eliminación.
   
   ![Mensaje de advertencia](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre la [adición de volúmenes y la configuración de ACR](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

