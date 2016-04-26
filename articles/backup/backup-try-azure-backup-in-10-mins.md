<properties
   pageTitle="Aprenda a realizar copias de seguridad de archivos y carpetas de Windows en Azure | Microsoft Azure"
   description="Aprenda a realizar una copia de seguridad de los datos de Windows Server mediante la creación de un almacén, la instalación del Agente de Copia de seguridad y la realización de una copia de seguridad de archivos y carpetas en Azure"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="cómo realizar la copia de seguridad; cómo realizar la copia de seguridad"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="04/14/2016"
   ms.author="jimpark;"/>

# Primer análisis: copia de seguridad de archivos y carpetas del servidor o cliente de Windows Server en Azure

En este artículo se explica cómo realizar una copia de seguridad de los archivos y carpetas de Windows Server (o del cliente de Windows) en Azure. Es un tutorial diseñado para guiarle por los aspectos básicos. Si desea empezar a usar Copia de seguridad de Azure, está en el lugar correcto.

Si desea más información acerca de Copia de seguridad de Azure, lea esta [introducción](backup-introduction-to-azure-backup.md).

La copia de seguridad de archivos y carpetas en Azure requiere estas actividades:

![Paso 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Conseguir una suscripción de Azure, si no tiene ya una.<br> ![Paso 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Crear un almacén de copia de seguridad y descargar los componentes necesarios.<br> ![Paso 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Instalar y registrar el agente de Copia de seguridad.<br> ![Paso 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Realizar copias de seguridad de los archivos y las carpetas.


![Cómo realizar copias de la máquina de Windows con Copia de seguridad de Azure](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Paso 1: obtener una suscripción de Azure

Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) que le permita acceder a cualquier servicio de Azure.

## Paso 2: Crear un almacén de copia de seguridad y descargar los componentes necesarios

Para hacer una copia de seguridad de los archivos y las carpetas, debe crear un almacén de copia de seguridad en la región donde desea almacenar los datos. Determine también cómo se replica el almacenamiento y descargue las credenciales y el agente de Copia de seguridad.

### Para crear un almacén de copia de seguridad

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/) mediante su suscripción.

2. Haga clic en **Nuevo > Integración híbrida > Copia de seguridad**.

    ![Preparación inicial hacer copia de seguridad de archivos y carpetas](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén de copia de seguridad.

4. Para **Región**, seleccione la región más cercana a su ubicación para acelerar las transferencias de archivos.

5. Haga clic en **CREAR ALMACÉN**.

    ![Creación de un almacén](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Una vez preparado el almacén de copia de seguridad, se muestra en la lista de recursos de Servicios de recuperación como **Activo**.

    ![El estado del almacén está activo](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

Después de crear el almacén, seleccione cómo se replica el almacenamiento.

>[AZURE.NOTE] Debe elegir cómo se replica almacenamiento justo después de crear un almacén y antes de registrar ninguna máquinas en él. Una vez registrado un elemento en el almacén, la replicación de almacenamiento se bloquea y no se puede modificar.

### Para seleccionar cómo se replica el almacenamiento

1. Haga clic en el almacén que creó.
2. En la página de Inicio rápido, seleccione **Configurar**.

    ![Configuración del almacén](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. Elija la opción de almacenamiento adecuada.

    Si está usando Azure como copia de seguridad principal, elija [almacenamiento con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage). Si está usando Azure como copia de seguridad terciaria, elija [almacenamiento con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage).

    ![Elección de la opción de replicación de almacenamiento](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. Si ha seleccionado **Almacenamiento con redundancia local**, haga clic en **Guardar**, ya que la opción predeterminada es **Almacenamiento con redundancia geográfica**.

Use las credenciales del almacén para autenticar su máquina con el almacén de copia de seguridad. Aquí le mostramos cómo descargar las credenciales.

### Para descargar las credenciales del almacén
El archivo de credenciales del almacén se usa solo durante el proceso de registro y expira después de 48 horas.

1. Para volver a la página **Inicio rápido** del almacén, haga clic en ![Selección del nuevo almacén](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png)

2. Haga clic en **Descargar las credenciales del almacén > Guardar**.

Después, debe descargar el agente de Copia de seguridad.

### Para descargar el agente de Copia de seguridad

Haga clic en **Agente para Windows Server, System Center Data Protection Manager o cliente de Windows > Guardar**.

![Guardado del agente de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/agent.png)

Ahora que el almacén está creado y que ha descargado todo, instale y registre el agente de Copia de seguridad.

## Paso 3: Instalar y registrar el agente de Copia de seguridad

1. Haga doble clic en el archivo **MARSagentinstaller.exe** desde la ubicación guardada.
2. Complete el asistente para la instalación del agente de Servicios de recuperación de Microsoft Azure. Para completar al asistente, tendrá que hacer lo siguiente:
    - Elija una ubicación para la instalación y la carpeta de caché.
    - Proporcione la información del servidor proxy si usa un servidor proxy para conectarse a Internet.
    - Si usa un servidor proxy autenticado, escriba los detalles de nombre y contraseña del usuario.
    - Guarde la frase de contraseña en una ubicación segura.

    >[AZURE.NOTE] Si pierde u olvida la frase de contraseña, Microsoft no puede ayudarle a recuperar los datos de copia de seguridad. Guarde el archivo en una ubicación segura. Es necesario restaurar una copia de seguridad.

Ahora está instalado el agente y el equipo está registrado en el almacén. Está listo para configurar y programar la copia de seguridad.

## Paso 4: Realizar copias de seguridad de los archivos y las carpetas
Si el agente de Copia de seguridad no está abierto, puede encontrarlo si busca en su equipo Copia de seguridad de Microsoft Azure.

1. En el **Agente de Copia de seguridad**, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Complete el Asistente para programar copias de seguridad. Para completar al asistente:

    - Seleccione los archivos y las carpetas de los que quiere realizar la copia de seguridad.
    - Especifique la programación de copia de seguridad (diaria o semanal).
    - Determine la directiva de retención.
    - Elija cómo desea completar la copia de seguridad inicial (a través de la red o sin conexión).

    Obtenga más información sobre cómo [completar la copia de seguridad inicial sin conexión](backup-azure-backup-import-export.md). <br><br>

3. Cuando el asistente termine, vuelva al **agente de Copia de seguridad** y haga clic en **Realizar copia de seguridad ahora** para completar la copia de seguridad inicial a través de la red.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. En la pantalla **Confirmación**, haga clic en **Copia de seguridad**. Si cierra el asistente antes de que se complete el proceso de copia de seguridad, se seguirá ejecutando en segundo plano.

    Una vez realizada la copia de seguridad inicial, la vista **Trabajos** en la consola muestra que el trabajo se ha completado.

    ![Copia de seguridad inicial completa](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Enhorabuena, ha realizado correctamente la copia de seguridad de los archivos y carpetas mediante Copia de seguridad de Azure.

## Pasos siguientes
- Obtenga más información acerca de cómo [realizar copias de seguridad de máquinas Windows](backup-configure-vault.md).
- Ahora que ha realizado una copia de seguridad de los archivos y las carpetas, puede [administrar los almacenes y servidores](backup-azure-manage-windows-server.md).
- Si necesita restaurar una copia de seguridad, use este artículo para [restaurar archivos en una máquina Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0420_2016-->