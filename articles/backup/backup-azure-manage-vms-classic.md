---
title: "Administración y supervisión de copias de seguridad de máquinas virtuales de Azure | Microsoft Docs"
description: "Aprenda a administrar y supervisar las copias de seguridad de máquinas virtuales de Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: trinadhk;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d876bb1759600fa29a26730bfa8b4ec19db1e442
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="manage-common-azure-backup-jobs-and-trigger-alerts-in-the-classic-portal"></a>Administración de trabajos comunes de copia de seguridad de Azure y desencadenado de alertas en el Portal clásico
> [!div class="op_single_selector"]
> * [Administrar copias de seguridad de máquina virtual de Azure](backup-azure-manage-vms.md)
> * [Administrar copias de seguridad de máquina virtual clásica](backup-azure-manage-vms-classic.md)
>
>

Este artículo proporciona información acerca de las tareas de administración y supervisión comunes para las máquinas virtuales de modelo clásico protegidas en Azure.  

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). Consulte [Preparación del entorno de copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-prepare.md) para más información sobre cómo trabajar con las máquinas virtuales del modelo de implementación clásica.
>
> [!IMPORTANT]
>A partir de marzo de 2017, ya no podrá usar el portal clásico para crear almacenes de Backup.
>
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> A partir del 15 de octubre de 2017, no podrá usar PowerShell para crear almacenes de Backup. **El 1 de noviembre de 2017**:
>- Todos los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.

## <a name="manage-protected-virtual-machines"></a>Administrar máquinas virtuales protegidas
Para administrar máquinas virtuales protegidas:

