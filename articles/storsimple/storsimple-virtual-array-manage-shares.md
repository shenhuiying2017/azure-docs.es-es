---
title: "Administración de recursos compartidos de StorSimple Virtual Array | Microsoft Docs"
description: "Describe el servicio StorSimple Device Manager y explica cómo usarlo para administrar recursos compartidos en StorSimple Virtual Array."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Uso del servicio StorSimple Device Manager para administrar recursos compartidos en StorSimple Virtual Array

## <a name="overview"></a>Información general

Este tutorial explica cómo usar el servicio StorSimple Device Manager para crear y administrar recursos compartidos en StorSimple Virtual Array.

El servicio StorSimple Device Manager es una extensión de Azure Portal que permite administrar la solución StorSimple desde una única interfaz web. Además de administrar volúmenes y recursos compartidos, puede usar el servicio StorSimple Device Manager para ver y administrar dispositivos, ver alertas, y administrar directivas de copia de seguridad y el catálogo de copias de seguridad.

## <a name="share-types"></a>Tipos de recursos compartidos

Los recursos compartidos de StorSimple pueden ser de uno de los siguientes tipos:

* **Anclado localmente**: los datos de estos recursos compartidos se mantienen en la matriz en todo momento y no se vuelcan en la nube.
* **En capas**: los datos de estos recursos compartidos pueden volcarse en la nube. Cuando se crea un recurso compartido en capas, el 10 % del espacio se aprovisiona en la capa local y el 90 % del espacio se aprovisiona en la nube. Por ejemplo, si se aprovisiona un recurso compartido de 1 TB, 100 GB residirían en el espacio local y 900 GB se utilizarían en la nube al apilar los datos. A su vez, esto hace que, si agota todo el espacio local en el dispositivo, no se podrá aprovisionar un recurso compartido en capas (porque el 10 % necesario de la capa local no estará disponible).

### <a name="provisioned-capacity"></a>Capacidad aprovisionada

Para conocer la capacidad máxima aprovisionada de cada tipo de recurso compartido, consulte la siguiente tabla.

| **Identificador de límites** | **Límite** |
| --- | --- |
| Tamaño mínimo de un recurso compartido en capas |500 GB |
| Tamaño máximo de un recurso compartido en capas |20 TB |
| Tamaño mínimo de un recurso compartido anclado localmente |50 GB |
| Tamaño máximo del recurso compartido anclado localmente |2 TB |

## <a name="the-shares-blade"></a>Hoja Recursos compartidos

El menú **Recursos compartidos** de la hoja de resumen del servicio StorSimple muestra la lista de recursos compartidos de almacenamiento en una matriz de StorSimple dada y permite administrarlos.

