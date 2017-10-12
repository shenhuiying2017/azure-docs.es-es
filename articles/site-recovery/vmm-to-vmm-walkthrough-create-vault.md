---
title: "Crear un almacén para la replicación de Hyper-V a un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo crear un almacén al replicar máquinas virtuales de Hyper-V a un sitio secundario de System Center VMM con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>Paso 5: Crear un almacén para la replicación de Hyper-V a un sitio secundario

Después de preparar [servidores locales de System Center Virtual Machine Manager (VMM) y hosts o clústeres de Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md) para la replicación de Hyper-V a un sitio secundario mediante [Azure Site Recovery](site-recovery-overview.md), puede crear un almacén de Recovery Services y seleccionar el escenario de replicación.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Elección de un objetivo de protección

Seleccione aquello que desea replicar y la ubicación en donde se va a realizar la replicación.

1. Haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.
2. Seleccione **Para sitio de recuperación** y seleccione **Sí, con Hyper-V**.
3. Seleccione **Sí** para indicar que usa VMM para administrar los hosts de Hyper-V.
4. Seleccione **Sí** si tiene un servidor VMM secundario. Si va a implementar la replicación entre nubes en un solo servidor VMM, haga clic en **No**. y, a continuación, haga clic en **Aceptar**.

    ![Elegir objetivos](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 6: Configuración de los valores de origen y de destino de la replicación](vmm-to-vmm-walkthrough-source-target.md).