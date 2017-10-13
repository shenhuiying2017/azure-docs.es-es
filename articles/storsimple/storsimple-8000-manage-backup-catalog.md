---
title: "Administración del catálogo de copias de seguridad de StorSimple | Microsoft Docs"
description: "Explica cómo usar la página del catálogo de copias de seguridad del servicio StorSimple Device Manager para enumerar, seleccionar y eliminar conjuntos de copias de seguridad."
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Uso del servicio StorSimple Device Manager para administrar su catálogo de copias de seguridad
## <a name="overview"></a>Información general
En la hoja **Catálogo de copias de seguridad** del servicio StorSimple Device Manager se muestran todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o programadas. Puede usar esta página para enumerar todas las copias de seguridad para un volumen o una directiva de copia de seguridad, seleccionar o eliminar las copias de seguridad, o usar una copia de seguridad para restaurar o clonar un volumen.

En este tutorial se explica cómo enumerar, seleccionar y eliminar un conjunto de copia de seguridad. Para obtener información sobre cómo restaurar su dispositivo de copia de seguridad, vaya a [Restaurar el dispositivo desde un conjunto de copia de seguridad](storsimple-8000-restore-from-backup-set-u2.md). Para aprender cómo clonar un volumen, vaya a [Clonar un volumen de StorSimple](storsimple-8000-clone-volume-u2.md).

![Catálogo de Backup](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

La hoja **Catálogo de copias de seguridad** proporciona una consulta para restringir la selección de conjuntos de copias de seguridad. Puede filtrar los conjuntos de copias de seguridad que se recuperan en función de los parámetros siguientes:

* **Dispositivo** : dispositivo en el que se creó el conjunto de copias de seguridad.
* **Directiva de copia de seguridad o volumen**: directiva de copia de seguridad o volumen asociado a este conjunto de copia de seguridad.
* **De y A** : el intervalo de fecha y hora en el que se creó el conjunto de copia de seguridad.

A continuación, los conjuntos de copias de seguridad filtrados se presentan en forma de tabla en función de los siguientes atributos:

* **Nombre** : nombre de la directiva de copias de seguridad o del volumen asociado al conjunto de copias de seguridad.
* **Tamaño** : tamaño real del conjunto de copias de seguridad.
* **Creada en** : Fecha y hora en que se crearon las copias de seguridad. 
* **Tipo** : los conjuntos de copias de seguridad pueden ser instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos del volumen que se almacenan localmente en el dispositivo, mientras que una instantánea en la nube hace referencia a la copia de seguridad de los datos del volumen que residen en la nube. Las instantáneas locales proporcionan un acceso más rápido, mientras que las instantáneas en la nube son preferibles para la resistencia de los datos.
* **Iniciada por** : las copias de seguridad se pueden iniciar automáticamente por una programación o manualmente por el usuario. Puede usar una directiva de copia de seguridad para programar copias de seguridad. Como alternativa, puede usar la opción **Realizar copia de seguridad** para crear una copia de seguridad manual.

## <a name="list-backup-sets-for-a-backup-policy"></a>Mostrar conjuntos de copia de seguridad para una directiva de copia de seguridad
Complete los pasos siguientes para enumerar todas las copias de seguridad para una directiva de copia de seguridad.

#### <a name="to-list-backup-sets"></a>Para enumerar los conjuntos de copia de seguridad
1. Vaya al servicio StorSimple Device Manager y haga clic en **Catálogo de copias de seguridad**.

2. Filtre las selecciones de la siguiente manera:
   
   1. Especifique el intervalo de tiempo.
   2. Seleccione el dispositivo adecuado.
   3. Filtre por **Directiva de copia de seguridad** para ver las copias de seguridad correspondientes.
   3. En la lista desplegable de directivas de copia de seguridad, elija **Todas** para ver todas las copias de seguridad del dispositivo seleccionado.
   4. Haga clic en **Aplicar** para ejecutar esta consulta.
      
      Las copias de seguridad asociadas a la directiva de copia de seguridad seleccionada deben aparecer en la lista de conjuntos de copias de seguridad.

      ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Seleccionar un conjunto de copia de seguridad
Complete los pasos siguientes para seleccionar un conjunto de copias de seguridad para un volumen o una directiva de copia de seguridad.

#### <a name="to-select-a-backup-set"></a>Para seleccionar un conjunto de copia de seguridad
1. Vaya al servicio StorSimple Device Manager y haga clic en **Catálogo de copias de seguridad**.
2. Filtre las selecciones de la siguiente manera:
   
   1. Especifique el intervalo de tiempo. 
   2. Seleccione el dispositivo adecuado. 
   3. Filtre por el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.
   4. Haga clic en **Aplicar** para ejecutar esta consulta.
      
      Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.

      ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecciona y expanda un conjunto de copia de seguridad. Ahora ya puede ver los conjuntos de copia de seguridad desglosados por los volúmenes que contienen. Las opciones **Restaurar** y **Eliminar** están disponibles mediante el menú contextual (haga clic con el botón derecho) del conjunto de copia de seguridad. Puede realizar cualquiera de estas acciones en el conjunto de copia de seguridad que haya seleccionado.

    ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Eliminación de un conjunto de copia de seguridad
Elimine una copia de seguridad cuando ya no desee conservar los datos asociados a ella. Realice los siguientes pasos para eliminar un conjunto de copia de seguridad.

#### <a name="to-delete-a-backup-set"></a>Para eliminar un conjunto de copia de seguridad
 Vaya al servicio StorSimple Device Manager y haga clic en **Catálogo de copias de seguridad**.
2. Filtre las selecciones de la siguiente manera:
   
   1. Especifique el intervalo de tiempo. 
   2. Seleccione el dispositivo adecuado. 
   3. Filtre por el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.
   4. Haga clic en **Aplicar** para ejecutar esta consulta.
      
      Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.

      ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecciona y expanda un conjunto de copia de seguridad. Ahora ya puede ver los conjuntos de copia de seguridad desglosados por los volúmenes que contienen. Las opciones **Restaurar** y **Eliminar** están disponibles mediante el menú contextual (haga clic con el botón derecho) del conjunto de copia de seguridad. Haga clic con el botón derecho en el conjunto de copia de seguridad seleccionado y, en el menú contextual, seleccione **Eliminar**.

    ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Cuando se le pida confirmación, revise la información que aparece y haga clic en **Eliminar**. La copia de seguridad seleccionada se eliminará definitivamente.

    ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Se le notificará cuando la eliminación esté en curso y cuando haya finalizado correctamente. Cuando finalice la eliminación, actualice la consulta en esta página. El conjunto de copia de seguridad eliminado ya no aparecerá en la lista de conjuntos de copia de seguridad.

    ![Ir al catálogo de copia de seguridad](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo usar el catálogo de copias de seguridad para [restaurar el dispositivo desde un conjunto de copias de seguridad](storsimple-8000-restore-from-backup-set-u2.md).
* Aprenda a [usar el servicio StorSimple Device Manager para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

