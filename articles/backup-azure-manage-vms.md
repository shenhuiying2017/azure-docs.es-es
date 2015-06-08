<properties
	pageTitle="Copia de seguridad de Azure: administrar máquinas virtuales"
	description="Información sobre cómo administrar una máquina virtual de Azure"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# Administración de máquinas virtuales


## Administrar máquinas virtuales protegidas

1. Para ver y administrar la configuración de copia de seguridad para una máquina virtual, haga clic en la pestaña **Elementos protegidos**.

  - Haga clic en el nombre de un elemento protegido para ver la pestaña **Detalles de copia de seguridad**, que muestra información acerca de la última copia de seguridad.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Para ver y administrar la configuración de directiva de copia de seguridad para una máquina virtual, haga clic en la pestaña **Directivas**.

  - La pestaña **Directivas de copia de seguridad** muestra la directiva existente. Se puede modificar según sea necesario. Si necesita crear una nueva directiva, haga clic en **crear** en el **directivas** página. Tenga en cuenta que si desea quitar una directiva no debería tener ninguna máquina virtual asociada a esta.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Puede obtener más información sobre las acciones o el estado de una máquina virtual en la página **Trabajos**. Haga clic en un trabajo en la lista para obtener más detalles, o filtre los trabajos de una máquina virtual específica.

    ![Trabajos](./media/backup-azure-manage-vms/backup-job.png)

## Copia de seguridad a petición de una máquina virtual
Puede tomar una demanda copia de seguridad de un disco duro una vez que se configura para la protección. Si la copia de seguridad inicial está pendiente para la máquina virtual, copia de seguridad a petición creará una copia completa de la máquina virtual en el almacén de copia de seguridad de Azure. Si se ha completado la primera copia de seguridad, copia de seguridad a petición sólo enviará los cambios desde la copia de seguridad anterior para el almacén de copia de seguridad de Azure.

Realizar copia de seguridad de una máquina virtual a petición:

