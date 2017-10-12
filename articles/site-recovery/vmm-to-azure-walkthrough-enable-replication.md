---
title: "Habilitación de la replicación en Azure de máquinas virtuales de Hyper-V en nubes de VMM con Azure Site Recovery | Microsoft Docs"
description: "Se explica cómo habilitar la replicación en Azure de máquinas virtuales de Hyper-V en nubes VMM con el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 96a817e43a830e836f2faa4603fc88ed9c0b1828
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>Paso 11: Habilitación de la replicación en Azure de máquinas virtuales de Hyper-V en nubes de VMM

Después de configurar una directiva de replicación, use este artículo para habilitar la replicación en Azure de máquinas virtuales locales de Hyper-V administradas en nubes de System Center Virtual Machine Manager (VMM) mediante el servicio [Azure Site Recovery](site-recovery-overview.md) de Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de comenzar

Asegúrese de que la cuenta de Azure tenga los [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) correctos para crear máquinas virtuales de Azure. [Más información](../active-directory/role-based-access-built-in-roles.md) sobre el control de acceso basado en roles de Azure.

## <a name="exclude-disks-from-replication"></a>Excluir discos de la replicación

De manera predeterminada, se replican todos los discos de una máquina. Puede excluir discos de la replicación. Por ejemplo, es posible que no desee replicar los discos con datos temporales, o los datos que se actualizan cada vez que una máquina o aplicación se reinicia (por ejemplo, pagefile.sys o tempdb de SQL Server). [Más información](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Replicación de máquinas virtuales

Habilite la replicación para máquinas virtuales como sigue:  

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**. Después de habilitar la replicación por primera vez, haga clic en **+Replicar** en el almacén para habilitar la replicación de más máquinas.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. En la hoja **Origen**, seleccione el servidor VMM y la nube en la que se encuentran los hosts de Hyper-V. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. En **Destino** seleccione la suscripción, el modelo de implementación posterior a la conmutación por error y la cuenta de almacenamiento que va a usar para los datos replicados.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. Seleccione la cuenta de almacenamiento que desea usar. Si desea utilizar una cuenta de almacenamiento diferente de las que tiene, puede [crear una](#set-up-an-azure-storage-account). Si utiliza una cuenta de almacenamiento Premium para los datos replicados, será preciso que seleccione una cuenta de almacenamiento Estándar adicional para almacenar los registros de replicación que capturan los cambios continuos de los datos locales. Para crear una cuenta de almacenamiento mediante el modelo de Resource Manager, haga clic en **Crear nueva**. Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará [en Azure Portal](../storage/common/storage-create-storage-account.md). A continuación, haga clic en **Aceptar**.
5. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. Si desea utilizar una red diferente de las que tiene, puede [crear una](#set-up-an-azure-network). Para crear una red mediante el modelo de Resource Manager, haga clic en **Crear nueva**. Si desea crear una red mediante el modelo clásico, lo hará [en Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Seleccione una subred si es posible. A continuación, haga clic en **Aceptar**.
6. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic en cada máquina que desea replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. En **Propiedades** > **Configurar propiedades**, seleccione el sistema operativo para las máquinas virtuales seleccionadas y el disco del sistema operativo.

    - Compruebe que el nombre de la máquina virtual de Azure (nombre de destino) cumple los [requisitos de las máquinas virtuales de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - De forma predeterminada, se seleccionan todos los discos de la máquina virtual para la replicación, pero se pueden borrar todos aquellos que se deseen excluir.

        - A veces se excluyen discos para reducir el ancho de banda de replicación. Por ejemplo, es posible que no desee replicar discos con datos temporales o datos que se actualizan cada vez que se reinicia un equipo o una aplicación (como pagefile.sys o tempdb de Microsoft SQL Server). Para excluir el disco de la replicación, deselecciónelo.
        - Solo se pueden excluir los discos básicos. Los discos del sistema operativo no se pueden excluir.
        - Se recomienda no excluir discos dinámicos. Site Recovery no se puede identificar si un disco duro virtual de una máquina virtual invitada es básico o dinámico. Si no se excluyen todos los discos de volumen dinámico dependientes, el disco dinámico protegido se mostrará como uno fallido cuando la máquina virtual se conmute por error, y no se podrá acceder a los datos de dicho disco.
        - Una vez habilitada la replicación, no puede agregar ni quitar discos para la replicación. Si desea agregar o excluir un disco, deshabilite la protección de la máquina virtual y vuelva a habilitarla.
        - No se produce una conmutación por recuperación de los discos que se crean manualmente en Azure. Por ejemplo, si realiza la conmutación por error de tres discos y crea dos directamente en una máquina virtual de Azure, solo tres discos que se conmutaron por error se conmutarán por recuperación desde Azure hasta Hyper-V. No puede incluir los discos creados manualmente en la conmutación por recuperación o en la replicación inversa de Hyper-V a Azure.
        - Si excluye un disco necesario para que una aplicación funcione, después de la conmutación por error a Azure, debe crearlo manualmente en Azure para poder ejecutar la aplicación replicada. También puede integrar Azure Automation en un plan de recuperación para crear el disco durante la conmutación por error de la máquina.

    Haga clic en **Aceptar** para guardar los cambios. Puede establecer propiedades adicionales más adelante.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. En **Configuración de replicación** > **Establecer configuración de replicación**, seleccione la directiva de replicación que desea aplicar para las máquinas virtuales protegidas. A continuación, haga clic en **Aceptar**. Puede modificar la directiva de replicación en **Directivas de replicación** > nombre de directiva > **Editar configuración**. Los cambios que aplique se utilizarán tanto para las máquinas que ya se estén replicando como para otras nuevas.

   ![Habilitar replicación](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección**.



## <a name="next-steps"></a>Pasos siguientes

Ir a [Paso 12: Ejecución de una conmutación por error de prueba](vmm-to-azure-walkthrough-test-failover.md)