![Hoja Recursos compartidos](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Un recurso compartido se compone de una serie de atributos:

* **Nombre del recurso compartido**: nombre descriptivo que debe ser único y que ayuda a identificar el recurso compartido.
* **Estado** : puede estar conectado o desconectado. Si un recurso compartido está desconectado, sus usuarios no podrán acceder a él.
* **Tipo**: indica si el tipo del recurso compartido es **En capas** (predeterminado) o **Anclado localmente**.
* **Capacidad**: especifica la cantidad de datos utilizados en comparación con la cantidad total de datos que se puede almacenar en el recurso compartido.
* **Descripción**: una configuración opcional que ayuda a describir el recurso compartido.
* **Permisos**: permisos NTFS para el recurso compartido que se pueden administrar mediante el Explorador de Windows.
* **Copia de seguridad**: en el caso de StorSimple Virtual Array, se habilitan automáticamente todos los recursos compartidos para la copia de seguridad.

![Detalles del recurso compartido](./media/storsimple-virtual-array-manage-shares/share-details.png)

Siga las instrucciones de este tutorial para realizar las siguientes tareas:

* Agregar un recurso compartido
* Modificación de un recurso compartido
* Desconexión de un recurso compartido
* Eliminación de un recurso compartido

## <a name="add-a-share"></a>Agregar un recurso compartido

1. En la hoja de resumen del servicio StorSimple, haga clic en **+ Agregar recurso compartido**, en la barra de comandos. Se abrirá la hoja **Agregar recurso compartido**.

    ![Incorporación de un recurso compartido](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. En la hoja **Agregar recurso compartido**, haga lo siguiente:
   
    1. En el campo **Nombre del recurso compartido**, escriba un nombre único para el recurso compartido. El nombre debe ser una cadena que contenga entre 3 y 127 caracteres.

    2. **Descripción** opcional del recurso compartido. La descripción ayudará a identificar a los propietarios del recurso compartido.

    3. En la lista desplegable **Tipo**, especifique si desea crear un recurso compartido **En capas** o **Anclado localmente**. Para las cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione **Recurso compartido anclado localmente**. Para todos los demás datos, seleccione **Recurso compartido en capas**.

    4. En el campo **Capacidad** especifique el tamaño del recurso compartido. Un recurso compartido en capas debe tener entre 500 GB y 20 TB, y uno anclado localmente debe tener entre 50 GB y 2 TB.

    5. En el campo **Set default full permissions to** (Establecer permisos totales predeterminados en), asigne los permisos al usuario o al grupo que va a acceder a este recurso compartido. Especifique el nombre del usuario o del grupo de usuarios en formato _john@contoso.com_. Se recomienda que utilice un grupo de usuarios (en lugar de un único usuario) para otorgar los privilegios de administrador para tener acceso a estos recursos compartidos. Después de haber asignado los permisos aquí, puede utilizar el Explorador de archivos para modificarlos.
3. Cuando haya terminado de configurar el recurso compartido, haga clic en **Crear**. Se creará un recurso compartido con la configuración especificada y verá una notificación. De forma predeterminada, se habilitará la copia de seguridad para el recurso compartido.
4. Para confirmar que el recurso compartido se creó correctamente, vaya a la hoja **Recursos compartidos**. Debería ver el recurso compartido en la lista.
   
    ![Creación de un recurso compartido correcta](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificación de un recurso compartido

Modifique un recurso compartido cuando necesite cambiar la descripción del recurso compartido. Ninguna otra propiedad del recurso compartido puede modificarse una vez creado.

#### <a name="to-modify-a-share"></a>Para modificar un recurso compartido

1. En la configuración **Recursos compartidos** de la hoja de resumen del servicio StorSimple, seleccione la matriz virtual en la que reside el recurso compartido que se desea modificar.
2. **Seleccione** el recurso compartido para ver su descripción actual y modificarla.
3. Guarde los cambios haciendo clic en **Guardar** en la barra de comandos. Se aplicará la configuración especificada y verá una notificación.
   
    ![ Editar recurso compartido](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Desconexión de un recurso compartido

Puede que necesite desconectar un recurso compartido si tiene previsto modificarlo o eliminarlo. Cuando un recurso compartido está desconectado, no está disponible para el acceso de lectura y escritura. Deberá desconectar el recurso compartido en el host, así como en el dispositivo.

#### <a name="to-take-a-share-offline"></a>Para desconectar un recurso compartido

1. Asegúrese de que el recurso compartido no está en uso antes de desconectarlo.
2. Para desconectar el recurso compartido de la matriz, realice los pasos siguientes:
   
    1. En la configuración **Recursos compartidos** de la hoja de resumen del servicio StorSimple, seleccione la matriz virtual en la que reside el recurso compartido que se desea desconectar.

    2. **Seleccione** el recurso compartido y haga clic en **...** (o bien haga clic con el botón derecho en esta fila) y, en el menú contextual, seleccione **Desconectar**.
     
        ![Recurso compartido sin conexión](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Revise la información de la hoja **Desconectar** y confirme la aceptación de la operación. Haga clic en **Desconectar** para desconectar el recurso compartido. Verá una notificación que le indica que la operación está en curso.

    4. Para confirmar que el recurso compartido se desconectó correctamente, vaya a la hoja **Recursos compartidos**. El recurso compartido debe aparecer como desconectado.

## <a name="delete-a-share"></a>Eliminación de un recurso compartido

> [!IMPORTANT]
> Puede eliminar un recurso compartido solo si está desconectado.


Siga estos pasos para eliminar un recurso compartido.

#### <a name="to-delete-a-share"></a>Para eliminar un recurso compartido

1. En la configuración **Recursos compartidos** de la hoja de resumen del servicio StorSimple, seleccione la matriz virtual en la que reside el recurso compartido que se desea eliminar.
2. **Seleccione** el recurso compartido y haga clic en **...** (o bien haga clic con el botón derecho en esta fila) y, en el menú contextual, seleccione **Eliminar**.
   
    ![Eliminación del recurso compartido](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Compruebe el estado del recurso compartido que desea eliminar. Si el recurso compartido que desea eliminar no está desconectado, desconéctelo primero. Siga los pasos de [Desconexión de un recurso compartido](#take-a-share-offline).
4. Cuando se le pida confirmación en la hoja **Eliminar**, acepte la confirmación y haga clic en **Eliminar**. El recurso compartido se eliminará y la hoja **Recursos compartidos** mostrará la lista actualizada de recursos compartidos en la matriz virtual.

## <a name="next-steps"></a>Pasos siguientes
Aprenda cómo [clonar un recurso compartido de StorSimple](storsimple-virtual-array-clone.md).

