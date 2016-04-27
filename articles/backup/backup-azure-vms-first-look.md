<properties
	pageTitle="Protección de máquinas virtuales en Azure con Copia de seguridad de Azure | Microsoft Azure"
	description="Protección de máquinas virtuales de Azure con el servicio Copia de seguridad de Azure. El tutorial explica la creación de un almacén, el registro de máquinas virtuales, la creación de directivas y la protección de máquinas virtuales en Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/12/2016"
	ms.author="markgal; jimpark"/>


# Primer contacto: copia de seguridad de máquinas virtuales de Azure

> [AZURE.SELECTOR]
- [Copia de seguridad de máquinas virtuales en ARM](backup-azure-vms-first-look-arm.md)
- [Copia de seguridad de máquinas virtuales en modo clásico](backup-azure-vms-first-look.md)

Este tutorial le guiará por los pasos para crear copias de seguridad de una máquina virtual (VM) de Azure. Para completar correctamente este tutorial, deben existir estos requisitos previos:

- Ha creado una máquina virtual en su suscripción de Azure.
- El servicio de copia de seguridad puede acceder a la máquina virtual.

Estos son los pasos de alto nivel en el tutorial.

![Vista de alto nivel del proceso de copia de seguridad de máquina virtual](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

1. Cree un almacén de copia de seguridad o identifique uno ya existente que se encuentre *en la misma región que la máquina virtual*.
2. Utilice el portal de Azure para detectar y registrar las máquinas virtuales en la suscripción.
3. Instale el agente de máquina virtual en la máquina virtual (si utiliza una máquina virtual de la galería de Azure, el agente de máquina virtual ya esta presente).
4. Cree la directiva para proteger las máquinas virtuales.
5. Ejecute la copia de seguridad.

>[AZURE.NOTE] Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). El servicio de copia de seguridad de Azure es compatible con máquinas virtuales basadas en Azure Resource Manager (ARM), también conocidas como máquinas virtuales de IaaS V2. Este tutorial es para su uso con las máquinas virtuales que se pueden crear en el Portal de Azure clásico.


## Paso 1: creación de un almacén de copia seguridad para una máquina virtual

Un almacén de copia de seguridad es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de copia de seguridad contiene también las directivas de copia de seguridad que se aplican a las máquinas virtuales cuya copia de seguridad se está realizando.

La siguiente imagen muestra las relaciones entre las diversas entidades de Copia de seguridad de Azure: ![Relaciones y entidades de Copia de seguridad de Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).

2. En la esquina inferior izquierda del Portal de Azure, haga clic en **Nuevo**

    ![Haga clic en el menú Nuevo](./media/backup-azure-vms-first-look/new-button.png)

3. En el asistente para la creación rápida, haga clic en **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida**.

    ![Crear un almacén de copia de seguridad](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    El asistente le solicita el **nombre** y la **región**. Si administra más de una suscripción, aparecerá un cuadro de diálogo para elegir la suscripción.

4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre de entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

5. En **Región**, seleccione la región geográfica del almacén. El almacén **debe** estar en la misma región que las máquinas virtuales que desea proteger.

    Si no está seguro de en qué región está la máquina virtual, cierre el asistente y haga clic en Máquinas virtuales en la lista de máquinas virtuales de los servicios de Azure. La columna ubicación proporciona el nombre de la región. Si tiene máquinas virtuales en varias regiones, cree un almacén de copia de seguridad en cada una de ellas.

6. Si no hay ningún cuadro de diálogo de **suscripción** en el asistente, vaya al paso siguiente. Si trabaja con varias suscripciones, seleccione una suscripción para asociar con el nuevo almacén de copia de seguridad.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

7. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Un mensaje confirma que el almacén se creó correctamente. Se muestra en la página **Servicios de recuperación** como **Activo**.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

8. En la lista de almacenes de la página **Servicios de recuperación**, seleccione el almacén que ha creado para iniciar la página **Inicio rápido**.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look/active-vault-demo.png)

