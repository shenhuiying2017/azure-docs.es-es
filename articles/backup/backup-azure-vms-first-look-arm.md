<properties
	pageTitle="Protección de máquinas virtuales de ARM con Copia de seguridad de Azure | Microsoft Azure"
	description="Proteja las máquinas virtuales de ARM con el servicio Copia de seguridad de Azure. Use copias de seguridad de máquinas virtuales de ARM para proteger los datos. Cree y registre un almacén de Servicios de recuperación. Registre máquinas virtuales, cree directivas y proteja máquinas virtuales en Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keyword="backups; vm backup"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/31/2016"
	ms.author="markgal; jimpark"/>


# Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Servicios de recuperación

> [AZURE.SELECTOR]
- [Copia de seguridad de máquinas virtuales en ARM](backup-azure-vms-first-look-arm.md)
- [Copia de seguridad de máquinas virtuales en modo clásico](backup-azure-vms-first-look.md)

Este tutorial le guiará por los pasos para crear un almacén de Servicios de recuperación y realizar copias de seguridad de una máquina virtual (VM) de Azure. En este tutorial se usan almacenes de Servicios de recuperación para proteger las máquinas virtuales basadas en Azure Resource Manager (ARM) o IaaS v.2.

>[AZURE.NOTE] Aquí se da por hecho que tiene una máquina virtual en su suscripción de Azure y que ha tomado las medidas necesarias para que el servicio de copia de seguridad tenga acceso a dicha máquina virtual. Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). Este artículo es para su uso con Resource Manager y máquinas virtuales basadas en ARM.

A un alto nivel, estos son los pasos que debe completar.

1. Crear un almacén de Servicios de recuperación para una máquina virtual.
2. Usar el Portal de Azure para seleccionar un escenario, establecer directivas e identificar los elementos para proteger.
3. Realizar la copia de seguridad inicial.



## Paso 1: Creación de un almacén de Servicios de recuperación para una máquina virtual

Un almacén de Servicios de recuperación es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de Servicios de recuperación contiene también la directiva de copia de seguridad que se aplica a las máquinas virtuales protegidas.

>[AZURE.NOTE] La copia de seguridad de máquinas virtuales es un proceso local. No puede hacer copias de seguridad de máquinas virtuales de una ubicación en un almacén de Servicios de recuperación que está en otra ubicación. Por lo tanto, para cada ubicación de Azure que tenga máquinas virtuales para realizar copias de seguridad, debe existir al menos un almacén de Servicios de recuperación en esa ubicación.


Para crear un almacén de Servicios de recuperación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En el menú Concentrador, haga clic en **Examinar** y, en la lista de recursos, escriba **Servicios de recuperación**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Servicios de recuperación**.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    Se muestra la lista de almacenes de Servicios de recuperación.

3. En el menú **Almacenes de Servicios de recuperación**, haga clic en **Agregar**.

    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    Se abre la hoja del almacén de Servicios de recuperación, que le pide que proporcione los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)

4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.

