---
title: "Administración de almacenes y servidores de Copia de seguridad de Azure mediante el modelo de implementación clásica | Microsoft Docs"
description: Use este tutorial para aprender a administrar almacenes y servidores de Copia de seguridad de Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: markgal;
ms.openlocfilehash: 91451b2cdc42ed05ef7c1ba9c66ad5b4b45dd788
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Administración de almacenes y servidores de Copia de seguridad de Azure mediante el modelo de implementación clásica
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-manage-windows-server.md)
> * [Clásico](backup-azure-manage-windows-server-classic.md)
>
>

En este artículo encontrará información general sobre las tareas de administración de copias de seguridad que tiene disponibles en el Portal de Azure clásico y el agente de Copia de seguridad de Microsoft Azure.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager.

> [!IMPORTANT]
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> A partir del 15 de octubre de 2017, no podrá usar PowerShell para crear almacenes de Backup. **El 1 de noviembre de 2017**:
>- Todos los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>

## <a name="management-portal-tasks"></a>Tareas del Portal de administración
1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).
2. Haga clic en **Servicios de recuperación**y luego en el nombre del almacén de copia de seguridad para ver la página de inicio rápido.

    ![Servicios de recuperación](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Si selecciona las opciones que se encuentran en la parte superior de la página de Inicio rápido, podrá ver las tareas de administración disponibles.

![Administración de pestañas](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Panel
Seleccione **Panel** para ver información general sobre el uso del servidor. En la **información general del uso** se incluyen los siguientes datos:

* El número de servidores de Windows registrados en la nube
* El número de máquinas virtuales de Azure protegidas en la nube
* La cantidad total de espacio de almacenamiento utilizada en Azure
* El estado de los trabajos recientes

En la parte inferior del panel puede llevar a cabo las tareas siguientes:

* **Administrar certificado** : si se ha usado un certificado para registrar el servidor, utilícelo para actualizar el certificado. Si usa credenciales de almacén, no utilice **Administrar certificado**.
* **Eliminar** : elimina el almacén de copia de seguridad actual. En caso de que ya no se use un almacén de credenciales de copia de seguridad, puede eliminarlo para liberar espacio de almacenamiento. **Eliminar** solo está habilitado después de que todos los servidores registrados se hayan eliminado del almacén.

![Copia de seguridad de las tareas del panel](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Elementos registrados
Seleccione la opción **Elementos registrados** para ver los nombres de los servidores que se registraron en este almacén de claves.

![Elementos registrados](./media/backup-azure-manage-windows-server-classic/registered-items.png)

El filtro **Tipo** tiene como valor predeterminado la opción Máquina virtual de Azure. Para ver los nombres de los servidores que están registrados en este almacén, seleccione **Servidor de Windows** en el menú desplegable.

Aquí puede realizar las siguientes tareas:

* **Permitir nuevo registro**: cuando se selecciona esta opción para un servidor, puede usar el **Asistente para registro** del agente local de Microsoft Azure Backup para registrar el servidor en el almacén de copia de seguridad por segunda vez. Es posible que necesite volver a registrarse debido a un error en el certificado o si un servidor tuvo que reconstruirse.
* **Eliminar** : elimina un servidor del almacén de copias seguridad. Todos los datos almacenados asociados con el servidor se eliminan inmediatamente.

    ![Tareas de elementos registrados](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Elementos protegidos
Haga clic en **Elementos protegidos** para ver los elementos de los que se han hecho copias de seguridad desde los servidores.

![Elementos protegidos](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Configuración
Desde la pestaña **Configurar** , puede seleccionar la opción de redundancia de almacenamiento idónea. El mejor momento para seleccionar la opción de redundancia de almacenamiento es justo después de la creación del almacén y antes de que las máquinas se registren en este.

> [!WARNING]
> Una vez que un elemento se ha registrado en el almacén, la opción de redundancia de almacenamiento está bloqueada y no se puede modificar.
>
>

![Configuración](./media/backup-azure-manage-windows-server-classic/configure.png)

Consulte este artículo para más información sobre la [redundancia de almacenamiento](../storage/common/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Tareas del agente de Copia de seguridad de Microsoft Azure
### <a name="console"></a>Consola
Abra el **agente de Copia de seguridad de Microsoft Azure** (puede encontrarlo si busca en su equipo *Copia de seguridad de Microsoft Azure*).

![Agente de copia de seguridad](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Con las **acciones** disponibles en la parte derecha de la consola del agente de Copia de seguridad, podrá llevar a cabo las siguientes tareas de administración:

* Registrar un servidor
* Programación de una copia de seguridad
* Realizar una copia de seguridad en ese momento
* Cambiar propiedades

![Acciones de la consola de agente](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> Para **recuperar datos**, consulte [Restauración de archivos en una máquina de Windows Server o del cliente de Windows](backup-azure-restore-windows-server.md).
>
>

### <a name="modify-an-existing-backup"></a>Modificación de una copia de seguridad existente
1. En el agente de Copia de seguridad de Microsoft Azure, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. En el **Asistente para programar copias de seguridad**, deje activada la opción **Cambiar la hora o las horas de las copias de seguridad** y haga clic en **Siguiente**.

    ![Modificación de una copia de seguridad programada](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Si quiere agregar o cambiar elementos, en la pantalla **Seleccionar elementos de los que realizar copia de seguridad**, haga clic en **Agregar elementos**.

    También puede establecer preferencias en **Configuración de exclusión** , en esta página del asistente. Si quiere excluir archivos o tipos de archivos, lea el procedimiento para agregar [configuración de exclusión](#exclusion-settings).
4. Seleccione los archivos y las carpetas de los que quiere realizar la copia de seguridad y haga clic en **Aceptar**.

    ![Agregar elementos](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Especifique la **programación de copia de seguridad** y haga clic en **Siguiente**.

    Puede programar copias de seguridad diarias (un máximo de 3 veces al día) o semanales.

    ![Especificación de la programación de copias de seguridad](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > En este [artículo](backup-azure-backup-cloud-as-tape.md)se explica detalladamente cómo especificar la programación de copias de seguridad.
   >
   >
6. Seleccione la **directiva de retención de la copia de seguridad** y haga clic en **Siguiente**.

    ![Selección de la directiva de retención](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. En la pantalla **Confirmación**, revise la información y haga clic en **Finalizar**.
8. Cuando el asistente finalice la creación de la **programación de copia de seguridad**, haga clic en **Cerrar**.

    Tras modificar la protección, puede confirmar que las copias de seguridad se estén activando correctamente; para ello, vaya a la pestaña **Trabajos** y confirme que se hayan reflejado los cambios en los trabajos de copia de seguridad.

### <a name="enable-network-throttling"></a>Habilitación de la limitación de la red
En el agente de Azure Backup se incluye la pestaña Limitación, donde podrá controlar cómo se utiliza el ancho de banda de red durante la transferencia de datos. Este control puede resultar de ayuda si necesita realizar una copia de seguridad de los datos durante las horas de trabajo, pero no quiere que el proceso interfiera con otro tráfico de Internet. La limitación de la transferencia de datos se aplica a las actividades de copia de seguridad y restauración.  

Para habilitar la limitación, siga estos pasos:

1. En el **agente de Backup**, haga clic en **Cambiar propiedades**.
2. Active la casilla **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad** .

    ![Limitación de la red](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. Una vez que se ha habilitado la limitación, especifique el ancho de banda permitido para la transferencia de datos de copia de seguridad durante la **jornada laboral** y las **horas de descanso**.

    Los valores de ancho de banda comienzan en 512 kilobytes por segundo (Kbps) y pueden subir hasta 1023 megabytes por segundo (Mbps). También puede designar el inicio y el final de la **jornada laboral**, así como qué días de la semana se consideran laborables. El tiempo no comprendido en la jornada laboral designada se considera horas de descanso.
4. Haga clic en **Aceptar**.

## <a name="exclusion-settings"></a>Configuración de exclusión
1. Abra el **agente de Copia de seguridad de Microsoft Azure** (puede encontrarlo si busca en su equipo *Copia de seguridad de Microsoft Azure*).

    ![Apertura del agente de copia de seguridad](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. En el agente de Copia de seguridad de Microsoft Azure, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. En el Asistente para programar copias de seguridad deje activada la opción **Cambiar la hora o las horas de las copias de seguridad** y haga clic en **Siguiente**.

    ![Modificación de una programación](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Haga clic en **Configuración de exclusión**.

    ![Selección de elementos para su exclusión](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Haga clic en **Agregar exclusión**.

    ![Adición de exclusiones](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Seleccione la ubicación y, después, haga clic en **Aceptar**.

    ![Selección de una ubicación para la exclusión](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Agregue la extensión de archivo en el campo **Tipo de archivo** .

    ![Exclusión por tipo de archivo](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Adición de una extensión .mp3

    ![Ejemplo de tipo de archivo](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Para agregar otra extensión, haga clic en **Agregar exclusión** y especifique otra extensión de tipo de archivo (por ejemplo, .jpeg).

    ![Otro ejemplo de tipo de archivo](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. Cuando haya agregado todas las extensiones, haga clic en **Aceptar**.
9. Avance por las distintas pantallas del Asistente para programar copias de seguridad haciendo clic en **Siguiente** hasta llegar a la página **Confirmación** y, después, en **Finalizar**.

    ![Confirmación de exclusión](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Pasos siguientes
* [Restauración de Windows Server o el cliente de Windows desde Azure](backup-azure-restore-windows-server.md)
* Para obtener más información sobre Azure Backup, consulte [Información general de Azure Backup](backup-introduction-to-azure-backup.md)
* Visite el [Foro de Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)
