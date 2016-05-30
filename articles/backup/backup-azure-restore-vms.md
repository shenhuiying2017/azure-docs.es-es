
<properties
	pageTitle="Restauración de una copia de seguridad de una máquina virtual | Microsoft Azure"
	description="Información sobre cómo restaurar una máquina virtual de Azure desde un punto de recuperación"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""
	keywords="restaurar copias de seguridad; cómo restaurar; punto de recuperación;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="trinadhk; jimpark;"/>


# Restauración de máquinas virtuales en Azure

> [AZURE.SELECTOR]
- [Restauración de máquinas virtuales en el Portal de Azure](backup-azure-arm-restore-vms.md)
- [Restauración de máquinas virtuales en el portal clásico](backup-azure-restore-vms.md)


Restaure una máquina virtual en una nueva máquina virtual desde las copias de seguridad almacenadas en el almacén de Copia de seguridad de Azure; para ello, siga estos pasos.

## Restauración del flujo de trabajo

### 1\. Selección de un elemento para restaurar

1. Navegue hasta la pestaña **Elementos protegidos** y seleccione la máquina virtual que desea restaurar en una máquina virtual nueva.

    ![Elementos protegidos](./media/backup-azure-restore-vms/protected-items.png)

    La columna **Punto de recuperación** en la página **Elementos protegidos** le indicará el número de puntos de recuperación para una máquina virtual. La columna **Punto de recuperación más reciente** indica el momento de la copia de seguridad más reciente desde la que se puede restaurar una máquina virtual.

2. Haga clic en **Restaurar** para abrir el asistente de **restauración de un elemento**.

    ![Restaurar un elemento](./media/backup-azure-restore-vms/restore-item.png)

### 2\. Elegir un punto de recuperación