1. Para ver y administrar la configuración de copia de seguridad para una máquina virtual, haga clic en la pestaña **Elementos protegidos** .
2. Haga clic en el nombre de un elemento protegido para ver la pestaña **Detalles de copia de seguridad** , que muestra información acerca de la última copia de seguridad.

    ![Copia de seguridad de máquina virtual](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. Para ver y administrar la configuración de directiva de copia de seguridad para una máquina virtual, haga clic en la pestaña **Directivas** .

    ![Directiva de máquina virtual](./media/backup-azure-manage-vms/manage-policy-settings.png)

    La pestaña **Directivas de copia de seguridad** muestra la directiva existente. Se puede modificar según sea necesario. Si necesita crear una nueva directiva, haga clic en **Crear** en la página **Directivas**. Tenga en cuenta que si desea quitar una directiva no debería tener ninguna máquina virtual asociada a esta.

    ![Directiva de máquina virtual](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. Puede obtener más información sobre las acciones o el estado de una máquina virtual en la página **Trabajos** . Haga clic en un trabajo en la lista para obtener más detalles, o filtre los trabajos de una máquina virtual específica.

    ![Trabajos](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>Copia de seguridad a petición de una máquina virtual
Puede realizar una copia de seguridad a petición de una máquina virtual una vez que esta esté configurada para la protección. Si está pendiente la copia de seguridad inicial para la máquina virtual, la copia de seguridad a petición creará una copia completa de la máquina virtual en el almacén de copia de seguridad de Azure. Si se ha realizado la primera copia de seguridad, la copia de seguridad a petición solo enviará al almacén de copia de seguridad de Azure los cambios que se hayan realizado desde la copia de seguridad anterior, es decir, siempre es incremental.

> [!NOTE]
> El intervalo de retención de una copia de seguridad a petición se establece en el valor de retención especificado para la retención diaria en la directiva de copia de seguridad correspondiente a la máquina virtual.  
>
>

Para realizar una copia de seguridad a petición de una máquina virtual:

1. Vaya a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como valor del campo **Tipo** (si no está ya seleccionado) y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)
2. Seleccione la máquina virtual de la que desea realizar copia de seguridad a petición. A continuación, haga clic en el botón **Copia de seguridad ahora**, situado en la parte inferior de la página.

    ![Copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now.png)

    Esto creará un trabajo de copia de seguridad en la máquina virtual seleccionada. El periodo de retención del punto de recuperación creado con este trabajo será el mismo que el especificado en la directiva asociada a la máquina virtual.

    ![Creación de trabajo de copia de seguridad](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > Para ver la directiva asociada a una máquina virtual, acceda a la máquina virtual en la página **Elementos protegidos** y vaya a la pestaña de la directiva de la copia de seguridad.
   >
   >
3. Una vez que se crea el trabajo, puede hacer clic en el botón **Ver trabajo** de la barra de notificación del sistema para ver el trabajo correspondiente en la página de los trabajos.

    ![Trabajo de copia de seguridad creado](./media/backup-azure-manage-vms/created-job.png)
4. Tras finalizar correctamente el trabajo, se creará un punto de recuperación que puede utilizar para restaurar la máquina virtual. Esto también incrementará el valor de la columna del punto de recuperación en 1 unidad en la página **Elementos protegidos** .

## <a name="stop-protecting-virtual-machines"></a>Detener la protección de máquinas virtuales
Puede detener las futuras copias de seguridad de una máquina virtual con las siguientes opciones:

* Conservar los datos de copia de seguridad asociados a la máquina virtual en el almacén de copia de seguridad de Azure
* Eliminación de datos de copia de seguridad asociados a la máquina virtual

Si ha seleccionado la opción de conservar los datos de copia de seguridad asociados a la máquina virtual, puede usar estos datos para restaurar la máquina virtual. Para obtener más detalles sobre el precio de estas máquinas virtuales, haga clic [aquí](https://azure.microsoft.com/pricing/details/backup/).

Para detener la protección de una máquina virtual:

1. Vaya a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como tipo de filtro (si no está ya seleccionado) y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)
2. Elija la máquina virtual y haga clic en **Detener protección** , en la parte inferior de la página.

    ![Detener protección](./media/backup-azure-manage-vms/stop-protection.png)
3. De forma predeterminada, la copia de seguridad de Azure no elimina los datos de copia de seguridad asociados a la máquina virtual.

    ![Confirmación de la detención de la protección](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Si desea eliminar los datos de copia de seguridad, active la casilla de verificación.

    ![Casilla de verificación](./media/backup-azure-manage-vms/checkbox.png)

    Especifique un motivo para detener la copia de seguridad. Aunque esto es opcional, proporcionar un motivo ayudará a Copia de seguridad de Azure a trabajar en los comentarios y dar prioridad a los escenarios del cliente.
4. Haga clic en el botón **Enviar** para enviar el trabajo **Detener protección**. Haga clic en **Ver trabajo** para ver el trabajo correspondiente en la página **Trabajos**.

    ![Detener protección](./media/backup-azure-manage-vms/stop-protect-success.png)

    Si no ha elegido la opción **Eliminar datos de copia de seguridad asociados** en el asistente **Detener protección**, publique los cambios del estado de protección y la finalización del trabajo en **Protección detenida**. Los datos permanecen en Copia de seguridad de Azure hasta que se eliminen explícitamente. Para eliminar los datos, seleccione la máquina virtual en la página **Elementos protegidos** y haga clic en **Eliminar**.

    ![Protección detenida](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Si ha elegido la opción **Eliminar datos de copia de seguridad asociados**, la máquina virtual no formará parte de la página **Elementos protegidos**.

## <a name="re-protect-virtual-machine"></a>Volver a proteger la máquina virtual
Si no ha elegido la opción **Eliminar datos de copia de seguridad asociados** en **Detener protección**, puede volver a proteger la máquina virtual siguiendo unos pasos similares a los de la copia de seguridad de las máquinas virtuales registradas. Una vez protegida, esta máquina virtual conservará los datos de copia de seguridad antes de detener la protección, así como los puntos de recuperación creados después de aplicar de nuevo la protección.

Después de aplicar de nuevo la protección, el estado de protección de la máquina virtual cambiará a **Protegida** si hay puntos de recuperación anteriores a la **detención de la protección**.

  ![Máquina virtual protegida de nuevo](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> Al volver a proteger la máquina virtual, puede elegir una directiva diferente de la directiva con la que estaba protegida inicialmente la máquina virtual.
>
>

## <a name="unregister-virtual-machines"></a>Anular registro de máquinas virtuales
Si desea quitar la máquina virtual del almacén de copia de seguridad:

1. Haga clic en el botón **ANULAR REGISTRO** en la parte inferior de la página.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/unregister-button.png)

    Aparecerá una notificación en la parte inferior de la pantalla de confirmación de solicitud. Haga clic en **Sí** para continuar.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>Eliminación de datos de copia de seguridad
Los datos de copia de seguridad asociados a una máquina virtual se pueden eliminar en los siguientes momentos:

* Durante el trabajo de detención de la protección
* Después de realizar un trabajo de detención de la protección en una máquina virtual

Para eliminar los datos de copia de seguridad de una máquina virtual que se encuentre en estado de *Protección detenida* después de haber realizado correctamente un trabajo **Detener copia de seguridad** :

1. Vaya a la página **Elementos protegidos**, elija **Máquina virtual de Azure** como *tipo* y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)
2. Seleccione la máquina virtual. La máquina virtual estará en el estado **Protección detenida** .

    ![Protección detenida](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. Haga clic en el botón **ELIMINAR** , situado en la parte inferior de la página.

    ![Eliminación de copia de seguridad](./media/backup-azure-manage-vms/delete-backup.png)
4. En el asistente **Eliminar datos de copia de seguridad**, elija un motivo para eliminar los datos de copia de seguridad (muy recomendable) y haga clic en **Enviar**.

    ![Eliminación de datos de copia de seguridad](./media/backup-azure-manage-vms/delete-backup-data.png)
5. Esto creará un trabajo para eliminar los datos de copia de seguridad de la máquina virtual seleccionada. Haga clic en **Ver trabajo** para ver el trabajo correspondiente en la página Trabajos.

    ![Eliminación de datos correcta](./media/backup-azure-manage-vms/delete-data-success.png)

    Una vez completado el trabajo, se eliminará la entrada correspondiente a la máquina virtual de la página **Elementos protegidos** .

## <a name="dashboard"></a>Panel
En la página **Panel** , puede revisar la información sobre las máquinas virtuales de Azure, su almacenamiento y los trabajos asociados a ellas durante las últimas 24 horas. Puede ver el estado de copia de seguridad y los errores de copia de seguridad asociados.

![Panel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> Los valores indicados en el panel se actualizan cada 24 horas.
>
>

## <a name="auditing-operations"></a>Operaciones de auditoría
Copia de seguridad de Azure proporciona la revisión de los "registros de operación" de las operaciones de copia de seguridad desencadenadas por el cliente, lo que facilita ver exactamente qué operaciones de administración se realizaron en el almacén de copia de seguridad. Los registros de operaciones permiten excelentes análisis finales y soportes técnicos de auditoría para las operaciones de copia de seguridad.

Las siguientes operaciones se registran en los registros de operaciones:

* Registro
* Unregister 
* Configuración de protección
* Copia de seguridad (tanto programada como a petición mediante BackupNow)
* Restauración
* Detener protección
* Eliminación de datos de copia de seguridad
* Add policy
* Eliminación de directiva
* Actualización de directiva
* Cancelar trabajo

Para ver los registros de operación correspondientes a un almacén de copia de seguridad:

1. Vaya a **Servicios de administración** en Azure Portal y, a continuación, haga clic en la pestaña **Registros de operaciones**.

    ![Registros de operaciones](./media/backup-azure-manage-vms/ops-logs.png)
2. En los filtros, seleccione **Copia de seguridad** como *Tipo*, especifique el nombre del almacén de copia de seguridad en *Nombre de servicio* y haga clic en **Enviar**.

    ![Filtro de registros de operaciones](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. En los registros de operaciones, seleccione cualquier operación y haga clic en **Detalles** para ver los detalles correspondientes a una operación.

    ![Detalles de obtención de registros de operaciones](./media/backup-azure-manage-vms/ops-logs-details.png)

    El **asistente para detalles** contiene información sobre la operación desencadenada, Id. de trabajo, recurso en el que se desencadena esta operación y hora de inicio de la operación.

    ![Detalles de la operación](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>Notificaciones de alerta
Puede obtener notificaciones de alerta personalizadas para los trabajos del portal. Esto se consigue mediante la definición de reglas de alerta basadas en PowerShell en los eventos de registros operativos. Se recomienda usar *PowerShell versión 1.3.0 o posterior*.

Para definir una notificación personalizada que avise de errores de copia de seguridad, puede ver un comando de ejemplo parecido al siguiente:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: puede obtenerlo en la ventana emergente de registros de operaciones tal como se describe en la sección anterior. ResourceUri en una ventana emergente de detalles de una operación es el ResourceId que se debe indicar para este cmdlet.

**OperationName**: tendrá el formato "Microsoft.Backup/backupvault/<EventName>" donde EventName es uno de estos valores: Register,Unregister,ConfigureProtection,Backup,Restore,StopProtection,DeleteBackupData,CreateProtectionPolicy,DeleteProtectionPolicy,UpdateProtectionPolicy

**Status**: los valores admitidos son: Started, Succeeded y Failed.

**ResourceGroup**: ResourceGroup del recurso en el que se desencadena la operación. Puede obtenerlo del valor de ResourceId. El valor entre los campos */resourceGroups/* y */providers/* en el valor de ResourceId es el valor de ResourceGroup.

**Name**: nombre de la regla de alerta.

**CustomEmail**: especifique la dirección de correo electrónico personalizada a la que desea enviar la notificación de alerta.

**SendToServiceOwners**: esta opción envía la notificación de alerta a todos los administradores y coadministradores de la suscripción. Se puede utilizar en el cmdlet **New-AzureRmAlertRuleEmail** .

### <a name="limitations-on-alerts"></a>Limitaciones de las alertas
Las alertas basadas en eventos están sometidas a las siguientes limitaciones:

1. Las alertas se activan en todas las máquinas virtuales del almacén de copia de seguridad. No es posible personalizar esto para obtener alertas para un conjunto específico de máquinas virtuales de un almacén de copia de seguridad.
2. Esta característica se encuentra en versión preliminar. [Más información](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Recibirá las alertas de alerts-noreply@mail.windowsazure.com. Actualmente, no se puede modificar el remitente de correo electrónico.

## <a name="next-steps"></a>Pasos siguientes
* [Restauración de máquinas virtuales de Azure](backup-azure-restore-vms.md)
