---
title: "Configuración de una directiva de replicación para replicar máquinas virtuales de Hyper-V (con VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo configurar una directiva de replicación para replicar máquinas virtuales de Hyper-V (con VMM) en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>Paso 10: Configuración de una directiva de replicación para replicar máquinas virtuales de Hyper-V (con VMM) en Azure


Tras configurar la [asignación de red](vmm-to-azure-walkthrough-network-mapping.md), use este artículo para configurar una directiva de replicación T\para replicar máquinas virtuales locales de Hyper-V administradas en nubes de System Center Virtual Machine Manager (VMM) en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="create-a-policy"></a>Para crear una directiva

1. Para crear una nueva directiva de replicación, haga clic en **Preparar infraestructura** > **Configuración de la replicación** > **+Crear y asociar**.

    ![Red](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. En **Crear y asociar directiva**, especifique un nombre de directiva.
3. En **Frecuencia de copia**, especifique la frecuencia con la que desea replicar diferencias de datos después de la replicación inicial (cada 30 segundos, 5 o 15 minutos).

    > [!NOTE]
    >  Cuando la replicación se realiza en Premium Storage, no se admite una frecuencia de 30 segundos. La limitación viene determinada por el número de instantáneas por blob (100) que admite Premium Storage. [Más información](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. En **Retención de punto de recuperación**, especifique, en horas, el tiempo que estará disponible el período de retención para cada punto de recuperación. Los equipos protegidos se pueden recuperar en cualquier punto dentro de un período.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (entre 1 y 12 horas) con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Hyper-V usa dos tipos de instantáneas, una instantánea estándar que proporciona una instantánea incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual. Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Tenga en cuenta que si habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.
6. En **Hora de inicio de la replicación inicial**, indique cuándo debe comenzar la replicación inicial. La replicación se produce utilizando el ancho de banda de Internet, así que puede que deba programarla fuera del horario de trabajo.
7. En **Cifrar datos almacenados en Azure**especifique si desea cifrar los datos en reposo en Azure Storage. A continuación, haga clic en **Aceptar**.

    ![Directiva de replicación](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. Cuando se crea una nueva directiva se asocia automáticamente con la nube de VMM. Haga clic en **Aceptar**. Puede asociar más nubes VMM (y las máquinas virtuales que contienen) a esta directiva de replicación en **Replicación** > nombre de directiva > **Associate VMM Cloud** (Asociar nube VMM).

    ![Directiva de replicación](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>Pasos siguientes

Ir a [Paso 11: Habilitación de la replicación](vmm-to-azure-walkthrough-enable-replication.md)
