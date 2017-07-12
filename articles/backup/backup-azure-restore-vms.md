---
title: "Restauración de una copia de seguridad de una máquina virtual | Microsoft Docs"
description: "Información sobre cómo restaurar una máquina virtual de Azure desde un punto de recuperación"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "restaurar copias de seguridad; cómo restaurar; punto de recuperación;"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: trinadhk; jimpark;
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: c6f00c51456ebf5b2a0c2464272bffcec2959266
ms.contentlocale: es-es
ms.lasthandoff: 06/16/2017


---
<a id="restore-virtual-machines-in-azure" class="xliff"></a>

# Restauración de máquinas virtuales en Azure
> [!div class="op_single_selector"]
> * [Restauración de máquinas virtuales en el Portal de Azure](backup-azure-arm-restore-vms.md)
> * [Restauración de máquinas virtuales en el portal clásico](backup-azure-restore-vms.md)
>
>

Restaure una máquina virtual en una nueva máquina virtual desde las copias de seguridad almacenadas en el almacén de Azure Backup; para ello, siga estos pasos.

> [!IMPORTANT]
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> **A partir del 1 de noviembre de 2017**:
>- Los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>

<a id="restore-workflow" class="xliff"></a>

## Restauración del flujo de trabajo
<a id="step-1-choose-an-item-to-restore" class="xliff"></a>

### Paso 1: Elección del elemento que se restaura
1. Navegue hasta la pestaña **Elementos protegidos** y seleccione la máquina virtual que desea restaurar en una máquina virtual nueva.

    ![Elementos protegidos](./media/backup-azure-restore-vms/protected-items.png)

    La columna **Punto de recuperación** de la página **Elementos protegidos** le indicará el número de puntos de recuperación de una máquina virtual. La columna **Punto de recuperación más reciente** indica el momento de la copia de seguridad más reciente desde la que se puede restaurar una máquina virtual.
2. Haga clic en **Restaurar** para abrir el Asistente para **restaurar un elemento**.

    ![restauración de un elemento](./media/backup-azure-restore-vms/restore-item.png)

<a id="step-2-pick-a-recovery-point" class="xliff"></a>

