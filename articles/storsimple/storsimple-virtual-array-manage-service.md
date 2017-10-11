---
title: "Implementación del servicio StorSimple Device Manager | Microsoft Docs"
description: "Aquí encontrará información acerca de cómo crear y eliminar el servicio StorSimple Device Manager en Azure Portal, así como una descripción acerca de cómo administrar la clave de registro del servicio."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Implementación del servicio StorSimple Device Manager en una instancia de StorSimple Virtual Array
## <a name="overview"></a>Información general

El servicio StorSimple Device Manager se ejecuta en Microsoft Azure y se conecta a varios dispositivos de StorSimple. Después de crear el servicio, puede usarlo para administrar los dispositivos desde el Portal de Microsoft Azure, ejecutándolo en un explorador. Esto permite supervisar todos los dispositivos que están conectados al servicio StorSimple Device Manager desde una única ubicación central, reduciendo la carga administrativa.

Las tareas comunes relacionadas con un servicio StorSimple Device Manager son:

* Crear un servicio
* Eliminar un servicio
* Obtener la clave de registro del servicio
* Volver a generar la clave de registro de servicio

Este tutorial describe cómo realizar cada una de las tareas anteriores. La información contenida en este artículo se aplica solo a las matrices virtuales de StorSimple. Para más información acerca de la serie 8000 de StorSimple, vaya a [Implementar el servicio StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Crear un servicio

Para crear un servicio, debe tener:

* Una suscripción con un contrato Enterprise
* Una cuenta de Almacenamiento de Microsoft Azure activa.
* La información de facturación que se usa para la administración de acceso

También puede generar una cuenta de almacenamiento al crear el servicio.

Un único servicio puede administrar varios dispositivos. Sin embargo, un dispositivo no puede abarcar varios servicios. Una gran empresa puede tener varias instancias de servicio para trabajar con distintas suscripciones, organizaciones o incluso las ubicaciones de implementación.

> [!NOTE]
> Necesita instancias separadas del servicio StorSimple Device Manager para administrar instancias de StorSimple Virtual Array y dispositivos de StorSimple de la serie 8000.


Realice los siguientes pasos para crear un servicio.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminar un servicio

Antes de eliminar un servicio, asegúrese de que no lo esté utilizando ningún dispositivo conectado. Si se está utilizando el servicio, desactive los dispositivos conectados. La operación de desactivación eliminará la conexión entre el dispositivo y el servicio, pero conservará los datos del dispositivo en la nube.

> [!IMPORTANT]
> Después de eliminar un servicio, no se puede revertir la operación. Cualquier dispositivo que estaba utilizando el servicio deberá restablecerse a los valores de fábrica para que pueda ser usado con otro servicio. En este escenario, se perderán los datos locales del dispositivo, así como la configuración.
 

Realice los siguientes pasos para eliminar un servicio.

#### <a name="to-delete-a-service"></a>Para eliminar un servicio

1. Vaya a **Todos los recursos**. Busque el servicio StorSimple Device Manager. Seleccione el servicio que desea eliminar.
   
    ![Selección del servicio que se eliminará](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Vaya al panel de servicio para asegurarse de que no hay ningún dispositivo conectado al servicio. Si no hay ningún dispositivo registrado en este servicio, también verá un mensaje en un banner que lo indica. Hacer clic en **Eliminar**.
   
    ![Eliminar servicio](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Cuando se le solicite confirmación, haga clic en **Sí** en la notificación de confirmación. 
   
    ![Confirmación de la eliminación del servicio](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Es posible que el servicio tarde unos minutos en eliminarse. Recibirá una notificación apropiada cuando el servicio se elimine correctamente.
   
    ![Eliminación del servicio correcta](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Se actualizará la lista de servicios.

 ![Lista de servicios actualizada](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Obtener la clave de registro del servicio
Después de haber creado correctamente un servicio, deberá registrar el dispositivo StorSimple con el servicio. Para registrar el primer dispositivo StorSimple, necesitará la clave de registro del servicio. Para registrar dispositivos adicionales con un servicio existente StorSimple, necesitará la clave de registro y la clave de cifrado de datos de servicio (que se genera en el primer dispositivo durante el registro). Para obtener más información acerca de la clave de cifrado de datos de servicio, consulte [Seguridad de StorSimple](storsimple-security.md). Para obtener la clave de registro, acceda a la hoja **Claves** para el servicio.

Realice los pasos siguientes para obtener la clave de registro del servicio.

#### <a name="to-get-the-service-registration-key"></a>Para obtener la clave de registro del servicio
1. En la hoja **StorSimple Device Manager**, vaya a **Administración &gt;** **Claves**.
   
   ![Hoja de claves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. En la hoja **Claves** se muestra una clave de registro del servicio. Copie la clave de registro mediante el icono de copia. 

Mantenga la clave de registro del servicio en una ubicación segura. Necesitará esta clave, así como la clave de cifrado de datos de servicio para registrar dispositivos adicionales con este servicio. Después de obtener la clave de registro del servicio, deberá configurar el dispositivo a través de la interfaz de Windows PowerShell para StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Volver a generar la clave de registro de servicio
Será necesario volver a generar una clave de registro del servicio si es necesario para realizar la rotación de claves o si ha cambiado la lista de administradores de servicios. Cuando se regenera la clave, la nueva clave se utiliza solo para registrar dispositivos posteriores. Los dispositivos que ya se han registrado no se ven afectados por este proceso.

Realice los pasos siguientes para volver a generar una clave de registro de servicio.

#### <a name="to-regenerate-the-service-registration-key"></a>Para volver a generar la clave de registro de servicio
1. En la hoja **StorSimple Device Manager**, vaya a **Administración &gt;** **Claves**.
   
   ![Hoja de claves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. En la hoja **Claves**, haga clic en **Regenerar**.
   
   ![Haga clic en Regenerar](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. En la hoja **Regenerar clave de registro del servicio**, revise la acción necesaria cuando se regeneren las claves. Todos los dispositivos posteriores que están registrados en este servicio usará la nueva clave del registro. Haga clic en **Regenerar** para confirmarlo. Se le notificará una vez completado el registro.
   
   ![Confirmación de la clave regenerada](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Aparecerá una nueva clave de registro de servicio.
   
    ![Confirmación de la clave regenerada](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copie esta clave y guárdela para registrar los dispositivos nuevos con este servicio.

## <a name="next-steps"></a>Pasos siguientes
* Lea una [introducción](storsimple-virtual-array-deploy1-portal-prep.md) a StorSimple Virtual Array.
* Obtenga información sobre cómo [administrar el dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

