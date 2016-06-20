
<properties
	pageTitle="Administración y supervisión de copias de seguridad de máquinas virtuales implementadas por Resource Manager | Microsoft Azure"
	description="Aprenda a administrar y supervisar las copias de seguridad de máquinas virtuales implementadas por Resource Manager."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# Administración y supervisión de copias de seguridad de máquinas virtuales de Azure

> [AZURE.SELECTOR]
- [Administración de copias de seguridad de máquinas virtuales de Azure](backup-azure-manage-vms.md)
- [Administración de copias de seguridad de máquinas virtuales clásicas](backup-azure-manage-vms-classic.md)

En este artículo se proporciona una guía para administrar las copias de seguridad de las máquinas virtuales y se explica la información de copia de seguridad disponible en el panel del portal. La orientación de este artículo se aplica al uso de máquinas virtuales con almacenes de Servicios de recuperación. Este artículo no trata sobre la creación de máquinas virtuales ni explica cómo protegerlas. Para ver una introducción sobre la protección de las máquinas virtuales implementadas por Azure Resource Manager en Azure con un almacén de Servicios de recuperación, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Servicios de recuperación](backup-azure-vms-first-look-arm.md).

## Acceso a almacenes y máquinas virtuales protegidas

En el Portal de Azure, el panel Almacén de Servicios de recuperación proporciona acceso a información sobre el almacén, como:

- la instantánea de copia de seguridad más reciente, que también es el último punto de restauración <br>
- la directiva de copia de seguridad <br>
- tamaño total de todas las instantáneas de copia de seguridad <br>
- número de máquinas virtuales que están protegidas con el almacén <br>

Muchas tareas de administración con una copia de seguridad de máquina virtual comienzan con la apertura del almacén en el panel. Sin embargo, dado que los almacenes sirven para proteger varios elementos (o varias máquinas virtuales), para ver los detalles sobre una máquina virtual determinada, debe abrir el panel del elemento del almacén. El procedimiento siguiente muestra cómo abrir el *panel del almacén* y después el *panel del elemento del almacén*. En ambos procedimientos, se ofrecen "sugerencias" sobre cómo agregar el almacén y el elemento del almacén al Panel de Azure con el comando Anclar al panel. La opción Anclar al panel es una manera de crear un acceso directo al almacén o al elemento. También puede ejecutar comandos habituales desde el acceso directo.

>[AZURE.TIP] Si tiene varios paneles y hojas abiertos, puede usar el control deslizante azul oscuro en la parte inferior de la ventana para deslizar la vista del Panel de Azure hacia delante y hacia atrás.