6. Haga clic en **Grupo de recursos** para ver la lista disponible de grupos de recursos, o haga clic en **Nuevo** para crear uno nuevo. Para información completa acerca de los grupos de recursos, consulte [Uso del Portal de Azure para implementar y administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. El almacén **debe** estar en la misma región que las máquinas virtuales que quiere proteger.

    >[AZURE.IMPORTANT] Si no está seguro de en qué ubicación existe la máquina virtual, cierre el cuadro de diálogo de creación del almacén y vaya a la lista de máquinas virtuales del portal. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de Servicios de recuperación en cada región. Cree el almacén en la primera ubicación antes de pasar a la siguiente ubicación. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de Servicios de recuperación y el servicio Copia de seguridad de Azure lo controlarán automáticamente.

8. Haga clic en **Crear**. La creación del almacén de Servicios de recuperación puede tardar unos minutos. Supervise las notificaciones de estado en la parte superior derecha del portal. Una vez creado el almacén, se abre en el portal.

9. En el almacén, haga clic en **Toda la configuración** > **Configuración de copia de seguridad** para ver el **Tipo de replicación de almacenamiento**. Elija la opción de replicación del almacenamiento para su almacén.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look-arm/choose-storage-configuration.png)

    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, se recomienda seguir utilizando el almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión del almacenamiento de copia de seguridad no principal, se puede considerar la posibilidad de elegir un almacenamiento con redundancia local, ya que ello reducirá el costo de almacenamiento de datos en Azure. En esta página de [información general](../storage/storage-redundancy.md), puede encontrar más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y con [redundancia local](../storage/storage-redundancy.md#locally-redundant-storage).

    Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## Paso 2: Selección de la directiva de establecimiento de escenario y definición de los elementos para proteger
Antes de registrar una máquina virtual en un almacén, ejecute el proceso de detección para asegurarse de que se identifican todas las nuevas máquinas virtuales que se hayan agregado a la suscripción. El proceso consulta a Azure la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, por ejemplo, el nombre del servicio en la nube y la región.

1. Si ya tiene un abierto de almacén de Servicios de recuperación, vaya al paso 2. Si no tiene un abierto de almacén de Servicios de recuperación, pero está en el Portal de Azure, en el menú Concentrador, haga clic en **Examinar**.

    - En la lista de recursos, escriba **Servicios de recuperación**.
    - Cuando comience a escribir, la lista se filtrará en función de la entrada. Cuando vea **Almacén de Servicios de recuperación**, haga clic en él.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    Aparece la lista de almacenes de Servicios de recuperación.

    - En la lista de almacenes de Servicios de recuperación, seleccione un almacén.

    Se abre el panel del almacén seleccionado.

    ![Hoja del almacén abierta](./media/backup-azure-vms-first-look-arm/vault-settings.png)

2. En el menú del panel del almacén, haga clic en **Copia de seguridad** para abrir la hoja Copia de seguridad.

    ![Hoja Copia de seguridad abierta](./media/backup-azure-vms-first-look-arm/backup-button.png)

    Cuando se abre la hoja, el servicio Copia de seguridad busca las máquinas virtuales nuevas en la suscripción.

    ![Detectar máquinas virtuales](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)

3. En la hoja Copia de seguridad, haga clic en **Escenario** para abrir la hoja Escenario.

    ![Hoja Escenario abierta](./media/backup-azure-vms-first-look-arm/select-backup-scenario-one.png)

4. En la hoja Escenario, en el menú **Tipo de copia de seguridad**, seleccione **Copia de seguridad de máquina virtual de Azure** y haga clic en **Aceptar**.

    ![Hoja Escenario abierta](./media/backup-azure-vms-first-look-arm/select-rs-backup-scenario-two.png)

    La hoja Escenario se cierra y se abre la hoja Directiva de copia de seguridad.

5. En la hoja Copia de seguridad, seleccione la directiva de copia de seguridad que desea aplicar al almacén y haga clic en **Aceptar**.

    ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy.png)

    La directiva predeterminada se muestra en los detalles. Si desea crear una nueva directiva, seleccione **Crear nuevo**. Para obtener instrucciones acerca de cómo definir una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Al hacer clic en Aceptar, la directiva de copia de seguridad se asocia con el almacén. A continuación, elija las máquinas virtuales que se asociarán con el almacén.

6. Elija las máquinas virtuales que se asociarán con la directiva especificada y haga clic en **Seleccionar**.

    ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look-arm/select-vms-to-backup.png)

    Si no ve la máquina virtual que desea en la lista, haga clic en **Actualizar**. Si aún no ve la máquina virtual deseada, compruebe que existe en la misma ubicación de Azure que el almacén de Servicios de recuperación.

7. Ahora que ha definido toda la configuración del almacén, en la hoja Copia de seguridad, haga clic en **Habilitar la copia de seguridad** en la parte inferior de la página. La directiva se implementa en el almacén y en las máquinas virtuales.

    ![Habilitar copia de seguridad](./media/backup-azure-vms-first-look-arm/enable-backup-settings.png)


## Paso 3: Copia de seguridad inicial

Una vez que se ha implementado una directiva de copia de seguridad en la máquina virtual, no significa que se copiarán los datos. De forma predeterminada, la primera copia de seguridad programada (tal y como se define en la directiva de copia de seguridad) es la copia de seguridad inicial. Hasta que se realice la copia de seguridad inicial, el estado de la última copia de seguridad en la hoja **Trabajos de copia de seguridad** se muestra como **Advertencia (copia de seguridad inicial pendiente)**.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que la copia de seguridad inicial deba empezar muy pronto, se recomienda que ejecute una **Hacer copia de seguridad ahora**.

Para ejecutar **Copia de seguridad ahora**

