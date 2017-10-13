---
title: "Administración de volúmenes en StorSimple Virtual Array | Microsoft Docs"
description: "Describe el servicio StorSimple Device Manager y explica cómo usarlo para administrar volúmenes en StorSimple Virtual Array."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Uso del servicio StorSimple Device Manager para administrar volúmenes en StorSimple Virtual Array

## <a name="overview"></a>Información general

Este tutorial explica cómo usar el servicio StorSimple Device Manager para crear y administrar volúmenes en StorSimple Virtual Array.

El servicio StorSimple Device Manager es una extensión de Azure Portal que permite administrar la solución StorSimple desde una única interfaz web. Además de administrar volúmenes y recursos compartidos, puede usar el servicio de StorSimple Device Manager para ver y administrar dispositivos, ver alertas, y ver y administrar directivas de copia de seguridad y el catálogo de copias de seguridad.

## <a name="volume-types"></a>Tipos de volúmenes

Los volúmenes de StorSimple pueden ser:

* **Anclado localmente**: los datos de estos volúmenes se mantienen en la matriz en todo momento y no se vuelcan en la nube.
* **En capas**: los datos de estos volúmenes pueden volcarse en la nube. Cuando se crea un volumen en capas, aproximadamente el 10 % del espacio se aprovisiona en la capa local y el 90 % restante en la nube. Por ejemplo, si se aprovisiona un volumen de 1 TB, 100 GB residirían en el espacio local y 900 GB se utilizarían en la nube cuando se apilen los datos. A su vez, esto hace que, si agota todo el espacio local en el dispositivo, no se podrá aprovisionar un volumen en capas (porque el 10 % necesario de la capa local no estará disponible).

### <a name="provisioned-capacity"></a>Capacidad aprovisionada
Para conocer la capacidad máxima aprovisionada de cada tipo de volumen, consulte la siguiente tabla.

| **Identificador de límites**                                       | **Límite**     |
|------------------------------------------------------------|---------------|
| Tamaño mínimo de un volumen en capas                            | 500 GB        |
| Tamaño máximo de un volumen en capas                            | 5 TB          |
| Tamaño mínimo de un volumen anclado localmente                    | 50 GB         |
| Tamaño máximo de un volumen anclado localmente                    | 500 GB        |

## <a name="the-volumes-blade"></a>La hoja Volúmenes
El menú **Volúmenes** de la hoja de resumen del servicio StorSimple muestra la lista de volúmenes de almacenamiento en una matriz dada de StorSimple y permite administrarlos.

