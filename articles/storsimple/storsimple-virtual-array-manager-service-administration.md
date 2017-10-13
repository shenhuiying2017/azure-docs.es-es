---
title: "Administración de Microsoft Azure StorSimple Manager Virtual Array | Microsoft Docs"
description: "Obtenga información sobre cómo administrar la matriz virtual local de StorSimple mediante el servicio StorSimple Device Manager en Azure Portal."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Uso del servicio StorSimple Device Manager para administrar StorSimple Virtual Array
![flujo del proceso de instalación](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Información general
En este artículo se describe la interfaz de servicio de StorSimple Device Manager, incluida la forma de conectarse a ella y las diversas opciones disponibles; además, se proporcionan vínculos a los flujos de trabajo específicos que se pueden realizar a través de esta interfaz de usuario.

Después de leer este artículo, aprenderá a:

* Conexión al servicio StorSimple Device Manager
* Navegación por la interfaz de usuario de StorSimple Device Manager
* Administración de StorSimple Virtual Array mediante el servicio StorSimple Device Manager.

> [!NOTE]
> Si desea ver las opciones de administración disponibles para el dispositivo StorSimple de la serie 8000, vaya a [Uso del servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Conexión al servicio StorSimple Device Manager
El servicio StorSimple Device Manager se ejecuta en Microsoft Azure y se conecta a varias instancias de StorSimple Virtual Arrays. Use una instancia de Azure Portal central que se ejecute en un navegador para administrar estos dispositivos. Para conectarse al servicio StorSimple Device Manager, haga lo siguiente.

#### <a name="to-connect-to-the-service"></a>Para conectarse al servicio
1. Vaya a [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Con las credenciales de su cuenta Microsoft, inicie sesión en el Portal de Microsoft Azure (situado en la parte superior derecha del panel).
3. Vaya a Examinar--> Filtro en Administradores de dispositivos de StorSimple para ver todos los administradores de dispositivos de una suscripción determinada.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Uso del servicio StorSimple Device Manager para realizar tareas de administración
En la siguiente tabla se muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden llevarse a cabo en la hoja de resumen del servicio StorSimple Device Manager. Estas tareas se organizan en función de las hojas en que se inician.

Para obtener más información sobre cada flujo de trabajo, haga clic en el procedimiento adecuado en la tabla.

#### <a name="storsimple-device-manager-workflows"></a>Flujos de trabajo de StorSimple Device Manager
| Si desea hacer esto... | Use este procedimiento |
| --- | --- | --- |
| Crear un servicio</br>Eliminar un servicio</br>Obtener la clave de registro del servicio</br>Volver a generar la clave de registro de servicio |[Implementar el servicio StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Visualizar los registros de actividad |[Usar del resumen del servicio StorSimple](storsimple-virtual-array-service-summary.md) |
| Desactivar una matriz virtual</br>Eliminar una matriz virtual |[Desactivar o eliminar una matriz virtual](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Recuperación ante desastres y conmutación por error de dispositivos</br>Requisitos previos de conmutación por error</br>Recuperación ante desastres y continuidad empresarial (BCDR)</br>Errores durante la recuperación ante desastres |[Recuperación ante desastres y conmutación por error de dispositivos para la matriz virtual de StorSimple](storsimple-virtual-array-failover-dr.md) |
| Crear copias de seguridad de los recursos compartidos y volúmenes</br>Creación de una copia de seguridad manual</br>Establecer la programación de copias de seguridad</br>Ver copias de seguridad existentes |[Crear una copia de seguridad de la matriz virtual de StorSimple](storsimple-virtual-array-backup.md) |
| Clonación de recursos compartidos de un conjunto de copias de seguridad</br>Clonación de volúmenes a partir de un conjunto de copias de seguridad</br>Recuperación a nivel de elemento (solo servidor de archivos) |[Clonación a partir de una copia de seguridad de StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Acerca de las cuentas de almacenamiento</br>Agregar una cuenta de almacenamiento</br>Editar una cuenta de almacenamiento</br>Eliminar una cuenta de almacenamiento |[Administrar cuentas de almacenamiento para la matriz virtual de StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Acerca de los registros de control de acceso</br>Agregar o modificar un registro de control de acceso </br>Eliminar un registro de control de acceso |[Administrar registros de control de acceso para la matriz virtual de StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Ver detalles del trabajo |[Administrar trabajos de matriz virtual de StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Configurar alertas</br>Recibir notificaciones de alerta</br>Administrar alertas</br>Revisar alertas |[Ver y administrar alertas para la matriz virtual de StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Modificar la contraseña del administrador de dispositivos |[Cambiar la contraseña del administrador de dispositivos de la matriz virtual de StorSimple](storsimple-virtual-array-change-device-admin-password.md) |
| Instalación de actualizaciones de software |[Actualizar la matriz virtual](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Debe usar la [interfaz de usuario web local](storsimple-ova-web-ui-admin.md) para realizar las tareas siguientes:
> 
> * [Recuperar la clave de cifrado de datos de servicio.](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Crear un paquete de soporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Detener y reiniciar una matriz virtual.](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Si desea obtener información sobre la interfaz de usuario web y cómo usarla, vaya a [Uso de la interfaz de usuario web de StorSimple para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

