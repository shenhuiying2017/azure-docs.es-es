---
title: "Azure Backup: restauración de máquinas virtuales mediante Azure Portal | Microsoft Docs"
description: "Restauración de una máquina virtual de Azure desde un punto de recuperación con el Portal de Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "restaurar copias de seguridad; cómo restaurar; punto de recuperación;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/26/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: d7a2b9c13b2c3372ba2e83f726c7bf5cc7e98c02
ms.openlocfilehash: 6f55bdbb97ead96edf7ca41562b1c4b5a712d6e8
ms.lasthandoff: 02/17/2017


---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Uso del Portal de Azure para restaurar máquinas virtuales
> [!div class="op_single_selector"]
> * [Restauración de máquinas virtuales en el portal clásico](backup-azure-restore-vms.md)
> * [Restauración de máquinas virtuales en el Portal de Azure](backup-azure-arm-restore-vms.md)
>
>

Proteja sus datos tomando instantáneas de sus datos a intervalos definidos. Estas instantáneas se denominan puntos de recuperación y se almacenan en almacenes de Servicios de recuperación. Cuando es necesario reparar o volver a generar una máquina virtual, puede restaurar la máquina virtual desde cualquiera de los puntos de recuperación guardados. Cuando se restaura un punto de recuperación, se devuelve o se revierte la máquina virtual al estado cuando se tomó el punto de recuperación. En este artículo se explica cómo restaurar una máquina virtual.

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se proporcionan información y procedimientos para restaurar las máquinas virtuales implementadas mediante el modelo de Resource Manager.
>
>

