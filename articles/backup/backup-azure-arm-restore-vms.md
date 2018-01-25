---
title: "Azure Backup: restauración de máquinas virtuales mediante Azure Portal | Microsoft Docs"
description: "Restauración de una máquina virtual de Azure desde un punto de recuperación con Azure Portal"
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
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 1a1855cc3f83d7fcba749ce94167039feb5bebe1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Uso de Azure Portal para restaurar máquinas virtuales
Proteja sus datos tomando instantáneas de sus datos a intervalos definidos. Estas instantáneas se denominan puntos de recuperación y se almacenan en almacenes de Recovery Services. Si es necesario reparar o recompilar una máquina virtual, puede restaurarla desde cualquiera de los puntos de recuperación guardados. Cuando restaura un punto de recuperación, puede:

* Crear una nueva máquina virtual que sea una representación de un momento dado de la máquina virtual de copia de seguridad.
* Restaurar discos y usar la plantilla que se incluye con el proceso para personalizar la máquina virtual restaurada o realizar una recuperación de archivos individuales. 

En este artículo se explica cómo restaurar una máquina virtual en una nueva máquina virtual o restaurar todos los discos de copia de seguridad. Para la recuperación de archivos individuales, consulte el artículo sobre cómo [recuperar archivos de copias de seguridad de máquinas virtuales de Azure](backup-azure-restore-files-from-vm.md).

