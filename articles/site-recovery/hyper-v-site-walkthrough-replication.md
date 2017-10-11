---
title: "Configurar una directiva de replicación para la replicación de máquinas virtuales de Hyper-V (sin System Center VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para configurar una directiva de replicación al replicar máquinas virtuales de Hyper-V en Azure Storage"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>Paso 9: configuración de una directiva de replicación para replicar máquinas virtuales de Hyper-V en Azure

En este artículo se describe cómo configurar una directiva de replicación al replicar máquinas virtuales de Hyper-V en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.


Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="about-snapshots"></a>Acerca de las instantáneas

Hyper-V usa dos tipos de instantáneas, una instantánea estándar que proporciona una instantánea incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual.
    - Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea.
    - S habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.

## <a name="set-up-a-replication-policy"></a>Configurar una directiva de replicación

1. Para crear una nueva directiva de replicación, haga clic en **Preparar infraestructura** > **Configuración de la replicación** > **+Crear y asociar**.

    ![Red](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. En **Crear y asociar directiva**, especifique un nombre de directiva.
3. En **Frecuencia de copia**, especifique la frecuencia con la que desea replicar diferencias de datos después de la replicación inicial (cada 30 segundos, 5 o 15 minutos).

    > [!NOTE]
    > Cuando la replicación se realiza en Premium Storage, no se admite una frecuencia de 30 segundos. La limitación viene determinada por el número de instantáneas por blob (100) que admite Premium Storage. [Más información](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. En **Retención de punto de recuperación**, especifique la duración en horas del período de retención para cada punto de recuperación. Las máquinas virtuales se pueden recuperar en cualquier punto dentro de un período.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (entre 1 y 12 horas) con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación.
6. En **Hora de inicio de la replicación inicial**, especifique cuándo debe comenzar la replicación inicial. La replicación se produce utilizando el ancho de banda de Internet, así que puede que deba programarla fuera del horario de trabajo. y, a continuación, haga clic en **Aceptar**.

    ![Directiva de replicación](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

Cuando se crea una nueva directiva, esta se asocia automáticamente con el sitio Hyper-V. Puede asociar un sitio Hyper-V y las máquinas virtuales que contiene con varias directivas de replicación en **Replicación** > nombre de directiva > **Associate Hyper-V Site** (Asociar sitio Hyper-V).



## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 10: Habilitación de la replicación](hyper-v-site-walkthrough-enable-replication.md)
