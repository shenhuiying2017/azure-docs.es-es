
<properties
	pageTitle="Uso del Portal de Azure para restaurar máquinas virtuales | Microsoft Azure"
	description="Restauración de una máquina virtual de Azure desde un punto de recuperación con el Portal de Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="restaurar copias de seguridad; cómo restaurar; punto de recuperación;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="trinadhk; jimpark;"/>


# Uso del Portal de Azure para restaurar máquinas virtuales

> [AZURE.SELECTOR]
- [Restauración de máquinas virtuales en el portal clásico](backup-azure-restore-vms.md)
- [Restauración de máquinas virtuales en el Portal de Azure](backup-azure-arm-restore-vms.md)


Proteja sus datos con el servicio Copia de seguridad tomando instantáneas de sus datos a intervalos definidos. Estas instantáneas se denominan puntos de recuperación y se almacenan en almacenes de Servicios de recuperación. Cuando es necesario reparar o volver a generar una máquina virtual, puede restaurar la máquina virtual desde cualquiera de los puntos de recuperación guardados. Cuando se restaura un punto de recuperación, se devuelve o se revierte la máquina virtual al estado cuando se tomó el punto de recuperación. En este artículo se explica cómo restaurar una máquina virtual.

> [AZURE.NOTE] Azure tiene dos modelos de implementación para crear y trabajar con recursos: [Administrador de recursos e implementación clásica](../resource-manager-deployment-model.md). En este artículo se proporcionan información y procedimientos para restaurar las máquinas virtuales implementadas mediante el modelo de Resource Manager.



## Restauración de un punto de recuperación

