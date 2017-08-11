---
title: "Asignación de redes para la replicación de máquinas virtuales de Hyper-V en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo asignar redes al replicar máquinas virtuales de Hyper-V en un sitio secundario de VMM con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.contentlocale: es-es
ms.lasthandoff: 08/01/2017

---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>Paso 7: Asignación de redes para la replicación de máquinas virtuales de Hyper-V en un sitio secundario


Una vez establecida la [configuración de origen y destino](vmm-to-vmm-walkthrough-source-target.md) para replicar máquinas virtuales de Hyper-V en un sitio de System Center Virtual Machine Manager (VMM) secundario, use este artículo para configurar la asignación de redes para la replicación de máquinas virtuales de Hyper-V en un sitio secundario mediante [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de comenzar

- Aprenda sobre la [asignación de red](vmm-to-vmm-walkthrough-network.md#network-mapping-overview) antes de empezar.
- Compruebe que las máquinas virtuales de los servidores VMM están conectadas a una red de máquina virtual.

## <a name="configure-network-mapping"></a>Configurar asignación de red

1. En **Asignación de red** > **Asignaciones de red**, haga clic en **+Asignación de red**.
2. En la pestaña **Agregar asignación de red** , seleccione los servidores VMM de origen y destino. Se recuperan las redes de VM asociadas a los servidores VMM.
3. En **Red de origen**, seleccione la red que quiere utilizar de la lista de redes de máquina virtual asociadas con el servidor VMM principal.
4. En **Red de destino** , seleccione la red que quiere usar en el servidor VMM secundario. y, a continuación, haga clic en **Aceptar**.

    ![Asignación de red](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

Esto es lo que sucede cuando comienza la asignación de red:

* Todas las máquinas virtuales de réplica existentes que correspondan a la red de VM de origen se conectarán a la red de la VM de destino.
* Las nuevas máquinas virtuales que se conecten a la red de VM de origen se conectarán a la red asignada de destino después de la replicación.
* Si modifica una asignación existente a una nueva red, las máquinas virtuales de réplica se conectarán con la nueva configuración.
* Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.



## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 8: Configuración de una directiva de replicación](vmm-to-vmm-walkthrough-replication.md).
