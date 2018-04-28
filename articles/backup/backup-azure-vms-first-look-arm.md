---
title: 'Primer análisis: protección de máquinas virtuales de Azure con un almacén de Recovery Services | Microsoft Docs'
description: Copia de seguridad de máquinas virtuales de Azure con ARM en un almacén de Servicios de recuperación. Use copias de seguridad de máquinas virtuales implementadas con Resource Manager, máquinas virtuales implementadas con el modelo clásico, máquinas virtuales con Premium Storage, máquinas virtuales cifradas y máquinas virtuales en instancias de Managed Disks para proteger los datos. Cree y registre un almacén de Servicios de recuperación. Registre máquinas virtuales, cree directivas y proteja máquinas virtuales en Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: markgal;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e6a29e184a47e3b4304f9c4683e76feab3e75dd4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vault"></a>Copia de seguridad de máquinas virtuales de Azure en almacenes de Recovery Services

En este artículo se explica cómo configurar la protección de una máquina virtual desde el menú de operaciones de máquinas virtuales o el almacén de Recovery Services. Los almacenes de Recovery Services protegen:

* Máquinas virtuales implementadas en Azure Resource Manager
* Máquinas virtuales clásicas
* Máquinas virtuales de almacenamiento estándar
* Máquinas virtuales de almacenamiento premium
* Máquinas virtuales en ejecución en instancias de Managed Disks
* Máquinas virtuales cifradas mediante Azure Disk Encryption
* Copia de seguridad coherente con la aplicación de máquinas virtuales Windows mediante VSS y máquinas virtuales Linux con scripts personalizados previos a la instantánea y posteriores a la instantánea

