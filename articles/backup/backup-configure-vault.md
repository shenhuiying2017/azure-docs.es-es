<properties
	pageTitle="Copia de seguridad de un equipo de Windows Server o de un cliente de Windows en Azure | Microsoft Azure"
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
	ms.date="03/19/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# Realización de copias de seguridad de archivos y carpetas de Windows Server o del Cliente de Windows en Azure
En este artículo se tratan los procedimientos necesarios para preparar el entorno y hacer una copia de seguridad de Windows Server (o del cliente de Windows) en Azure. También se describen los aspectos que se deben tener en cuenta al implementar la solución de copia de seguridad. Si está interesado en probar el servicio Copia de seguridad de Azure por primera vez, [este artículo](backup-configure-vault.md) le guiará rápidamente en ese proceso.

![Crear almacén](./media/backup-configure-vault/initial-backup-process.png)

## Antes de comenzar
Para realizar una copia de seguridad de Windows Server (o del cliente de Windows) en Azure, necesita una cuenta de Azure. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en tan solo unos minutos.

## Paso 1: Creación de un almacén de copia de seguridad
Para hacer una copia de seguridad de los archivos y las carpetas de un equipo de Windows cliente o servidor, debe crear un almacén de copia de seguridad en la región geográfica donde desea almacenar los datos.

### Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).

2. Haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** y elija **Creación rápida**.

3. Para el parámetro **Nombre**, escriba un nombre descriptivo para identificar el almacén de copia de seguridad. Escriba un nombre que tenga entre 2 y 50 caracteres. El campo debe comenzar por una letra y solo puede contener letras, números y guiones. Esto debe ser único para cada suscripción.

    Para el parámetro **Región**, seleccione la región geográfica para el almacén de credenciales de copia de seguridad. La elección determina la región geográfica a la que se envían los datos de copia de seguridad. Si elige una región geográfica cerca de su ubicación, puede reducir la latencia de red al hacer una copia de seguridad en Azure.

    Haga clic en **Crear almacén**.

    ![Crear almacén](./media/backup-configure-vault/demo-vault-name.png)

    La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal.

    Después de crear el almacén de copia de seguridad, verá un mensaje que indica que el almacén se ha creado correctamente. El almacén aparece también en los recursos de Servicios de recuperación como**Activo**.

    ![Estado de creación de almacén](./media/backup-configure-vault/recovery-services-select-vault.png)

4. Seleccione las opciones de **redundancia de almacenamiento**.

    >[AZURE.IMPORTANT] El mejor momento para identificar la opción de redundancia de almacenamiento es justo después de la creación del almacén y antes de que las máquinas se registren en este. Una vez que un elemento se ha registrado en el almacén, la opción de redundancia de almacenamiento está bloqueada y no se puede modificar.

    Si usa Azure como punto de conexión principal de almacenamiento de copia de seguridad (por ejemplo, va a hacer una copia de seguridad en Azure desde un equipo de Windows Server), debería considerar la selección de la opción de [almacenamiento con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) (valor predeterminado).

    Si usa Azure como punto de conexión terciario de almacenamiento de copia de seguridad (por ejemplo, si usa SCDPM para tener una copia de seguridad local y Azure para cubrir sus necesidades de retención a largo plazo), debería considerar la posibilidad de elegir el [almacenamiento con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage). Esto reduce el costo de almacenamiento de datos en Azure, a la vez que ofrece un menor nivel de durabilidad de los datos, el cual podría ser aceptable para copias terciarias.

    **Para seleccionar la opción de redundancia de almacenamiento:**

    a. Haga clic en el almacén que acaba de crear.

    b. En la página **Inicio rápido**, seleccione **Configurar**.

    ![Configurar el estado de almacén](./media/backup-configure-vault/configure-vault.png)

    c. Elija la opción de redundancia de almacenamiento adecuada.

    Si ha seleccionado **Almacenamiento con redundancia local**, haga clic en **Guardar**, ya que la opción predeterminada es **Almacenamiento con redundancia geográfica**.

    d. Haga clic en **Servicios de recuperación**, en el panel de navegación izquierdo, para volver a la lista de recursos de **Servicios de recuperación**.

## Paso 2: Descarga del archivo de credenciales de almacén
El equipo local (Windows Server o el cliente de Windows) se debe autenticar con un almacén de copia de seguridad antes de poder hacer copia de seguridad de los datos en Azure. La autenticación se realiza mediante las *credenciales de almacén*. El archivo de credenciales de almacén se descarga a través de un canal seguro desde el Portal de Azure. El servicio Copia de seguridad de Azure no conoce la clave privada del certificado, dicha clave no se conserva en el portal ni en el servicio.