1. Vaya a **elementos protegidos** página y seleccione **Máquina Virtual de Azure** como **tipo** (si no está ya seleccionada) y haga clic en **seleccione** botón.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual en el que desea realizar copia de seguridad a petición y haga clic en **copia de seguridad ahora** situado en la parte inferior de la página.

    ![Realizar una copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now.png)

    Esto creará un trabajo de copia de seguridad en la máquina virtual seleccionada. Duración de retención de punto de recuperación creado a través de este trabajo será la misma que la especificada en la directiva asociada a la máquina virtual.

    ![Crear trabajo de copia de seguridad](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Para ver la directiva asociada a una máquina virtual, profundizar en la máquina virtual en el **elementos protegidos** página y vaya a la ficha Directiva de copia de seguridad.

3. Una vez que se crea el trabajo, puede hacer clic en **Ver trabajo** botón en la barra de notificación del sistema para ver el trabajo correspondiente en la página trabajos.

    ![Trabajo de copia de seguridad creado](./media/backup-azure-manage-vms/created-job.png)

4. Tras finalizar correctamente el trabajo, un punto de recuperación se creará que puede utilizar para restaurar la máquina virtual. Esto también incrementará el valor de columna de punto de recuperación en 1 en **elementos protegidos** página.

## Detener la protección de máquinas virtuales
Puede dejar las futuras copias de seguridad de una máquina virtual con las siguientes opciones:

- Conservar los datos de copia de seguridad asociados a la máquina virtual en el almacén de copia de seguridad de Azure
- Eliminar los datos de copia de seguridad asociados con la máquina virtual

Si ha seleccionado la primera opción, puede usar los datos de copia de seguridad para restaurar la máquina virtual. Para detalles de precios de estas máquinas virtuales, haga clic en [aquí](http://azure.microsoft.com/pricing/details/backup/).

Para detener la protección de una máquina virtual:

1. Vaya a **elementos protegidos** página y seleccione **máquina virtual de Azure** como el tipo de filtro (si no está ya seleccionada) y haga clic en **seleccione** botón.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual y haga clic en **Detener protección** en la parte inferior de la página.

    ![Detener la protección](./media/backup-azure-manage-vms/stop-protection.png)

3. De forma predeterminada, copia de seguridad de Azure no elimina los datos de copia de seguridad asociados a la máquina Virtual.

    ![Confirmar detención de protección](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Si desea eliminar los datos de copia de seguridad, active la casilla de verificación.

    ![Casilla de verificación](./media/backup-azure-manage-vms/checkbox.png)

    Seleccione un motivo para detener la copia de seguridad. Aunque esto es opcional, proporcionar un motivo ayudará a copia de seguridad de Azure funcionan en los comentarios y dar prioridad a los escenarios de cliente.

4. Haga clic en **Enviar** botón para enviar el **Detener la protección** trabajo. Haga clic en **Ver trabajo** para ver el trabajo en correspondiente **trabajos** página.

    ![Detener la protección](./media/backup-azure-manage-vms/stop-protect-success.png)

    Si no ha seleccionado **eliminar los datos de copia de seguridad asociados** opción durante **Detener protección** asistente y, a continuación, los cambios de estado de protección para finalizar el trabajo post **protección detiene**.

    ![Detención de protección](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Si ha seleccionado la **eliminar los datos de copia de seguridad asociados** opción, la máquina virtual no formar parte de **elementos protegidos** página.

## Volver a proteger la máquina Virtual
Si no ha seleccionado la **Eliminar datos de copia de seguridad asociados** opción **Detener protección**, puede volver a proteger la máquina virtual siguiendo los pasos similares a la copia de seguridad de las máquinas virtuales registradas. Una vez protegido, esta máquina virtual tendrá conserva antes de detener protección de datos de backup y crean puntos de recuperación después de volver a proteger.

Después de volver a proteger, estado de protección de la máquina virtual se cambiarán a **protegido** si hay puntos de recuperación anteriores a **Detener protección**.

  ![VM vuelto a proteger](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Al volver a proteger la máquina virtual, puede elegir una directiva diferente a la directiva con la que era inicialmente proteger la máquina virtual.

## Anular registro de máquinas virtuales

Si desea quitar la máquina virtual del almacén de copia de seguridad:

1. Haga clic en el botón **ANULAR REGISTRO** en la parte inferior de la página. 

    ![Deshabilitar protección](./media/backup-azure-manage-vms/unregister-button.png)

    Aparecerá una notificación en la parte inferior de la pantalla de confirmación de solicitud. Haga clic en **Sí** para continuar.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/confirm-unregister.png)

## Eliminar datos de copia de seguridad
Puede eliminar los datos de copia de seguridad asociados con una máquina virtual, ya sea:

- Durante el trabajo de detención de protección
- Después de una protección de detención se complete el trabajo en una máquina virtual

Para eliminar datos de copia de seguridad en una máquina virtual, que se encuentra en la "protección detiene" estado registrar la finalización correcta de **Detener copia de seguridad** trabajo:

1. Vaya a **elementos protegidos** página y seleccione **Máquina Virtual de Azure** como tipo y haga clic en **seleccione** botón.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual. La máquina virtual estará en **protección detiene** estado.

    ![Protección detenido](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Haga clic en el **Eliminar** situado en la parte inferior de la página.

    ![Eliminar copia de seguridad](./media/backup-azure-manage-vms/delete-backup.png)

4. En el **Eliminar datos de copia de seguridad** asistente, seleccione un motivo para eliminar los datos de copia de seguridad (altamente recomendados) y haga clic en **Enviar**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Esto creará un trabajo para eliminar datos de copia de seguridad de la máquina virtual seleccionada. Haga clic en **Ver trabajo** para ver el trabajo correspondiente en la página trabajos.

    ![Eliminación de datos correcta](./media/backup-azure-manage-vms/delete-data-success.png)

    Una vez completado el trabajo, se quitará la entrada correspondiente a la máquina virtual de **elementos protegidos** página.


###Panel

En el **panel** página puede revisar la información sobre máquinas virtuales de Azure, su almacenamiento y los trabajos asociados a ellos en las últimas 24 horas. Puede ver el estado de copia de seguridad y los errores de copia de seguridad asociados.

  ![Panel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir-->