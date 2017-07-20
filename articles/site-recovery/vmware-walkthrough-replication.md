---
title: "Configuración de una directiva de replicación para replicar máquinas virtuales de VMware en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para configurar una directiva de replicación al replicar máquinas virtuales de VMware en Azure Storage"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 3c4b7ad16e6a03fb605447def18f7475d502fdd1
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
# <a name="step-9-set-up-a-replication-policy-for-vmware-vm-replication-to-azure"></a>Paso 9: Configuración de una directiva de replicación para replicar máquinas virtuales de VMware en Azure


En este artículo se describe cómo configurar una directiva de replicación al replicar máquinas virtuales de VMware en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.


Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="configure-a-policy"></a>Configuración de una directiva

Vea un vídeo introductorio rápido antes de empezar:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Para crear una directiva de replicación, haga clic en **Site Recovery Infrastructure (Infraestructura de Site Recovery)** > **Directivas de replicación** > **+Directiva de replicación**.
2. En **Crear directiva de replicación**, especifique un nombre de directiva.
3. En **Umbral de RPO**, especifique el límite de RPO. Este valor especifica la frecuencia con que se crean puntos de recuperación de datos. Se genera una alerta cuando la replicación continua supera este límite.
4. En **Retención de punto de recuperación**, especifique (en horas) la duración del período de retención para cada punto de recuperación. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de un período. Se admite una retención de hasta 24 horas para máquinas replicadas en Premium Storage y 72 horas para almacenamiento estándar.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Haga clic en **Aceptar** para crear la directiva.

    ![Directiva de replicación](./media/vmware-walkthrough-replication/gs-replication2.png)
8. Cuando se crea una nueva directiva, esta se asocia automáticamente con el servidor de configuración. De forma predeterminada, se crea automáticamente una directiva correspondiente para la conmutación por recuperación. Por ejemplo, si la directiva de replicación es **rep-policy**, la directiva de conmutación por recuperación será **rep-policy-failback**. Esta directiva no se usa hasta que se inicie una conmutación por recuperación desde Azure.

## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 10: Instalación de Mobility Service](vmware-walkthrough-install-mobility.md)