![Tres formas de restaurar copias de seguridad de máquinas virtuales](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Azure Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se proporcionan la información y los procedimientos usados para restaurar las máquinas virtuales implementadas mediante el modelo de Resource Manager.
>
>

Para restaurar una máquina virtual o todos los discos a partir de copias de seguridad de máquinas virtuales, hay que realizar dos pasos:

* Seleccione un punto de restauración.
* Seleccionar el tipo de restauración, cree una nueva máquina virtual o restaure los discos y especifique los parámetros necesarios. 

## <a name="select-a-restore-point-for-restore"></a>Seleccionar un punto de restauración
1. Inicie sesión en el [Azure Portal](http://portal.azure.com/).

2. En el menú de Azure, seleccione **Examinar**. En la lista de servicios, escriba **Recovery Services**. La lista de servicios se ajusta a lo que escribe. Cuando vea la opción **Almacenes de Recovery Services**, haga clic en ella.

    ![Almacén de Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de la suscripción.

    ![Lista de almacenes de Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. En la lista, seleccione el almacén asociado a la máquina virtual que desea restaurar. Al hacer clic en el almacén, se abre su panel.

    ![Almacén de Recovery Services seleccionado](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. En el panel del almacén, en el icono **Elementos de copia de seguridad**, seleccione **Azure Virtual Machines**.

    ![Panel del almacén](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Se abre la hoja **Elementos de copia de seguridad** y se muestra la lista de máquinas virtuales de Azure.

    ![Lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. En la lista, seleccione una máquina virtual para abrir el panel. Se abre el panel de máquinas virtuales en el área Supervisión que contiene el icono **Puntos de recuperación**.

    ![Puntos de restauración](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. En el menú del panel de máquina virtual, seleccione **Restaurar**.

    ![Botón Restaurar](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Se abre la hoja **Restaurar**.

    ![Hoja Restaurar](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. En la hoja **Restaurar**, seleccione **Punto de restauración**. Se abre la hoja **Seleccionar punto de restauración**.

    ![Seleccionar punto de restauración](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    De forma predeterminada, el cuadro de diálogo muestra todos los puntos de restauración de los últimos 30 días. Utilice el **filtro** para modificar el intervalo de tiempo de los puntos de restauración mostrados. De forma predeterminada, se muestran todos los puntos de restauración de todas las coherencias. Modifique el filtro **Todos los puntos de restauración** para seleccionar una coherencia específica de puntos de restauración. Para más información acerca de cada tipo de punto de restauración, consulte [Coherencia de datos](backup-azure-vms-introduction.md#data-consistency).

    Opciones de coherencia de puntos de restauración:

     * Puntos de restauración coherentes frente a bloqueos
     * Puntos de restauración coherentes con la aplicación
     * Puntos de restauración coherentes con el sistema de archivos
     * Todos los puntos de restauración

8. Elija un punto de restauración y seleccione **Aceptar**.

    ![Opciones de puntos de restauración](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    La hoja **Restaurar** muestra que el punto de restauración está establecido.

9. Si aún no está allí, vaya a la hoja **Restaurar**. Asegúrese de que se selecciona un [punto de restauración](#select-restore-point-for-restore) y seleccione **Restaurar configuración**. Se abre la hoja **Restaurar configuración**.

## <a name="choose-a-vm-restore-configuration"></a>Elección de la configuración de restauración de una máquina virtual
Después de seleccionar el punto de restauración, elija la configuración de restauración de una máquina virtual. Para configurar la máquina virtual restaurada, puede usar Azure Portal o PowerShell.

1. Si aún no está allí, vaya a la hoja **Restaurar**. Asegúrese de que se selecciona un [punto de restauración](#select-restore-point-for-restore) y seleccione **Restaurar configuración**. Se abre la hoja **Restaurar configuración**.

    ![Asistente para configuración de restauración](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. En la hoja **Configuración de restauración** tiene dos opciones:

   * **Crear la máquina virtual**

   * **Restaura los discos**

El portal proporciona una opción **Creación rápida** para la máquina virtual restaurada. Para personalizar la configuración de la máquina virtual o los nombres de los recursos creados como parte de la creación de una nueva opción de máquina virtual, use PowerShell o el portal para restaurar los discos de copia de seguridad. Use comandos de PowerShell para asociarlos a su elección de configuración de máquina virtual. También puede usar la plantilla que se incluye con los discos restaurados para personalizar la máquina virtual restaurada. Para más información sobre cómo restaurar una máquina virtual que tiene varias NIC o que se encuentra bajo un equilibrador de carga, consulte [Restore a VM with special network configurations](#restore-a vm-with-special-network-configurations) (Restauración de una máquina virtual con configuraciones de red especiales). Si su máquina virtual Windows usa [licencia de concentrador](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaure los discos y use PowerShell o una plantilla como se ha especificado en este artículo para crear la máquina virtual. Asegúrese de que especifica el **tipo de licencia** como "Windows_Server" al crear la máquina virtual para aprovechar las ventajas de HUB en la máquina virtual restaurada. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Creación de una nueva máquina virtual desde el punto de restauración
1. Si no aún no lo ha hecho, [seleccione un punto de restauración](#restore-a vm-with-special-network-configurations) antes de comenzar a crear una nueva máquina virtual desde un punto de restauración. Después de seleccionar el punto de restauración, en la hoja **Restaurar configuración**, escriba o seleccione valores en cada uno de los siguientes campos:

    a. **Tipo de restauración**. Cree una máquina virtual.

    b. **Nombre de la máquina virtual**. Proporcione un nombre para la máquina virtual. El nombre debe ser único para el grupo de recursos (para una máquina virtual implementada mediante Azure Resource Manager) o para el servicio en la nube (para una máquina virtual clásica). No puede reemplazar la máquina virtual si ya existe en la suscripción.

    c. **Grupo de recursos**. Use un grupo de recursos existente o cree uno. Si va a restaurar una máquina virtual clásica, use este campo para especificar el nombre de un nuevo servicio en la nube. Si va a crear un nuevo grupo de recursos o servicio en la nube, el nombre debe ser globalmente único. Por lo general, el nombre del servicio en la nube está asociado a una dirección URL pública; por ejemplo, [cloudservice].cloudapp.net. Si intenta usar un nombre para el servicio en la nube o el grupo de recursos de nube que ya se ha utilizado, Azure asigna al servicio en la nube o grupo de recursos el mismo nombre que la máquina virtual. Azure muestra servicios en la nube o grupos de recursos y máquinas virtuales no asociados a ningún grupo de afinidad. Para más información, consulte [cómo migrar grupos de afinidad a una red virtual regional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Red virtual**. Seleccione la red virtual al crear la máquina virtual. El campo proporciona todas las redes virtuales asociadas a la suscripción. El grupo de recursos de la máquina virtual se muestra entre paréntesis.

    e. **Subred**. Si la red virtual tiene subredes, la primera subred se selecciona de forma predeterminada. Si hay subredes adicionales, seleccione la que desee.

    f. **Cuenta de almacenamiento**. Este menú muestra la lista de cuentas de almacenamiento de la misma ubicación que el almacén de Recovery Services. No se admiten cuentas de almacenamiento con redundancia de zona. Si no hay ninguna cuenta de almacenamiento con la misma ubicación que el almacén de Recovery Services, debe crear una antes de iniciar la operación de restauración. El tipo de replicación de la cuenta de almacenamiento se muestra entre paréntesis.

    ![Asistente para configuración de restauración](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Si restaura una máquina virtual implementada mediante Resource Manager, debe identificar una red virtual. Una red virtual es opcional en una máquina virtual clásica.

    > * Si va a restaurar las máquinas virtuales con discos administrados, asegúrese de que la cuenta de almacenamiento seleccionada no esté habilitada para el Cifrado del servicio Azure Storage (SSE) durante su ciclo de vida.

    > * En función del tipo de almacenamiento de la cuenta de almacenamiento seleccionada (premium o estándar), todos los discos restaurados serán premium o bien estándar. En este momento no se admite el modo mixto de los discos durante la restauración.
    >
    >

2. En la hoja **Restore configuration** (Configuración de restauración), haga clic en **Aceptar** para finalizar la configuración de la restauración. En la hoja **Restaurar**, seleccione **Restaurar** para desencadenar la operación de restauración.

## <a name="restore-backed-up-disks"></a>Restauración de discos de copia de seguridad
Para personalizar la máquina virtual que quiere crear con discos de copia de seguridad diferentes a los que existen en la hoja **Restore configuration** (Configuración de restauración), seleccione **Restaurar discos** como valor de **Tipo de restauración**. Esta opción solicita una cuenta de almacenamiento en la que se copiarán los discos desde las copias de seguridad. Al elegir una cuenta de almacenamiento, seleccione una cuenta que comparta la misma ubicación que el almacén de Recovery Services. No se admiten cuentas de almacenamiento con redundancia de zona. Si no hay ninguna cuenta de almacenamiento con la misma ubicación que el almacén de Recovery Services, debe crear una antes de iniciar la operación de restauración. El tipo de replicación de la cuenta de almacenamiento se muestra entre paréntesis.

Después de que finaliza la operación de restauración, puede:

* [Usar la plantilla para personalizar la máquina virtual restaurada](#use-templates-to-customize-restore-vm)
* [Usar los discos restaurados para asociarlos a una máquina virtual existente](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Crear una máquina virtual con PowerShell desde los discos restaurados](./backup-azure-vms-automation.md#restore-an-azure-vm)

En la hoja **Restore configuration** (Configuración de restauración), haga clic en **Aceptar** para finalizar la configuración de la restauración. En la hoja **Restaurar**, seleccione **Restaurar** para desencadenar la operación de restauración.

![Configuración de recuperación completa](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Seguimiento de la operación de restauración
Una vez que se desencadene la operación de restauración, el servicio de copia de seguridad crea un trabajo para realizar su seguimiento. El servicio de copia de seguridad también crea y muestra temporalmente la notificación en el área **Notificaciones** del portal. Si no ve la notificación, seleccione el símbolo **Notificaciones** para ver las notificaciones.

![Restauración desencadenada](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para ver la operación mientras se está procesando, o para verla cuando se ha completado, abra la lista **Trabajos de copia de seguridad**.

1. En el menú de Azure, seleccione **Examinar** y, en la lista de servicios, escriba **Recovery Services**. La lista de servicios se ajusta a lo que escribe. Cuando vea la opción **Almacenes de Recovery Services**, haga clic en ella.

    ![Abrir el almacén de Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de la suscripción.

    ![Lista de almacenes de Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. En la lista, seleccione el almacén asociado a la máquina virtual ha restaurado. Al hacer clic en el almacén, se abre su panel.

3. En el panel del almacén en el icono **Trabajos de copia de seguridad**, haga clic en **Azure Virtual Machines** para mostrar los trabajos asociados al almacén.

    ![Panel del almacén](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Se abre la hoja **Trabajos de copia de seguridad**, que muestra la lista de trabajos.

    ![Lista de máquinas virtuales en un almacén](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Uso de plantillas para personalizar una máquina virtual restaurada
Una vez que ha [finalizado la operación de los discos de restauración](#Track-the-restore-operation), use la plantilla que se generó como parte de la operación de restauración para crear una nueva máquina virtual con una configuración diferente a la de la configuración de copia de seguridad. También puede usarla para personalizar los nombres de recursos que se crearon durante el proceso de creación de una nueva máquina virtual desde un punto de restauración. 

> [!NOTE]
> Las plantillas se agregan como parte de los discos de restauración para los puntos de recuperación tomados a partir del 1 de marzo de 2017. Son aplicables a máquinas virtuales de discos sin administrar. La compatibilidad con las máquinas virtuales de discos administrados estará disponible en próximas versiones. 
>
>

Para obtener la plantilla que se generó como parte de la opción de discos de restauración, siga estos pasos:

1. Vaya a los detalles del trabajo de restauración correspondientes al trabajo.

2. En la pantalla **Restore Job Details** (Detalles del trabajo de restauración), seleccione **Implementar plantilla** para iniciar la implementación de la plantilla. 

     ![Exploración en profundidad del trabajo de restauración](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. En la hoja **Implementar plantilla** para la implementación personalizada, use la implementación de plantillas para [editar e implementar la plantilla](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), o bien agregue más personalizaciones y [cree una plantilla](../azure-resource-manager/resource-group-authoring-templates.md) antes de realizar la implementación. 

   ![Carga de la implementación de plantilla](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Después de escribir los valores necesarios, acepte los **términos y condiciones** y seleccione **Comprar**.

   ![Envío de la implementación de plantilla](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Pasos posteriores a la restauración
* Si usa una distribución de Linux basada en cloud-init, como Ubuntu, la contraseña se bloquea después de la restauración por motivos de seguridad. Use la extensión VMAccess en la máquina virtual restaurada para [restablecer la contraseña](../virtual-machines/linux/reset-password.md). Se recomienda usar claves SSH en estas distribuciones para evitar que se restablezca la contraseña después de la restauración.
* Las extensiones existentes durante la configuración de copia de seguridad están instaladas, pero no se habilitarán. Si ve un problema, vuelva a instalar las extensiones. 
* Si la máquina virtual de copia de seguridad tiene una dirección IP estática posterior a la restauración, la máquina virtual restaurada tendrá una dirección IP dinámica para evitar conflictos cuando se cree una máquina virtual restaurada. Aprenda más sobre cómo puede [agregar una dirección IP estática a una máquina virtual restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Una máquina virtual restaurada no tiene un conjunto de valores de disponibilidad. Se recomienda usar la opción de discos de restauración para [agregar un conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md) cuando se crea una máquina virtual con PowerShell o plantillas mediante los discos restaurados. 


## <a name="backup-for-restored-vms"></a>Copia de seguridad de máquinas virtuales restauradas
Si restauró una máquina virtual en el mismo grupo de recursos con el mismo nombre que la máquina virtual de copia de seguridad original, la copia de seguridad seguirá en la máquina virtual después de la restauración. Si restauró la máquina virtual en otro grupo de recursos o especificó un nombre diferente para la máquina virtual restaurada, la máquina virtual se trata como si fuera una nueva máquina virtual. Deberá configurar la copia de seguridad para la máquina virtual restaurada.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Restauración de una máquina durante un desastre del centro de datos de Azure
Azure Backup permite restaurar máquinas virtuales de copia de seguridad en el centro de datos emparejado, en caso de que el centro de datos principal donde se ejecutan las máquinas virtuales experimente una situación de desastre y haya configurado el almacén de copia de seguridad para que tenga redundancia geográfica. Durante estos escenarios, seleccione una cuenta de almacenamiento, que esté presente en un centro de datos emparejado. El resto del proceso de restauración sigue siendo el mismo. Backup usa el servicio de proceso de la zona geográfica emparejada para crear la máquina virtual restaurada. Para más información, consulte [Azure datacenter resiliency](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) (Resistencia del centro de datos de Azure).

## <a name="restore-domain-controller-vms"></a>Restauración de máquinas virtuales de controlador de dominio
La copia de seguridad de las máquinas virtuales de controlador de dominio (DC) es un escenario admitido con Azure Backup. Sin embargo, se debe tener cuidado durante el proceso de restauración. El proceso de restauración correcto depende de la estructura del dominio. El caso más simple es tener un único DC en un solo dominio. El caso más común en las cargas de producción es tener un solo dominio con varios DC y, quizás, algún DC local. Por último, puede tener un bosque con varios dominios. 

Desde la perspectiva de Active Directory, una máquina virtual de Azure es como cualquier otra máquina virtual de un hipervisor admitido moderno. La principal diferencia respecto a los hipervisores locales es que no hay ninguna consola de máquina virtual disponible en Azure. Se necesita una consola para determinados escenarios, como para la recuperación mediante una copia de seguridad de reconstrucción completa (BMR). Sin embargo, la restauración de una VM desde el almacén de Backup es una sustitución completa para una BMR. El modo de restauración de servicios de directorio (DSRM) también está disponible, de modo que todos los escenarios de recuperación de Active Directory son viables. Para más información, consulte [Consideraciones relacionadas con la copia de seguridad y la restauración para controladores de dominio virtualizados](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) y [Planning for Active Directory Forest Recovery](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx) (Planeamiento de la recuperación de bosques de Active Directory).

### <a name="single-dc-in-a-single-domain"></a>Controlador de dominio único en un solo dominio
La máquina virtual se puede restaurar (como cualquier otra máquina virtual) desde Azure Portal o mediante PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Varios controladores de dominio en un solo dominio
Cuando se pueda acceder a otros controladores de dominio del mismo dominio a través de la red, el controlador de dominio se podrá restaurar como cualquier VM. Si se trata del último controlador de dominio que queda en el dominio o bien se lleva a cabo una recuperación en una red aislada, debe seguirse el procedimiento de recuperación de bosques.

### <a name="multiple-domains-in-one-forest"></a>Varios dominios en un solo bosque
Cuando se pueda acceder a otros controladores de dominio del mismo dominio a través de la red, el controlador de dominio se podrá restaurar como cualquier VM. En todos los demás casos, se recomienda una recuperación de bosques.

## <a name="restore-vms-with-special-network-configurations"></a>Restauración de máquinas virtuales con configuraciones de red especiales
Es posible hacer una copia de seguridad y restaurar máquinas virtuales con las siguientes configuraciones de red especiales. Sin embargo, estas configuraciones requieren consideraciones especiales al pasar por el proceso de restauración:

* Máquinas virtuales con equilibradores de carga (interno y externo)
* Máquinas virtuales con varias direcciones IP reservadas
* Paso 3: Creación de máquinas virtuales con varias NIC

> [!IMPORTANT]
> Al crear la configuración especial de red para las máquinas virtuales, debe usar PowerShell para crear máquinas virtuales a partir de los discos restaurados.
>
>

Con el fin de volver a crear completamente las máquinas virtuales después de restaurarlas en el disco, siga estos pasos:

1. Restaure los discos desde un almacén de Recovery Services mediante [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Cree la configuración de máquina virtual necesaria para el equilibrador de carga/varias NIC/varias IP reservadas mediante los cmdlets de PowerShell. Úsela para crear la máquina virtual con la configuración deseada:

   a. Cree una máquina virtual en el servicio en la nube con el [equilibrador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Cree una máquina virtual para conectarse al [equilibrador de carga accesible desde Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/).

   c. Cree una máquina virtual con [varias NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Cree una máquina virtual con [varias direcciones IP reservadas](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>pasos siguientes
Ahora que se pueden restaurar las máquinas virtuales, consulte el artículo de solución de problemas para más información sobre los errores comunes con las máquinas virtuales. Asimismo, consulte el artículo sobre la administración de las tareas con las máquinas virtuales.

* [Solución de errores](backup-azure-vms-troubleshoot.md#restore)
* [Administración de máquinas virtuales](backup-azure-manage-vms.md)
