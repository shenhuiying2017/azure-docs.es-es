---
title: "Primer vistazo: copia de seguridad de máquinas virtuales de Azure con un almacén de copia de seguridad | Microsoft Docs"
description: "Use el portal clásico para hacer copia de seguridad de las máquinas virtuales de Azure en un almacén de Backup. En este tutorial se explican todas las fases, que incluyen la creación del almacén de Backup, el registro de las máquinas virtuales, la creación de directivas de copia de seguridad y la ejecución del trabajo inicial de copia de seguridad."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/2/2017
ms.author: markgal;
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 1a0e738cfac01d90e91959412b1ad5591705f617
ms.contentlocale: es-es
ms.lasthandoff: 08/03/2017

---
# <a name="first-look-backing-up-azure-virtual-machines"></a>Primer contacto: copia de seguridad de máquinas virtuales de Azure
> [!div class="op_single_selector"]
> * [Protección de máquinas virtuales con un almacén de Recovery Services](backup-azure-vms-first-look-arm.md)
> * [Protección de máquinas virtuales con un almacén de copia de seguridad](backup-azure-vms-first-look.md)
>
>

Este tutorial le guiará por los pasos para crear copias de seguridad de una máquina virtual de Azure en un almacén de copia de seguridad de Azure. En este artículo se describe el modelo de implementación clásica o de Service Manager para la creación de copias de seguridad de máquinas virtuales. Los siguientes pasos se aplican únicamente a los almacenes de Backup creados en el portal clásico. Microsoft recomienda usar el modelo de Resource Manager para nuevas implementaciones.

Si está interesado en la realización de una copia de seguridad de una máquina virtual en un almacén de Recovery Services que pertenece a un grupo de recursos, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Recovery Services](backup-azure-vms-first-look-arm.md).

Para completar correctamente este tutorial, se deben cumplir estos requisitos previos:

* Ha creado una máquina virtual en su suscripción de Azure.
* La máquina virtual tiene conectividad a las direcciones IP públicas de Azure. Para más información, consulte [Conectividad de red](backup-azure-vms-prepare.md#network-connectivity).


> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). El uso de este tutorial está destinado a máquinas virtuales creadas en el portal clásico.
>
>

## <a name="create-a-backup-vault"></a>Creación de un almacén de copia de seguridad
Un almacén de copia de seguridad es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de copia de seguridad contiene también las directivas de copia de seguridad que se aplican a las máquinas virtuales cuya copia de seguridad se está realizando.

> [!IMPORTANT]
> A partir de marzo de 2017, ya no podrá usar el portal clásico para crear almacenes de Backup.
> Puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> A partir del 15 de octubre de 2017, no podrá usar PowerShell para crear almacenes de Backup. **El 1 de noviembre de 2017**:
>- Todos los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>

## <a name="discover-and-register-azure-virtual-machines"></a>Detección y registro de máquinas virtuales de Azure
Antes de registrar la máquina virtual con un almacén, ejecute el proceso de detección para identificar nuevas máquinas virtuales. Este proceso devuelve una lista de las máquinas virtuales incluidas en la suscripción, junto con información adicional; por ejemplo, el nombre del servicio en la nube y la región.

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com/)
2. En el Portal de Azure clásico, haga clic en **Recovery Services** para abrir la lista de almacenes de Recovery Services.
    ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look/recovery-services-icon.png)
3. En la lista de almacenes de copia de seguridad, seleccione el almacén de copia de una máquina virtual.

    Cuando seleccione el almacén, este se abrirá en la página **Inicio rápido**
4. En el menú del almacén, haga clic en **Elementos registrados**.

    ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look/configure-registered-items.png)
5. En el menú **Tipo**, seleccione **Máquina virtual de Azure**.

    ![Seleccionar carga de trabajo](./media/backup-azure-vms/discovery-select-workload.png)
6. Haga clic en **DETECTAR** en la parte inferior de la página.
    ![Botón Detectar](./media/backup-azure-vms/discover-button-only.png)

    El proceso de detección puede tardar unos minutos mientras se tabulan las máquinas virtuales. Hay una notificación en la parte inferior de la pantalla que informa de que el proceso se está ejecutando.

    ![Detectar máquinas virtuales](./media/backup-azure-vms/discovering-vms.png)

    La notificación cambia cuando el proceso se completa.

    ![Detección realizada](./media/backup-azure-vms-first-look/discovery-complete.png)
7. Haga clic en **REGISTRAR** en la parte inferior de la página.
    ![Botón Registrar](./media/backup-azure-vms-first-look/register-icon.png)
8. En el menú contextual **Elementos registrados** , seleccione las máquinas virtuales que desea registrar.

   > [!TIP]
   > Se pueden registrar varias máquinas virtuales al mismo tiempo.
   >
   >

    Se crea un trabajo para cada máquina virtual que ha seleccionado.
