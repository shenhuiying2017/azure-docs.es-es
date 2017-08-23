---
title: "Configurar la asignación de red para la replicación de máquinas virtuales de Hyper-V en nubes de VMM en Azure con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo configurar la asignación de red al replicar máquinas virtuales de Hyper-V en nubes de VMM en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>Paso 9: Configuración de la asignación de red para la replicación de Hyper-V (con VMM) en Azure

Después de configurar los [valores de replicación de origen y de destino](vmm-to-azure-walkthrough-source-target.md), use este artículo para configurar la asignación de red para efectuar asignaciones entre las redes de máquina virtual de VMM locales y las redes de Azure.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de comenzar

- Obtenga información sobre la [asignación de redes](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- [Prepare VMM para la asignación de red](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- Compruebe que las máquinas virtuales del servidor VMM están conectadas a una red de máquinas virtuales y que ha creado al menos una red virtual de Azure. Se pueden asignar varias redes de VM a una sola red de Azure.

## <a name="configure-mapping"></a>Configuración de la asignación

Configure la asignación como sigue:

1. En **Infraestructura de Site Recovery** > **Asignaciones de red** > **Asignación de red**, haga clic en el icono de **+Asignación de red**.

    ![Asignación de red](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. En **Agregar asignación de red**, seleccione el servidor VMM de origen y **Azure** como destino.
3. Compruebe la suscripción y el modelo de implementación después de la conmutación por error.
4. En **Red de origen**, seleccione la red de máquinas virtuales de origen local que desea asignar en la lista asociada con el servidor VMM.
5. En **Red de destino**seleccione la red de Azure en la que se ubicarán las máquinas virtuales de Azure replicadas cuando estas se creen. A continuación, haga clic en **Aceptar**.

    ![Asignación de red](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

Esto es lo que sucede cuando comienza la asignación de red:

* Las máquinas virtuales existentes en la red de máquinas virtuales de origen se conectan a la red de destino cuando comienza la asignación. Las nuevas máquinas virtuales conectadas a la red de VM de origen se conectarán a la red de Azure asignada cuando se produzca la replicación.
* Si modifica una asignación de red existente, las máquinas virtuales de réplica se conectarán con la nueva configuración.
* Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error.
* Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.



## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 10: Creación de una directiva de replicación](vmm-to-azure-walkthrough-replication.md)

