---
title: Copias de seguridad del estado del sistema de Windows en Azure | Microsoft Docs
description: Aprenda a hacer copias de seguridad del estado del sistema de Windows Server y/o equipos Windows en Azure.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: "cómo realizar copias de seguridad; cómo realizar una copia de seguridad; copia de seguridad de archivos y carpetas"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Copias de seguridad del estado del sistema de Windows en la implementación de Resource Manager
En este artículo se explica cómo realizar copias de seguridad del estado del sistema de Windows Server en Azure. Es un tutorial diseñado para guiarle por los aspectos básicos.

Si desea más información acerca de Copia de seguridad de Azure, lea esta [introducción](backup-introduction-to-azure-backup.md).

Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) que le permita acceder a todos los servicios de Azure.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
Para hacer una copia de seguridad de los archivos y las carpetas, tiene que crear un almacén de Servicios de recuperación en la región donde desea almacenar los datos. También debe determinar cómo desea que se replique el almacenamiento.

### <a name="to-create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/) mediante su suscripción.
2. En el menú central, haga clic en **Más servicios** y, en la lista de recursos, escriba **Recovery Services** y haga clic en **Almacenes de Recovery Services**.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Si hay almacenes de Recovery Services en la suscripción, estos aparecerán en una lista.
3. En el menú **Almacenes de servicios de recuperación**, haga clic en **Agregar**.

    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Se abre la hoja del almacén de Recovery Services, donde se le pide que especifique los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Creación del almacén de Recovery Services, paso 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

5. En la sección **Suscripción**, utilice el menú desplegable para elegir la suscripción de Azure. Si utiliza una sola suscripción, esta aparece y puede pasar al paso siguiente. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo hay varias opciones si la cuenta de su organización está asociada a más de una suscripción de Azure.