9. Haga clic en **Ver trabajo** en la notificación para ir a la página **Trabajos**.

    ![Registrar trabajo](./media/backup-azure-vms/register-create-job.png)

    La máquina virtual también aparece en la lista de elementos registrados junto con el estado de la operación de registro.

    ![Registrando estado 1](./media/backup-azure-vms/register-status01.png)

    Una vez completada la operación, el estado cambia a *registrado* .

    ![Registrando estado 2](./media/backup-azure-vms/register-status02.png)

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instale el agente de máquina virtual en la máquina virtual.
El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual; puede pasar directamente a la [protección de las máquinas virtuales](backup-azure-vms-first-look.md#create-the-backup-policy).

Si la máquina virtual se migra desde un centro de datos local, es probable que no tenga instalado el agente de máquina virtual. Debe instalar al agente de máquina virtual en la máquina virtual antes de continuar con su protección. Para más información sobre cómo instalar el agente de máquina virtual, consulte la [sección Agente de máquina virtual del artículo Copia de seguridad de máquinas virtuales](backup-azure-vms-prepare.md#vm-agent).

## <a name="create-the-backup-policy"></a>Creación de la directiva de copia de seguridad
Antes de desencadenar el trabajo de copia de seguridad inicial, establezca la programación de cuándo se van a realizar las instantáneas de copia de seguridad. La programación de cuándo se realizan las instantáneas de copia de seguridad y el período de tiempo durante el que se conservan dichas instantáneas, forman la directiva de copia de seguridad. La información de retención se basa en un esquema de rotación abuelo-padre-hijo (GFS) para las copias de seguridad.

1. Navegue al almacén de copia de seguridad en **Recovery Services** en el Portal de Azure clásico y haga clic en **Elementos registrados**.
2. Seleccione **Máquina virtual de Azure** en el menú desplegable.

    ![Seleccionar carga de trabajo en el portal](./media/backup-azure-vms/select-workload.png)
3. Haga clic en **PROTEGER** , en la parte inferior de la página.
    ![Haga clic en Proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    Aparece el **Asistente para la protección de elementos** , que enumera *solo* las máquinas virtuales que están registradas y no protegidas.

    ![Configuración de protección a escala](./media/backup-azure-vms/protect-at-scale.png)
4. Seleccione las máquinas virtuales que desee proteger.

    Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguirlas.
5. En el menú **Configurar la protección** , seleccione una directiva existente o cree una nueva para proteger las máquinas virtuales que ha identificado.

    Los nuevos almacenes de copia de seguridad tienen una directiva predeterminada asociada con el almacén. Esta directiva realiza una instantáneas diaria cada noche que se conserva durante 30 días. Cada directiva de copia de seguridad puede tener asociadas varias máquinas virtuales. Sin embargo, la máquina virtual solo se puede asociar con una directiva cada vez.

    ![Protección mediante nueva directiva](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > Una directiva de copia de seguridad incluye un esquema de retención de las copias de seguridad programadas. Si selecciona una directiva de copia de seguridad existente, no podrá modificar las opciones de retención en el paso siguiente.
   >
   >
6. En **Duración de retención** , defina el ámbito diario, semanal, mensual y anual de los puntos de copia de seguridad específicos.

    ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/long-term-retention.png)

    La directiva de retención especifica el período de tiempo que se almacena una copia de seguridad. Puede especificar directivas de retención diferentes en función de cuándo se realizó la copia de seguridad.
7. Haga clic en **Trabajos** para ver la lista de trabajos de **Configurar protección**.

    ![Configurar trabajo de protección](./media/backup-azure-vms/protect-configureprotection.png)

    Ahora que ha establecido la directiva, vaya al paso siguiente y ejecute la copia de seguridad inicial.

## <a name="initial-backup"></a>Copia de seguridad inicial
Una vez que una máquina virtual se ha protegido con una directiva, dicha relación se puede ver en la pestaña **Elementos protegidos** . Hasta que se realiza la copia de seguridad inicial, en **Estado de protección** se muestra el valor **Protegido (copia de seguridad inicial pendiente)**. De forma predeterminada, la primera copia de seguridad programada es la *copia de seguridad inicial*.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para empezar con la copia de seguridad inicial de forma inmediata:

1. En la página **Elementos protegidos**, haga clic en **Realizar copia de seguridad ahora** en la parte inferior de la página.
    ![Icono Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    El servicio Copia de seguridad de Azure crea un trabajo de copia de seguridad para la operación de copia de seguridad inicial.
2. Haga clic en la pestaña **Trabajos** para ver la lista de los trabajos.

    ![Copia de seguridad en curso](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Una vez completada la copia de seguridad inicial, el estado de la máquina virtual en la pestaña **Elementos protegidos** es *Protegido*.

    ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/protect-backedupvm.png)

   > [!NOTE]
   > La copia de seguridad de máquinas virtuales es un proceso local. No puede realizar copias de seguridad de máquinas virtuales desde una región hasta un almacén de copia de seguridad de otra región. Por lo tanto, para cada región de Azure que tiene máquinas virtuales que necesiten una copia de seguridad, debe crearse al menos un almacén de copia de seguridad en esa región.
   >
   >

## <a name="next-steps"></a>Pasos siguientes
Una vez que se ha copiado correctamente una máquina virtual, hay varios pasos que pueden ser de interés. El paso más lógico sería familiarizarse con la restauración de datos en una máquina virtual. Sin embargo, hay tareas de administración que le ayudarán a entender cómo mantener seguros los datos y minimizar los costos.

* [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md)
* [Restauración de máquinas virtuales](backup-azure-restore-vms.md)
* [Guía de solución de problemas](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