## <a name="restore-a-recovery-point"></a>Restauración de un punto de recuperación
1. Inicie sesión en el [Portal de Azure](http://portal.azure.com/)
2. En el menú de Azure, haga clic en **Examinar** y, en la lista de servicios, escriba **Recovery Services**. La lista de servicios se ajusta a lo que escribe. Cuando vea la opción **Almacenes de Servicios de recuperación**, haga clic en ella.

    ![Abrir el almacén de Servicios de recuperación](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de la suscripción.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. En la lista, seleccione el almacén asociado a la máquina virtual que desea restaurar. Al hacer clic en el almacén, se abre su panel.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Ahora que está en el panel del almacén. En el icono **Elementos de copia de seguridad**, haga clic en **Azure Virtual Machines** para mostrar las máquinas virtuales asociadas al almacén.

    ![panel del almacén](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Se abre la hoja **Elementos de copia de seguridad** y se muestra la lista de máquinas virtuales de Azure.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. En la lista, seleccione una máquina virtual para abrir el panel. Se abre el panel de máquinas virtuales en el área Supervisión que contiene el icono de puntos de recuperación.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. En el menú del panel de máquina virtual, haga clic en **Restaurar**

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Se abre la hoja Restaurar.

    ![hoja de restauración](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. En la hoja **Restaurar**, haga clic en **Punto de restauración** para abrir la hoja **Seleccionar punto de restauración**.

    ![hoja de restauración](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    De forma predeterminada, el cuadro de diálogo muestra todos los puntos de restauración de los últimos 30 días. Utilice el **filtro** para modificar el intervalo de tiempo de los puntos de restauración mostrados. De forma predeterminada, se muestran los puntos de restauración de toda la coherencia. Modifique el filtro **Todos los puntos de restauración** para seleccionar una coherencia específica de puntos de restauración. Para más información acerca de cada tipo de punto de restauración, consulte la explicación que se proporciona en [Coherencia de datos](backup-azure-vms-introduction.md#data-consistency).  

   * **Coherencia del punto de restauración** , elija:
     * Puntos de restauración coherentes frente a bloqueos
     * Puntos de restauración coherentes con la aplicación
     * Puntos de restauración coherentes con el sistema de archivos
     * Todos los puntos de restauración.  
8. Elija un punto de restauración y haga clic en **Aceptar**.

    ![elegir punto de restauración](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    La hoja **Restaurar** muestra que el punto de restauración está establecido.

    ![se establece el punto de restauración](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. En la hoja **Restaurar**, se abre automáticamente **Configuración de restauración** en cuanto se establece el punto de restauración.

    ![el asistente para configuración de restauración está establecido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## <a name="choosing-a-vm-restore-configuration"></a>Elección de una configuración de restauración para una máquina virtual
Ahora que ha seleccionado el punto de restauración, elija una configuración para la restauración de la máquina virtual. Las opciones para configurar la máquina virtual restaurada son usar el Portal de Azure o PowerShell.

> [!NOTE]
> El Portal proporciona una opción de creación rápida para la máquina virtual restaurada. Si desea personalizar la configuración de la máquina virtual que se restaurará, use PowerShell o el portal para restaurar los discos en copia de seguridad y use comandos de PowerShell para adjuntarlos a la opción de configuración de máquina virtual. Consulte [Restauración de máquinas virtuales con configuraciones de red especiales](#restoring-vms-with-special-network-configurations).
>
>

1. Si aún no está allí, vaya a la hoja **Restaurar** . Asegúrese de que se ha seleccionado un **punto de restauración** y haga clic en **Restore configuration** (Configuración de restauración) para abrir la hoja **Configuración de recuperación**.

    ![el asistente para configuración de recuperación está establecido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)
2. En la hoja **Configuración de restauración** tiene dos opciones:

   * Restaurar toda la máquina virtual.
   * Restaurar discos en copia de seguridad.

   ### <a name="restore-full-virtual-machine"></a>Restaurar toda la máquina virtual.
   En la hoja **Restore configuration** (Configuración de restauración), escriba o seleccione valores para cada uno de los siguientes campos:

   * **Tipo de restauración**: cree la máquina virtual.
   * **Nombre de la máquina virtual** : especifique un nombre para la máquina virtual. El nombre debe ser único para el grupo de recursos (para una máquina virtual implementada mediante Resource Manager) o para el servicio en la nube (para una máquina virtual clásica). No puede reemplazar la máquina virtual si ya existe en la suscripción.
   * **Grupo de recursos** : use un grupo de recursos existente o cree uno. Si va a restaurar una máquina virtual clásica, utilice este campo para especificar el nombre de un nuevo servicio en la nube. Si crea un nuevo grupo de recursos o servicio en la nube, el nombre debe ser globalmente único. Por lo general, el nombre del servicio en la nube está asociado a una dirección URL pública; por ejemplo, [cloudservice].cloudapp.net. Si intenta utilizar un nombre para el servicio en la nube o grupo de recursos de nube que ya se ha utilizado, Azure asigna el servicio en la nube o grupo de recursos al mismo nombre que la máquina virtual. Azure muestra servicios en la nube o grupos de recursos y máquinas virtuales no asociados a ningún grupo de afinidad. Para más información, consulte [Migración de grupos de afinidad a una red virtual regional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
   * **Red virtual** : seleccione la red virtual (VNET) al crear la máquina virtual. El campo proporciona todas las redes virtuales asociadas a la suscripción. El grupo de recursos de la máquina virtual se muestra entre paréntesis.
   * **Subred** : si la red virtual tiene subredes, la primera subred se selecciona de forma predeterminada. Si hay subredes adicionales, seleccione la subred deseada.
   * **Cuenta de almacenamiento** : este menú muestra la lista de cuentas de almacenamiento de la misma ubicación que el almacén de Servicios de recuperación. Al elegir una cuenta de almacenamiento, seleccione una cuenta que comparta la misma ubicación que el almacén de Servicios de recuperación. No se admiten cuentas de almacenamiento con redundancia de zona. Si no hay ninguna cuenta de almacenamiento con la misma ubicación que el almacén de Servicios de recuperación, debe crear uno antes de iniciar la operación de restauración. El tipo de replicación de la cuenta de almacenamiento se menciona entre paréntesis.

     > [!NOTE]
     > Si va a restaurar una máquina virtual implementada mediante Resource Manager, debe identificar una red virtual (VNET). Una red virtual es opcional para una máquina virtual clásica.
     >
     >

   ### <a name="restore-backed-up-disks"></a>Restaurar discos en copia de seguridad.
   Si desea personalizar la máquina virtual que quisiera crear a partir de discos en copia de seguridad en lugar de hacerlo a partir de lo que existe en la hoja de configuración de restauración, seleccione **Restaurar discos** como valor para **Tipo de restauración**. Esta opción pide una cuenta de almacenamiento (aplican las mismas restricciones que para la selección Restaurar toda la máquina virtual de la cuenta de almacenamiento) a la que se copiarán los discos desde las copias de seguridad. Puede [usar los discos restaurados para adjuntarlos a una máquina virtual existente](../virtual-machines/virtual-machines-windows-attach-disk-portal.md) o [crear una máquina virtual nueva a partir de discos restaurados.](./backup-azure-vms-automation.md#restore-an-azure-vm)

3. En la hoja **Restore configuration** (Configuración de restauración), haga clic en **Aceptar** para finalizar la configuración de la restauración.
4. En la hoja **Restaurar**, haga clic en **Restaurar** para desencadenar la operación de restauración.

    ![Configuración de recuperación completa](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Seguimiento de la operación de restauración
Una vez que se desencadene la operación de restauración, el servicio Copia de seguridad crea un trabajo para realizar el seguimiento de la operación de restauración. El servicio Copia de seguridad también crea y muestra temporalmente la notificación en el área de notificaciones del portal. Si no ve la notificación, siempre puede hacer clic en el icono de notificaciones para ver las notificaciones.

![Restauración desencadenada](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para ver la operación mientras se está procesando, o para verla cuando se ha completado, abra la lista de trabajos de Copia de seguridad.

1. En el menú de Azure, haga clic en **Examinar** y, en la lista de servicios, escriba **Recovery Services**. La lista de servicios se ajusta a lo que escribe. Cuando vea la opción **Almacenes de Servicios de recuperación**, haga clic en ella.

    ![Abrir el almacén de Servicios de recuperación](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de la suscripción.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. En la lista, seleccione el almacén asociado a la máquina virtual ha restaurado. Al hacer clic en el almacén, se abre su panel.
3. En el panel del almacén en el icono **Trabajos de copia de seguridad**, haga clic en **Azure Virtual Machines** para mostrar los trabajos asociados al almacén.

    ![panel del almacén](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Se abre la hoja **Trabajos de copia de seguridad** y muestra la lista de trabajos.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)

## <a name="post-restore-steps"></a>Pasos posteriores a la restauración
* Si usa una distribución de Linux basada en cloud-init, como Ubuntu, la contraseña se bloquea después de la restauración por seguridad. Use la extensión VMAccess en la máquina virtual restaurada para [restablecer la contraseña](../virtual-machines/virtual-machines-linux-classic-reset-access.md). Se recomienda utilizar claves SSH en estas distribuciones para evitar que se restablezca la contraseña después de la restauración.
* Se instalarán las extensiones presentes durante la configuración de la copia de seguridad, pero no se habilitarán. Vuelva a instalar las extensiones si surge algún problema. 
* Si la máquina virtual de copia de seguridad tiene una dirección IP estática, tras la restauración, la máquina virtual restaurada tendrá una dirección IP dinámica para evitar conflictos cuando se crea una máquina virtual restaurada. Más información sobre cómo se puede [agregar una dirección IP estática a la máquina virtual restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
* La máquina virtual restaurada no tendrá un conjunto de valores de disponibilidad. Se recomienda usar la opción de discos de restauración y [agregar un conjunto de disponibilidad](../virtual-machines/virtual-machines-windows-create-availability-set.md#use-powershell-to-create-an-availability-set) cuando se crea una máquina virtual desde PowerShell mediante los discos restaurados. 

## <a name="backup-for-restored-vms"></a>Copia de seguridad de máquinas virtuales restauradas
Si restauró la máquina virtual en el mismo grupo de recursos con el mismo nombre con el que originalmente se creó una copia de seguridad de ella, la copia de seguridad seguirá en la máquina virtual después de la restauración. Si restauró la máquina virtual en un grupo de recursos distinto o especificó un nombre diferente para la máquina virtual restaurada, esta se trata como una nueva y debe configurar la copia de seguridad para ella.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restauración de una máquina virtual en caso de desastre de centro de datos de Azure
Copia de seguridad de Azure permite restaurar copias de seguridad de máquinas virtuales en el centro de datos asociado, en caso de que el centro de datos principal donde se ejecutan las máquinas virtuales experimente una situación de desastre y haya configurado el almacén de copia de seguridad para que tenga redundancia geográfica. En estos casos, deberá seleccionar una cuenta de almacenamiento que esté presente en el centro de datos asociado. El resto del proceso de restauración permanece igual. Copia de seguridad de Azure usa el servicio de proceso de la zona geográfica asociada para crear la máquina virtual restaurada. Más información sobre la [resistencia del centro de datos de Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-vms-with-special-network-configurations"></a>Restauración de máquinas virtuales con configuraciones de red especiales
Es posible hacer una copia de seguridad y restaurar las máquinas virtuales con las siguientes configuraciones de red especiales. Sin embargo, estas configuraciones requieren consideraciones especiales al pasar por el proceso de restauración.

* Máquinas virtuales en el equilibrador de carga (interno y externo)
* Máquinas virtuales con varias direcciones IP reservadas
* Paso&3;: Creación de máquinas virtuales con varias NIC

> [!IMPORTANT]
> Al crear la configuración especial de red para las máquinas virtuales, debe usar PowerShell para crear máquinas virtuales a partir de los discos restaurados.
>
>

Con el fin de volver a crear completamente las máquinas virtuales después de restaurarlas en el disco, siga estos pasos:

1. Restaure los discos desde un almacén de Recovery Services mediante [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)
2. Cree la configuración de máquina virtual necesaria para el equilibrador de carga/varias NIC/varias IP reservadas mediante los cmdlets de PowerShell y úsela para crear la máquina virtual de la configuración que quiera.

   * Cree una máquina virtual en el servicio en la nube con el [equilibrador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Cree una máquina virtual para conectarse al [equilibrador de carga accesible desde Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Creación de una máquina virtual con [varias NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Creación de una máquina virtual con [varias direcciones IP reservadas](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Pasos siguientes
Ahora que se pueden restaurar las máquinas virtuales, consulte el artículo de solución de problemas para más información sobre los errores comunes con las máquinas virtuales. Asimismo, consulte el artículo sobre la administración de las tareas con las máquinas virtuales.

* [Solución de errores](backup-azure-vms-troubleshoot.md#restore)
* [Administración de máquinas virtuales](backup-azure-manage-vms.md)

