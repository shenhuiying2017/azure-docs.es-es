<properties
	pageTitle="Copia de seguridad desde Windows Server o un cliente de Windows en Azure con Copia de seguridad de Azure mediante el modelo de implementación de Resource Manager | Microsoft Azure"
	description="Cree una copia de seguridad de los servidores o clientes de Windows en Azure mediante la creación de un almacén de copia de seguridad, la descarga de credenciales, la instalación del agente de copia de seguridad y la realización de una copia de seguridad inicial de sus archivos y carpetas."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="almacén de copia de seguridad; copia de seguridad de un equipo de Windows Server; ventanas de copia de seguridad;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="jimpark; trinadhk; markgal"/>

# Copia de seguridad desde Windows Server o un cliente de Windows en Azure con Copia de seguridad de Azure mediante el modelo de implementación de Resource Manager

> [AZURE.SELECTOR]
- [Portal de Azure](backup-configure-vault.md)
- [Portal clásico](backup-configure-vault-classic.md)

En este artículo se explica cómo realizar una copia de seguridad de los archivos y carpetas de Windows Server (o del cliente de Windows) en Azure con Copia de seguridad de Azure usando el modelo de implementación de Resource Manager.

![Pasos del proceso de copia de seguridad](./media/backup-configure-vault/initial-backup-process.png)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.

## Antes de comenzar
Si desea crear una copia de seguridad de un servidor o cliente en Azure, necesita una cuenta de Azure. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en tan solo unos minutos.

## Paso 1: Creación de un almacén de Servicios de recuperación

Un almacén de Servicios de recuperación es una entidad que almacena todas las copias de seguridad y todos los puntos de recuperación creados con el tiempo. El almacén de Servicios de recuperación contiene también la directiva de copia de seguridad que se aplica a los archivos y las carpetas protegidos. Cuando se crea un almacén de Servicios de recuperación, también se debe seleccionar la opción de redundancia de almacenamiento adecuada.

### Creación de un almacén de Servicios de recuperación

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/) mediante su suscripción.

2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Servicios de recuperación**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Servicios de recuperación**.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-configure-vault/browse-to-rs-vaults.png) <br/>

    Se muestra la lista de almacenes de Servicios de recuperación.

3. En el menú **Almacenes de Servicios de recuperación**, haga clic en **Agregar**.

    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-configure-vault/rs-vault-menu.png)

    Se abre la hoja del almacén de Servicios de recuperación, que le pide que proporcione los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-configure-vault/rs-vault-attributes.png)

4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.

6. Haga clic en **Grupo de recursos** para ver la lista disponible de grupos de recursos o en **Nuevo** para crear uno. Para una información completa sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../resource-group-overview.md)

7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. Esta elección determina la región geográfica a la que se envían los datos de copia de seguridad. Si elige una región geográfica cercana a su ubicación, puede reducir la latencia de red al crear una copia de seguridad en Azure.

8. Haga clic en **Crear**. La creación del almacén de Servicios de recuperación puede tardar unos minutos. Supervise las notificaciones de estado en la parte superior derecha del portal. Una vez creado el almacén, se debería abrir en el portal. Si cuando ha terminado no ve el almacén, haga clic en **Actualizar**. Cuando se actualiza la lista, haga clic en el nombre del almacén.

### Determinación de la redundancia de almacenamiento
Al crear un almacén de Servicios de recuperación se determina cómo se replica el almacenamiento.

1. En la hoja **Configuración**, que se abre automáticamente con el panel del almacén, haga clic en **Backup Infrastructure** (Infraestructura de copia de seguridad).

2. En la hoja Backup Infrastructure (Infraestructura de copia de seguridad), haga clic en **Configuración de copia de seguridad** para ver el **tipo de replicación de almacenamiento**.

    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-configure-vault/backup-infrastructure.png)

3. Elija la opción de replicación del almacenamiento para su almacén.

    ![Lista de almacenes de Servicios de recuperación](./media/backup-configure-vault/choose-storage-configuration.png)

    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, siga utilizando el almacenamiento con redundancia geográfica. Si usa Azure como punto de conexión del almacenamiento de copia de seguridad no principal, elija un almacenamiento con redundancia local, ya que ello reducirá el costo de almacenamiento de datos en Azure. En esta página de [información general](../storage/storage-redundancy.md), encontrará más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y con [redundancia local](../storage/storage-redundancy.md#locally-redundant-storage).

    Tras elegir la opción de almacenamiento del almacén, está listo para asociar los archivos y las carpetas con el almacén.

Ahora que ha creado un almacén, prepare su infraestructura para realizar una copia de seguridad de los archivos y las carpetas; para ello, descargue e instale el Agente de Servicios de recuperación de Microsoft Azure, descargue las credenciales del almacén y luego use esas credenciales para registrar el agente en el almacén.

## Paso 2: Descarga de los archivos

>[AZURE.NOTE] La habilitación de la copia de seguridad a través del portal de Azure estará pronto disponible. En este momento, se usa el agente de Servicios de recuperación de Microsoft Azure local para hacer copia de seguridad de archivos y carpetas.

1. Haga clic en **Configuración** en el panel del almacén de Servicios de recuperación.

    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-configure-vault/settings-button.png)

