---
title: "Visualización y administración de trabajos para la serie StorSimple 8000 | Microsoft Docs"
description: "Describe la hoja Trabajos del servicio StorSimple Device Manager y cómo usarla para realizar un seguimiento de los trabajos de copia de seguridad programados, actuales y recientes."
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
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Uso del servicio StorSimple Device Manager para ver y administrar trabajos (Versión Update 3 o posterior)

## <a name="overview"></a>Información general
En la hoja **Trabajos** se ofrece un único portal central para ver y administrar trabajos iniciados en dispositivos conectados a su servicio StorSimple Device Manager. Puede ver los trabajos programados, en ejecución, completados, cancelados y con error de varios dispositivos. Los resultados se presentan en un formato tabular.

![Hoja de trabajos](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Puede buscar rápidamente los trabajos que le interesan filtrando por campos como:

* **Estado**: los trabajos pueden tener los siguientes estados: en curso, correcto, cancelado, erróneo, cancelando o se completó con errores.
* **Intervalo de tiempo**: los trabajos se pueden filtrar según el intervalo de fecha y hora. Los intervalos de tiempo pueden ser: última hora, últimas 24 horas, últimos 7 días, últimos 30 días, último año y fecha personalizada.
* **Tipo**: los tipos pueden ser: copia de seguridad programada, copia de seguridad manual, restauración de copia de seguridad, clonación de volumen, conmutación por error de contenedores de volúmenes, creación de volúmenes anclados localmente, modificación de volumen, instalación de actualizaciones, recopilación de registros de soporte técnico y creación de dispositivos en la nube.
* **Dispositivos** : los trabajos se inician en un determinado dispositivo conectado a su servicio.
  
A continuación, los trabajos filtrados se tabulan en función de los siguientes atributos:
  
* **Nombre**: copia de seguridad programada, copia de seguridad manual, restauración de copia de seguridad, clonación de volumen, conmutación por error de contenedores de volúmenes, creación de volúmenes anclados localmente, modificación de volumen, instalación de actualizaciones, recopilación de registros de soporte técnico o creación de dispositivos en la nube.
* **Estado** : en ejecución, programados, con errores, cancelándose o completados con errores.
* **Entidad** : los trabajos se pueden asociar a un volumen, una directiva de copia de seguridad o un dispositivo. Por ejemplo, un trabajo de clonación se asocia a un volumen, mientras que un trabajo de copia de seguridad programado está asociado a una directiva de copia de seguridad. Se crea un trabajo de dispositivo como resultado de una recuperación ante desastres (DR) o una operación de restauración.
* **Dispositivo** : el nombre del dispositivo en el que se inició el trabajo.
* **Hora de inicio** : la hora a la que se inició el trabajo.
* **Duración**: el tiempo necesario para completar el trabajo.

La lista de trabajos se actualiza cada 30 segundos.

Puede realizar las siguientes acciones relacionadas con el trabajo en esta página:

* Ver detalles del trabajo
* Cancelación de un trabajo

## <a name="view-job-details"></a>Ver detalles del trabajo
Realice los pasos siguientes para ver los detalles de cualquier trabajo.

#### <a name="to-view-job-details"></a>Para ver los detalles del trabajo
1. Vaya al servicio StorSimple Device Manager y, después, haga clic en **Trabajos**.

2. En la hoja **Trabajos**, para mostrar los trabajos en los que esté interesado, ejecute una consulta con los filtros adecuados. Puede buscar trabajos completados, en ejecución o cancelados.

    ![Hoja de trabajos](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Seleccione y haga clic en un trabajo.

    ![Hoja de trabajos](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. En la hoja Detalles del trabajo, puede ver el estado, los detalles, así como las estadísticas de tiempo y de los datos.
   
    ![Detalles del trabajo](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Cancelación de un trabajo
Realice los pasos siguientes para cancelar un trabajo en ejecución.

> [!NOTE]
> No se pueden cancelar algunos trabajos, como la modificación de un volumen para cambiar el tipo de volumen o expandir un volumen.


### <a name="to-cancel-a-job"></a>Para cancelar un trabajo
1. En la página **Trabajos** , para mostrar los trabajos en curso que quiere cancelar, ejecute una consulta con los filtros adecuados. Seleccione el trabajo.

2. Haga clic con el botón derecho en el trabajo seleccionado para invocar el menú contextual y haga clic en **Cancelar**.

    ![Detalles del trabajo](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Cuando se le pida confirmación, haga clic en **Sí**. Ahora se cancelará este trabajo.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [administrar las directivas de copia de seguridad de StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Aprenda a [usar el servicio StorSimple Device Manager para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