Más información sobre el [uso de credenciales de almacén para autenticarse con el servicio Copia de seguridad de Azure](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### Para descargar el archivo de credenciales de almacén en una máquina local:

1. Haga clic en **Servicios de recuperación** en el panel de navegación izquierdo y seleccione el almacén de copia de seguridad que ha creado.

    ![IR completado](./media/backup-configure-vault/rs-left-nav.png)

2.  En la página **Inicio rápido**, haga clic en **Descargar credenciales de almacén**.

    El portal generará una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro y expira tras 48 horas.

    El archivo de credenciales de almacén puede descargarse desde el portal.

3. Haga clic en **Guardar** para descargar las credenciales del almacén en la carpeta de descargas de la cuenta local, o elija **Guardar como** en el menú **Guardar** para especificar una ubicación para las credenciales de almacén.

    >[AZURE.NOTE] Asegúrese de que las credenciales de almacén se guardan en una ubicación a la que se pueda acceder desde la máquina. Si se almacenan en un recurso compartido/SMB de archivo, compruebe los permisos de acceso.

## Paso 3: Descarga, instalación y registro del agente de Copia de seguridad de Azure
Después de crear el almacén de Copia de seguridad de Azure y descargar credenciales de almacén, debe instalarse un agente en cada uno de los equipos de Windows (Windows Server o cliente de Windows).

### Para descargar, instalar y registrar el agente:

1. Haga clic en **Servicios de recuperación** y seleccione el almacén de copia de seguridad que desee registrar en un servidor.

2. En la página Inicio rápido, haga clic en **Agente para Windows Server, System Center Data Protection Manager o cliente de Windows > Guardar**.

    ![Guardar agente](./media/backup-configure-vault/agent.png)

3. Cuando finalice la descarga de *MARSagentinstaller.exe*, haga clic en **Ejecutar** (o haga doble clic en **MARSAgentInstaller.exe** en la ubicación guardada).

4. 	Elija la *carpeta de instalación* y la *carpeta de caché* requerida para el agente y haga clic en **Siguiente**.

    La ubicación de caché especificada debe tener un espacio libre de al menos el 5 % de los datos de copia de seguridad.

5. Puede seguir conectándose a Internet mediante la configuración predeterminada del proxy o, si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy**, active la casilla **Utilice una configuración de proxy personalizada** y especifique los detalles del servidor proxy.

    Si utiliza un servidor proxy autenticado, escriba los detalles de nombre y contraseña del usuario.

    Haga clic en **Siguiente**.

6. Haga clic en **Instalar** para comenzar la instalación del agente.

    El agente de Copia de seguridad de Azure instala .NET Framework 4.5 y Windows PowerShell (si aún no está instalado) para completar la instalación.

7. Cuando el agente esté instalado, haga clic en **Proceder al registro** para seguir con el flujo de trabajo.

8. En la pantalla **Identificación del almacén**, busque y seleccione el *archivo de credenciales del almacén* que descargó anteriormente.

    El archivo de almacén de credenciales solo es válido durante 48 horas (después de descargarlo desde el portal). Si encuentra algún error en esta pantalla (por ejemplo "El archivo de credenciales de almacén especificado expiró"), inicie sesión en el Portal de Azure y vuelva a descargar el archivo de credenciales de almacén.

    Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de almacén de credenciales en una ubicación temporal en esta máquina y vuelva a intentar la operación.

    Si se produce un error de credenciales de almacén no válidas (por ejemplo, "Las credenciales del almacén no son válidas"), el archivo está dañado o no tiene asociadas las credenciales más recientes con el servicio de recuperación. Vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error suele aparecer si el usuario hace clic en la opción *Descargar credenciales de almacén* en sucesión rápida. En este caso, solo es válido el último archivo de credenciales de almacén.

9. En la pantalla **Configuración de cifrado**, puede *generar* una frase de contraseña o *proporcionarla* (mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.

    Haga clic en **Finalizar**

    > [AZURE.WARNING] Si la frase de contraseña se pierde u olvida, Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no puede ver la frase de contraseña que usa el usuario final. Guarde el archivo en una ubicación segura, ya que puede ser necesario durante una operación de recuperación.

    El **Asistente para registrar servidor** registra el servidor con el servicio Copia de seguridad de Microsoft Azure.

10. Cuando la **clave de cifrado** esté establecida, deje la casilla **Inicio del agente de Servicios de recuperación de Microsoft Azure** activada y haga clic en **Cerrar**.

## Paso 4: Realización de la copia de seguridad inicial

La copia de seguridad inicial se compone de dos tareas fundamentales: **creación de la programación de copia de seguridad** y **copia de seguridad de los archivos y las carpetas por primera vez**. Después de realizar la copia de seguridad inicial, según la programación que defina, la directiva de copia de seguridad creará puntos de copia de seguridad que puede usar si necesita recuperar los datos.

### Programación de la copia de seguridad

1. Abra **Microsoft Azure Backup Agent** (se abrirá automáticamente si dejó la casilla **Inicio del agente de Servicios de recuperación de Microsoft Azure** activada cuando cerró el **Asistente para registrar servidor**, o puede encontrarlo si busca en su equipo *Copia de seguridad de Microsoft Azure*).

    ![Programar una copia de seguridad de Windows Server](./media/backup-configure-vault/snap-in-search.png)

2. En **Microsoft Azure Backup Agent** haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-configure-vault/schedule-backup-close.png)

3. En la pantalla de introducción del **Asistente para programar copias de seguridad**, haga clic en **Siguiente**.

4. En la pantalla **Seleccionar elementos de los que realizar copia de seguridad**, haga clic en **Agregar elementos**.

5. Seleccione los archivos y las carpetas de los que quiere realizar la copia de seguridad y haga clic en **Correcto**.

6. Haga clic en **Siguiente**.

7. En la pantalla **Especificar una programación de copias de seguridad**, especifique la **programación de copia de seguridad** y haga clic en **Siguiente**.

    Puede programar copias de seguridad diarias (un máximo de 3 veces al día) o semanales.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] La especificación de la programación de copia de seguridad se explica con todo detalle en este [artículo](backup-azure-backup-cloud-as-tape.md).

