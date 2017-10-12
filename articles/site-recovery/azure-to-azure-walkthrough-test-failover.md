---
title: "Ejecución de una conmutación por error de prueba para la replicación de VM de Azure con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos necesarios para ejecutar una conmutación por error de prueba para VM de Azure que se replican en otra región de Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>Paso 6: Ejecución de una conmutación por error de prueba para la replicación de VM de Azure

Después de habilitar la replicación de la máquina virtual (VM) de Azure, siga los pasos descritos en este artículo para ejecutar la conmutación por error de una región de Azure a otra mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

- Cuando termine el artículo, también debe haber realizado una comprobación con una conmutación por error de prueba de que al menos una VM de Azure puede conmutar por error la región secundaria de Azure. 
- Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>
> La replicación de VM de Azure se encuentra en una versión preliminar en este momento.


## <a name="before-you-start"></a>Antes de comenzar

- Antes de ejecutar una conmutación por error de prueba, se recomienda comprobar las propiedades del servidor y realizar los cambios necesarios. Puede acceder a las propiedades de la VM en **Elementos replicados**. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.
- Se recomienda usar una red de VM de Azure independiente para la conmutación por error de prueba y no en la red (predeterminada o personalizada) que se configuró para la conmutación por error de producción.
- La conmutación por error de prueba realiza dicha conmutación en VM de Azure (y su almacenamiento) en la región secundaria de Azure. No se replican aplicaciones o recursos dependientes. Si las aplicaciones que se ejecutan en VM con conmutación por error dependen de otros recursos, como Active Directory o DNS, debe replicarlos también si no están disponibles en su región secundaria. [Más información](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- Si desea acceder a VM replicadas después de la conmutación por error desde un sitio local, tiene que [prepararse para conectarse](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) a estas VM.

## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1. En **Configuración** > **Elementos replicados**, haga clic en la VM > icono **+Probar conmutación por error**. 

2. En **Conmutación por error**, seleccione el punto de recuperación que usar para la conmutación por error:

    - **Procesado más recientemente**: error de la VM en el último punto de recuperación procesado por el servicio Site Recovery. Se muestra la marca de tiempo. Con esta opción, no se empleó tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
    - **Más reciente coherente con la aplicación**: esta opción conmuta por error todas las VM en el punto de recuperación más reciente coherente con la aplicación. Se muestra la marca de tiempo. 
    - **Personalizado**: seleccione un punto de recuperación.
 
3. Seleccione la red virtual de Azure de destino a la se conectarán las VM de Azure en la región secundaria después de que se produzca la conmutación por error.
4. Para iniciar la conmutación por error, haga clic en **Aceptar**. Para realizar el seguimiento del progreso, haga clic en la máquina virtual para abrir sus propiedades. También puede hacer clic en el trabajo **Conmutación por error de prueba** en el nombre del almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.
5. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Debe asegurarse de que la VM tiene el tamaño adecuado, que está conectada a la red correspondiente y que se está ejecutando.
6. Para eliminar las máquinas virtuales que se crearon durante la conmutación por error de prueba, haga clic en **Cleanup test failover** (Limpiar conmutación por error de prueba) en el artículo replicado o el plan de recuperación. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba. 

[Más información](site-recovery-test-failover-to-azure.md) sobre las conmutaciones por error de prueba.

## <a name="next-steps"></a>Pasos siguientes

Después de realizar la conmutación por error, este tutorial se habrá completado. Ahora, obtenga información sobre la ejecución de conmutaciones por error en producción:

- [Aprenda más](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.
- Obtenga más información sobre la conmutación por error de varias VM [con un plan de recuperación](site-recovery-create-recovery-plans.md).
- Aprenda sobre el [uso de planes de recuperación](site-recovery-create-recovery-plans.md).
- Más información sobre la [reprotección de máquinas virtuales de Azure](site-recovery-how-to-reprotect.md) después de una conmutación por error.

