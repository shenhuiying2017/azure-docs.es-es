---
title: "Visualización y administración de trabajos de StorSimple Virtual Array | Microsoft Docs"
description: "Describe la página Trabajos del servicio StorSimple Device Manager y cómo usarla para hacer un seguimiento de los trabajos actuales y recientes de StorSimple Virtual Array."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Uso del servicio StorSimple Device Manager para ver los trabajos de StorSimple Virtual Array
## <a name="overview"></a>Información general
La hoja **Trabajos** ofrece un portal central único para ver y administrar los trabajos que se inician en las matrices virtuales y que están conectados al servicio StorSimple Device Manager. Puede ver los trabajos en ejecución, finalizados y con error de varios dispositivos virtuales. Los resultados se presentan en un formato tabular.

![Hoja de trabajos](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Puede buscar rápidamente los trabajos que le interesan filtrando por campos como:

* **Intervalo de tiempo**: los trabajos se pueden filtrar según el intervalo de fecha y hora.
* **Dispositivos** : los trabajos se inician en un dispositivo específico conectado al servicio. A continuación, los trabajos filtrados se presentan en forma de tabla en función de los siguientes atributos:
  
  * **Nombre**: el nombre del trabajo puede ser **Todos**, **Copia de seguridad**, **Clonar**, **Conmutación por error**, **Descargar actualizaciones** o **Instalar actualizaciones**.
  * **Estado**: los trabajos pueden **Todos**, **En curso**, **Correcto**, **Error** o **Cancelado**.
  * **Entidad** : los trabajos pueden estar asociados a un volumen, un recurso compartido o un dispositivo.
  * **Dispositivo** : el nombre del dispositivo en el que se inició el trabajo.
  * **Hora de inicio** : la hora a la que se inició el trabajo.
  * **Duración**: la duración de la ejecución del trabajo.
* **Estado** : puede buscar todos los trabajos o los trabajos en ejecución, completados o con error.
* **Tipo de trabajo**: el tipo de trabajo puede ser todos, copia de seguridad, restauración, conmutación por error, descarga de actualizaciones o instalación de actualizaciones.

La lista de trabajos se actualiza cada 30 segundos.

## <a name="view-job-details"></a>Ver detalles del trabajo
Realice los pasos siguientes para ver los detalles de cualquier trabajo.

#### <a name="to-view-job-details"></a>Para ver los detalles del trabajo
1. En la hoja **Trabajos**, para mostrar los trabajos en los que esté interesado, ejecute una consulta con los filtros adecuados. Puede buscar los trabajos completados o en ejecución.
2. Seleccione un trabajo en la lista tabular de trabajos.
   
    ![Hoja de trabajos](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. En la parte inferior de la página, haga clic en **Detalles**.
4. En el cuadro de diálogo **Detalles** , puede ver el estado, los detalles y las estadísticas de tiempo. La siguiente ilustración muestra un ejemplo del cuadro de diálogo **Detalles del trabajo de copia de seguridad** .
   
    ![Detalles del trabajo](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Errores en el trabajo cuando la máquina virtual está en pausa en el hipervisor
Cuando un trabajo se encuentra en curso en StorSimple Virtual Array y el dispositivo (máquina virtual aprovisionada en hipervisor) se pone en pausa durante más de 15 minutos, se producirá un error en el trabajo. Esto es debido a que la hora de la matriz virtual de StorSimple no está sincronizada con la hora de Microsoft Azure. 

Verá un error que indica que la hora del dispositivo está desincronizada con la hora de Microsoft Azure en más de 15 minutos. Asegúrese de que la hora del hipervisor y del dispositivo están sincronizadas con un servidor NTP. Compruebe que no haya ningún problema de conectividad. Para solucionar problemas de conectividad, ejecute pruebas de diagnóstico desde la interfaz de usuario del dispositivo virtual.

Estos errores se aplicarán a los trabajos de copia de seguridad, restauración, actualización y conmutación por error. Si se aprovisiona la máquina virtual en Hyper-V, la máquina sincronizará finalmente la hora con el hipervisor. Cuando esto ocurra, puede reiniciar el trabajo.

## <a name="next-steps"></a>Pasos siguientes
[Obtenga información sobre cómo usar la interfaz de usuario web local para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