8. En la pantalla **Seleccionar la directiva de retención**, seleccione la **directiva de retención** de la copia de seguridad.

    La directiva de retención especifica el tiempo durante el que se almacenará la copia de seguridad. En vez de especificar solo una directiva para todos los puntos de copia de seguridad, puede especificar directivas de retención diferentes en función de cuándo se realice la copia de seguridad. Puede modificar las directivas de retención diarias, semanales, mensuales y anuales según sus necesidades.

     Haga clic en **Siguiente**.

9. En la pantalla **Elija el tipo de copia de seguridad inicial**, elija el tipo de la copia de seguridad inicial**.

    Puede hacer una copia de seguridad automáticamente en la red, o puede realizar una copia sin conexión. En el resto de este artículo se sigue el proceso para realizar una copia de seguridad automáticamente. Si prefiere hacer una copia de seguridad sin conexión, consulte este artículo, donde encontrará información adicional sobre el [flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md).

    Deje la opción **Automáticamente a través de la red** seleccionada y haga clic en **Siguiente**.

10. En la pantalla **Confirmación**, revise la información y haga clic en **Finalizar**.

11. Cuando el asistente finalice la creación de la **programación de copia de seguridad**, haga clic en **Cerrar**.

### Habilitación de la velocidad moderada de la red (opcional)

Azure Backup Agent proporciona velocidad moderada de la red. Esta limitación controla cómo se utiliza el ancho de banda de red durante la transferencia de datos. Este control puede resultar de ayuda si necesita realizar una copia de seguridad de los datos durante las horas de trabajo, pero no quiere que el proceso interfiera con otro tráfico de Internet. La limitación de la transferencia de datos se aplica a las actividades de copia de seguridad y restauración.

1. En el **agente de copia de seguridad**, haga clic en **Cambiar propiedades**.

    ![Cambiar propiedades](./media/backup-configure-vault/change-properties.png)

2. En la pestaña **Limitación**, active la casilla **Habilitar el límite de uso del ancho de banda de Internet para operaciones de copia de seguridad**.

    ![Limitación de la red](./media/backup-configure-vault/throttling-dialog.png)

3. Una vez que se ha habilitado la limitación, especifique el ancho de banda permitido para la transferencia de datos de copia de seguridad durante las **horas laborables** y las **horas no laborables**.

    Los valores de ancho de banda comienzan en 512 kilobytes por segundo (Kbps) y pueden subir hasta 1023 megabytes por segundo (Mbps). También puede designar el inicio y fin de las **horas laborables**, y qué días de la semana se consideran días laborables. El tiempo fuera de las horas laborables designadas se considera horas no laborables.

4. Haga clic en **Aceptar**.

### Copia de seguridad ahora

1. En el **agente de copia de seguridad**, haga clic en **Copia de seguridad ahora** para completar la propagación inicial a través de la red.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-configure-vault/backup-now.png)

2. En la pantalla **Confirmación**, revise la configuración que utilizará el asistente para realizar la copia de seguridad de la máquina y haga clic en **Copia de seguridad **.

3. Haga clic en **Cerrar** para cerrar el asistente. Esto puede hacerlo antes de que se complete el **proceso de copia de seguridad** y se seguirá ejecutando en segundo plano.

Después de que finaliza la copia de seguridad inicial, el estado *Trabajo completado* se refleja en la **consola de Copia de seguridad de Azure**.

![IR completado](./media/backup-configure-vault/ircomplete.png)

## Pasos siguientes
- Regístrese para obtener una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).

Para más información sobre la copia de seguridad de máquinas virtuales u otras cargas de trabajo, consulte:

- [Preparación del entorno de copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-prepare.md)
- [Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure](backup-azure-microsoft-azure-backup.md)
- [Preparación para la copia de seguridad de cargas de trabajo en Azure con DPM](backup-azure-dpm-introduction.md).

<!---HONumber=AcomDC_0323_2016-->