1. En el panel del almacén, en el icono **Copia de seguridad**, haga clic en **Máquinas virtuales de Azure** <br/> ![Icono Configuración](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Se abrirá la hoja **Elementos de copia de seguridad**.

2. En la hoja **Elementos de copia de seguridad**, haga clic con el botón derecho en el almacén del que desea realizar la copia de seguridad, y haga clic en **Hacer copia de seguridad ahora**.

    ![Icono Configuración](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Se desencadena el trabajo de copia de seguridad. <br/>

    ![Trabajo de copia de seguridad desencadenado](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. Para ver si la copia de seguridad inicial se ha completado, en el panel del almacén, en el icono **Trabajos de copia de seguridad**, haga clic en **Máquinas virtuales de Azure**.

    ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Se abrirá la hoja Trabajos de copia de seguridad.

4. En la hoja Trabajos de copia de seguridad, puede ver el estado de todos los trabajos.

    ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Como parte de la operación de copia de seguridad, el servicio Copia de seguridad de Azure emite un comando a la extensión de copia de seguridad en cada máquina virtual para vaciar toda la escritura y tomar una instantánea coherente.

    Una vez finalizado el trabajo de copia de seguridad, el estado es *Completado*.

## Definición de una directiva de copia de seguridad

Una directiva de copia de seguridad define una matriz del momento en que se toman las instantáneas de datos y cuánto tiempo se retienen las instantáneas. Al definir una directiva para la copia de seguridad de una máquina virtual, puede desencadenar un trabajo de copia de seguridad *una vez al día*. Cuando se crea una nueva directiva, se aplica al almacén. La interfaz de la directiva de copia de seguridad tiene el siguiente aspecto:

![Directiva de copia de seguridad](./media/backup-azure-vms-first-look-arm/backup-policy-daily-raw.png)

Para crear una directiva:

1. En **Nombre de directiva**, asigne un nombre a la directiva.

2. Puede tomar instantáneas de los datos con un intervalo diario o semanal. Use el menú desplegable **Frecuencia de copia de seguridad** para elegir si las instantáneas de datos se realizan diaria o semanalmente.

    - Si elige un intervalo diario, use el control resaltado para seleccionar la hora del día para la instantánea. Para cambiar la hora, anule la selección de la hora y seleccione la nueva hora.

    ![Directiva de copia de seguridad diaria](./media/backup-azure-vms-first-look-arm/backup-policy-daily.png) <br/>

    - Si elige un intervalo semanal, use los controles resaltados para seleccionar los días de la semana y la hora del día para realizar la instantánea. En el menú del día, seleccione uno o varios días. En el menú de hora, seleccione una hora. Para cambiar la hora, anule la selección de la hora y seleccione la nueva hora.

    ![Directiva de copia de seguridad semanal](./media/backup-azure-vms-first-look-arm/backup-policy-weekly.png)

3. De forma predeterminada, todas las opciones de **Duración de retención** están seleccionadas. Desactive los límites de intervalo de retención que no quiera usar.

    >[AZURE.NOTE] Al proteger una máquina virtual, un trabajo de copia de seguridad se ejecuta una vez al día. La hora a la que se ejecuta la copia de seguridad es la misma en cada intervalo de retención.

    En los controles correspondientes, especifique los intervalos que se usarán. Los intervalos de retención mensual y anual le permiten especificar las instantáneas con un incremento diario o semanal.

4. Después de configurar todas las opciones de la directiva, en la parte inferior de la hoja, haga clic en **Aceptar**.

    La nueva directiva está establecida para aplicarse al almacén una vez completada la configuración del almacén de Servicios de recuperación. Vuelva al paso 6 de la sección, [Selección de la directiva de establecimiento de escenario y definición de los elementos para proteger](backup-azure-vms-first-look-arm.md#step-2---select-scenario-set-policy-and-define-items-to-protect).

## Instale el agente de máquina virtual en la máquina virtual.

Esta información se proporciona en caso de que sea necesaria. El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Sin embargo, si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual. Sin embargo, las máquinas virtuales que se migran desde centros de datos locales no tienen instalado el agente de máquina virtual. En ese caso, el agente de máquina virtual debe instalarse explícitamente. Si tiene problemas para realizar una copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure está instalado correctamente en la máquina virtual (consulte la tabla siguiente). Si va a crear una máquina virtual personalizada, [asegúrese de que la casilla **Instalar el agente de máquina virtual** esté activada](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) antes de que se aprovisione la máquina virtual.

Obtenga información acerca del [Agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y de [cómo instalarlo](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

La tabla siguiente proporciona información adicional acerca del agente de máquina virtual para las máquinas virtuales de Windows y Linux.

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual | <li>Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación. <li>[Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. | <li> Instale el [agente de Linux](https://github.com/Azure/WALinuxAgent) más reciente desde GitHub. Necesitará privilegios de administrador para efectuar la instalación. <li> [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |
| Actualización del agente de la máquina virtual | Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Asegúrese de que no se ejecuta ninguna operación de copia de seguridad durante la actualización del agente de máquina virtual. | Siga las instrucciones que se indican en la sección sobre la [actualización del agente de máquina virtual Linux](../virtual-machines-linux-update-agent.md). <br>Asegúrese de que no se ejecutan operaciones de copia de seguridad durante la actualización del agente de máquina virtual. |
| Validación de la instalación del agente de máquina virtual | <li>Vaya a la carpeta *C:\\WindowsAzure\\Packages* de la máquina virtual de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, vaya a **Propiedades** y, luego, seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. | N/D |


### Extensión de copia de seguridad

Una vez que el agente de máquina virtual está instalado en la máquina virtual, el servicio Copia de seguridad de Azure instala la extensión de copia de seguridad en el agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza y aplica revisiones perfectamente a la extensión de copia de seguridad sin la intervención del usuario.

El servicio Copia de seguridad instala la extensión de copia de seguridad tanto si la máquina virtual está en ejecución como si no lo está. Una máquina virtual en ejecución ofrece más probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure realizará la copia de seguridad aunque esté apagada y no haya sido posible instalar la extensión. Esto se conoce como máquina virtual sin conexión. En este caso, el punto de recuperación será *coherente con los bloqueos*.



## Información sobre solución de problemas
Si tiene problemas para realizar algunas de las tareas en este artículo, consulte la [Guía de solución de problemas](backup-azure-vms-troubleshoot.md).


## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0406_2016-->