2. Haga clic en **Introducción > Copia de seguridad** en la hoja Configuración.

    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-configure-vault/getting-started-backup.png)

3. Haga clic en **Backup goal** (Objetivo de la copia de seguridad) en la hoja Copia de seguridad.

    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-configure-vault/backup-goal.png)

4. Seleccione **Local** desde el menú Where is your workload running? (¿Desde dónde se ejecuta la carga de trabajo?).

5. Seleccione **Archivos y carpetas** en el menú What do you want to backup? (¿De qué desea realizar copias de seguridad?) y haga clic en **Aceptar**.

#### Descarga del agente de Servicios de recuperación

1. Haga clic en **Download Agent for Windows Server or Windows Client** (Descargar el agente para Windows Server o cliente de Windows) en la hoja **Prepare infrastructure** (Preparar la infraestructura).

    ![Preparar infraestructura](./media/backup-configure-vault/prepare-infrastructure-short.png)

2. Haga clic en **Guardar** en la ventana emergente de descarga. De forma predeterminada, se guarda el archivo **MARSagentinstaller.exe** en la carpeta de descargas.

#### Descarga de las credenciales de almacén

1. Haga clic en **Descargar > Guardar** en la hoja Prepare infrastructure(Preparar la infraestructura).

    ![Preparar infraestructura](./media/backup-configure-vault/prepare-infrastructure-download.png)

## Paso 3: Instalación y registro del agente

1. Busque y haga doble clic en **MARSagentinstaller.exe** en la carpeta de descargas (u otra ubicación guardada).

2. Complete el asistente para la instalación del agente de Servicios de recuperación de Microsoft Azure. Para completar al asistente, tendrá que hacer lo siguiente:

    - Elija una ubicación para la instalación y la carpeta de caché.
    - Proporcione la información del servidor proxy si usa un servidor proxy para conectarse a Internet.
    - Si usa un servidor proxy autenticado, escriba los detalles de nombre y contraseña del usuario.
    - Proporcione las credenciales del almacén descargado
    - Guarde la frase de contraseña en una ubicación segura.

    >[AZURE.NOTE] Si pierde u olvida la frase de contraseña, Microsoft no puede ayudarle a recuperar los datos de copia de seguridad. Guarde el archivo en una ubicación segura. Es necesario restaurar una copia de seguridad.

Ahora está instalado el agente y el equipo está registrado en el almacén. Está listo para configurar y programar la copia de seguridad.

## Paso 4: Realización de la copia de seguridad inicial

La copia de seguridad inicial incluye dos tareas clave:

- Programación de la copia de seguridad
- Creación de copias de seguridad de archivos y carpetas por primera vez

Para realizar la copia de seguridad inicial, use el agente de Copia de seguridad de Microsoft Azure.

### Para programar la copia de seguridad

1. Abra el agente de Copia de seguridad de Microsoft Azure. Para encontrarlo, busque **Copia de seguridad de Microsoft Azure** en la máquina.

    ![Lanzamiento del agente de Copia de seguridad de Azure](./media/backup-configure-vault/snap-in-search.png)

2. En el agente de copia de seguridad, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. En la página de introducción del Asistente para programar copias de seguridad, haga clic en **Siguiente**.

4. En la página Seleccionar elementos de los que realizar copia de seguridad, haga clic en **Agregar elementos**.

5. Seleccione los archivos y las carpetas de los que desea crear la copia de seguridad y, luego, haga clic en **Aceptar**.

6. Haga clic en **Siguiente**.

7. En la página **Especifique la programación de copia de seguridad**, indique la **programación de copias de seguridad** y haga clic en **Siguiente**.

    Puede programar copias de seguridad diarias (con una frecuencia máxima de tres veces al día) o semanales.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Para más información sobre cómo especificar la programación de la copia de seguridad, consulte el artículo [Usar la copia de seguridad de Azure para cambiar su infraestructura de cintas](backup-azure-backup-cloud-as-tape.md).

