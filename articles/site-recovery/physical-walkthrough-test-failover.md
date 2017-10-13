---
title: "Ejecutar una conmutación por error de prueba para la replicación del servidor físico en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para ejecutar una conmutación por error de prueba de servidores físicos que se replican en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94aa3bfc700cad3de9fc5516c0c9a4d86ade3fed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>Paso 11: Ejecutar una conmutación por error de prueba de servidores físicos en Azure

En este artículo se explica cómo ejecutar una conmutación por error de prueba de servidores físicos locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) de Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de comenzar

Antes de ejecutar una conmutación por error de prueba se recomienda comprobar las propiedades del servidor y realizar los cambios necesarios. Puede acceder a las propiedades de la máquina virtual en **Elementos replicados**. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.

## <a name="managed-disk-considerations"></a>Consideraciones sobre discos administrados

Los [discos administrados](../virtual-machines/windows/managed-disks-overview.md) simplifican la administración de los discos de las máquinas virtuales de Azure al administrar las cuentas de almacenamiento asociadas a los discos de la máquina virtual. 

- Al habilitar la protección de un servidor, los datos de las máquinas virtuales se replican en una cuenta de almacenamiento. Los discos administrados se crean y se asocian a la máquina virtual únicamente cuando se produce la conmutación por error.
- Los discos administrados solo se pueden crear para máquinas virtuales de Azure implementadas con el modelo de Resource Manager.  
- Con esta opción habilitada, solo se pueden seleccionar los conjuntos de disponibilidad de los grupos de recursos que tienen la opción **Usar discos administrados** habilitada. Las máquinas virtuales con discos administrados deben estar en conjuntos de disponibilidad con la opción **Usar discos administrados** establecida en **Sí**. Si esta opción no está habilitada para las máquinas virtuales, solo se pueden seleccionar los conjuntos de disponibilidad de los grupos de recursos sin discos administrados habilitados.
- Obtenga [más información](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) sobre discos administrados y conjuntos de disponibilidad.
- Si la cuenta de almacenamiento que usa para la replicación se ha cifrado con cifrado del servicio Storage, no se pueden crear discos administrados durante la conmutación por error. En este caso no habilite el uso de discos administrados, o deshabilite la protección de la máquina virtual y vuelva a habilitarla de modo que use una cuenta de almacenamiento que no tenga habilitado el cifrado. [Más información](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="network-considerations"></a>Consideraciones sobre la red

Puede establecer la dirección IP de destino de una máquina virtual de Azure creada después de la conmutación por error.

- Si no proporciona una dirección, la máquina conmutada por error usará DHCP.
- Si establece una dirección que no esté disponible en el momento de la conmutación por error, esta no funcionará.
- Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red que se vaya a usar en la prueba.
- El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino:

     - Si el número de adaptadores de red en la máquina de origen es igual o menor que el número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
     - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, se utilizará el tamaño máximo de destino.
     - Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador.     
   - Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.
   - Si la máquina virtual tiene varios adaptadores de red, el primero de ellos que se muestre en la lista se convertirá en el *predeterminado* en la máquina virtual de Azure.
 - Obtenga [más información](vmware-walkthrough-network.md) sobre direcciones IP.



## <a name="view-and-modify-vm-settings"></a>Ver y modificar la configuración de una máquina virtual

Se recomienda comprobar las propiedades del servidor de origen antes de ejecutar una conmutación por error.

1. En **Elementos protegidos**, haga clic en **Elementos replicados** y luego en la máquina.
2. En el panel **Elemento replicado**, puede ver un resumen de información de la máquina, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.
3. En **Proceso y red**, puede:
    - Modificar el nombre de la máquina virtual de Azure. El nombre debe satisfacer los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Especificar un [grupo de recursos] posterior a la conmutación por error.
    - Especificar un tamaño de destino para la máquina virtual de Azure
    - Seleccione un [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md).
    - Especifique si quiere usar [discos administrados](#managed-disk-considerations). Seleccione **Sí** si quiere asociar discos administrados a la máquina al migrar a Azure.
    - Ver o modificar la configuración de red, incluida la red o subred en la que se va a ubicar la máquina virtual de Azure después de la conmutación por error y la dirección IP que se le va a asignar.
4. En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.

## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

Una vez terminada la configuración, ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.

- Si quiere conectarse a máquinas virtuales de Azure mediante RDP después de la conmutación por error, [prepárese para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para realizar una comprobación completa, es preciso que realice una copia de Active Directory y DNS de su entorno de prueba. [Más información](site-recovery-active-directory.md#test-failover-considerations).
 - Para obtener información completa sobre la conmutación por error de prueba, lea [este artículo](site-recovery-test-failover-to-azure.md).
- Vea un vídeo introductorio rápido antes de empezar:

     
     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]

Ahora ejecute una conmutación por error:

1. Para conmutar por error una sola máquina, en **Configuración** > **Elementos replicados**, haga clic en el icono de la máquina > **+Probar conmutación por error**.

    ![Probar conmutación por error](./media/physical-walkthrough-test-failover/test-failover.png)

2. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan > **Probar conmutación por error**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).  

3. En **Probar conmutación por error**, seleccione la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

4. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento del progreso si hace clic en la máquina para abrir sus propiedades o en el trabajo **Probar conmutación por error** en nombre de almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.

5. Cuando termine la conmutación por error, debería ver la máquina virtual de réplica de Azure en Azure Portal > **Máquinas virtuales**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.

6. Si se preparó para las conexiones después de la conmutación por error, debe ser capaz de conectarse a la máquina virtual de Azure.

### <a name="delete-test-failover-vms"></a>Eliminar las máquinas virtuales conmutadas por error como prueba

1. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpiar conmutación por error de prueba) en el plan de recuperación o en la máquina.
2. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.
3. La acción de limpieza elimina las máquinas virtuales de Azure que se crearon durante la conmutación por error de prueba.

## <a name="summary"></a>Resumen

Si la conmutación por error de prueba se ha realizado correctamente, los servidores físicos se replican y ha comprobado que pueden conmutarse por error en Azure. Ahora puede ejecutar conmutaciones por error según los requisitos de la organización. 

Recuerde que actualmente no se puede conmutar por recuperación desde Azure a un servidor físico. Tiene que conmutar por recuperación a una máquina virtual de VMware. Esto significa que necesita una infraestructura de VMware local para realizar conmutaciones por recuperación. [Más información](site-recovery-failback-azure-to-vmware.md) sobre la conmutación por recuperación de máquinas virtuales de Azure en VMware.


## <a name="next-steps"></a>Pasos siguientes

- [Ejecute conmutaciones por error](site-recovery-failover.md) según necesidad.
