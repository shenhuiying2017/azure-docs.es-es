
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

  - La pestaña **Directivas de copia de seguridad** muestra la directiva existente. Se puede modificar según sea necesario. Si necesita crear una nueva directiva, haga clic en **Crear** en la página **Directivas**. Tenga en cuenta que si desea quitar una directiva no debería tener ninguna máquina virtual asociada a esta.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Puede obtener más información sobre las acciones o el estado de una máquina virtual en la página **Trabajos**. Haga clic en un trabajo en la lista para obtener más detalles, o filtre los trabajos de una máquina virtual específica.

    ![Trabajos](./media/backup-azure-manage-vms/backup-job.png)

## Copia de seguridad a petición de una máquina virtual
Puede realizar una copia de seguridad a petición de una máquina virtual una vez que esta esté configurada para la protección. Si está pendiente la copia de seguridad inicial para la máquina virtual, la copia de seguridad a petición creará una copia completa de la máquina virtual en el almacén de copia de seguridad de Azure. Si se ha realizado la primera copia de seguridad, la copia de seguridad a petición solo enviará al almacén de copia de seguridad de Azure los cambios que se hayan realizado después de la copia de seguridad anterior.

Para realizar una copia de seguridad a petición de una máquina virtual:

1. Acceda a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como valor del campo **Tipo** (si no se ha seleccionado todavía) y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual de la que desea realizar copia de seguridad a petición. A continuación, haga clic en el botón **Copia de seguridad ahora**, situado en la parte inferior de la página.

    ![Copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now.png)

    Esto creará un trabajo de copia de seguridad en la máquina virtual seleccionada. El periodo de retención del punto de recuperación creado con este trabajo será el mismo que el especificado en la directiva asociada a la máquina virtual.

    ![Creación de trabajo de copia de seguridad](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Para ver la directiva asociada a una máquina virtual, acceda a la máquina virtual en la página **Elementos protegidos** y vaya a la pestaña de la directiva de la copia de seguridad.

3. Una vez que se crea el trabajo, puede hacer clic en el botón **Ver trabajo** de la barra de notificación del sistema para ver el trabajo correspondiente en la página de los trabajos.

    ![Trabajo de copia de seguridad creado](./media/backup-azure-manage-vms/created-job.png)

4. Tras finalizar correctamente el trabajo, se creará un punto de recuperación que puede utilizar para restaurar la máquina virtual. Esto también incrementará el valor de la columna del punto de recuperación en 1 unidad en la página **Elementos protegidos**.

## Detener la protección de máquinas virtuales
Puede detener las futuras copias de seguridad de una máquina virtual con las siguientes opciones:

- Conservar los datos de copia de seguridad asociados a la máquina virtual en el almacén de copia de seguridad de Azure
- Eliminación de datos de copia de seguridad asociados a la máquina virtual

Si ha elegido la primera opción, puede usar los datos de la copia de seguridad para restaurar la máquina virtual. Para obtener más detalles sobre el precio de estas máquinas virtuales, haga clic [aquí](http://azure.microsoft.com/pricing/details/backup/).

Para detener la protección de una máquina virtual:

1. Vaya a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como el tipo de filtro (si no se ha seleccionado todavía) y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Elija la máquina virtual y haga clic en **Detener protección**, en la parte inferior de la página.

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

## Volver a proteger la máquina virtual
Si no ha elegido la opción **Eliminar datos de copia de seguridad asociados** en **Detener protección**, puede volver a proteger la máquina virtual siguiendo unos pasos similares a los de la copia de seguridad de las máquinas virtuales registradas. Una vez protegida, esta máquina virtual conservará los datos de copia de seguridad antes de detener la protección, así como los puntos de recuperación creados después de aplicar de nuevo la protección.

Después de aplicar de nuevo la protección, el estado de protección de la máquina virtual cambiará a **Protegida** si hay puntos de recuperación anteriores a la **detención de la protección**.

  ![Máquina virtual protegida de nuevo](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Al volver a proteger la máquina virtual, puede elegir una directiva diferente de la directiva con la que estaba protegida inicialmente la máquina virtual.

## Anular registro de máquinas virtuales

Si desea quitar la máquina virtual del almacén de copia de seguridad:

1. Haga clic en el botón **ANULAR REGISTRO** en la parte inferior de la página.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/unregister-button.png)

    Aparecerá una notificación en la parte inferior de la pantalla de confirmación de solicitud. Haga clic en **Sí** para continuar.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/confirm-unregister.png)

## Eliminación de datos de copia de seguridad
Los datos de copia de seguridad asociados a una máquina virtual se pueden eliminar en los siguientes momentos:

- Durante el trabajo de detención de la protección
- Después de realizar un trabajo de detención de la protección en una máquina virtual

Para eliminar los datos de copia de seguridad de una máquina virtual que se encuentre en estado de "Protección detenida" después de haber realizado correctamente un trabajo **Detener copia de seguridad**:

1. Vaya a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como tipo y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual. La máquina virtual estará en el estado **Protección detenida**.

    ![Detención de la protección](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Haga clic en el botón **Eliminar**, situado en la parte inferior de la página.

    ![Eliminación de copia de seguridad](./media/backup-azure-manage-vms/delete-backup.png)

4. En el asistente **Eliminar datos de copia de seguridad**, elija un motivo para eliminar los datos de copia de seguridad (se recomienda especificar el motivo) y haga clic en **Enviar**.

    ![Eliminación de datos de copia de seguridad](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Esto creará un trabajo para eliminar los datos de copia de seguridad de la máquina virtual seleccionada. Haga clic en **Ver trabajo** para ver el trabajo correspondiente en la página de los trabajos.

    ![Eliminación de datos correcta](./media/backup-azure-manage-vms/delete-data-success.png)

    Una vez completado el trabajo, se eliminará la entrada correspondiente a la máquina virtual de la página **Elementos protegidos**.


###Panel

En la página **Panel**, puede revisar la información sobre las máquinas virtuales de Azure, su almacenamiento y los trabajos asociados a ellas durante las últimas 24 horas. Puede ver el estado de copia de seguridad y los errores de copia de seguridad asociados.

  ![Panel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)
 

<!---HONumber=62-->