9. En la página **Inicio rápido**, haga clic en **Configurar** para abrir la opción de replicación del almacenamiento. ![Lista de copias de seguridad](./media/backup-azure-vms-first-look/configure-storage.png)

10. En la opción de **replicación del almacenamiento**, elija la opción de replicación para su almacén.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, se recomienda seguir utilizando el almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión del almacenamiento de copia de seguridad no principal, considere la posibilidad de elegir un almacenamiento con redundancia local, ya que ello reducirá el costo de almacenamiento de datos en Azure. En esta página de [información general](../storage/storage-redundancy.md), puede encontrar más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y con [redundancia local](../storage/storage-redundancy.md#locally-redundant-storage).

Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## Paso 2: detección y registro de máquinas virtuales de Azure
Antes de registrar la máquina virtual con un almacén, ejecute el proceso de detección para identificar nuevas máquinas virtuales. El proceso consulta a Azure la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, por ejemplo, el nombre del servicio en la nube y la región.

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/)

2. En el Portal de Azure clásico, haga clic en **Servicios de recuperación** para abrir la lista de almacenes de Servicios de recuperación. ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. En la lista de almacenes de copia de seguridad, seleccione el almacén de copia de una máquina virtual.

    Cuando seleccione el almacén, se abrirá en la página **Inicio rápido**.

4. En el menú de almacén, haga clic en **Elementos registrados**.

    ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look/configure-registered-items.png)

5. En el menú **Tipo**, seleccione **Máquina virtual de Azure**.

    ![Seleccionar carga de trabajo](./media/backup-azure-vms/discovery-select-workload.png)