8. En la página **Seleccione la directiva de retención**, elija la **directiva de retención** para la copia de seguridad.

    La directiva de retención especifica el tiempo durante el que se almacenará la copia de seguridad. En vez de especificar solo una directiva para todos los puntos de copia de seguridad, puede especificar directivas de retención distintas en función de cuándo se realice la copia de seguridad. Puede modificar las directivas de retención diarias, semanales, mensuales y anuales según sus necesidades.

9. En la página Elija el tipo de copia de seguridad inicial, elija el tipo de copia de seguridad inicial. Deje activada la opción **Automáticamente por encima de la red** y, luego, haga clic en **Siguiente**.

    Puede hacer una copia de seguridad automáticamente en la red, o puede realizar una copia sin conexión. En el resto de este artículo, se describe el proceso para crear automáticamente una copia de seguridad. Si prefiere crear una copia de seguridad sin conexión, consulte el artículo [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md) para más información.

10. En la página Confirmación, revise la información y, luego, haga clic en **Finalizar**.

11. Cuando el asistente termine de crear la programación de copia de seguridad, haga clic en **Cerrar**.

### Habilitación de la velocidad moderada de la red (opcional)

El agente de copia de seguridad ofrece limitación de la red. Esta limitación controla cómo se utiliza el ancho de banda de red durante la transferencia de datos. Este control puede resultar útil si necesita realizar una copia de seguridad durante las horas de trabajo, pero no quiere que el proceso interfiera con otro tráfico de Internet. La limitación se aplica a las actividades de copia de seguridad y restauración.

>[AZURE.NOTE] La limitación de la red no está disponible en Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ni en Windows 7 (con Service Packs). La característica de limitación de la red de copia de seguridad de Azure participa en la Calidad del servicio (QoS) del sistema operativo local. Aunque la copia de seguridad de Azure puede proteger estos sistemas operativos, la versión de QoS disponible en estas plataformas no funciona con la limitación de la red de copia de seguridad de Azure. La limitación de la red puede usarse en todos los demás [sistemas operativos admitidos](backup-azure-backup-faq.md#installation-amp-configuration).

**Para habilitar la limitación de red**

1. En el agente de Copia de seguridad, haga clic en **Cambiar propiedades**.

    ![Cambiar propiedades](./media/backup-configure-vault/change-properties.png)

2. En la pestaña **Limitación**, active la casilla **Habilitar el límite de uso del ancho de banda de Internet para operaciones de copia de seguridad**.

    ![Limitación de la red](./media/backup-configure-vault/throttling-dialog.png)

3. Una vez que se ha habilitado la limitación, especifique el ancho de banda permitido para la transferencia de datos de copia de seguridad durante la **jornada laboral** y las **horas de descanso**.

    Los valores de ancho de banda comienzan en 512 kilobytes por segundo (Kbps) y pueden subir hasta 1023 megabytes por segundo (Mbps). También puede designar el inicio y el final de la **jornada laboral**, así como qué días de la semana se consideran laborables. Las horas que se encuentran fuera de las horas laborables designadas se consideran como no laborables.

4. Haga clic en **Aceptar**.

### Creación de copias de seguridad de archivos y carpetas por primera vez

1. En el agente de Copia de seguridad, haga clic en **Iniciar copia de seguridad** para completar la propagación inicial a través de la red.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-configure-vault/backup-now.png)

2. En la página Confirmación, revise la configuración que el asistente para iniciar copia de seguridad usará para crear la copia de seguridad de la máquina. Luego, haga clic en **Crear copia de seguridad**.

3. Haga clic en **Cerrar** para cerrar el asistente. Si lo hace antes de que finalice el proceso de copia de seguridad, el asistente se sigue ejecutando en segundo plano.

Una vez que finalice la copia de seguridad inicial, el estado **Trabajo completado** se refleja en la consola de Copia de seguridad.

![IR completado](./media/backup-configure-vault/ircomplete.png)

## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## Pasos siguientes
Para más información sobre la copia de seguridad de máquinas virtuales u otras cargas de trabajo, consulte:

- Ahora que ha realizado una copia de seguridad de los archivos y las carpetas, puede [administrar los almacenes y servidores](backup-azure-manage-windows-server.md).
- Si necesita restaurar una copia de seguridad, use este artículo: [Restaurar archivos en una máquina de Windows Server o del Cliente de Windows](backup-azure-restore-windows-server.md).

<!----HONumber=AcomDC_0720_2016-->
