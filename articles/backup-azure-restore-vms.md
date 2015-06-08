<properties
	pageTitle="Copia de seguridad de Azure: restauración de una máquina virtual"
	description="Información sobre cómo restaurar una máquina virtual de Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="aashishr"/>

# Restauración de una máquina virtual
Puede restaurar una máquina virtual en una nueva máquina virtual desde las copias de seguridad almacenadas en el almacén de copia de seguridad de Azure mediante la acción de restauración.

## Selección de un elemento para restaurar

1. Navegue hasta la pestaña **Elementos protegidos** y seleccione la máquina virtual que desea restaurar en una máquina virtual nueva.

    ![Elementos protegidos](./media/backup-azure-restore-vms/protected-items.png)

    La columna **Punto de recuperación** en la página **Elementos protegidos** le indicará el número de puntos de recuperación para una máquina virtual. La columna **Punto de recuperación más reciente** indica el momento de la copia de seguridad más reciente desde la que se puede restaurar una máquina virtual.

2. Haga clic en **Restaurar** para abrir el asistente de **restauración de un elemento**.

    ![Restaurar un elemento](./media/backup-azure-restore-vms/restore-item.png)

## Elegir un punto de recuperación

1. En la pantalla **Seleccionar un punto de recuperación**, puede restaurar desde el punto de recuperación más reciente, o desde un punto anterior en el tiempo. La opción predeterminada seleccionada cuando se abre el asistente es Punto de recuperación más reciente.

    ![Seleccionar un punto de recuperación](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Para elegir un momento anterior en el tiempo, elija la opción **Seleccionar fecha** en la lista desplegable y seleccione una fecha en el control de calendario haciendo clic en el icono del calendario. En el control, todas las fechas que tienen puntos de recuperación se rellenan con un tono de color gris claro y son seleccionables para el usuario.

    ![Seleccionar una fecha](./media/backup-azure-restore-vms/select-date.png)

    Una vez que se hace clic en una fecha en el control de calendario, los puntos de recuperación disponibles en la fecha se mostrarán en la siguiente tabla de puntos de recuperación. La columna **Tiempo** indica el momento en que se tomó la instantánea. El **tipo** columna muestra el [coherencia](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) del punto de recuperación. El encabezado de tabla muestra el número de puntos de recuperación disponibles de ese día entre paréntesis.

    ![Puntos de recuperación](./media/backup-azure-restore-vms/recovery-points.png)

3. Seleccione el punto de recuperación desde la tabla **Puntos de recuperación** de la tabla y haga clic en la flecha siguiente para ir a la pantalla siguiente.

## Especifique una ubicación de destino.

1. En la pantalla **Seleccionar instancia de restauración**, especifique detalles de dónde desea restaurar la máquina virtual.

  - Especifique el nombre de la máquina virtual: en un servicio de nube determinado, el nombre de la máquina virtual debe ser único. Si planea reemplazar una máquina virtual existente con el mismo nombre, primero elimine la máquina virtual existente y los discos de datos y, a continuación, restaure los datos de copia de seguridad de Azure.
  - Seleccione un servicio de nube para la máquina virtual: esto es obligatorio para la creación de una máquina virtual. Puede usar un servicio de nube existente o crear un nuevo servicio de nube.

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](https://msdn.microsoft.com/en-us/library/azure/jj156085.aspx).

2. Seleccione una cuenta de almacenamiento para la máquina virtual: esto es obligatorio para la creación de una máquina virtual. Puede seleccionar desde cuentas de almacenamiento existentes en la misma región que el almacén de copia de seguridad de Azure. No se admiten cuentas de almacenamiento redundantes de zona ni de almacenamiento Premium.

    Si no hay ninguna cuenta de almacenamiento con configuración compatible, cree una cuenta de almacenamiento de configuración admitida antes de iniciar la operación de restauración.

    ![Seleccionar una red virtual](./media/backup-azure-restore-vms/restore-sa.png)

3. Seleccione una red virtual: la red virtual (VNET) para la máquina virtual debe seleccionarse en el momento de crear la máquina virtual. La interfaz de usuario de restauración muestra todas las redes virtuales dentro de esta suscripción que se pueden utilizar. No es obligatorio seleccionar una red virtual para la máquina virtual restaurada; podrá conectarse a la máquina virtual restaurada a través de Internet incluso si no se aplica la red virtual.

    Si el servicio de nube seleccionado está asociado a una red virtual, no puede cambiar la red virtual.

    ![Seleccionar una red virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Seleccione una subred: en caso de que la red virtual tenga subredes, de forma predeterminada se seleccionará la primera subred. Elija la subred que prefiera de las opciones de la lista desplegable. Para obtener detalles de subred, vaya a la extensión de las redes en el [página principal del portal](https://manage.windowsazure.com/), vaya a las redes virtuales y seleccione la red virtual y profundizar en configurar para ver los detalles de subred.

    ![Seleccionar una subred](./media/backup-azure-restore-vms/select-subnet.png)

5. Haga clic en el icono **Enviar** en el Asistente para enviar los detalles y crear un trabajo de restauración.

## Seguimiento de la operación de restauración
Una vez que ha especificado toda la información en el Asistente de restauración y lo ha enviado, la Copia de seguridad de Azure intentará crear un trabajo para realizar un seguimiento de la operación de restauración.

![Creación de un trabajo de restauración](./media/backup-azure-restore-vms/create-restore-job.png)

Si la creación del trabajo se realiza correctamente, verá una notificación de aviso que indica que se ha creado un trabajo. Puede obtener más detalles haciendo clic en el botón **Ver trabajo** que le llevará a la pestaña **Trabajos**.

![Trabajo de restauración creado](./media/backup-azure-restore-vms/restore-job-created.png)

Una vez finalizada la operación de restauración, se marcará como completada en la pestaña **Trabajos**.

![Trabajo de restauración completado](./media/backup-azure-restore-vms/restore-job-complete.png)

Después de restaurar la máquina virtual puede que necesite volver a instalar las extensiones existentes en la máquina virtual original y [volver a crear los extremos](virtual-machines-set-up-endpoints) para la máquina virtual en el portal de Azure.

## Solución de errores
Para la mayoría de los errores, puede seguir la acción recomendada sugerida en los detalles del error. Estos son algunos puntos adicionales para ayudar con la solución de problemas:

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Restauración | Error en la restauración con error interno de nube | <ol><li>El servicio de nube que está intentando restaurar está configurado con la configuración de DNS. Puede consultar <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si hay una dirección configurada, significa que los ajustes de DNS están configurados.<br> <li>El servicio de nube que está intentando restaurar está configurado con ReservedIP, y las VM existentes en el servicio de nube están detenidas.<br>Puede comprobar que un servicio de nube tiene IP reservada usando los siguientes cmdlets de Powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## Pasos siguientes
- [Administración de máquinas virtuales](backup-azure-manage-vms)

<!---HONumber=GIT-SubDir-->