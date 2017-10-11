---
title: "Administración de los registros de control de acceso en StorSimple | Microsoft Docs"
description: "Describe cómo utilizar los registros de control de acceso (ACR) para determinar qué hosts pueden conectarse a un volumen en el dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Usar el servicio de Administrador de StorSimple para administrar registros de control de acceso

## <a name="overview"></a>Información general
Los registros de control de acceso (ACR) le permiten especificar qué hosts pueden conectarse a un volumen del dispositivo StorSimple. Los ACR se establecen en un volumen específico y contienen los nombres calificados iSCSI (IQNs) de los hosts. Cuando un host intenta conectarse a un volumen, el dispositivo comprueba el ACR asociado a ese volumen para el nombre IQN y, a continuación, si hay una coincidencia, se establece la conexión. Los registros de control de acceso de la sección **Configuración** de la hoja del servicio StorSimple Device Manager muestra todos los registros de control de acceso con los IQN correspondientes de los hosts.

Este tutorial explica las siguientes tareas comunes relacionadas con los ACR comunes:

* Agregar un registro de control de acceso
* Editar un registro de control de acceso
* Eliminar un registro de control de acceso

> [!IMPORTANT]
> * Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen.
> * Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.

## <a name="get-the-iqn"></a>Obtención del IQN

Siga estos pasos para obtener el nombre completo del IQN de un host de Windows que ejecute Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Agregar un registro de control de acceso
Use la sección **Configuración** en la hoja del servicio StorSimple Device Manager para agregar los registros de control de acceso. Normalmente, se asociará un ACR a un volumen.

Realice los pasos siguientes para agregar un ACR.

#### <a name="to-add-an-acr"></a>Para agregar un ACR

1. Vaya al servicio StorSimple Device Manager, haga doble clic en el nombre del servicio y, en la sección **Configuración**, haga clic en **Registros de control de acceso**.
2. En la hoja **Registros de control de acceso**, haga clic en **Agregar ACR**.

    ![Hacer clic en Agregar ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. En la hoja **Agregar ACR**, haga lo siguiente:

    1. Proporcione un Nombre para el ACR.
    
    2. Proporcione el nombre IQN del host de Windows Server en **Nombre del iniciador iSCSI (IQN)**.

    3. Haga clic en **Agregar** para crear el ACR.

        ![Hacer clic en Agregar ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  El ACR recién agregado aparecerá en la lista tabular de ACR.

    ![Hacer clic en Agregar ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Editar un registro de control de acceso
Use la sección **Configuración** en la hoja del servicio StorSimple Device Manager para editar los registros de control de acceso.

> [!NOTE]
> Es recomendable que modifique solo los ACR que no están actualmente en uso. Para editar un ACR asociado a un volumen que está actualmente en uso, primero debe establecer el volumen como sin conexión.

Realice los pasos siguientes para editar un ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar un registro de control de acceso
1.  Vaya al servicio StorSimple Device Manager, haga doble clic en el nombre del servicio y, en la sección **Configuración**, haga clic en **Registros de control de acceso**.

    ![Ir a los registros de control de acceso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. En la lista tabular de los registros de control de acceso, haga clic y seleccione el ACR que desea modificar.

    ![Edición de registros de control de acceso](./media/storsimple-8000-manage-acrs/editacr1.png)

3. En la hoja **Editar registro de control de acceso**, proporcione un IQN diferente correspondiente a otro host.

    ![Edición de registros de control de acceso](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Haga clic en **Guardar**. Cuando se le pida confirmación, haga clic en **Sí**. 

    ![Edición de registros de control de acceso](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Se le notificará cuando se actualice el ACR. La lista tabular se actualizará también para reflejar los cambios.

   
## <a name="delete-an-access-control-record"></a>Eliminar un registro de control de acceso
Use la sección **Configuración** en la hoja del servicio StorSimple Device Manager para eliminar los registros de control de acceso.

> [!NOTE]
> Solo puede eliminar esos ACR que no están actualmente en uso. Para eliminar un ACR asociado a un volumen que está actualmente en uso, primero debe establecer el volumen como sin conexión.

Realice los pasos siguientes para eliminar un registro de control de acceso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar un registro de control de acceso
1.  Vaya al servicio StorSimple Device Manager, haga doble clic en el nombre del servicio y, en la sección **Configuración**, haga clic en **Registros de control de acceso**.

    ![Ir a los registros de control de acceso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. En la lista tabular de los registros de control de acceso, haga clic y seleccione el ACR que desea eliminar.

    ![Ir a los registros de control de acceso](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Haga clic con el botón derecho para invocar el menú contextual y seleccione **Eliminar**.

    ![Ir a los registros de control de acceso](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Cuando se le pida confirmación, revise la información y, a continuación, haga clic en **Eliminar**.

    ![Ir a los registros de control de acceso](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Se le notificará cuando se complete la eliminación. La lista tabular se actualizará para reflejar la eliminación.

    ![Ir a los registros de control de acceso](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [administración de volúmenes de StorSimple](storsimple-8000-manage-volumes-u2.md).
* Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

