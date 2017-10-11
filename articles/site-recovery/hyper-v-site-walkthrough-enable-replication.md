---
title: "Habilitar la replicación para la replicación de máquinas virtuales de Hyper-V en Azure (sin System Center VMM) con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para habilitar la replicación en Azure para máquinas virtuales de Hyper-V con el servicio de Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>Paso 10: habilitación de la replicación para replicar máquinas virtuales de Hyper-V en Azure


Este artículo describe cómo habilitar la replicación de máquinas virtuales de Hyper-V locales (no administradas por System Center VMM) en Azure, mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="before-you-start"></a>Antes de comenzar

Antes de empezar, asegúrese de que la cuenta de usuario de Azure tiene los [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necesarios para habilitar la replicación de una nueva máquina virtual en Azure.

## <a name="exclude-disks-from-replication"></a>Excluir discos de la replicación

De manera predeterminada, se replican todos los discos de una máquina. Puede excluir discos de la replicación. Por ejemplo, es posible que no desee replicar los discos con datos temporales, o los datos que se actualizan cada vez que una máquina o aplicación se reinicia (por ejemplo, pagefile.sys o tempdb de SQL Server). [Más información](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>Replicación de máquinas virtuales

Habilite la replicación para máquinas virtuales como sigue:          

1. Haga clic en **Replicar la aplicación** > **Origen**. Después de configurar la replicación por primera vez, haga clic en **+Replicar** para habilitar la replicación de más máquinas.

    ![Habilitar replicación](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. En **Origen**, seleccione el sitio Hyper-V. A continuación, haga clic en **Aceptar**.
3. En **Destino** seleccione la suscripción de almacén y el modelo de conmutación por error que se va a utilizar en Azure (Resource Manager o clásico) después de la conmutación por error.
4. Seleccione la cuenta de almacenamiento que desea usar. Si no tiene una cuenta que desea utilizar, puede [crear una](#set-up-an-azure-storage-account). A continuación, haga clic en **Aceptar**.
5. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.

    - Para aplicar la configuración de red a todas las máquinas habilitadas para replicación, seleccione la opción **Configurar ahora para las máquinas seleccionadas**.
    - Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina.
    - Si no tiene una red que desea utilizar, puede [crear una](#set-up-an-azure-network). Seleccione una subred si es posible. A continuación, haga clic en **Aceptar**.

   ![Habilitar replicación](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic en cada máquina que desea replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. En **Propiedades** > **Configurar propiedades**, seleccione el sistema operativo para las máquinas virtuales seleccionadas y el disco del sistema operativo.
8. Compruebe que el nombre de la máquina virtual de Azure (nombre de destino) cumple los [requisitos de las máquinas virtuales de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. De forma predeterminada se seleccionan todos los discos de la máquina virtual para la replicación. Borre discos para excluirlos.
10. Haga clic en **Aceptar** para guardar los cambios. Puede establecer propiedades adicionales más adelante.

    ![Habilitar replicación](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. En **Configuración de replicación** > **Establecer configuración de replicación**, seleccione la directiva de replicación que desea aplicar para las máquinas virtuales protegidas. A continuación, haga clic en **Aceptar**. Puede modificar la directiva de replicación en **Directivas de replicación** > nombre de directiva > **Editar configuración**. Los cambios que aplique se utilizarán tanto para las máquinas que ya se estén replicando como para otras nuevas.


   ![Habilitar replicación](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .


## <a name="next-steps"></a>Pasos siguientes


Vaya a [Paso 11: Ejecución de una conmutación por error de prueba](hyper-v-site-walkthrough-test-failover.md)