6. En la sección **Grupo de recursos**:

    * Si desea crear un nuevo grupo de recursos, seleccione **Crear nuevo**.
    O
    * Seleccione **Use existing** (Usar existente) y haga clic en el menú desplegable para ver la lista de grupos de recursos disponibles.

  Para más información sobre los grupos de recursos, consulte [Introducción a Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. Esta elección determina la región geográfica a la que se envían los datos de copia de seguridad.

8. En la parte inferior de la hoja de almacén de recovery Services, haga clic en **Create** (Crear).

    La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones de estado de la parte superior derecha del portal. Una vez creado el almacén, aparece en la lista de almacenes de servicios de recuperación. Si no ve el almacén pasados unos minutos, haga clic en **Refresh** (Actualizar).

    ![Clic en el botón Refresh (Actualizar)](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Cuando vea el almacén en la lista de almacenes de Recovery Services, estará listo para configurar la redundancia de almacenamiento.

### <a name="set-storage-redundancy-for-the-vault"></a>Establecimiento de la redundancia de almacenamiento para el almacén
Cuando cree un almacén de Recovery Services, asegúrese de que la configuración de la redundancia de almacenamiento sea la que quiere.

1. En la hoja **Almacenes de Recovery Services**, haga clic en el almacén nuevo.

    ![Selección del almacén nuevo en la lista de almacenes de Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Al seleccionar el almacén, la hoja **Almacén de Recovery Services** se delimita, y la hoja de configuración (*con el nombre del almacén en la parte superior*) y la hoja de detalles del almacén se abren.

    ![Vista de la configuración de almacenamiento del nuevo almacén](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. En la hoja de configuración del nuevo almacén, desplácese con el control deslizante vertical hasta la sección Manage (Administrar) y haga clic en **Backup Infrastructure** (Infraestructura de copia de seguridad).
    Con ello, se abrirá esta hoja.
3. En la hoja Infraestructura de copia de seguridad, haga clic en **Configuración de copia de seguridad** para abrir la hoja **Configuración de copia de seguridad**.

    ![Establecimiento de la configuración de almacenamiento del nuevo almacén](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Elija la opción de replicación del almacenamiento apropiada para su almacén.

    ![opciones de configuración de almacenamiento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si usa Azure como punto de conexión de almacenamiento de copia de seguridad principal, siga utilizando **Redundancia geográfica**. Si no utiliza Azure como punto de conexión de almacenamiento de copia de seguridad principal, elija **Redundancia local** para reducir los costes de almacenamiento de Azure. En esta página de [información general sobre la redundancia del almacenamiento](../storage/common/storage-redundancy.md) encontrará más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) y [redundancia local](../storage/common/storage-redundancy.md#locally-redundant-storage).

Ahora que ha creado un almacén, configúrelo para realizar copias de seguridad del estado del sistema de Windows.

## <a name="configure-the-vault"></a>Configuración del almacén
1. En la hoja del almacén de Recovery Services (el almacén que acaba de crear), en la sección de introducción, haga clic en **Copia de seguridad** y, a continuación, en la hoja **Introducción a la copia de seguridad**, seleccione **Objetivo de copia de seguridad**.

    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Se abrirá la hoja **Objetivo de copia de seguridad**.

    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. En el menú desplegable **¿Dónde se ejecuta su carga de trabajo?**, seleccione **Local**.

    Elija **Local** ya que su equipo Windows o Windows Server es una máquina física que no está en Azure.

3. En el menú **What do you want to backup?** (¿De qué quiere realizar una copia de seguridad?), seleccione **Estado del sistema** y haga clic en **Aceptar**.

    ![Configuración de archivos y carpetas](./media/backup-azure-system-state/backup-goal-system-state.png)

    Después de hacer clic en Aceptar, aparecerá una marca de verificación junto a **Objetivo de copia de seguridad**y se abrirá la hoja **Preparar infraestructura**.

    ![Objetivo de copia de seguridad configurado, a continuación, prepare la infraestructura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. En la hoja **Preparar infraestructura**, haga clic en **Descargar agente para Windows Server o cliente de Windows**.

    ![Prepare infrastructure](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Si usa Windows Server Essential, elija descargar el agente de Windows Server Essential. Un menú emergente le preguntará si desea ejecutar o guardar MARSAgentInstaller.exe.

    ![Cuadro de diálogo de MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Haga clic en **Guardar** en el menú emergente de descarga.

    De forma predeterminada, se guarda el archivo **MARSagentinstaller.exe** en la carpeta de descargas. Cuando haya finalizado el instalador, verá un menú emergente que le preguntará si desea ejecutar el instalador o abrir la carpeta.

    ![Prepare infrastructure](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    No es necesario instalar el agente todavía. Puede instalar el agente una vez descargadas las credenciales del almacén.

6. En la hoja **Preparar infraestructura**, haga clic en **Descargar**.

    ![descargar las credenciales de almacén](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Descargue las credenciales de almacén en la carpeta Descargas. Una vez que haya terminado de descargar las credenciales del almacén, aparecerá una ventana emergente en la que se le preguntará si desea abrirlas o guardarlas. Haga clic en **Guardar**. Si, accidentalmente, hace clic en **Abrir**, deje que el cuadro de diálogo intente abrir las credenciales de almacén. Se producirá un error. No se pueden abrir las credenciales de almacén. Siga con el paso siguiente. Las credenciales del almacén están en la carpeta de descargas.   

    ![finalizó la descarga de las credenciales de almacén](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Instalación y registro del agente

> [!NOTE]
> La habilitación de la copia de seguridad a través de Azure Portal todavía no está disponible. Use el agente de Microsoft Azure Recovery Services para hacer la copia de seguridad del estado del sistema de Windows Server.
>

1. Busque y haga doble clic en **MARSagentinstaller.exe** en la carpeta de descargas (u otra ubicación guardada).

    El instalador proporciona una serie de mensajes ya que este extrae, instala y registra el agente de Recovery Services.

    ![ejecutar las credenciales del instalador del agente de Recovery Services](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Complete el asistente para la instalación del agente de Servicios de recuperación de Microsoft Azure. Para completar al asistente, tendrá que hacer lo siguiente:

   * Elija una ubicación para la instalación y la carpeta de caché.
   * Proporcione la información del servidor proxy si usa un servidor proxy para conectarse a Internet.
   * Si usa un servidor proxy autenticado, escriba los detalles de nombre y contraseña del usuario.
   * Proporcione las credenciales del almacén descargado
   * Guarde la frase de contraseña en una ubicación segura.

     > [!NOTE]
     > Si pierde u olvida la frase de contraseña, Microsoft no puede ayudarle a recuperar los datos de copia de seguridad. Guarde el archivo en una ubicación segura. Es necesario restaurar una copia de seguridad.
     >
     >

Ahora está instalado el agente y el equipo está registrado en el almacén. Está listo para configurar y programar la copia de seguridad.

## <a name="back-up-windows-server-system-state-preview"></a>Copia de seguridad del estado del sistema de Windows Server (versión preliminar)
La copia de seguridad inicial incluye tres tareas:

* Habilitación de la copia de seguridad del estado del sistema mediante el agente de Azure Backup
* Programación de la copia de seguridad
* Creación de copias de seguridad de archivos y carpetas por primera vez

Para realizar la copia de seguridad inicial use el agente de Microsoft Azure Recovery Services.

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>Para habilitar la copia de seguridad del estado del sistema mediante el agente de Azure Backup

1. En una sesión de PowerShell, ejecute el siguiente comando para detener el motor de Azure Backup.

  ```
  PS C:\> Net stop obengine
  ```

2. Abra el Registro de Windows.

  ```
  PS C:\> regedit.exe
  ```

3. Agregue la siguiente clave del Registro con el valor DWord especificado.

  | Ruta de acceso del Registro | Clave del Registro | Valor DWord |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. Reinicie el motor de Backup ejecutando el siguiente comando en un símbolo del sistema con privilegios elevados.

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>Programación de la copia de seguridad

1. Abra el agente de Servicios de recuperación de Microsoft Azure. Para encontrarlo, busque **Copia de seguridad de Microsoft Azure**en la máquina.

    ![Iniciar el agente de los Servicios de recuperación de Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. En el agente de Servicios de recuperación, haga clic en **Programar copia de seguridad**.

    ![Programación de una copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. En la página de introducción del Asistente para programar copias de seguridad, haga clic en **Siguiente**.

4. En la página Seleccionar elementos de los que realizar copia de seguridad, haga clic en **Agregar elementos**.

5. Seleccione **Estado del sistema** y haga clic en **Aceptar**.

6. Haga clic en **Siguiente**.

7. La programación de retención y copia de seguridad del estado del sistema se establece automáticamente para hacer la copia de seguridad todos los domingos a las 9:00 p. m. (hora local) y el período de retención se establece en 60 días.

   > [!NOTE]
   > La directiva de retención y copia de seguridad del estado del sistema se configura automáticamente. Si la copia de seguridad también incluye archivos y carpetas, además del estado del sistema de Windows Server, especifique solamente la directiva de copia de seguridad y retención para copias de seguridad de archivos desde el asistente. 
   >

8. En la página Confirmación, revise la información y, luego, haga clic en **Finalizar**.

9. Cuando el asistente termine de crear la programación de copia de seguridad, haga clic en **Cerrar**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para hacer una copia de seguridad del estado del sistema de Windows Server por primera vez

1. Asegúrese de que no hay actualizaciones pendientes para Windows Server que requieran un reinicio.

2. En el agente de Servicios de recuperación, haga clic en **Back Up Now** (Iniciar copia de seguridad) para completar la propagación inicial a través de la red.

    ![Copia de seguridad de Windows Server ahora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. En la página Confirmación, revise la configuración que el asistente para iniciar copia de seguridad usará para crear la copia de seguridad de la máquina. Luego, haga clic en **Crear copia de seguridad**.

4. Haga clic en **Cerrar** para cerrar el asistente. Si lo hace antes de que finalice la copia de seguridad, el asistente se sigue ejecutando en segundo plano.

5. Si realiza la copia de seguridad de archivos y carpetas en el servidor, además del estado del sistema de Windows Server, el asistente para copias de seguridad solo se centrará en los archivos. Para llevar a cabo una copia de seguridad del estado del sistema ad hoc, use el siguiente comando de PowerShell:

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  Una vez que finalice la copia de seguridad inicial, el estado **Trabajo completado** se refleja en la consola de Copia de seguridad.

  ![IR completado](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

Las siguientes preguntas y respuestas proporcionan información complementaria.

### <a name="what-is-the-staging-volume"></a>¿Qué es el volumen de almacenamiento provisional?

El volumen de almacenamiento provisional representa la ubicación intermedia donde Copias de seguridad de Windows Server, disponible de forma nativa, almacena temporalmente la copia de seguridad del estado del sistema. Después, el agente de Azure Backup comprime y cifra esta copia de seguridad intermedia y la envía a través del protocolo HTTPS seguro al almacén de Recovery Services configurado. **Se recomienda encarecidamente establecer el volumen de almacenamiento provisional en un volumen con un SO que no sea Windows. Si observa problemas con las copias de seguridad del estado del sistema, la comprobación de la ubicación del volumen de almacenamiento provisional es el primer paso para solucionar problemas.** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>¿Cómo se puede cambiar la ruta de acceso del volumen de almacenamiento provisional especificada en el agente de Azure Backup?

El volumen de almacenamiento provisional se encuentra en la carpeta de caché de forma predeterminada. 

1. Para cambiar esta ubicación, utilice el siguiente comando (en un símbolo del sistema con privilegios elevados):
  ```
  PS C:\> Net stop obengine
  ```

2. Después, actualice las siguientes entradas del Registro con la ruta de acceso a la nueva carpeta del volumen de almacenamiento provisional.

  |Ruta de acceso del Registro|Clave del Registro|Valor|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | nueva ubicación del volumen de almacenamiento provisional |

La ruta de acceso del almacenamiento provisional distingue mayúsculas de minúsculas y debe escribirse exactamente igual que la existente en el servidor. 

3. Cuando cambie la ruta de acceso del volumen de almacenamiento provisional, reinicie el motor de Backup:
  ```
  PS C:\> Net start obengine
  ```
4. Para adquirir la ruta de acceso cambiada, abra el agente de Microsoft Azure Recovery Services y desencadene una copia de seguridad ad hoc del estado del sistema.

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>¿Por qué se establece la retención predeterminada del estado del sistema en 60 días?

La vida útil de una copia de seguridad del estado del sistema es la misma que el valor de "duración del marcador de exclusión" para el rol de Windows Server Active Directory. El valor predeterminado para la entrada de duración del marcador de exclusión es de 60 días. Este valor se puede establecer en el objeto de configuración del servicio de directorio (NTDS).

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>¿Cómo se cambia la directiva predeterminada de copia de seguridad y retención para el estado del sistema?

Para cambiar la directiva predeterminada de copia de seguridad y retención para el estado del sistema:
1. Detenga el motor de Backup. Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados.

  ```
  PS C:\> Net stop obengine
  ```

2. Agregue o actualice las siguientes entradas de clave del Registro en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.

  |Nombre en el Registro|Descripción|Valor|
  |-------------|-----------|-----|
  |SSBScheduleTime|Se utiliza para configurar la hora de la copia de seguridad. El valor predeterminado es 9 p. m. (hora local).|DWord: Formato HHMM (decimal), por ejemplo 2130 para 9:30 p. m. (hora local)|
  |SSBScheduleDays|Se utiliza para configurar los días en los que se debe realizar la copia de seguridad del estado del sistema a la hora especificada. Los dígitos individuales especifican los días de la semana. 0 representa el domingo, 1 es el lunes y así sucesivamente. El día predeterminado para hacer la copia de seguridad es el domingo.|DWord: Días de la semana para ejecutar la copia de seguridad (decimal); por ejemplo, 1230 programa copias de seguridad para el lunes, martes, miércoles y domingo.|
  |SSBRetentionDays|Se utiliza para configurar los días para conservar la copia de seguridad. El valor predeterminado es 60. El valor máximo permitido es 180.|DWord: Días que se conserva la copia de seguridad (decimal).|

3. Utilice el siguiente comando para reiniciar el motor de Backup.
    ```
    PS C:\> Net start obengine
    ```

4. Abra el agente de Microsoft Recovery Services.

5. Haga clic en **Programar copia de seguridad** y luego en **Siguiente** hasta que vea los cambios reflejados.

6. Haga clic en **Finalizar** para aplicar los cambios.


## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de cómo [realizar copias de seguridad de máquinas Windows](backup-configure-vault.md).
* Ahora que ha realizado una copia de seguridad de los archivos y las carpetas, puede [administrar los almacenes y servidores](backup-azure-manage-windows-server.md).
* Si necesita restaurar una copia de seguridad, use este artículo: [Restaurar archivos en una máquina de Windows Server o del Cliente de Windows](backup-azure-restore-windows-server.md).
