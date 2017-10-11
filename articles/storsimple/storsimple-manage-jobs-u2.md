---
title: "Visualización y administración de trabajos de StorSimple | Microsoft Docs"
description: "Describe la página Trabajos del servicio Administrador de StorSimple y cómo usarla para realizar un seguimiento de los trabajos de copia de seguridad programados, actuales y recientes."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: cdd3e9e8-7ccd-40a3-8c07-0ab1338c0e59
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 6df1b27ce76de7a781ecc40af8430114d80b20d6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>Usar el servicio de Administrador de StorSimple para ver y administrar trabajos de StorSimple (Update 2)
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Información general
En la página **Trabajos** se ofrece un único portal central para ver y administrar trabajos iniciados en dispositivos conectados a su servicio StorSimple Manager. Puede ver los trabajos programados, en ejecución, completados, cancelados y con error de varios dispositivos. Los resultados se presentan en un formato tabular. 

![Página de trabajos](./media/storsimple-manage-jobs-u2/jobs.png)

Puede buscar rápidamente los trabajos que le interesan filtrando por campos como:

* **Estado** : los trabajos pueden estar en ejecución, programados, con errores, cancelándose o completados con errores.
* **Desde y Hasta** : los trabajos se pueden filtrar según el intervalo de fecha y hora.
* **Tipo** : el tipo de trabajo puede ser copia de seguridad, copia de seguridad manual, restaurar, clonar, conmutación por error de dispositivo, crear volumen anclado localmente, modificar volumen, actualizar, paquete de soporte o aprovisionamiento de dispositivos virtuales.
* **Dispositivos** : los trabajos se inician en un determinado dispositivo conectado a su servicio.
  A continuación, los trabajos filtrados se tabulan en función de los siguientes atributos:
  
  * **Tipo** : copia de seguridad, copia de seguridad manual, restaurar, clonar, conmutación por error de dispositivo, crear volumen anclado localmente, modificar volumen, actualizar, paquete de soporte o aprovisionamiento de dispositivos virtuales.
  * **Estado** : en ejecución, programados, con errores, cancelándose o completados con errores.
  * **Entidad** : los trabajos se pueden asociar a un volumen, una directiva de copia de seguridad o un dispositivo. Por ejemplo, un trabajo de clonación se asocia a un volumen, mientras que un trabajo de copia de seguridad programado está asociado a una directiva de copia de seguridad. Se crea un trabajo de dispositivo como resultado de una recuperación ante desastres (DR) o una operación de restauración.
  * **Dispositivo** : el nombre del dispositivo en el que se inició el trabajo.
  * **Hora de inicio** : la hora a la que se inició el trabajo.
  * **Progreso** : el porcentaje de finalización de un trabajo en ejecución. Para un trabajo completado, este debe equivaler siempre al 100%.

La lista de trabajos se actualiza cada 30 segundos.

Puede realizar las siguientes acciones relacionadas con el trabajo en esta página:

* Ver detalles del trabajo
* Cancelación de un trabajo

## <a name="view-job-details"></a>Ver detalles del trabajo
Realice los pasos siguientes para ver los detalles de cualquier trabajo.

#### <a name="to-view-job-details"></a>Para ver los detalles del trabajo
1. En la página **Trabajos** , para mostrar los trabajos en los que esté interesado, ejecute una consulta con los filtros adecuados. Puede buscar trabajos completados, en ejecución o cancelados.
2. Seleccione un trabajo.
3. En la parte inferior de la página, haga clic en **Detalles**.
4. En el cuadro de diálogo **Detalles del trabajo de copia de seguridad** , puede ver el estado, los detalles, así como las estadísticas de tiempo y de los datos.
   
    ![Página Detalles del trabajo](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Cancelación de un trabajo
Realice los pasos siguientes para cancelar un trabajo en ejecución.

> [!NOTE]
> No se pueden cancelar algunos trabajos, como la modificación de un volumen para cambiar el tipo de volumen o expandir un volumen.
> 
> 

### <a name="to-cancel-a-job"></a>Para cancelar un trabajo
1. En la página **Trabajos** , para mostrar los trabajos en curso que quiere cancelar, ejecute una consulta con los filtros adecuados.
2. Seleccione el trabajo.
3. En la parte inferior de la página, haga clic en **Cancelar**.
4. Cuando se le pida confirmación, haga clic en **Sí**.

Ahora se cancelará este trabajo.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [administrar las directivas de copia de seguridad de StorSimple](storsimple-manage-backup-policies.md).
* Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