1. En la pantalla **Seleccionar un punto de recuperación**, puede restaurar desde el punto de recuperación más reciente, o desde un punto anterior en el tiempo. La opción predeterminada seleccionada cuando se abre el asistente es *Punto de recuperación más reciente*.

    ![Seleccionar un punto de recuperación](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Para elegir un momento anterior en el tiempo, elija la opción **Seleccionar fecha** en la lista desplegable y seleccione una fecha en el control de calendario haciendo clic en el **icono del calendario**. En el control, todas las fechas que tienen puntos de recuperación se rellenan con un tono de color gris claro y son seleccionables para el usuario.

    ![Seleccionar una fecha](./media/backup-azure-restore-vms/select-date.png)

    Una vez que se hace clic en una fecha en el control de calendario, los puntos de recuperación disponibles en la fecha se mostrarán en la siguiente tabla de puntos de recuperación. La columna **Tiempo** indica el momento en que se tomó la instantánea. La columna **Tipo** muestra la [coherencia](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) del punto de recuperación. El encabezado de tabla muestra el número de puntos de recuperación disponibles de ese día entre paréntesis.

    ![Puntos de recuperación](./media/backup-azure-restore-vms/recovery-points.png)

3. Seleccione el punto de recuperación desde la tabla **Puntos de recuperación** y haga clic en la flecha Siguiente para ir a la pantalla siguiente.

### 3\. Especifique una ubicación de destino.

1. En la pantalla **Seleccionar instancia de restauración**, especifique detalles de dónde desea restaurar la máquina virtual.

  - Especifique el nombre de la máquina virtual: en un servicio de nube determinado, el nombre de la máquina virtual debe ser único. No admitimos la sobrescritura de máquinas virtuales existentes. 
  - Seleccione un servicio de nube para la máquina virtual: esto es obligatorio para la creación de una máquina virtual. Puede usar un servicio de nube existente o crear un nuevo servicio de nube.

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

2. Seleccione una cuenta de almacenamiento para la máquina virtual: esto es obligatorio para la creación de una máquina virtual. Puede seleccionar desde cuentas de almacenamiento existentes en la misma región que el almacén de copia de seguridad de Azure. No se admiten cuentas de almacenamiento redundantes de zona ni de almacenamiento Premium.

    Si no hay ninguna cuenta de almacenamiento con configuración compatible, cree una cuenta de almacenamiento de configuración admitida antes de iniciar la operación de restauración.

    ![Seleccionar una red virtual](./media/backup-azure-restore-vms/restore-sa.png)

3. Seleccione una red virtual: la red virtual (VNET) para la máquina virtual debe seleccionarse en el momento de crear la máquina virtual. La interfaz de usuario de restauración muestra todas las redes virtuales dentro de esta suscripción que se pueden utilizar. No es obligatorio seleccionar una red virtual para la máquina virtual restaurada; podrá conectarse a la máquina virtual restaurada a través de Internet incluso si no se aplica la red virtual.

    Si el servicio de nube seleccionado está asociado a una red virtual, no puede cambiar la red virtual.

    ![Seleccionar una red virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Seleccione una subred: en caso de que la red virtual tenga subredes, de forma predeterminada se seleccionará la primera subred. Elija la subred que prefiera de las opciones de la lista desplegable. Para obtener detalles acerca de la subred, vaya a la extensión Redes en la [página principal del portal](https://manage.windowsazure.com/), vaya a **Redes virtuales** y seleccione la red virtual. Acceda a Configurar para ver los detalles de la subred.

    ![Seleccionar una subred](./media/backup-azure-restore-vms/select-subnet.png)

5. Haga clic en el icono **Enviar** en el asistente para enviar los detalles y crear un trabajo de restauración.

## Seguimiento de la operación de restauración
Una vez que ha especificado toda la información en el Asistente de restauración y lo ha enviado, la Copia de seguridad de Azure intentará crear un trabajo para realizar un seguimiento de la operación de restauración.

![Creación de un trabajo de restauración](./media/backup-azure-restore-vms/create-restore-job.png)

Si la creación del trabajo se realiza correctamente, verá una notificación de aviso que indica que se ha creado un trabajo. Puede obtener más detalles haciendo clic en el botón **Ver trabajo** que le llevará a la pestaña **Trabajos**.

![Trabajo de restauración creado](./media/backup-azure-restore-vms/restore-job-created.png)

Una vez finalizada la operación de restauración, se marcará como completada en la pestaña **Trabajos**.

![Trabajo de restauración completado](./media/backup-azure-restore-vms/restore-job-complete.png)

Después de restaurar la máquina virtual, puede que necesite volver a instalar las extensiones existentes en la máquina virtual original y [modificar los extremos](virtual-machines-set-up-endpoints) de la máquina virtual en el Portal de Azure.

## Copia de seguridad de máquinas virtuales restauradas
Si ha restaurado la máquina virtual en el mismo servicio en la nube con el mismo nombre con el que originalmente se hizo copia de seguridad de ella, la copia de seguridad continuará en la máquina virtual después de la restauración. Si ha restaurado la VM en un servicio en la nube diferente o ha especificado un nombre diferente para la VM restaurada, esta se trata como nueva y deberá configurar la copia de seguridad para ella.

## Restauración de una máquina virtual en caso de desastre de centro de datos de Azure
Copia de seguridad de Azure permite restaurar copias de seguridad de máquinas virtuales en el centro de datos asociado, en caso de que el centro de datos principal donde se ejecutan las máquinas virtuales experimente una situación de desastre y haya configurado el almacén de copia de seguridad para que tenga redundancia geográfica. En estos casos, deberá seleccionar una cuenta de almacenamiento que esté presente en el centro de datos asociado. El resto del proceso de restauración permanece igual. Copia de seguridad de Azure usa el servicio de proceso de la zona geográfica asociada para crear la máquina virtual restaurada.

## Restauración de máquinas virtuales de controlador de dominio
Copia de seguridad de las máquinas virtuales de controlador de dominio (DC) es un escenario admitido con Copia de seguridad de Azure. Sin embargo, se debe tener cierto cuidado durante el proceso de restauración. La experiencia de restauración es muy diferente para las máquinas virtuales de controlador de dominio en una configuración de un único controlador de dominio frente a las máquinas virtuales en una configuración de varios controladores de dominio.

### Controlador de dominio único
Se puede restaurar la máquina virtual (como cualquier otra máquina virtual) desde el Portal de Azure o mediante PowerShell.

### Varios controladores de dominio
Si tiene un entorno de varios controladores de dominio, estos tienen su propia manera de mantener los datos sincronizados. Cuando se restaura un punto de copia de seguridad anterior *sin las debidas precauciones*, proceso de reversión de USN puede causar estragos en un entorno de varios controladores de dominio. La forma adecuada de recuperar dicha VM es iniciarla en modo DSRM.

El problema surge porque el modo DSRM no está presente en Azure. Así que para restaurar dicha VM, no puede usar el Portal de Azure. El único mecanismo de restauración admitido es la restauración basada en disco mediante PowerShell.

>[AZURE.WARNING] En el caso de máquinas virtuales de controlador de dominio en un entorno de varios controladores de dominio, no use el Portal de Azure para la restauración. Solo admite la restauración basada en PowerShell.

Obtenga más información sobre el [problema de reversión de USN](https://technet.microsoft.com/library/dd363553) y las estrategias sugeridas para corregirlo.

## Restauración de máquinas virtuales con configuraciones de red especiales
Copia de seguridad de Azure es compatible con copia de seguridad para las siguientes configuraciones de red especiales de máquinas virtuales.

- Máquinas virtuales en el equilibrador de carga (interno y externo)
- Máquinas virtuales con varias direcciones IP reservadas
- Paso 3: Creación de máquinas virtuales con varias NIC

Estas configuraciones exigen las siguientes consideraciones al restaurarlas.

>[AZURE.TIP] Use el flujo de restauración basado en PowerShell para volver a crear la configuración de red especial posterior a la restauración de máquinas virtuales.

### Restauración a partir de la interfaz de usuario:
Al restaurar desde la interfaz de usuario, **elija siempre un nuevo servicio en la nube**. Tenga en cuenta que, puesto que portal solo acepta parámetros obligatorios durante el flujo de restauración, las máquinas virtuales restauradas con la interfaz de usuario perderán la configuración de red especial que posean. En otras palabras, las máquinas virtuales restauradas serán máquinas virtuales normales sin configuración de equilibrador de carga ni de varias NIC o varias IP reservadas.

### Restauración a partir de PowerShell:
PowerShell tiene la capacidad de restaurar solo los discos de máquina virtual de la copia de seguridad y no crear la máquina virtual. Esto resulta útil al restaurar máquinas virtuales que requieran las configuraciones de red especiales mencionadas anteriormente.

Con el fin de volver a crear por completo los discos de máquina virtual posteriores a la restauración, siga estos pasos:

1. Restaure los discos desde el almacén de copia de seguridad mediante [PowerShell con Copia de seguridad de Azure](../backup-azure-vms-automation.md#restore-an-azure-vm).

2. Cree la configuración de máquina virtual necesaria para el equilibrador de carga/varias NIC/varias IP reservadas mediante los cmdlets de PowerShell y úsela para crear la máquina virtual de la configuración que quiera.
	- Creación de una máquina virtual en el servicio en la nube con el [equilibrador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
	- Creación de una máquina virtual para conectarse al [equilibrador de carga accesible desde Internet](https://azure.microsoft.com/es-ES/documentation/articles/load-balancer-internet-getstarted/)
	- Creación de una máquina virtual con [varias NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
	- Creación de una máquina virtual con [varias direcciones IP reservadas](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)


## Pasos siguientes
- [Solución de errores](backup-azure-vms-troubleshoot.md#restore)
- [Administración de máquinas virtuales](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0518_2016-->