![Vista completa con control deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

### Apertura de un almacén de Servicios de recuperación en el panel:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En el menú central, haga clic en **Examinar** y, en la lista de recursos, escriba **Servicios de recuperación**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Servicios de recuperación**.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    Se muestra la lista de almacenes de Servicios de recuperación.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Si ancla un almacén al Panel de Azure, ese almacén es accesible de inmediato cuando abra el Portal de Azure. Para anclar un almacén al panel, en la lista de almacenes, haga clic con el botón derecho en el almacén y seleccione **Anclar al panel**.

3. En la lista de almacenes, seleccione el almacén para abrir su panel. Cuando selecciona el almacén, se abren el panel del almacén y la hoja **Configuración**. En la siguiente imagen, el panel de **Contoso-vault** aparece resaltado.

    ![Abrir el panel del almacén y la hoja Configuración](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### Apertura del panel de un elemento del almacén

En el procedimiento anterior, abrió el panel del almacén. Para abrir el panel de un elemento del almacén:

1. En el panel del almacén, en el icono **Elementos de copia de seguridad**, haga clic en **Máquinas virtuales de Azure**.

    ![Abrir el icono Elementos de copia de seguridad](./media/backup-azure-manage-vms/contoso-vault.png)

    En la hoja **Elementos de copia de seguridad**, se muestra el último trabajo de copia de seguridad para cada elemento. En este ejemplo, hay una máquina virtual, demovm-markgal, protegida por este almacén.

    ![Icono Elementos de copia de seguridad](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] Para facilitar el acceso, puede anclar un elemento del almacén en el Panel de Azure. Para anclar un elemento del almacén, en la lista de elementos de almacén, haga clic con el botón derecho en el elemento y seleccione **Anclar al panel**.

2. En la hoja **Elementos de copia de seguridad**, haga clic en el elemento para abrir el panel del elemento del almacén.

    ![Icono Elementos de copia de seguridad](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Se abren el panel del elemento de almacén y su hoja **Configuración**.

    ![Panel Elementos de copia de seguridad con hoja Configuración](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    En el panel del elemento del almacén, puede llevar a cabo muchas tareas de administración claves, como:

    - cambiar de directiva o crear una directiva de copia de seguridad<br>
	- ver los puntos de restauración y su estado de coherencia <br>
	- hacer la copia de seguridad a petición de una máquina virtual <br>
	- detener la protección de máquinas virtuales <br>
	- reanudar la protección de una máquina virtual <br>
	- eliminar los datos de una copia de seguridad (o un punto de recuperación) <br>
	- [restaurar una copia de seguridad (o un punto de recuperación)](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

Para los procedimientos siguientes, el punto de partida es el panel del elemento del almacén.

## Cambio de directiva o creación de una directiva de copia de seguridad

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Toda la configuración** para abrir la hoja **Configuración**.

    ![Hoja Directiva de copia de seguridad](./media/backup-azure-manage-vms/all-settings-button.png)

2. En la hoja **Configuración**, haga clic en **Directiva de copia de seguridad** para abrir esa hoja.

    En la hoja, se muestran los detalles de frecuencia de copia de seguridad y duración de retención.

    ![Hoja Directiva de copia de seguridad](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. En el menú **Elegir directiva de copia de seguridad**:
    - Para cambiar de directiva, seleccione una directiva diferente y haga clic en **Guardar**. La nueva directiva se aplica inmediatamente al almacén. <br>
    - Para crear una directiva, seleccione **Crear nuevo**.

    ![Copia de seguridad de máquina virtual](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    Si quiere instrucciones para crear una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-manage-vms.md#defining-a-backup-policy).


## Copia de seguridad a petición de una máquina virtual
Puede realizar una copia de seguridad a petición de una máquina virtual una vez que esta esté configurada para la protección. Si está pendiente la copia de seguridad inicial para la máquina virtual, la copia de seguridad a petición creará una copia completa de la máquina virtual en el almacén de Servicios de recuperación. Si se ha completado la copia de seguridad inicial, una copia de seguridad a petición solo enviará los cambios respecto a la instantánea anterior al almacén de Servicios de recuperación, es decir, siempre es incremental.

>[AZURE.NOTE] La duración de retención para una copia de seguridad a petición es el valor de retención especificado para el punto de copia de seguridad diaria en la directiva. Si no se selecciona ningún punto de copia de seguridad diaria, se usa el semanal.

Para desencadenar la copia de seguridad a petición de una máquina virtual:

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Realizar copia de seguridad ahora**.

    ![Botón Realizar copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now-button.png)

    El portal se asegura de que desea iniciar un trabajo de copia de seguridad a petición. Haga clic en **Sí** para iniciar el trabajo.

    ![Botón Realizar copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now-check.png)

    El trabajo de copia de seguridad crea un punto de recuperación. La duración de retención del punto de recuperación creado será la misma que la especificada en la directiva asociada a la máquina virtual. Para realizar un seguimiento del progreso del trabajo, en el panel del almacén, haga clic en el icono **Trabajos de copia de seguridad**.


## Detener la protección de máquinas virtuales
Si opta por dejar de proteger una máquina virtual, se le pregunta si desea conservar los puntos de recuperación. Hay dos formas de dejar de proteger máquinas virtuales: detener todos los trabajos de copia de seguridad futuros y eliminar todos los puntos de recuperación o detener todos los trabajos de copia de seguridad futuros, pero dejar los puntos de recuperación. Dejar los puntos de recuperación almacenados conlleva un costo. Sin embargo, la ventaja de dejarlos es que puede restaurar la máquina virtual más adelante, si lo desea. Para obtener más detalles sobre el precio de estas máquinas virtuales, haga clic [aquí](https://azure.microsoft.com/pricing/details/backup/). Si opta por eliminar todos los puntos de recuperación, se carece de la opción de restaurar la máquina virtual.

Para detener la protección de una máquina virtual:

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Detener copia de seguridad**.

    ![Botón Detener copia de seguridad](./media/backup-azure-manage-vms/stop-backup-button.png)

    Se abre la hoja Detener copia de seguridad.

    ![Hoja Detener copia de seguridad](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. En la hoja **Detener copia de seguridad**, elija si desea conservar o eliminar los datos de copia de seguridad. El cuadro de información proporciona detalles acerca de su elección.

    ![Detener protección](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. Si decide conservar los datos de copia de seguridad, vaya al paso 4. Si opta por eliminar los datos de copia de seguridad, confirme que desea detener los trabajos de copia de seguridad y eliminar los puntos de recuperación. Escriba el nombre del elemento.

    ![Comprobación de la detención](./media/backup-azure-manage-vms/item-verification-box.png)

    Si no está seguro del nombre, mantenga el mouse sobre el signo de exclamación para verlo. Además, el nombre del elemento aparece debajo de **Detener copia de seguridad** en la parte superior de la hoja.

4. Opcionalmente, proporcione valores en **Motivo** o **Comentario**.

5. Para detener el trabajo de copia de seguridad para el elemento actual, haga clic en ![Botón Detener copia de seguridad](./media/backup-azure-manage-vms/stop-backup-button-blue.png).

    Un mensaje de notificación le confirma que se han detenido los trabajos de copia de seguridad.

    ![Confirmación de la detención de la protección](./media/backup-azure-manage-vms/stop-message.png)


## Reanudación de la protección de una máquina virtual
Si se eligió la opción **Retener datos de copia de seguridad** cuando se detuvo la protección de la máquina virtual, es posible reanudar la protección. Si se eligió la opción **Eliminar datos de copia de seguridad**, no se puede reanudar la protección de la máquina virtual.

Para reanudar la protección de la máquina virtual

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Reanudar copia de seguridad**.

    ![Reanudar protección](./media/backup-azure-manage-vms/resume-backup-button.png)

    Se abre la hoja Directiva de copia de seguridad.

    >[AZURE.NOTE] Al volver a proteger la máquina virtual, puede elegir una directiva diferente de la directiva con la que estaba protegida inicialmente la máquina virtual.

2. Siga los pasos de [Cambio de directiva o creación de una directiva de copia de seguridad](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy) para asignar la directiva para la máquina virtual.

    Una vez que se aplique la directiva de copia de seguridad a la máquina virtual, verá el mensaje siguiente.

    ![Máquina virtual protegida correctamente](./media/backup-azure-manage-vms/success-message.png)

## Eliminación de datos de copia de seguridad
Puede eliminar los datos de copia de seguridad asociados a una máquina virtual durante el trabajo **Detener copia de seguridad** o en cualquier momento una vez detenidas las copias de seguridad. Si conviene permitir que se detenga el trabajo de copia de seguridad de una máquina virtual y esperar días o semanas a decidir si desea eliminar los puntos de recuperación, también es posible. A diferencia de la restauración de puntos de recuperación, cuando se eliminan datos de copia de seguridad, no se pueden elegir los puntos de recuperación específicos que se van a eliminar. Si elige eliminar un elemento, se eliminarán todos los puntos de recuperación asociados con él.

En el siguiente procedimiento se da por sentado que el trabajo de copia de seguridad de la máquina virtual se ha detenido o deshabilitado. Solamente cuando se ha deshabilitado el trabajo de copia de seguridad, están disponibles las opciones **Reanudar copia de seguridad** y **Delete backup** (Eliminar copia de seguridad) en el panel del elemento del almacén.

![Botones Reanudar y Eliminar](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Para eliminar datos de copia de seguridad en una máquina virtual con la *copia de seguridad deshabilitada*:

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Delete backup** (Eliminar copia de seguridad).

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Se abre la hoja **Eliminar datos de copia de seguridad**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. Debe escribir el nombre del elemento para confirmar que desea eliminar los puntos de recuperación.

    ![Comprobación de la detención](./media/backup-azure-manage-vms/item-verification-box.png)

    Si no está seguro del nombre, mantenga el mouse sobre el signo de exclamación para verlo. Además, el nombre del elemento aparece debajo de **Eliminar datos de copia de seguridad** en la parte superior de la hoja.

3. Opcionalmente, proporcione valores en **Motivo** o **Comentario**.

4. Para eliminar los datos de copia de seguridad para el elemento actual, haga clic en ![Botón Detener copia de seguridad](./media/backup-azure-manage-vms/delete-button.png).

    Un mensaje de notificación le confirma que se han eliminado los datos de copia de seguridad.

## Operaciones de auditoría
Puede revisar los registros de eventos y operaciones para ver las operaciones de administración llevadas a cabo en el almacén de Servicios de recuperación. Los registros de operaciones permiten excelentes análisis finales y soportes técnicos de auditoría para las operaciones de copia de seguridad. Puede usar la característica Registros de auditoría para ver los registros de todas las operaciones *en la suscripción*. Para más información sobre eventos, operaciones y registros de auditoría, consulte el artículo [Visualización de eventos y registros de auditoría](../azure-portal/insights-debugging-with-events.md). Use la configuración **Registros de auditoría** para ver los registros de eventos y operaciones específicos de un almacén de Servicios de recuperación o de un elemento del almacén.

Se incluyen las siguientes operaciones en los registros de auditoría:

- Registro
- Anulación del registro
- Configuración de protección
- Copia de seguridad (tanto programada como a petición)
- Restauración
- Detener protección
- Eliminación de datos de copia de seguridad
- Add policy
- Eliminación de directiva
- Actualización de directiva
- Cancelar trabajo

Para ver los registros de eventos para un almacén de Servicios de recuperación:

1. En el [panel del almacén](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), busque y haga clic en **Registros de auditoría** para abrir la hoja **Eventos**.

    ![Registros de auditoría](./media/backup-azure-manage-vms/audit-logs.png)

    Se abre la hoja Eventos con los eventos operativos filtrados solo para el almacén actual. En la hoja se muestra la lista de eventos críticos, con error, de advertencia e informativos que se produjeron la semana anterior. El intervalo es un valor predeterminado establecido en la configuración **Filtro**. Además, en la hoja **Eventos** también se muestra un gráfico de barras que hace un seguimiento de cuándo se produjeron los eventos. Si no desea ver el gráfico de barras, en el menú **Eventos**, haga clic en **Mostrar gráfico** para desactivarlo.

    ![Filtro Registros de auditoría](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. Para ver más información sobre una operación determinada, en la lista de operaciones, haga clic en ella para abrir su hoja. La hoja contiene información detallada acerca de la operación y una lista de los eventos que se produjeron en el intervalo.

    ![Detalles de la operación](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. Para ver información detallada acerca de un evento determinado, en la lista de eventos, haga clic en la operación para abrir su hoja Detalle.

    ![Detalles del evento](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    La información de nivel de evento es la más detallada. El resto de este procedimiento explica cómo editar o modificar la información disponible.

4. Para editar la lista de filtros disponibles, en el menú **Eventos**, haga clic en **Filtro** para abrir esa hoja.

    ![Hoja de filtro abierta](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. En la hoja **Filtro**, ajuste los filtros **Nivel**, **Intervalo de tiempo** y **Llamador**. Los demás filtros no están disponibles porque se establecieron para proporcionar la información actual del almacén de Servicios de recuperación.

    ![Registros de auditoria: detalles de la consulta](./media/backup-azure-manage-vms/filter-blade.png)

    Puede especificar un valor para **Nivel** de evento: Crítico, Error, Advertencia o Informativo. Puede elegir cualquier combinación de niveles de evento, pero debe tener al menos uno seleccionado. Active el nivel o desactívelo. El filtro **Intervalo de tiempo** permite especificar el intervalo para capturar eventos. Si usa un intervalo personalizado, puede establecer las horas de inicio y finalización.

6. Cuando esté listo para consultar los registros de operaciones mediante el filtro, haga clic en **Actualizar**. Los resultados se muestran en la hoja **Eventos**.

    ![Detalles de la operación](./media/backup-azure-manage-vms/edited-list-of-events.png)


## Notificaciones de alerta
Puede obtener notificaciones de alerta personalizadas para los trabajos del portal. Para obtener estos trabajos, defina reglas de alerta con PowerShell en los eventos de registros operativos. Use *PowerShell versión 1.3.0 o posterior*.

Para definir una notificación personalizada que avise de errores de copia de seguridad, use un comando como este:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: puede obtener esta información de los registros de auditoría. ResourceId se proporciona en la columna de recursos de los registros de operaciones.

**OperationName**: tendrá el formato "Microsoft.RecoveryServices/recoveryServicesVault/<EventName>", donde EventName es uno de estos valores: Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy o UpdateProtectionPolicy.

**Status**: los valores admitidos son Started, Succeeded y Failed.

**ResourceGroup**: es el grupo de recursos al que pertenece el recurso. Puede agregar la columna de grupo de recursos a los registros generados. El grupo de recursos es uno de los tipos de información de evento disponibles.

**Name**: nombre de la regla de alerta.

**CustomEmail**: especifique la dirección de correo electrónico personalizada a la que desea enviar la notificación de alerta.

**SendToServiceOwners**: esta opción envía la notificación de alerta a todos los administradores y coadministradores de la suscripción. Se puede usar en el cmdlet **New-AzureRmAlertRuleEmail**.

### Limitaciones de las alertas
Las alertas basadas en eventos están sujetas a las siguientes limitaciones:

1. Las alertas se activan en todas las máquinas virtuales del almacén de Servicios de recuperación. No se puede personalizar la alerta para un conjunto específico de máquinas virtuales en un almacén de Servicios de recuperación.
2. Esta característica se encuentra en versión preliminar. [Más información](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. Las alertas se envían desde "alerts-noreply@mail.windowsazure.com". Actualmente, no se puede modificar el remitente de correo electrónico.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## Pasos siguientes

Para información sobre cómo volver a crear una máquina virtual a partir de un punto de recuperación, consulte [Restauración de máquinas virtuales en Azure](backup-azure-restore-vms.md). Si necesita información sobre la protección de las máquinas virtuales, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Servicios de recuperación](backup-azure-vms-first-look-arm.md).

<!---HONumber=AcomDC_0608_2016-->