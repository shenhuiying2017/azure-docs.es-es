---
title: Configuración y administración de directivas de replicación para la replicación de VMware con Azure Site Recovery | Microsoft Docs
description: Se describe cómo configurar opciones de replicación para la replicación de VMware en Azure con Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: sutalasi
ms.openlocfilehash: 6a8e6e7c6fbdbcbf58557a0896e976a608164041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Configuración y administración de directivas de replicación para la replicación de VMware
En este artículo se describe cómo configurar una directiva de replicación cuando se replican máquinas virtuales de VMware en Azure mediante [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Para crear una directiva

1. Seleccione **Administrar** > **Infraestructura de Site Recovery**.
2. En **For VMware and Physical machines** (Para máquinas de VMware y físicas), seleccione **Directivas de replicación**. 
3. Haga clic en **+Directiva de replicación** y especifique el nombre de la directiva.
5. En **Umbral de RPO**, especifique el límite de RPO. Se generan alertas cuando la replicación continua supera este límite.
6. En **Retención de punto de recuperación**, especifique (en horas) la duración del período de retención para cada punto de recuperación. Las máquinas protegidas se pueden recuperar en cualquier punto dentro de un período de retención. Se admite una retención de hasta 24 horas para máquinas replicadas en Premium Storage. Se admite hasta 72 horas para un almacenamiento estándar.
7. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan las instantáneas coherentes con la aplicación.
8. Haga clic en **OK**. La directiva debe demorar entre 30 y 60 segundos en crearse.

Cuando se crea una directiva de replicación, se crea automáticamente una directiva de replicación para la conmutación por recuperación, con el sufijo "failback". Una vez creada la directiva, se puede editar seleccionándola > **Editar configuración**.

## <a name="associate-a-configuration-server"></a>Asociación de un servidor de configuración 

Asocie la directiva de replicación con el servidor de configuración local.

1. Haga clic en **Asociar** y seleccione el servidor de configuración.

    ![Asociación de servidor de configuración](./media/vmware-azure-set-up-replication/associate1.png)

2. Haga clic en **OK**. El servidor de configuración debería demorar entre uno y dos minutos en asociarse.

    ![Asociación del servidor de configuración](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Desasociación o eliminación de una directiva de replicación
1. Elija la directiva de replicación.
    a. Para desasociar la directiva del servidor de configuración, asegúrese de que no haya máquinas replicadas que estén utilizando la directiva. Después, haga clic en **Desasociar**.
    b. Para eliminar la directiva, asegúrese de que no esté asociada con un servidor de configuración. Después, haga clic en **Eliminar**. Tardará entre 30 y 60 segundos en eliminarla.
2. Haga clic en **OK**.