1. Inicie sesión en el [Portal de Azure](http://ms.portal.azure.com/)

2. En el menú de Azure, haga clic en **Examinar** y, en la lista de servicios, escriba **Servicios de recuperación**. La lista de servicios se ajusta a lo que escribe. Cuando vea la opción **Almacenes de Servicios de recuperación**, haga clic en ella.

    ![Abrir el almacén de Servicios de recuperación](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de la suscripción.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

3. En la lista, seleccione el almacén asociado a la máquina virtual que desea restaurar. Al hacer clic en el almacén, se abre su panel.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)

4. Ahora que está en el panel del almacén. En el icono **Elementos de copia de seguridad**, haga clic en **Máquinas virtuales de Azure** para mostrar las máquinas virtuales asociadas al almacén.

    ![panel del almacén](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Se abre la hoja **Elementos de copia de seguridad** y se muestra la lista de máquinas virtuales de Azure.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)

5. En la lista, seleccione una máquina virtual para abrir el panel.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault-selected.png)

    Se abre el panel de máquinas virtuales en el área Supervisión que contiene el icono de puntos de recuperación.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. En el menú del panel de máquina virtual, haga clic en **Restaurar**.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Se abre la hoja Restaurar.

    ![hoja de restauración](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. En la hoja **Restaurar**, haga clic en **Punto de recuperación** para abrir la hoja **Seleccionar punto de recuperación**.

    ![hoja de restauración](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    De forma predeterminada, el cuadro de diálogo muestra todos los puntos de recuperación de los últimos 30 días. Edite las listas **Período**, **Año** y **Coherencia del punto de restauración** como desee. Para más información sobre cada tipo de punto de restauración, consulte la explicación de [Coherencia de datos](./backup-azure-vms-introduction.md#data-consistency).
    - **Período** son los meses del año o los últimos 30 días.
    - **Año** es el año específico.
    - **Coherencia del punto de restauración**, en esta lista elija:
        - Puntos de restauración coherentes frente a bloqueos
        - Puntos de restauración coherentes con la aplicación
        - Puntos de restauración coherentes con el sistema de archivos
        - Todos los puntos de restauración.  

8. Elija un punto de recuperación y haga clic en **Aceptar**.

    ![elegir un punto de recuperación](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    La hoja **Restaurar** muestra que el punto de recuperación está establecido.

    ![el punto de recuperación está establecido](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. En la hoja **Restaurar**, haga clic en **Configuración de recuperación** para abrir su hoja.

    ![el asistente para configuración de recuperación está establecido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## Elección de una configuración de recuperación de máquina virtual

Ahora que ha seleccionado el punto de recuperación, elija una cuenta de almacenamiento y el método para configurar la máquina virtual de recuperación. Las opciones para configuración la máquina virtual de recuperación son el uso del Portal de Azure o de PowerShell. Al elegir una cuenta de almacenamiento, debe elegir en cuentas que comparten la misma ubicación que el almacén de Servicios de recuperación. No se admiten cuentas de almacenamiento con redundancia de zona. Si no hay ninguna cuenta de almacenamiento con la misma ubicación que el almacén de Servicios de recuperación, debe crear uno antes de iniciar la operación de restauración.

1. Si no se ya existe, vaya a la hoja **Restaurar**. Asegúrese de que se ha seleccionado un **punto de recuperación** y haga clic en **Configuración de recuperación** para abrir la hoja **Configuración de recuperación**.

    ![el asistente para configuración de recuperación está establecido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. En la hoja **Configuración de recuperación**, haga clic en **Cuenta de almacenamiento** para abrir la lista de cuentas de almacenamiento en la misma ubicación que el almacén de Servicios de recuperación.

3. En la lista, elija una cuenta de almacenamiento y haga clic en Aceptar.

    ![lista de cuentas de almacenamiento](./media/backup-azure-arm-restore-vms/list-of-storage-accounts.png)

    En la hoja **Configuración de recuperación**, la cuenta de almacenamiento elegida aparece en el cuadro de diálogo **Cuenta de almacenamiento**.

    ![elegir cuentas de almacenamiento](./media/backup-azure-arm-restore-vms/selected-storage-account.png)

4. Puede configurar una máquina virtual clásica o de ARM básica cuando restaura una máquina virtual en el portal. Si quiere restaurar una configuración compleja, como una de las opciones de la lista siguiente, debe usar PowerShell para configurar la máquina virtual desde el disco. Si no va a configurar una configuración compleja, vaya al paso 5.

    ![lista de configuraciones complejas de máquinas virtuales](./media/backup-azure-arm-restore-vms/complex-vm-configurations.png)

    Si la configuración de restauración *se considera* compleja, en la hoja **Configuración de recuperación**, haga clic en **Aceptar**. La hoja **Configuración de recuperación** se cierra y aparece una marca de verificación junto a la configuración de recuperación. Haga clic en **Restaurar** para iniciar el trabajo de restauración del disco. No continúe con el paso 5; en su lugar, continúe en [Restauración de una máquina virtual con configuraciones de red especiales](#restoring-vms-with-special-network-configurations).

    ![Restauración configurada](./media/backup-azure-arm-restore-vms/restore-configured.png)

5. En la hoja **Configuración de recuperación**, haga clic en **Configuración de la máquina virtual** para abrir la hoja **Crear VM**.

    ![Abrir hoja de creación de máquina virtual](./media/backup-azure-arm-restore-vms/recovery-configuration-create-vm.png)

6. En la hoja **Crear VM**, escriba o seleccione valores para cada uno de los siguientes campos:

    - **Nombre de la máquina virtual**: especifique un nombre para la máquina virtual. El nombre debe ser único para el grupo de recursos (para una máquina virtual de ARM) o para el servicio en la nube (para una máquina virtual clásica). Si está reemplazando una máquina virtual existente con el mismo nombre, primero elimine la máquina virtual existente y los discos de datos y, después, restaure los datos de Copia de seguridad de Azure.
    - **Grupo de recursos**: use un grupo de recursos existente o cree uno nuevo. Si va a restaurar una máquina virtual clásica, utilice este campo para especificar el nombre de un nuevo servicio en la nube. Al crear un nuevo servicio en la nube o grupo de recursos, el nombre debe ser globalmente único. Por lo general, el nombre del servicio en la nube está asociado a una dirección URL pública; por ejemplo, [cloudservice].cloudapp.net. Si intenta utilizar un nombre para el servicio en la nube o grupo de recursos de nube que ya se ha utilizado, Azure asigna el servicio en la nube o grupo de recursos al mismo nombre que la máquina virtual. Azure muestra servicios en la nube o grupos de recursos y máquinas virtuales no asociados a ningún grupo de afinidad. Para más información, consulte [Migración de grupos de afinidad a una red virtual regional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
    - **Red virtual**: seleccione la red virtual (VNET) al crear la máquina virtual. El campo proporciona todas las redes virtuales asociadas a la suscripción.

    > [AZURE.NOTE] Debe seleccionar una red virtual al restaurar una máquina virtual de ARM. Una red virtual es opcional para una máquina virtual clásica.

    - **Subred**: si la red virtual tiene subredes, la primera subred se selecciona de forma predeterminada. Si hay subredes adicionales, seleccione la subred deseada.

7. En la hoja **Crear VM**, haga clic en **Aceptar** para completar la configuración.

    La hoja **Crear VM** se cierra y la hoja **Configuración de recuperación** muestra el nombre de la nueva máquina virtual.

    ![Configuración de recuperación completa](./media/backup-azure-arm-restore-vms/recovery-configuration-complete.png)

8. En la hoja **Configuración de recuperación**, haga clic en **Aceptar** para finalizar la configuración de restauración.

9. En la hoja **Restaurar**, haga clic en **Restaurar** para desencadenar la operación de restauración.

    ![Configuración de recuperación completa](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## Seguimiento de la operación de restauración

Una vez que se desencadene la operación de restauración, el servicio Copia de seguridad crea un trabajo para realizar el seguimiento de la operación de restauración. El servicio Copia de seguridad también crea y muestra temporalmente la notificación.

![Restauración desencadenada](./media/backup-azure-arm-restore-vms/restore-triggered.png)

Si no ve la notificación, siempre puede hacer clic en el icono de notificaciones para ver las notificaciones.

![Restauración desencadenada](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para ver la operación mientras se está procesando, o para verla cuando se ha completado, abra la lista de trabajos de Copia de seguridad.

1. En el menú de Azure, haga clic en **Examinar** y, en la lista de servicios, escriba **Servicios de recuperación**. La lista de servicios se ajusta a lo que escribe. Cuando vea la opción **Almacenes de Servicios de recuperación**, haga clic en ella.

    ![Abrir el almacén de Servicios de recuperación](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de la suscripción.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. En la lista, seleccione el almacén asociado a la máquina virtual ha restaurado. Al hacer clic en el almacén, se abre su panel.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-arm-restore-vms/select-vault-open-vault-jobs.png)

3. En el panel del almacén en el icono **Trabajos de copia de seguridad**, haga clic en **Máquinas virtuales de Azure** para mostrar los trabajos asociados al almacén.

    ![panel del almacén](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Se abre la hoja **Trabajos de copia de seguridad** y muestra la lista de trabajos.

    ![lista de máquinas virtuales en el almacén](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)




## Restauración de máquinas virtuales con configuraciones de red especiales
Es posible hacer una copia de seguridad y restaurar las máquinas virtuales con las siguientes configuraciones de red especiales. Sin embargo, estas configuraciones requieren consideraciones especiales al pasar por el proceso de restauración.

- Máquinas virtuales en el equilibrador de carga (interno y externo)
- Máquinas virtuales con varias direcciones IP reservadas
- Paso 3: Creación de máquinas virtuales con varias NIC

>[AZURE.IMPORTANT] Al crear la configuración de red especiales para las máquinas virtuales, debe usar PowerShell para restaurar desde el disco.


Con el fin de volver a crear completamente las máquinas virtuales después de restaurarlas en el disco, siga estos pasos:

1. Restaure los discos desde el almacén de Servicios de recuperación mediante [PowerShell con Copia de seguridad de Azure](../backup-azure-vms-automation.md#restore-an-azure-vm).

2. Cree la configuración de máquina virtual necesaria para el equilibrador de carga/varias NIC/varias IP reservadas mediante los cmdlets de PowerShell y úsela para crear la máquina virtual de la configuración que quiera.
	- Creación de una máquina virtual en el servicio en la nube con el [equilibrador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
	- Creación de una máquina virtual para conectarse al [equilibrador de carga accesible desde Internet](https://azure.microsoft.com/es-ES/documentation/articles/load-balancer-internet-getstarted/)
	- Creación de una máquina virtual con [varias NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
	- Creación de una máquina virtual con [varias direcciones IP reservadas](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## Pasos siguientes
Ahora que se pueden restaurar las máquinas virtuales, consulte el artículo de solución de problemas para más información sobre los errores comunes con las máquinas virtuales. Asimismo, consulte el artículo sobre la administración de las tareas con las máquinas virtuales.

- [Solución de errores](backup-azure-vms-troubleshoot.md#restore)
- [Administración de máquinas virtuales](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0420_2016-->