6. Haga clic en **DETECTAR** en la parte inferior de la página. ![Botón Detectar](./media/backup-azure-vms/discover-button-only.png)

    El proceso de detección puede tardar unos minutos mientras se tabulan las máquinas virtuales. Hay una notificación en la parte inferior de la pantalla que informa de que el proceso se está ejecutando.

    ![Detectar máquinas virtuales](./media/backup-azure-vms/discovering-vms.png)

    La notificación cambia cuando el proceso se completa.

    ![Detección realizada](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Haga clic en **REGISTRAR** en la parte inferior de la página. ![Botón Registrar](./media/backup-azure-vms-first-look/register-icon.png)

8. En el menú contextual **Elementos registrados**, seleccione las máquinas virtuales que desea registrar. Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguirlas.

    >[AZURE.TIP] Se pueden registrar varias máquinas virtuales al mismo tiempo.

    Se crea un trabajo para cada máquina virtual que ha seleccionado.

9. Haga clic en **Ver trabajo** en la notificación para ir a la página **Trabajos**.

    ![Registrar trabajo](./media/backup-azure-vms/register-create-job.png)

    La máquina virtual también aparece en la lista de elementos registrados junto con el estado de la operación de registro.

    ![Registrando estado 1](./media/backup-azure-vms/register-status01.png)

    Una vez completada la operación, el estado cambiará a *Registrado*.

    ![Registrando estado 2](./media/backup-azure-vms/register-status02.png)

## Paso 3: instalación del agente de máquina virtual en la máquina virtual

El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual. Puede ir directamente a la sección de [protección de máquinas virtuales](backup-azure-vms-first-look.md#step-4---protect-azure-virtual-machines).

Si la máquina virtual se migra desde un centro de datos local, es probable que no tenga instalado el agente de máquina virtual. Debe instalar al agente de máquina virtual en la máquina virtual antes de continuar con su protección. Para una información más detallada acerca de cómo instalar el agente de máquina virtual, consulte la [sección correspondiente del artículo de copia de seguridad de máquinas virtuales](backup-azure-vms-prepare.md#vm-agent).


## Paso 4: protección de máquinas virtuales de Azure
Ahora puede configurar una directiva de retención y copia de seguridad para la máquina virtual. Se pueden proteger varias máquinas virtuales en una sola acción de protección. Los almacenes de Copia de seguridad de Azure creados después de mayo de 2015 incluyen una directiva predeterminada integrada en el almacén. Esta directiva predeterminada viene con un período de retención predeterminado de 30 días y una programación de copia de seguridad diaria.

1. Vaya al almacén de copias de seguridad en **Servicios de recuperación**, en el Portal de Azure, y haga clic en **Elementos registrados**.
2. Seleccione **Máquina virtual de Azure** en el menú desplegable.

    ![Seleccionar carga de trabajo en el portal](./media/backup-azure-vms/select-workload.png)

3. Haga clic en **PROTEGER**, en la parte inferior de la página. ![Haga clic en Proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    Aparece el **asistente para la protección de elementos**, que muestra *solo* las máquinas virtuales que están registradas y no protegidas.

    ![Configuración de protección a escala](./media/backup-azure-vms/protect-at-scale.png)

4. Seleccione las máquinas virtuales que desee proteger.

    Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguirlas.

5. En el menú **Configurar la protección**, seleccione una directiva existente o cree una nueva para proteger las máquinas virtuales que ha identificado.

    Cada directiva de copia de seguridad puede tener asociadas varias máquinas virtuales. Sin embargo, la máquina virtual solo se puede asociar con una directiva cada vez.

    ![Protección mediante nueva directiva](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Una directiva de copia de seguridad incluye un esquema de retención de las copias de seguridad programadas. Si selecciona una directiva de copia de seguridad existente, no podrá modificar las opciones de retención en el paso siguiente.

6. En **Duración de retención**, defina el ámbito diario, semanal, mensual y anual de los puntos de copia de seguridad específicos.

    ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/long-term-retention.png)

    La directiva de retención especifica el período de tiempo que se almacena una copia de seguridad. Puede especificar directivas de retención diferentes en función de cuándo se realizó la copia de seguridad.

7. Haga clic en **Trabajos** para ver la lista de trabajos en **Configurar protección**.

    ![Configurar trabajo de protección](./media/backup-azure-vms/protect-configureprotection.png)

    Ahora que ha establecido la directiva, vaya al paso siguiente y ejecute la copia de seguridad inicial.

## Paso 5: copia de seguridad inicial

Cuando una máquina virtual se ha protegido con una directiva, puede ver esa relación en la pestaña **Elementos protegidos**. Hasta que se realiza la copia de seguridad inicial, el **Estado de protección** aparece como **Protegido (copia de seguridad inicial pendiente)**. De forma predeterminada, la primera copia de seguridad programada es la *copia de seguridad inicial*.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para desencadenar la copia de seguridad inicial inmediatamente después de configurar la protección:

1. En la página **Elementos protegidos**, haga clic en **Realizar copia de seguridad ahora** en la parte inferior de la página. ![Icono Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    El servicio Copia de seguridad de Azure crea un trabajo de copia de seguridad para la operación de copia de seguridad inicial.

2. Haga clic en la pestaña **Trabajos** para ver la lista de los trabajos.

    ![Copia de seguridad en curso](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Una vez completada la copia de seguridad inicial, el estado de la máquina virtual en la pestaña **Elementos protegidos** se mostrará como *Protegido*.

    ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] La copia de seguridad de máquinas virtuales es un proceso local. No puede realizar copias de seguridad de máquinas virtuales desde una región hasta un almacén de copia de seguridad de otra región. Por lo tanto, para cada región de Azure que tiene máquinas virtuales que necesiten una copia de seguridad, debe crearse al menos un almacén de copia de seguridad en esa región.

## Pasos siguientes
Una vez que se ha copiado correctamente una máquina virtual, hay varios pasos que pueden ser de interés. El paso más lógico sería familiarizarse con la restauración de datos en una máquina virtual. Sin embargo, hay tareas de administración que le ayudarán a entender cómo mantener seguros sus datos y minimizar los costos.

- [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md)
- [Restauración de máquinas virtuales](backup-azure-restore-vms.md)
- [Guía de solución de problemas](backup-azure-vms-troubleshoot.md)


## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0420_2016-->