Para más información sobre la protección de máquinas virtuales de Premium Storage, consulte el artículo sobre la [copia de seguridad y la restauración de máquinas virtuales de Premium Storage](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Para más información sobre la compatibilidad con máquinas virtuales de disco administrado, consulte el artículo sobre la [copia de seguridad y la restauración de máquinas virtuales en discos administrados](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Para más información acerca de los marcos previo y posterior al script para copias de seguridad de máquinas virtuales Linux, consulte [Copias de seguridad coherentes con la aplicación de las máquinas virtuales Linux de Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Para obtener más información sobre qué copias de seguridad que se pueden realizar, vea [Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

> [!NOTE]
> En este tutorial se asume que tiene una máquina virtual en su suscripción de Azure y que ha tomado las medidas necesarias para que el servicio de copia de seguridad pueda acceder a dicha máquina virtual.
>
>

Dependiendo del número de máquinas virtuales que quiera proteger, puede usar diferentes puntos de partida. Si quiere hacer una copia de seguridad de varias máquinas virtuales en una operación, vaya al almacén de Recovery Services e [inicie el trabajo de copia de seguridad desde el panel del almacén](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Si quiere realizar una copia de seguridad de una sola máquina virtual, [inicie el trabajo de copia de seguridad desde el menú de operaciones de máquina virtual](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>Configuración del trabajo de copia de seguridad desde el menú de operaciones de máquina virtual

Siga estos pasos para configurar el trabajo de copia de seguridad desde el menú de operaciones de máquina virtual. Los pasos se aplican únicamente a máquinas virtuales de Azure Portal.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. En el menú central, haga clic en **Todos los servicios** y, en el cuadro de diálogo Filtrar, escriba **Máquinas virtuales**. A medida que escribe, se filtra la lista de recursos. Cuando vea Máquinas virtuales, selecciónelo.

  ![Captura de pantalla que muestra cómo ir a las máquinas virtuales desde Todos los servicios](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  Aparece la lista de máquinas virtuales (VM) de la suscripción.

  ![Aparece la lista de máquinas virtuales de la suscripción.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. En la lista, seleccione la máquina virtual de la que quiere realizar una copia de seguridad.

  ![Aparece la lista de máquinas virtuales de la suscripción.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Cuando se selecciona la máquina virtual, la lista de máquinas virtuales pasa a la izquierda y se abren el menú Administración de máquina virtual y el panel de máquinas virtuales.

4. En el menú Administración de máquina virtual, en la sección **Operaciones**, haga clic en **Copia de seguridad**. </br>

  ![Opción Copia de seguridad del menú Administración de máquina virtual](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  Se abre el menú Habilitar copia de seguridad.

  ![Opción Copia de seguridad del menú Administración de máquina virtual](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. En el almacén de Recovery Services, haga clic en **Seleccionar existente** y elija un almacén de la lista desplegable.

  ![Habilitar el Asistente para copia de seguridad](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  Si no hay ningún almacén de Recovery Services o quiere usar uno nuevo, haga clic en **Create new** (Crear nuevo) y proporcione el nombre del nuevo almacén. Se crea un nuevo almacén en el mismo grupo de recursos y en la misma región que la máquina virtual. Si quiere crear un almacén de Recovery Services con valores diferentes, consulte la sección sobre cómo [crear un directorio de Recovery Services](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. En el menú Elegir directiva de copia de seguridad, seleccione una directiva. Los detalles de la directiva seleccionada aparecen debajo del menú desplegable.

  Si quiere crear otra directiva o modificar la directiva existente, haga clic en **Crear una nueva directiva (o editar una)** para abrir el editor de directivas de copia de seguridad. Si quiere instrucciones para definir una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Para guardar los cambios en la directiva de copia de seguridad y volver al menú Habilitar copia de seguridad, haga clic en **Aceptar**.

  ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Para aplicar la directiva de copia de seguridad y almacén de Recovery Services a la máquina virtual, haga clic en **Habilitar copia de seguridad** para implementar la directiva. La implementación de la directiva la asocia con el almacén y las máquinas virtuales.

  ![Botón Enable Backup (Habilitar la copia de seguridad)](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Puede realizar el seguimiento del progreso de la configuración mediante la notificación que aparece en el portal. En el ejemplo siguiente se muestra que se ha iniciado la implementación.

  ![Notificaciones de la opción Habilitar copia de seguridad](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Cuando el progreso de la configuración haya finalizado, en el menú Administración de máquina virtual, haga clic en **Copia de seguridad** para abrir el menú Copia de seguridad y ver los detalles disponibles.

  ![Vista de Elemento de copia de seguridad de la máquina virtual](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  Hasta que se haya completado la copia de seguridad inicial, el **estado de la última copia de seguridad** se muestra como **Advertencia (copia de seguridad inicial pendiente)**. Para ver cuándo se produce el siguiente trabajo de copia de seguridad programado, en **Resumen**, haga clic en el nombre de la directiva. Se abre el menú Directiva de copia de seguridad y muestra la hora de la copia de seguridad programada.

10. Para proteger la máquina virtual, haga clic en **Realizar copia de seguridad ahora**. 

  ![Haga clic en Realizar copia de seguridad ahora para ejecutar la copia de seguridad inicial.](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Se abrirá el menú Realizar copia de seguridad ahora. 

  ![muestra la hoja Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. En el menú Realizar copia de seguridad ahora, haga clic en el icono del calendario, use el control de calendario para seleccionar el último día en que se mantendrá este punto de recuperación y haga clic en **Aceptar**.

  ![establece el último día en que se mantiene el punto de recuperación de Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Las notificaciones de implementación le permiten saber si se ha desencadenado el trabajo de copia de seguridad y que puede supervisar el progreso del trabajo en la página de trabajos de copia de seguridad.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Configuración del trabajo de copia de seguridad desde el almacén de Recovery Services
Para configurar el trabajo de copia de seguridad, complete los pasos siguientes.

1. Cree un almacén de Recovery Services para una máquina virtual.
2. Use Azure Portal para seleccionar un escenario, establecer directivas de copia de seguridad e identificar los elementos que desea proteger.
3. Realizar la copia de seguridad inicial.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creación de un almacén de Servicios de recuperación para una máquina virtual
Un almacén de Recovery Services es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de Recovery Services contiene también la directiva de copia de seguridad que se aplica a las máquinas virtuales protegidas.

> [!NOTE]
> La copia de seguridad de máquinas virtuales es un proceso local. No se pueden hacer copias de seguridad de máquinas virtuales de una región en un almacén de Recovery Services de otra. Por lo tanto, en todas las regiones de Azure que tenga máquinas virtuales de las que se vayan a realizar copias de seguridad, debe existir al menos un almacén de Recovery Services.
>
>

Para crear un almacén de Recovery Services:

1. Si aún no lo ha hecho, inicie sesión en [Azure Portal](https://portal.azure.com/) mediante su suscripción de Azure.
2. En el menú central, haga clic en **Todos los servicios** y, en el cuadro de diálogo Filtro, escriba **Recovery Services**. A medida que escribe, se filtra la lista de recursos. Cuando vea almacenes de Recovery Services en la lista, haga clic en ellos.

    ![Creación del almacén de Recovery Services, paso 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Si hay almacenes de Recovery Services en la suscripción, estos aparecerán en una lista.

    ![Creación del almacén de Recovery Services, paso 2](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. En el menú **Almacenes de Recovery Services**, haga clic en **Agregar**.

    ![Creación del almacén de Recovery Services, paso 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Se abre el menú del almacén de Recovery Services, donde se le pide que especifique los valores de **nombre**, **suscripción**, **grupo de recursos** y **ubicación**.

    ![Creación del almacén de Recovery Services, paso 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

5. En la sección **Suscripción**, utilice el menú desplegable para elegir la suscripción de Azure. Si utiliza una sola suscripción, esta aparece y puede pasar al paso siguiente. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo hay varias opciones si la cuenta de su organización está asociada a más de una suscripción de Azure.

6. En la sección **Grupo de recursos**:

    * Si desea crear un nuevo grupo de recursos, seleccione **Crear nuevo**.
    o
    * Seleccione **Use existing** (Usar existente) y haga clic en el menú desplegable para ver la lista de grupos de recursos disponibles.

  Para más información sobre los grupos de recursos, consulte [Introducción a Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. Esta elección determina la región geográfica a la que se envían los datos de copia de seguridad.

  > [!IMPORTANT]
  > Si no está seguro de la región en que se encuentra la máquina virtual, cierre el cuadro de diálogo de creación del almacén y vaya a la lista de máquinas virtuales del portal. Si tiene máquinas virtuales en varias regiones, cree un almacén de Recovery Services en cada una de ellas. Cree el almacén en la primera región antes de pasar a la siguiente. No es necesario especificar las cuentas de almacenamiento usadas para almacenar los datos de copia de seguridad (el almacén de Recovery Services y el servicio Azure Backup controlarán automáticamente el almacenamiento).
  >

8. En la parte inferior del menú Almacenes de Recovery Services, haga clic en **Crear**.

    La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones de estado de la parte superior derecha del portal. Una vez creado el almacén, aparece en la lista de almacenes de Recovery Services. Si no ve el almacén pasados unos minutos, haga clic en **Refresh** (Actualizar).

    ![Clic en el botón Refresh (Actualizar)](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Cuando vea el almacén en la lista de almacenes de Recovery Services, estará listo para configurar la redundancia de almacenamiento.

Ahora que ha creado el almacén, aprenda a configurar la replicación de almacenamiento.

### <a name="set-storage-replication"></a>Configuración de la replicación de almacenamiento
La opción de replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si el almacén de Recovery Services es la copia de seguridad principal, deje la opción de replicación de almacenamiento establecida en almacenamiento con redundancia geográfica. Elija un almacenamiento con redundancia local si desea una opción más económica que no sea tan duradera. Para más información sobre las opciones de almacenamiento [con redundancia geográfica](../storage/common/storage-redundancy-grs.md) y [con redundancia local](../storage/common/storage-redundancy-lrs.md), consulte [Replicación de Azure Storage](../storage/common/storage-redundancy.md).

Para editar la configuración de replicación de almacenamiento:

1. En el menú **Almacenes de Recovery Services**, seleccione un almacén nuevo.

  ![Selección del almacén nuevo en la lista de almacenes de Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Al seleccionar el almacén, se abren el menú Configuración (*con el nombre del almacén en la parte superior*) y el panel del almacén.

  ![Vista de la configuración de almacenamiento del nuevo almacén](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. En el menú Administración de máquina virtual del nuevo almacén, desplácese con el control deslizante vertical hasta la sección Administrar y haga clic en **Infraestructura de Backup** para abrir el menú Infraestructura de Backup.
 
   ![Establecimiento de la configuración de almacenamiento del nuevo almacén](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. En el menú Infraestructura de Backup, haga clic en **Configuración de copia de seguridad** para abrir el menú **Configuración de copia de seguridad**.

    ![Establecimiento de la configuración de almacenamiento del nuevo almacén](./media/backup-try-azure-backup-in-10-mins/set-storage-open-infra.png)
4. Elija la opción de replicación del almacenamiento apropiada para su almacén.

    ![opciones de configuración de almacenamiento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si usa Azure como punto de conexión de almacenamiento de copia de seguridad principal, siga utilizando **Redundancia geográfica**. Si no utiliza Azure como punto de conexión de almacenamiento de copia de seguridad principal, elija **Redundancia local** para reducir los costes de almacenamiento de Azure. En esta página de [información general sobre la redundancia del almacenamiento](../storage/common/storage-redundancy.md) encontrará más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/common/storage-redundancy-grs.md) y [redundancia local](../storage/common/storage-redundancy-lrs.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selección de un objetivo de copia de seguridad, establecimiento de la directiva y definición de los elementos para proteger
Antes de registrar una máquina virtual en un almacén, ejecute el proceso de detección para asegurarse de que se identifican todas las nuevas máquinas virtuales que se hayan agregado a la suscripción. El proceso consulta a Azure para recibir la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, como el nombre del servicio en la nube y la región. En el Portal de Azure, el escenario se refiere a lo que va a colocar en el almacén de servicios de recuperación. Directiva es la programación de la frecuencia y de cuándo se eligen los puntos de recuperación. La directiva también incluye el intervalo de retención de los puntos de recuperación.

1. Si ya tiene abierto un almacén de Servicios de recuperación, vaya al paso 2. En caso contrario, haga clic en **Todos los servicios**. Escriba **Recovery Services** y haga clic en **Almacenes de Recovery Services**.

    ![Creación del almacén de Recovery Services, paso 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Aparece la lista de almacenes de Servicios de recuperación.

    ![Visualización de la lista de almacenes de Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    En la lista de almacenes de Recovery Services, seleccione un almacén para abrir su panel.

     ![Abrir el menú Almacén](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. En el menú del panel del almacén, haga clic en **Copia de seguridad** para abrir el menú Copia de seguridad.

    ![Abrir el menú Copia de seguridad](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Se abren los menús Copia de seguridad y Objetivo de Backup.

    ![Abrir el menú Escenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. En el menú Objetivo de Backup, en el menú desplegable **¿Dónde se ejecuta su carga de trabajo?**, elija Azure. En la lista desplegable **What do you want to backup** (¿De qué quiere realizar una copia de seguridad?), elija Máquina virtual y luego haga clic en **Aceptar**.

    Estas acciones permiten registrar la extensión de la máquina virtual con el almacén. El menú Objetivo de Backup se cierra y se abre el menú **Directiva de copia de seguridad**.

    ![Abrir el menú Escenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. En el menú Directiva de copia de seguridad, seleccione la directiva que quiera aplicar al almacén.

    ![Seleccionar directiva de copia de seguridad](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Los detalles de la directiva predeterminada se muestran en el menú desplegable. Si desea crear una directiva, seleccione **Crear nuevo** en el menú desplegable. Si quiere instrucciones para definir una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Haga clic en **Aceptar** para asociar la directiva de copia de seguridad con el almacén.

    El menú Directiva de copia de seguridad se cierra y se abre el menú **Seleccionar máquinas virtuales**.
5. En el menú **Seleccionar máquinas virtuales**, elija las máquinas virtuales que quiere asociar a la directiva especificada y haga clic en **Aceptar**.

    ![Seleccionar carga de trabajo](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Se valida la máquina virtual seleccionada. Si no ve las máquinas virtuales que espera ver, compruebe que existen en la misma ubicación de Azure que el almacén de Recovery Services y que no estén ya protegidas. La ubicación del almacén de Recovery Services se muestra en el panel del almacén.

6. Ahora que ha definido toda la configuración del almacén, en el menú Copia de seguridad, haga clic en **Habilitar copia de seguridad** para implementar la directiva en el almacén y las máquinas virtuales. La implementación de la directiva de copia de seguridad no crea el punto de recuperación inicial para la máquina virtual.

    ![Habilitar copia de seguridad](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Después de habilitar correctamente la copia de seguridad, la directiva de copia de seguridad se ejecutará en la programación. Sin embargo, proceda a iniciar el primer trabajo de copia de seguridad.

## <a name="initial-backup"></a>Copia de seguridad inicial
Una vez que se ha implementado una directiva de copia de seguridad en la máquina virtual, no significa que se copiarán los datos. De forma predeterminada, la primera copia de seguridad programada (tal y como se define en la directiva de copia de seguridad) es la copia de seguridad inicial. Hasta que se realice la copia de seguridad inicial, el estado de la última copia aparecerá como **Advertencia (copia de seguridad inicial pendiente)** en el menú **Trabajos de copia de seguridad**.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que la copia de seguridad inicial vaya a comenzar pronto, se recomienda ejecutar **Hacer ahora una copia de seguridad**.

Para ejecutar el trabajo de copia de seguridad inicial:

1. En el panel del almacén, haga clic en el número situado bajo **Elementos de copia de seguridad** o haga clic en el icono **Elementos de copia de seguridad**. <br/>
  ![Icono Configuración](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Se abrirá el menú **Elementos de copia de seguridad**.

  ![Elementos de copias de seguridad](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. En el menú **Elementos de copia de seguridad**, seleccione el elemento.

  ![Icono Configuración](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Se abrirá la lista **Elementos de copia de seguridad**. <br/>

  ![Trabajo de copia de seguridad desencadenado](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. En la lista **Elementos de copia de seguridad**, haga clic en el botón de puntos suspensivos **...** para abrir el menú contextual.

  ![Menú contextual](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Aparece el menú contextual.

  ![Menú contextual](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. En el menú contextual, haga clic en **Realizar copia de seguridad ahora**.

  ![Menú contextual](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Se abrirá el menú Realizar copia de seguridad ahora.

  ![muestra el menú Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. En el menú Realizar copia de seguridad ahora, haga clic en el icono del calendario, use el control de calendario para seleccionar el último día en que se mantendrá este punto de recuperación y haga clic en **Copia de seguridad**.

  ![establece el último día en que se mantiene el punto de recuperación de Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Las notificaciones de implementación le permiten saber si se ha desencadenado el trabajo de copia de seguridad y que puede supervisar el progreso del trabajo en la página de trabajos de copia de seguridad. Según el tamaño de la máquina virtual, la creación de la copia de seguridad inicial puede tardar un tiempo.

6. Para ver o realizar el seguimiento del estado de la copia de seguridad inicial, en el panel del almacén, en el icono **Trabajos de Backup**, haga clic en **En curso**.

  ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Se abrirá el menú Trabajos de copia de seguridad.

  ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  En el menú **Trabajos de copia de seguridad**, puede ver el estado de todos los trabajos. Compruebe si el trabajo de copia de seguridad para la máquina virtual aún está en curso o si se ha terminado. Cuando finalice un trabajo de copia de seguridad, el estado será *Completado*.

  > [!NOTE]
  > Como parte de la operación de copia de seguridad, el servicio Azure Backup emite un comando a la extensión de copia de seguridad en cada máquina virtual para vaciar toda la escritura y tomar una instantánea coherente.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instale el agente de máquina virtual en la máquina virtual.
Esta información se proporciona en caso de que sea necesaria. El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Sin embargo, si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual. Las máquinas virtuales que se migran desde centros de datos locales no tienen instalado el agente de máquina virtual. En ese caso, el agente de máquina virtual debe instalarse explícitamente. Si tiene problemas para realizar una copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure está instalado correctamente en la máquina virtual (consulte la tabla siguiente). Si crea una máquina virtual personalizada, instale el agente de máquina virtual antes de aprovisionarla.

Obtenga información acerca del [agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y [cómo instalarlo](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

La tabla siguiente proporciona información adicional acerca del agente de máquina virtual para las máquinas virtuales de Windows y Linux.

| **operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual |<li>Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Para completar la instalación, necesita privilegios de administrador. <li>[Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |<li> Instale el [agente de Linux](https://github.com/Azure/WALinuxAgent) más reciente desde GitHub. Para completar la instalación, necesita privilegios de administrador. <li> [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |
| Actualización del agente de la máquina virtual |Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |Siga las instrucciones para [actualizar el agente de máquina virtual Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |
| Validación de la instalación del agente de máquina virtual |<li>Acceda a la carpeta *C:\WindowsAzure\Packages* de la máquina virtual de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, desplácese hasta **Propiedades** y seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. |N/D |

### <a name="backup-extension"></a>Extensión de Backup
Una vez que el agente de máquina virtual está instalado en la máquina virtual, el servicio Azure Backup instala la extensión de copia de seguridad en el agente de máquina virtual. El servicio Azure Backup actualiza y aplica revisiones perfectamente a la extensión de copia de seguridad sin la intervención del usuario.

El servicio Backup instalará la extensión de la copia de seguridad, incluso si no se está ejecutando la máquina virtual. Una máquina virtual en ejecución ofrece más probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Azure Backup sigue realizando la copia de seguridad de la máquina virtual aunque esté apagada y no haya sido posible instalar la extensión. Este tipo de copia de seguridad se conoce como máquina virtual sin conexión y el punto de recuperación es *coherente frente a bloqueos*.

## <a name="troubleshooting-information"></a>Información para solución de problemas
Si tiene problemas para realizar cualquiera de las tareas de este artículo, consulte la [guía de solución de problemas](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Precios
El costo de la copia de seguridad de máquinas virtuales de Azure se basa en el número de instancias protegidas. Para obtener una definición de una instancia protegida, consulte [Descripción de una instancia protegida](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Para obtener un ejemplo de cálculo del costo de copia de seguridad de una máquina virtual, consulte [¿Cómo se calculan las instancias protegidas?](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances). Consulte la página de precios de Azure Backup para obtener información acerca de los [precios de Backup](https://azure.microsoft.com/pricing/details/backup/).

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).