### Paso 2: Elección de un punto de recuperación
1. En la pantalla **Seleccionar un punto de recuperación** , puede restaurar desde el punto de recuperación más reciente, o desde un punto anterior en el tiempo. La opción predeterminada seleccionada cuando se abre el asistente es *Punto de recuperación más reciente*.

    ![Seleccionar un punto de recuperación](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Para selecciona un momento anterior en el tiempo, elija la opción **Seleccionar fecha** en la lista desplegable y seleccione una fecha en el control de calendario, para lo que debe hacer clic en el **icono del calendario**. En el control, todas las fechas que tienen puntos de recuperación se rellenan con un tono de color gris claro y son seleccionables para el usuario.

    ![Seleccionar una fecha](./media/backup-azure-restore-vms/select-date.png)

    Una vez que se hace clic en una fecha en el control de calendario, los puntos de recuperación disponibles en la fecha se mostrarán en la siguiente tabla de puntos de recuperación. La columna **Tiempo** indica el momento en que se tomó la instantánea. La columna **Tipo** muestra la [coherencia](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) del punto de recuperación. El encabezado de tabla muestra el número de puntos de recuperación disponibles ese día entre paréntesis.

    ![Puntos de recuperación](./media/backup-azure-restore-vms/recovery-points.png)
3. Seleccione el punto de recuperación desde la tabla **Puntos de recuperación** y haga clic en la flecha Siguiente para ir a la pantalla siguiente.

<a id="step-3-specify-a-destination-location" class="xliff"></a>

### Paso 3: Especificación de una ubicación de destino
1. En la pantalla **Seleccionar instancia de restauración** , especifique detalles de dónde desea restaurar la máquina virtual.

   * Especifique el nombre de la máquina virtual: en un servicio de nube determinado, el nombre de la máquina virtual debe ser único. No admitimos la sobrescritura de máquinas virtuales existentes.
   * Seleccione un servicio de nube para la máquina virtual: esto es obligatorio para la creación de una máquina virtual. Puede usar un servicio de nube existente o crear un nuevo servicio de nube.

        El nombre del servicio en la nube que se seleccione debe ser único global. Por lo general, el nombre del servicio en la nube está asociado a una dirección URL pública, que tiene el siguiente formato: [servicioEnLaNube].cloudapp.net. Azure no permite crear un servicio en la nube si ya se ha utilizado el nombre. Si decide crear un nuevo servicio en la nube, se le dará el mismo nombre que a la máquina virtual, en cuyo caso el nombre de la máquina virtual seleccionado debe ser suficientemente exclusivo como para aplicarlo al servicio en la nube asociado.

        Solo se muestran servicios en la nube y redes virtuales que no están asociados con ningún grupo de afinidad en los detalles de la instancia de restauración. [Más información](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
2. Seleccione una cuenta de almacenamiento para la máquina virtual: esto es obligatorio para la creación de una máquina virtual. Puede seleccionar desde cuentas de almacenamiento existentes en la misma región que el almacén de copia de seguridad de Azure. No se admiten cuentas de almacenamiento redundantes de zona ni de almacenamiento Premium.

    Si no hay ninguna cuenta de almacenamiento con configuración compatible, cree una cuenta de almacenamiento de configuración admitida antes de iniciar la operación de restauración.

    ![Seleccionar una red virtual](./media/backup-azure-restore-vms/restore-sa.png)
3. Seleccione una red virtual: la red virtual (VNET) para la máquina virtual debe seleccionarse en el momento de crear la máquina virtual. La interfaz de usuario de restauración muestra todas las redes virtuales dentro de esta suscripción que se pueden utilizar. No es obligatorio seleccionar una red virtual para la máquina virtual restaurada; podrá conectarse a la máquina virtual restaurada a través de Internet incluso si no se aplica la red virtual.

    Si el servicio de nube seleccionado está asociado a una red virtual, no puede cambiar la red virtual.

    ![Seleccionar una red virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Seleccione una subred: en caso de que la red virtual tenga subredes, de forma predeterminada se seleccionará la primera subred. Elija la subred que prefiera de las opciones de la lista desplegable. Para obtener información acerca de la subred, vaya a la extensión Redes de la [página principal del portal](https://manage.windowsazure.com/), vaya a **Redes virtuales** y seleccione la red virtual. Acceda a Configurar para ver los detalles de la subred.

    ![Seleccionar una subred](./media/backup-azure-restore-vms/select-subnet.png)
5. Haga clic en el icono **Enviar** en el asistente para enviar los detalles y crear un trabajo de restauración.

<a id="track-the-restore-operation" class="xliff"></a>

## Seguimiento de la operación de restauración
Una vez que ha especificado toda la información en el Asistente de restauración y lo ha enviado, la Copia de seguridad de Azure intentará crear un trabajo para realizar un seguimiento de la operación de restauración.

![Creación de un trabajo de restauración](./media/backup-azure-restore-vms/create-restore-job.png)

Si la creación del trabajo se realiza correctamente, verá una notificación de aviso que indica que se ha creado un trabajo. Para más detalles, haga clic en el botón **Ver trabajo**, lo que le llevará a la pestaña **Trabajos**.

![Trabajo de restauración creado](./media/backup-azure-restore-vms/restore-job-created.png)

Una vez finalizada la operación de restauración, se marcará como completada en la pestaña **Trabajos** .

![Trabajo de restauración completado](./media/backup-azure-restore-vms/restore-job-complete.png)

Después de restaurar la máquina virtual, puede que necesite volver a instalar las extensiones existentes en la máquina virtual original y [modificar los extremos](../virtual-machines/windows/classic/setup-endpoints.md) de la máquina virtual en el Portal de Azure.

<a id="post-restore-steps" class="xliff"></a>

## Pasos posteriores a la restauración
Si utiliza una distribución de Linux basada en cloud-init, como Ubuntu, la contraseña se bloqueará después de la restauración por seguridad. Use la extensión VMAccess en la máquina virtual restaurada para [restablecer la contraseña](../virtual-machines/linux/classic/reset-access.md). Se recomienda utilizar claves SSH en estas distribuciones para evitar que se restablezca la contraseña después de la restauración.

<a id="backup-for-restored-vms" class="xliff"></a>

## Copia de seguridad de máquinas virtuales restauradas
Si ha restaurado la máquina virtual en el mismo servicio en la nube con el mismo nombre con el que originalmente se hizo copia de seguridad de ella, la copia de seguridad continuará en la máquina virtual después de la restauración. Si ha restaurado la máquina virtual en un servicio en la nube diferente o ha especificado un nombre diferente para la VM restaurada, esta se trata como nueva, por lo que deberá configurar su copia de seguridad.

<a id="restoring-a-vm-during-azure-datacenter-disaster" class="xliff"></a>

## Restauración de una máquina virtual en caso de desastre de centro de datos de Azure
Copia de seguridad de Azure permite restaurar copias de seguridad de máquinas virtuales en el centro de datos asociado, en caso de que el centro de datos principal donde se ejecutan las máquinas virtuales experimente una situación de desastre y haya configurado el almacén de copia de seguridad para que tenga redundancia geográfica. En estos casos, deberá seleccionar una cuenta de almacenamiento que esté presente en el centro de datos asociado. El resto del proceso de restauración permanece igual. Copia de seguridad de Azure usa el servicio de proceso de la zona geográfica asociada para crear la máquina virtual restaurada. Más información sobre la [resistencia del centro de datos de Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

<a id="restoring-domain-controller-vms" class="xliff"></a>

## Restauración de máquinas virtuales de controlador de dominio
Copia de seguridad de las máquinas virtuales de controlador de dominio (DC) es un escenario admitido con Copia de seguridad de Azure. Sin embargo, se debe tener cuidado durante el proceso de restauración. El proceso de restauración correcto depende de la estructura del dominio. El caso más simple es tener un único DC en un solo dominio. El caso más común en las cargas de producción es tener un solo dominio con varios DC y, quizás, algún DC local. Por último, puede tener un bosque con varios dominios.

Desde la perspectiva de Active Directory, una VM de Azure es como cualquier otra VM de un hipervisor admitido moderno. La principal diferencia respecto a los hipervisores locales es que no hay ninguna consola de VM disponible en Azure. Es necesaria una consola para determinados escenarios, como para una recuperación mediante una copia de seguridad de reconstrucción completa (BMR). Sin embargo, la restauración de una VM desde el almacén de Backup es una sustitución completa para una BMR. El modo de restauración de Active Directory (DSRM) también está disponible, de modo que todos los escenarios de recuperación de Active Directory son viables. Para obtener más información general, consulte [Consideraciones relacionadas con la copia de seguridad y la restauración para controladores de dominio virtualizados](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) y [Planning for Active Directory Forest Recovery](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx) (Planificación de la recuperación de bosques de Active Directory).

<a id="single-dc-in-a-single-domain" class="xliff"></a>

### Controlador de dominio único en un solo dominio
Se puede restaurar la máquina virtual (como cualquier otra máquina virtual) desde el Portal de Azure o mediante PowerShell.

<a id="multiple-dcs-in-a-single-domain" class="xliff"></a>

### Varios controladores de dominio en un solo dominio
Cuando se pueda acceder a otros controladores de dominio del mismo dominio a través de la red, el controlador de dominio se podrá restaurar como cualquier VM. Si se trata del último controlador de dominio que queda en el dominio o bien se lleva a cabo una recuperación en una red aislada, debe seguirse el procedimiento de recuperación de bosques.

<a id="multiple-domains-in-one-forest" class="xliff"></a>

### Varios dominios en un solo bosque
Cuando se pueda acceder a otros controladores de dominio del mismo dominio a través de la red, el controlador de dominio se podrá restaurar como cualquier VM. Sin embargo, en el resto de casos se recomienda una recuperación de bosques.

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

<a id="restoring-vms-with-special-network-configurations" class="xliff"></a>

## Restauración de máquinas virtuales con configuraciones de red especiales
Copia de seguridad de Azure es compatible con copia de seguridad para las siguientes configuraciones de red especiales de máquinas virtuales.

* Máquinas virtuales en el equilibrador de carga (interno y externo)
* Máquinas virtuales con varias direcciones IP reservadas
* Paso 3: Creación de máquinas virtuales con varias NIC

Estas configuraciones exigen las siguientes consideraciones al restaurarlas.

> [!TIP]
> Use el flujo de restauración basado en PowerShell para volver a crear la configuración de red especial posterior a la restauración de máquinas virtuales.
>
>

<a id="restoring-from-the-ui" class="xliff"></a>

### Restauración a partir de la interfaz de usuario:
Al restaurar desde la interfaz de usuario, **elija siempre un nuevo servicio en la nube**. Tenga en cuenta que, puesto que portal solo acepta parámetros obligatorios durante el flujo de restauración, las máquinas virtuales restauradas con la interfaz de usuario perderán la configuración de red especial que posean. En otras palabras, las máquinas virtuales restauradas serán máquinas virtuales normales sin configuración de equilibrador de carga ni de varias NIC o varias IP reservadas.

<a id="restoring-from-powershell" class="xliff"></a>

### Restauración a partir de PowerShell:
PowerShell tiene la capacidad de restaurar solo los discos de máquina virtual de la copia de seguridad y no crear la máquina virtual. Esto resulta útil al restaurar máquinas virtuales que requieran las configuraciones de red especiales mencionadas anteriormente.

Con el fin de volver a crear por completo los discos de máquina virtual posteriores a la restauración, siga estos pasos:

1. Restaure los discos del almacén de copia de seguridad mediante [PowerShell de Azure Backup](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Cree la configuración de máquina virtual necesaria para el equilibrador de carga/varias NIC/varias IP reservadas mediante los cmdlets de PowerShell y úsela para crear la máquina virtual de la configuración que quiera.

   * Creación de una máquina virtual en el servicio en la nube con el [equilibrador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Cree una máquina virtual para conectarse al [equilibrador de carga accesible desde Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Creación de una máquina virtual con [varias NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Creación de una máquina virtual con [varias direcciones IP reservadas](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
* [Solución de errores](backup-azure-vms-troubleshoot.md#restore)
* [Administración de máquinas virtuales](backup-azure-manage-vms.md)

