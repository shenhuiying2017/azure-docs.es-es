---
title: "Configuración de la replicación en Azure Site Recovery | Microsoft Docs"
description: "Describe cómo implementar Site Recovery para orquestar la replicación, la conmutación por error y la recuperación, en Azure, de máquinas virtuales de Hyper-V que están en nubes de VMM."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Administración de una directiva de replicación de VMware en Azure


## <a name="create-a-new-replication-policy"></a>Creación de una directiva de replicación nueva

1. Haga clic en "Administrar-> Infraestructura de Site Recovery" en el menú de la izquierda. 
2. Seleccione "Directivas de replicación" en la sección "For VMware and Physical machines" (Para VMware y equipos físicos).
3. Haga clic en "+Replication policy" (+ Directiva de replicación) en la parte superior.

    ![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Escriba el nombre de la directiva.

5. En el umbral de RPO, especifique el límite de RPO. Se generarán alertas cuando la replicación continua supera este límite.
6. En Retención de punto de recuperación, especifique, en horas, el tiempo que estará disponible la ventana de retención para cada punto de recuperación. Los equipos protegidos se pueden recuperar en cualquier punto dentro de un período. 

    > [!NOTE] 
    > Se admite una retención máxima de 24 horas para las máquinas replicadas en Premium Storage y de 72 horas para replicadas en almacenamiento estándar.
    
    > [!NOTE] 
    > Se creará automáticamente una directiva de replicación para la conmutación por recuperación.

7. En Frecuencia de instantánea coherente con la aplicación, especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan instantáneas coherentes con la aplicación.

8. Haga clic en "Aceptar". La directiva debería tardar entre 30 segundos y 1 minuto en crearse.

![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Asociación del servidor de configuración con la directiva de replicación
1. Haga clic en la directiva de replicación a la que desea asociar el servidor de configuración.
2. Haga clic en "Asociar" en la parte superior.
![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Seleccione el "servidor de configuración" en la lista.
4. Haga clic en Aceptar. El servidor de configuración debería tardar entre 1 y 2 en asociarse.

![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Edición de una directiva de replicación
1. Haga clic en la directiva de replicación cuya configuración desee editar.
![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Haga clic en "Editar configuración".
![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Cambie la configuración para ajustarla a sus necesidades.
4. En la parte superior, haga clic en "Guardar". La directiva debería tardar entre 2 y 5 minutes en guardarse, en función del número de máquinas virtuales que la usen.

![Crear una directiva de replicación](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>Desasociación del servidor de configuración de la directiva de replicación
1. Haga clic en la directiva de replicación a la que desea asociar el servidor de configuración.
2. Haga clic en "Desasociar" en la parte superior.
3. Seleccione el "servidor de configuración" en la lista.
4. Haga clic en Aceptar. El servidor de configuración debería tardar entre 1 y 2 en desasociarse.
    
    > [!NOTE] 
    > Un servidor de configuración no se puede desasociar si hay al menos un elemento replicado que use la directiva. Asegúrese de que no hay elementos replicados que usen la directiva antes de desasociar el servidor de configuración.

## <a name="delete-replication-policy"></a>Eliminación de una directiva de replicación 

1. Haga clic en la directiva de replicación que desea eliminar.
2. Haga clic en Eliminar. La directiva debería tardar entre 30 segundos y 1 minuto en eliminarse.

    > [!NOTE] 
    > Una directiva de replicación no se puede eliminar si tiene al menos un servidor de configuración asociado. Asegúrese de que no hay elementos replicados que usen la directiva y elimine todos los servidores de configuración asociados antes de eliminar la directiva.


<!--HONumber=Jan17_HO4-->


