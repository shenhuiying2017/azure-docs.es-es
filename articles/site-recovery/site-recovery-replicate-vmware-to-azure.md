---
title: "Replicación de aplicaciones (VMware a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo configurar la replicación de máquinas virtuales que se ejecutan en VMware en Azure."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e0047a996c9bfd7d950b32f0871ddd7608924b42
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---


# <a name="replicate-applications-running-on-vmware-vms-to-azure"></a>Replicar aplicaciones que se ejecutan en máquinas virtuales de VMware en Azure



En este artículo se describe cómo configurar la replicación de máquinas virtuales que se ejecutan en VMware en Azure.
## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que ya ha seguido estos pasos:

1.  [Configuración del entorno de origen local](site-recovery-set-up-vmware-to-azure.md)
2.  [Configuración del entorno de destino en Azure](site-recovery-prepare-target-vmware-to-azure.md)


## <a name="enable-replication"></a>Habilitar replicación
#### <a name="before-you-start"></a>Antes de comenzar
Al replicar máquinas virtuales de VMware, tenga en cuenta que:

* Su cuenta de usuario de Azure debe tener ciertos [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar la replicación de una nueva máquina virtual en Azure.
* Las máquinas virtuales de VMware se detectan cada 15 minutos. Es posible que tarden 15 minutos o más en aparecer en el portal tras la detección. Del mismo modo, la detección podría tardar 15 minutos o más si agrega un servidor vCenter o host vSphere nuevos.
* Los cambios de entorno en la máquina virtual (como la instalación de herramientas de VMware) pueden tardar 15 minutos o más en actualizarse en el portal.
* Puede consultar la hora de la detección más reciente de las máquinas virtuales de VMware en el campo **Último contacto a las** correspondiente al servidor vCenter/host de vSphere en la hoja **Servidores de configuración**.
* Para agregar máquinas para la replicación sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar**.
* Al habilitar la replicación, si la máquina está preparada, el servidor de procesos instala automáticamente Mobility Service en ella.


**Ahora habilite la replicación como sigue**:

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**. Después de habilitar la replicación por primera vez, haga clic en **+Replicar** en el almacén para habilitar la replicación de más máquinas.
2. En la hoja **Origen** > **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas virtuales** o **Máquinas físicas**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host de vSphere, o bien seleccione el host. Esta configuración es relevante si va a replicar máquinas físicas.
5. Seleccione el servidor de procesos. Si no ha creado ningún servidor de procesos adicional, este será el nombre del servidor de configuración. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.
7. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación. Observe lo siguiente:

   * Puede seleccionar una cuenta de almacenamiento Estándar o Premium. Si selecciona una cuenta Premium, debe especificar una cuenta de almacenamiento Estándar adicional para los registros de replicación en curso. Las cuentas deben estar en la misma región que el almacén de Recovery Services.
   * Si desea usar una cuenta de almacenamiento diferente a las que tiene, puede crear una *vínculo de marcador de posición para la creación de cuenta de almacenamiento con Resource Manager, lo que se explicará en el artículo de introducción*. Para crear una cuenta de almacenamiento mediante Resource Manager, haga clic en **Crear nueva**. Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará [en Azure Portal](../storage/common/storage-create-storage-account.md).

8. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se pongan en marcha después de la conmutación por error. La red virtual de Azure debe estar en la misma región que el almacén de Recovery Services. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. Si no dispone de una red, debe [crear una](#set-up-an-azure-network). Para crear una red mediante Resource Manager, haga clic en **Crear nueva**. Si desea crear una red mediante el modelo clásico, lo hará [en Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Seleccione una subred si es posible. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic en cada máquina que desea replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que utilizará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.  
11. De manera predeterminada se replican todos los discos. Para excluir discos de la replicación, haga clic en **Todos los discos** y desactive los discos que no quiera replicar.  A continuación, haga clic en **Aceptar**. Puede establecer propiedades adicionales más adelante. [Obtenga más información](site-recovery-exclude-disk.md) sobre cómo excluir discos.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta. Puede modificar la configuración de la directiva de replicación en **Configuración** > **Directivas de replicación** > nombre de directiva > **Editar configuración**. Los cambios aplicados a una directiva también se aplicarán a las máquinas nuevas y replicadas.
13. Habilite la **coherencia de múltiples VM** si desea recopilar las máquinas en un grupo de replicación y especifique un nombre para el grupo. y, a continuación, haga clic en **Aceptar**. Observe lo siguiente:

    * Todas las máquinas de un grupo de replicación se replican al mismo tiempo y comparten puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error.
    * Se recomienda que recopile las máquinas virtuales y los servidores físicos juntos para que reflejen las cargas de trabajo. Habilitar la coherencia de múltiples VM puede afectar al rendimiento de la carga de trabajo y solo debe utilizarse si las máquinas ejecutan la misma carga de trabajo y necesita coherencia.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Haga clic en **Enable Replication**. Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .

> [!NOTE]
> Si la máquina está preparada para la instalación de inserción, el componente Mobility Service se instalará cuando la protección esté habilitada. Después de que el componente se ha instalado en la máquina, se inicia un trabajo de protección y se produce un error. Debe reiniciar manualmente cada máquina después del error. Una vez que reinicie, el trabajo de protección vuelve a comenzar y se produce la replicación inicial.
>
>

## <a name="view-and-manage-vm-properties"></a>Visualización y administración de las propiedades de la máquina virtual

Es recomendable que compruebe las propiedades de la máquina de origen. Recuerde que el nombre de la máquina virtual de Azure debe cumplir los [requisitos para las máquinas virtuales de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Haga clic en **Configuración** > **Elementos replicados** > y seleccione la máquina. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.
2. En **Propiedades** puede ver la información de replicación y conmutación por error de la máquina virtual.
3. En **Proceso y red** > **Propiedades de proceso**, puede especificar el nombre y el tamaño de destino de la máquina virtual de Azure. Modifique el nombre para que cumpla con los requisitos de Azure si es necesario.
    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/VMProperties_AVSET.png)
 
4.  Puede seleccionar un [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) del cual la máquina formará parte de la conmutación por error posterior. Puede cambiar esta configuración en cualquier momento antes de una conmutación por error. Después de la conmutación por error: si se migra la máquina a otro grupo de recursos, la configuración de protección de una máquina se interrumpirá.
5. Puede seleccionar un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) si su máquina debe formar parte de la conmutación por error posterior. Al seleccionar el conjunto de disponibilidad, tenga en cuenta lo siguiente:

    * Solo se mostrarán los conjuntos de disponibilidad que pertenecen al grupo de recursos especificado.  
    * Las máquinas con distintas redes virtuales no pueden formar parte del mismo conjunto de disponibilidad.
    * Solo las máquinas virtuales del mismo tamaño pueden formar parte del mismo conjunto de disponibilidad.
5. También puede ver y agregar la información sobre la red, la subred y la dirección IP de destino que se asignarán a la máquina virtual de Azure.
6. En **Discos** puede ver los discos de datos y del sistema operativo en la máquina virtual que se va a replicar.

### <a name="network-adapters-and-ip-addressing"></a>Adaptadores de red y direccionamiento IP 

- Puede establecer la dirección IP de destino. Si no proporciona una dirección, la máquina conmutada por error usará DHCP. Si establece una dirección que no está disponible en el momento de la conmutación por error, la conmutación no funcionará. Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red.
- El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino, de la siguiente manera:
    - Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
    - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
    - Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino es compatible con cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador.
    - Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.
    - Si la máquina virtual tiene varios adaptadores de red, el primero de ellos que se muestre en la lista se convertirá en el *predeterminado* en la máquina virtual de Azure.
   



## <a name="common-issues"></a>Problemas comunes

* Cada disco debe tener menos de 1 TB de tamaño.
* El disco del sistema operativo debe ser un disco básico y no uno dinámico.
* Para las máquinas virtuales de generación 2 o habilitadas para UEFI, la familia del sistema operativo debe ser Windows y el disco de arranque debe ser menor de 300 GB.

## <a name="next-steps"></a>Pasos siguientes

Una vez completada la protección, puede tratar de realizar una [conmutación por error](site-recovery-failover.md) para comprobar si la aplicación aparece en Azure o no.

Si desea deshabilitar la protección, consulte cómo [borrar el registro y la configuración de la protección](site-recovery-manage-registration-and-protection.md).

