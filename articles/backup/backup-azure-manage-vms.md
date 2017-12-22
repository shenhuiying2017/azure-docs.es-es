---
title: "Administración de copias de seguridad de máquinas virtuales implementadas por Resource Manager | Microsoft Docs"
description: "Aprenda a administrar y supervisar las copias de seguridad de máquinas virtuales implementadas por Resource Manager."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: f3050283-d60f-472d-b464-cb844e70d67e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk;markgal
ms.openlocfilehash: f4613746a427e6987366eeb46605524cd3aacbe2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-azure-virtual-machine-backups"></a>Administración de copias de seguridad de máquinas virtuales de Azure

Este artículo proporciona orientación acerca de cómo administrar copias de seguridad de máquinas virtuales y explica la información disponible sobre alertas de copia de seguridad en el panel del portal. La orientación de este artículo se aplica al uso de máquinas virtuales con almacenes de Recovery Services. Este artículo no trata sobre la creación de máquinas virtuales ni explica cómo protegerlas. Para ver una introducción sobre la protección de las máquinas virtuales implementadas por Azure Resource Manager en Azure con un almacén de Recovery Services, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Recovery Services](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Administración de almacenes y máquinas virtuales protegidas
En Azure Portal, el panel Almacén de Recovery Services proporciona acceso a información sobre el almacén, como:

* La instantánea de copia de seguridad más reciente, que también es el último punto de restauración.
* La directiva de copia de seguridad.
* El tamaño total de todas las instantáneas de copia de seguridad.
* El número de máquinas virtuales que están protegidas con el almacén.

Muchas tareas de administración con una copia de seguridad de máquina virtual comienzan con la apertura del almacén en el panel. Sin embargo, dado que los almacenes sirven para proteger varios elementos (o varias máquinas virtuales), para ver los detalles sobre una máquina virtual determinada, abra el panel del elemento del almacén. El procedimiento siguiente muestra cómo abrir el *panel del almacén* y, después, pasar al *panel del elemento del almacén*. En ambos procedimientos, se ofrecen "sugerencias" sobre cómo agregar el almacén y el elemento del almacén al Panel de Azure con el comando Anclar al panel. La opción Anclar al panel es una manera de crear un acceso directo al almacén o al elemento. También puede ejecutar comandos habituales desde el acceso directo.

> [!TIP]
> Si tiene varios paneles y hojas abiertos, use el control deslizante azul oscuro en la parte inferior de la ventana para deslizar el Panel de Azure hacia delante y hacia atrás.
>
>

![Vista completa con control deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Apertura de un almacén de Recovery Services en el panel:
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Recovery Services**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Recovery Services**.

    ![Creación del almacén de Recovery Services, paso 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Se muestra la lista de almacenes de Recovery Services.

    ![Lista de almacenes de Recovery Services ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Si ancla un almacén al Panel de Azure, ese almacén es accesible de inmediato cuando abra el Portal de Azure. Para anclar un almacén al panel, en la lista de almacenes, haga clic con el botón derecho en el almacén y seleccione **Anclar al panel**.
   >
   >
3. En la lista de almacenes, seleccione el almacén para abrir su panel. Cuando selecciona el almacén, se abren el panel del almacén y la hoja **Configuración** . En la siguiente imagen, el panel de **Contoso-vault** aparece resaltado.

    ![Abrir el panel del almacén y la hoja Configuración](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Apertura del panel de un elemento del almacén
En el procedimiento anterior, abrió el panel del almacén. Para abrir el panel de un elemento del almacén:

1. En el panel del almacén, en el icono **Elementos de copia de seguridad**, haga clic en **Azure Virtual Machines**.

    ![Abrir el icono Elementos de copia de seguridad](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    En la hoja **Elementos de copia de seguridad** , se muestra el último trabajo de copia de seguridad para cada elemento. En este ejemplo, hay una máquina virtual, demovm-markgal, protegida por este almacén.  

    ![Icono Elementos de copia de seguridad](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Para facilitar el acceso, puede anclar un elemento del almacén en el Panel de Azure. Para anclar un elemento del almacén, en la lista de elementos de almacén, haga clic con el botón derecho en el elemento y seleccione **Anclar al panel**.
   >
   >
2. En la hoja **Elementos de copia de seguridad** , haga clic en el elemento para abrir el panel del elemento del almacén.

    ![Icono Elementos de copia de seguridad](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Se abren el panel del elemento de almacén y su hoja **Configuración** .

    ![Panel Elementos de copia de seguridad con hoja Configuración](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    En el panel del elemento del almacén, puede llevar a cabo muchas tareas de administración claves, como:

   * Cambio de directiva o creación de una directiva de copia de seguridad
   * Visualización de los puntos de restauración y su estado de coherencia
   * Copia de seguridad a petición de una máquina virtual
   * Detener la protección de máquinas virtuales
   * Reanudación de la protección de una máquina virtual
   * Eliminación de los datos de una copia de seguridad (o un punto de recuperación)
   * [Restauración de discos de copia de seguridad](backup-azure-arm-restore-vms.md#restore-backed-up-disks)

Para los procedimientos siguientes, el punto de partida es el panel del elemento del almacén.

## <a name="manage-backup-policies"></a>Administrar directivas de copia de seguridad
1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Toda la configuración** para abrir la hoja **Configuración**.

    ![Hoja Directiva de copia de seguridad](./media/backup-azure-manage-vms/all-settings-button.png)
2. En la hoja **Configuración**, haga clic en **Directiva de copia de seguridad** para abrir esa hoja.

    En la hoja, se muestran los detalles de frecuencia de copia de seguridad y duración de retención.

    ![Hoja Directiva de copia de seguridad](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. En el menú **Elegir directiva de copia de seguridad** :

   * Para cambiar de directiva, seleccione una directiva diferente y haga clic en **Guardar**. La nueva directiva se aplica inmediatamente al almacén.
   * Para crear una directiva, seleccione **Crear nuevo**.

     ![Copia de seguridad de máquina virtual](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Si quiere instrucciones para crear una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Al administrar las directivas de copia de seguridad, asegúrese de seguir los [procedimientos recomendados](backup-azure-vms-introduction.md#best-practices) para obtener el máximo rendimiento de las copias de seguridad.
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Copia de seguridad a petición de una máquina virtual
Puede realizar una copia de seguridad a petición de una máquina virtual una vez que esta esté configurada para la protección. Si está pendiente la copia de seguridad inicial, la copia de seguridad a petición creará una copia completa de la máquina virtual en el almacén de Recovery Services. Si se ha completado la copia de seguridad inicial, una copia de seguridad a petición solo enviará los cambios respecto a la instantánea anterior al almacén de Recovery Services. Es decir, las copias de seguridad posteriores siempre son incrementales.

> [!NOTE]
> La duración de retención para una copia de seguridad a petición es el valor de retención especificado para el punto de copia de seguridad diaria en la directiva. Si no se selecciona ningún punto de copia de seguridad diaria, se usa el semanal.
>
>

Para desencadenar la copia de seguridad a petición de una máquina virtual:

* En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Realizar copia de seguridad ahora**.

    ![Botón Realizar copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now-button.png)

    El portal se asegura de que desea iniciar un trabajo de copia de seguridad a petición. Haga clic en **Sí** para iniciar el trabajo.

    ![Botón Realizar copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now-check.png)

    El trabajo de copia de seguridad crea un punto de recuperación. La duración de retención del punto de recuperación será la misma que la especificada en la directiva asociada a la máquina virtual. Para realizar un seguimiento del progreso del trabajo, en el panel del almacén, haga clic en el icono **Trabajos de copia de seguridad** .  

## <a name="stop-protecting-virtual-machines"></a>Detener la protección de máquinas virtuales
Si opta por dejar de proteger una máquina virtual, se le pregunta si desea conservar los puntos de recuperación. Hay dos formas de dejar de proteger máquinas virtuales:

* detener todos los trabajos futuros de copia de seguridad y eliminar todos los puntos de recuperación, o
* detener todos los trabajos futuros de copia de seguridad pero dejar los puntos de recuperación 

Dejar los puntos de recuperación almacenados conlleva un costo. Sin embargo, la ventaja de dejarlos es que puede restaurar la máquina virtual más adelante, si lo desea. Para más información acerca del costo de dejar los puntos de recuperación, consulte la [información sobre precios](https://azure.microsoft.com/pricing/details/backup/). Si opta por eliminar todos los puntos de recuperación, no podrá restaurar la máquina virtual.

Para detener la protección de una máquina virtual:

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Detener copia de seguridad**.

    ![Botón Detener copia de seguridad](./media/backup-azure-manage-vms/stop-backup-button.png)

    Se abre la hoja Detener copia de seguridad.

    ![Hoja Detener copia de seguridad](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. En la hoja **Detener copia de seguridad** , elija si desea conservar o eliminar los datos de copia de seguridad. El cuadro de información proporciona detalles acerca de su elección.

    ![Detener protección](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Si decide conservar los datos de copia de seguridad, vaya al paso 4. Si opta por eliminar los datos de copia de seguridad, confirme que desea detener los trabajos de copia de seguridad y eliminar los puntos de recuperación. Escriba el nombre del elemento.

    ![Comprobación de la detención](./media/backup-azure-manage-vms/item-verification-box.png)

    Si no está seguro del nombre, mantenga el mouse sobre el signo de exclamación para verlo. Además, el nombre del elemento aparece debajo de **Detener copia de seguridad** en la parte superior de la hoja.
4. Opcionalmente, añada un valor a los campos **Motivo** o **Comentario**.
5. Para detener el trabajo de copia de seguridad del elemento actual, haga clic en el botón ![Detener copia de seguridad](./media/backup-azure-manage-vms/stop-backup-button-blue.png).

    Un mensaje de notificación le confirma que se han detenido los trabajos de copia de seguridad.

    ![Confirmación de la detención de la protección](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Reanudación de la protección de una máquina virtual
Si se eligió la opción **Retener datos de copia de seguridad** cuando se detuvo la protección de la máquina virtual, es posible reanudar la protección. Si se eligió la opción **Eliminar datos de copia de seguridad** , no se puede reanudar la protección de la máquina virtual.

Para reanudar la protección de la máquina virtual

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Reanudar copia de seguridad**.

    ![Reanudar protección](./media/backup-azure-manage-vms/resume-backup-button.png)

    Se abre la hoja Directiva de copia de seguridad.

   > [!NOTE]
   > Al volver a proteger la máquina virtual, puede elegir una directiva diferente de la directiva con la que estaba protegida inicialmente la máquina virtual.
   >
   >
2. Siga los pasos de [Administrar directivas de copia de seguridad](backup-azure-manage-vms.md#manage-backup-policies) para asignar la directiva para la máquina virtual.

    Una vez que se aplique la directiva de copia de seguridad a la máquina virtual, verá el mensaje siguiente.

    ![Máquina virtual protegida correctamente](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Eliminar datos de copia de seguridad
Puede eliminar los datos de copia de seguridad asociados a una máquina virtual durante el trabajo **Detener copia de seguridad** o en cualquier momento una vez finalizados los trabajos de copia de seguridad. Incluso puede resultar útil esperar días o semanas antes de eliminar los puntos de recuperación. A diferencia de la restauración de puntos de recuperación, cuando se eliminan datos de copia de seguridad, no se pueden elegir los puntos de recuperación específicos que se van a eliminar. Si elige eliminar los datos de copia de seguridad, se eliminarán todos los puntos de recuperación asociados con el elemento.

En el siguiente procedimiento se da por sentado que el trabajo de copia de seguridad de la máquina virtual se ha detenido o deshabilitado. Una vez que se haya deshabilitado el trabajo de Backup, las opciones **Reanudar copia de seguridad** y **Eliminar copia de seguridad** en el panel del elemento del almacén.

![Botones Reanudar y Eliminar](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Para eliminar datos de copia de seguridad en una máquina virtual con la *copia de seguridad deshabilitada*:

1. En el [panel del elemento del almacén](backup-azure-manage-vms.md#open-a-vault-item-dashboard), haga clic en **Delete backup**(Eliminar copia de seguridad).

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Se abre la hoja **Eliminar datos de copia de seguridad** .

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Escriba el nombre del elemento para confirmar que desea eliminar los puntos de recuperación.

    ![Comprobación de la detención](./media/backup-azure-manage-vms/item-verification-box.png)

    Si no está seguro del nombre, mantenga el mouse sobre el signo de exclamación para verlo. Además, el nombre del elemento aparece debajo de **Eliminar datos de copia de seguridad** en la parte superior de la hoja.
3. Opcionalmente, añada un valor a los campos **Motivo** o **Comentario**.
4. Para eliminar los datos de copia de seguridad del elemento actual, haga clic en el botón ![Detener copia de seguridad](./media/backup-azure-manage-vms/delete-button.png).

    Un mensaje de notificación le confirma que se han eliminado los datos de copia de seguridad.

## <a name="next-steps"></a>Pasos siguientes
Para información sobre cómo volver a crear una máquina virtual a partir de un punto de recuperación, consulte [Restauración de máquinas virtuales en Azure](backup-azure-arm-restore-vms.md). Si necesita información sobre la protección de las máquinas virtuales, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Recovery Services](backup-azure-vms-first-look-arm.md). Para más información sobre la supervisión de eventos, consulte [Supervisión de alertas de copias de seguridad de máquinas virtuales de Azure](backup-azure-monitor-vms.md).
