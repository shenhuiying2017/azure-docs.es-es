---
title: "Habilitación de la replicación de Hyper-V en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo habilitar la replicación para máquinas virtuales de Hyper-V en un sitio secundario de System Center VMM con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>Paso 9: Habilitación de la replicación en un sitio secundario para máquinas virtuales de Hyper-V


Después de configurar una directiva de replicación, use este artículo para habilitar la replicación en un sitio secundario de System Center Virtual Machine Manager (VMM) para máquinas virtuales locales de Hyper-V mediante [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="select-vms-to-replicate"></a>Seleccionar las máquinas virtuales que se van a replicar

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**. 

    ![Habilitar replicación](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. En la hoja **Origen**, seleccione el servidor VMM y la nube donde se encuentran los hosts de Hyper-V que quiere replicar. y, a continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. En **Destino**, compruebe el servidor VMM y la nube secundarios.
4. En **Máquinas virtuales**, seleccione las máquinas virtuales que quiere proteger de la lista.

    ![Habilitar protección de máquina virtual](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

Puede hacer un seguimiento del progreso de la acción **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. Una vez concluido el trabajo **Finalizar protección**, la replicación inicial finaliza y la máquina virtual está preparada para la conmutación por error.

Observe lo siguiente:

- También puede habilitar la protección de las máquinas virtuales en la consola VMM. Haga clic en la opción **Habilitar protección** de la barra de herramientas de las propiedades de la máquina virtual > pestaña **Azure Site Recovery**.
- Después de habilitar la replicación, puede ver las propiedades de la máquina virtual en **Elementos replicados**. En el panel de **información básica**, puede ver información sobre la directiva de replicación de la máquina virtual y su estado. Haga clic en **Propiedades** para obtener más información.

## <a name="onboard-existing-vms"></a>Incorporar máquinas virtuales existentes

Si tiene máquinas virtuales en VMM que se replican mediante Réplica de Hyper-V, puede incorporarlas a la replicación de Azure Site Recovery de la forma siguiente:

1. Asegúrese de que el servidor Hyper-V que hospeda la máquina virtual existente se encuentra en la nube principal y que el servidor de Hyper-V que hospeda la máquina virtual de réplica se encuentra en la nube secundaria.
2. Asegúrese de que hay una directiva de replicación configurada para la nube VMM principal.
3. Habilite la replicación para la máquina virtual principal. Azure Site Recovery y VMM garantizan que se detecta el mismo host de réplica y la misma máquina virtual, y Azure Site Recovery reutilizará y restablecerá la replicación mediante los valores configurados.


## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 10: Ejecución de una conmutación por error de prueba](vmm-to-vmm-walkthrough-test-failover.md).
