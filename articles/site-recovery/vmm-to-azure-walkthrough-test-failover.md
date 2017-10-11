---
title: "Ejecución de una conmutación por error de prueba para la replicación de Hyper-V (con System Center VMM) en Azure | Microsoft Docs"
description: "Se resumen los pasos necesarios para ejecutar una conmutación por error para la replicación de máquinas virtuales de Hyper-V en nubes de VMM a Azure mediante el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b562a23-7ba7-48ee-905d-c22b4b5d6466
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: 4688fc4bc74a9e0e04487cfbe965006070fd9a7b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-run-a-test-failover-for-hyper-v-replication-with-vmm-to-azure"></a>Paso 11: Ejecución de una conmutación por error de prueba para la replicación de Hyper-V (con VMM) en Azure

Tras [habilitar la replicación para máquinas virtuales de Hyper-V](vmm-to-azure-walkthrough-enable-replication.md), use este artículo para ejecutar una conmutación por error de prueba desde máquinas virtuales locales de Hyper-V administradas en nubes de System Center Virtual Machine Manager (VMM) en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de comenzar

Antes de ejecutar una conmutación por error de prueba, se recomienda comprobar las propiedades del servidor y realizar los cambios necesarios. Puede acceder a las propiedades de la máquina virtual en **Elementos replicados**. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.

## <a name="managed-disk-considerations"></a>Consideraciones sobre discos administrados

Los [discos administrados](../virtual-machines/windows/managed-disks-overview.md) simplifican la administración de los discos de las máquinas virtuales de Azure al administrar las cuentas de almacenamiento asociadas a los discos de la máquina virtual. 

- Los discos administrados se crean y se asocian a la máquina virtual únicamente cuando se produce la conmutación por error a Azure. Al habilitar la protección, los datos de las máquinas virtuales locales se replican a las cuentas de almacenamiento.
- Los discos administrados solo se pueden crear para máquinas virtuales implementadas con el modelo de implementación de Resource Manager.
- La conmutación por recuperación desde Azure a un entorno local de Hyper-V no se admite actualmente en máquinas con discos administrados. Solo debe establecer **Usar discos administrados** en **Sí** si solo está realizando una migración (conmutación por error a Azure sin conmutación por recuperación).
- Con esta opción habilitada, solo se pueden seleccionar los conjuntos de disponibilidad de los grupos de recursos que tienen habilitada la opción **Usar discos administrados**. Las máquinas virtuales con discos administrados deben estar en conjuntos de disponibilidad con la opción **Usar discos administrados** establecida en **Sí**. Si esta opción no está habilitada para las máquinas virtuales, solo se pueden seleccionar los conjuntos de disponibilidad de los grupos de recursos sin discos administrados habilitados. [Más información](../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).
- - Si la cuenta de almacenamiento que usa para la replicación se ha cifrado con cifrado del servicio Storage, no se pueden crear discos administrados durante la conmutación por error. En este caso no habilite el uso de discos administrados, o deshabilite la protección de la máquina virtual y vuelva a habilitarla de modo que use una cuenta de almacenamiento que no tenga habilitado el cifrado. [Más información](../virtual-machines/windows/managed-disks-overview.md#managed-disks-and-encryption).

 
## <a name="network-considerations"></a>Consideraciones sobre la red
    
- Puede establecer la dirección IP de destino que se usará en la máquina virtual de Azure después de la conmutación por error. Si no proporciona una dirección, la máquina conmutada por error usará DHCP. Si establece una dirección que no está disponible en el momento de la conmutación por error, se producirá un error. Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red.
- El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino, de la siguiente manera:
    - Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
    - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
    - Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino es compatible con cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador.     
- Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.
- Si la máquina virtual tiene varios adaptadores de red, el primero de ellos que se muestre en la lista se convertirá en el *predeterminado* en la máquina virtual de Azure.


## <a name="view-and-manage-vm-settings"></a>Visualización y administración de la configuración de máquina virtual

Se recomienda comprobar las propiedades del servidor de origen antes de ejecutar una conmutación por error.

1. En **Elementos protegidos**, haga clic en **Elementos replicados** y luego en la máquina.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-test-failover/test-failover1.png)
2. En el panel **Elemento replicado**, puede ver un resumen de información de la máquina, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-test-failover/test-failover2.png)
3. En **Compute and Network** (Proceso y red), puede:
    - Modificar el nombre de la máquina virtual de Azure. El nombre debe satisfacer los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Especificar un [grupo de recursos] posterior a la conmutación por error.
    - Especificar un tamaño de destino para la máquina virtual de Azure
    - Seleccione un [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md).
    - Especifique si quiere usar [discos administrados](#managed-disk-considerations). Seleccione **Sí** si quiere asociar discos administrados a la máquina al migrar a Azure.
    - Vea o modifique la configuración de red, incluida la red o subred en la que se va a ubicar la máquina virtual de Azure después de la conmutación por error y la dirección IP que se le va a asignar.

    ![Habilitar replicación](./media/vmm-to-azure-walkthrough-test-failover/test-failover4.png)
4. En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.


## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

Ahora, ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la manera esperada.

- Si quiere conectarse a máquinas virtuales de Azure mediante RDP después de la conmutación por error, [prepárese para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para realizar una comprobación completa, es preciso que realice una copia de Active Directory y DNS de su entorno de prueba. [Más información](site-recovery-active-directory.md#test-failover-considerations).
 - Para obtener información completa sobre la conmutación por error de prueba, lea [este artículo](site-recovery-test-failover-to-azure.md).
 
 Ahora ejecute una conmutación por error:

1. Para conmutar por error una sola máquina, en **Elementos replicados**, haga clic en el icono Máquina virtual > **+Probar conmutación por error**.
2. Para conmutar por error un plan de recuperación, en **Planes de recuperación**, haga clic con el botón derecho en el plan > **Probar conmutación por error**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).
3. En **Probar conmutación por error**, seleccione la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.
4. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo **Probar conmutación por error** en nombre de almacén > **Trabajos** > **Trabajos de Site Recovery**.
5. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure en Azure Portal > **Virtual Machines**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.
6. Si se preparó para las conexiones después de la conmutación por error, debe ser capaz de conectarse a la máquina virtual de Azure.
7. Cuando haya terminado, haga clic en **Cleanup test failover** (Limpiar conmutación por error de prueba) en el plan de recuperación. En **Notas** , registre y guarde las observaciones asociadas a la conmutación por error de prueba. Así se eliminarán las máquinas virtuales que se crearon durante la conmutación por error de prueba.



## <a name="next-steps"></a>Pasos siguientes

- [Aprenda más](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.
- [Lea sobre la conmutación por recuperación](site-recovery-failback-from-azure-to-hyper-v.md), para realizarla y replicar máquinas virtuales de Azure de nuevo en la nube de VMM local principal.