![Hoja Volúmenes](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Un volumen se compone de una serie de atributos:

* **Nombre** : nombre descriptivo que debe ser único y que ayuda a identificar el volumen.
* **Estado** : puede estar conectado o desconectado. Si un volumen está desconectado, no es visible para los iniciadores (servidores) que tienen permitido el acceso para usar el volumen.
* **Tipo**: indica si el tipo del volumen es **En capas** (predeterminado) o **Anclado localmente**.
* **Capacidad**: especifica la cantidad de datos utilizados en comparación con la cantidad total de datos que puede almacenar el iniciador (servidor).
* **Copia de seguridad**: en el caso de StorSimple Virtual Array, se habilitan automáticamente todos los volúmenes para la copia de seguridad.
* **Hosts conectados**: especifica los iniciadores (servidores) que pueden tener acceso a este volumen.

![Detalles de volúmenes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Siga las instrucciones de este tutorial para realizar las siguientes tareas:

* Agregar un volumen
* Modificar un volumen
* Desconectar un volumen
* Eliminar un volumen

## <a name="add-a-volume"></a>Agregar un volumen

1. En la hoja de resumen del servicio StorSimple, haga clic en **+ Agregar volumen** desde la barra de comandos. Con ello, se abrirá la hoja **Agregar volumen**.
   
    ![Agregar volumen](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. En la hoja **Agregar volumen**, haga lo siguiente:
   
   * En el campo **Nombre del volumen**, escriba un nombre único para el volumen. El nombre debe ser una cadena que contenga entre 3 y 127 caracteres.
   * En la lista desplegable **Tipo**, especifique si desea crear un volumen **en capas** o **anclado localmente**. Para las cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione **Volumen anclado localmente**. Para todos los demás datos, seleccione volumen **en capas**.
   * En el campo **Capacidad**, especifique el tamaño del volumen. Un volumen en capas debe tener entre 500 GB y 5 TB, mientras que un volumen anclado localmente debe oscilar entre 50 GB y 500 GB.
   * * Haga clic en **Hosts conectados**, seleccione un registro de control de acceso (ACR) correspondiente al iniciador iSCSI que desea conectar a este volumen y, a continuación, haga clic en **Seleccionar**.
3. Para agregar un nuevo host conectado, haga clic en **Agregar nuevo**, escriba un nombre para el host y su nombre calificado iSCSI (IQN) y, a continuación, haga clic en **Agregar**.
   
    ![Agregar volumen](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Cuando haya terminado de configurar el volumen, haga clic en **Crear**. Se creará un volumen con la configuración especificada y verá una notificación al finalizar correctamente la creación de este. De forma predeterminada, se habilitará la copia de seguridad para el volumen.
5. Para confirmar que el volumen se creó correctamente, vaya a la hoja **Volúmenes**. Debería ver el volumen mostrado.
   
    ![Creación de volumen correcta](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificar un volumen

Modifique un volumen cuando necesite cambiar los hosts que tienen acceso al volumen. No se podrán modificar los demás atributos de un volumen una vez que se haya creado este.

#### <a name="to-modify-a-volume"></a>Para modificar un volumen

1. Desde el valor **Volúmenes** de la hoja de resumen del servicio StorSimple, seleccione la matriz virtual en la que reside el volumen que se desea modificar.
2. **Seleccione** el volumen y haga clic en **Hosts conectados** para ver el host conectado actualmente y modifíquelo en un servidor diferente.
   
    ![Modificación del volumen](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Guarde los cambios haciendo clic en **Guardar** en la barra de comandos. Se aplicará la configuración especificada y verá una notificación.

## <a name="take-a-volume-offline"></a>Desconectar un volumen

Puede que necesite desconectar un volumen si tiene previsto modificarlo o eliminarlo. Cuando un volumen está desconectado, no está disponible para el acceso de lectura y escritura. Deberá desconectar el volumen en el host, así como en el dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para desconectar un volumen

1. Asegúrese de que el volumen en cuestión no está en uso antes de desconectarlo.
2. Desconecte el volumen primero en el host. Esto elimina cualquier riesgo potencial de dañar los datos del volumen. Para conocer los pasos específicos, consulte las instrucciones del sistema operativo del host.
3. Una vez desconectado el volumen del host, desconecte el volumen de la matriz siguiendo estos pasos:
   
   * Desde el valor **Volúmenes** de la hoja de resumen del servicio StorSimple, seleccione la matriz virtual en la que reside el volumen que desea desconectar.
   * **Seleccione** el volumen y haga clic en **...** (o bien haga clic derecho en esta fila) y en el menú contextual, seleccione **Desconectar**.
     
        ![Volumen desconectado](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Revise la información de la hoja **Desconectar** y confirme la aceptación de la operación. Haga clic en **Desconectar** para desconectar el volumen. Verá una notificación que le indica que la operación está en curso.
   * Para confirmar que el volumen se desconectó correctamente, vaya a la hoja **Volúmenes**. El volumen debe aparecer como desconectado.
     
       ![Confirmación de volumen desconectado](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Eliminar un volumen

> [!IMPORTANT]
> Puede eliminar un volumen solo si está desconectado.
> 
> 

Siga estos pasos para eliminar un volumen.

#### <a name="to-delete-a-volume"></a>Para eliminar un volumen

1. Desde el valor **Volúmenes** de la hoja de resumen del servicio StorSimple, seleccione la matriz virtual en la que reside el volumen que se desea eliminar.
2. **Seleccione** el volumen y haga clic en **...** (o bien haga clic derecho en esta fila) y en el menú contextual, seleccione **Eliminar**.
   
    ![Eliminar volumen](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Compruebe el estado del volumen que desea eliminar. Si el volumen que desea eliminar no está desconectado, desconéctelo primero siguiendo los pasos indicados en [Desconectar un volumen](#take-a-volume-offline).
4. Cuando se le pida confirmación en la hoja **Eliminar**, acepte la confirmación y haga clic en **Eliminar**. El volumen se eliminará y la hoja **Volúmenes** mostrará la lista actualizada de volúmenes dentro de la matriz virtual.

## <a name="next-steps"></a>Pasos siguientes

Aprenda cómo [clonar un volumen de StorSimple](storsimple-virtual-array-clone.md).

