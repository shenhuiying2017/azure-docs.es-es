---
title: "Aprenda realizar una copia de seguridad de archivos y carpetas desde Windows Server o el cliente a Azure con Azure Backup usando el modelo de implementación de Resource Manager | Microsoft Docs"
description: "Aprenda a realizar una copia de seguridad de los datos de Windows Server mediante la creación de un almacén, la instalación del Agente de Servicios de recuperación y la realización de una copia de seguridad de archivos y carpetas en Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: "cómo realizar la copia de seguridad; cómo realizar la copia de seguridad"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8ac37244d1e691a2f075116f3d78b89923cfb845


---
# <a name="first-look-back-up-files-and-folders-with-azure-backup-using-the-resource-manager-deployment-model"></a>Primer análisis: Copia de seguridad de archivos y carpetas con Copia de seguridad de Azure usando el modelo de implementación de Resource Manager
En este artículo se explica cómo realizar una copia de seguridad de los archivos y carpetas de Windows Server (o del cliente de Windows) en Azure con Copia de seguridad de Azure usando Resource Manager. Es un tutorial diseñado para guiarle por los aspectos básicos. Si desea empezar a usar Copia de seguridad de Azure, está en el lugar correcto.

Si desea más información acerca de Copia de seguridad de Azure, lea esta [introducción](backup-introduction-to-azure-backup.md).

La copia de seguridad de archivos y carpetas en Azure requiere estas actividades:

![Paso 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Obtener una suscripción de Azure, si no tiene una.<br>
![Paso 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Crear un almacén de Recovery Services.<br>
![Paso 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Descargar los archivos necesarios.<br>
![Paso 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Instalar y registrar el agente de Recovery Services.<br>
![Paso 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) Realizar copias de seguridad de los archivos y las carpetas.

![Cómo realizar copias de la máquina de Windows con Copia de seguridad de Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## <a name="step-1-get-an-azure-subscription"></a>Paso 1: obtener una suscripción de Azure
Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) que le permita acceder a todos los servicios de Azure.

## <a name="step-2-create-a-recovery-services-vault"></a>Paso 2: Creación de un almacén de Servicios de recuperación
Para hacer una copia de seguridad de los archivos y las carpetas, tiene que crear un almacén de Servicios de recuperación en la región donde desea almacenar los datos. También debe determinar cómo desea que se replique el almacenamiento.

### <a name="to-create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/) mediante su suscripción.
2. En el menú central, haga clic en **Examinar** y, en la lista de recursos, escriba **Recovery Services** y haga clic en **Almacenes de Recovery Services**.
   
    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>
3. En el menú **Almacenes de servicios de recuperación**, haga clic en **Agregar**.
   
    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)
   
    Se abre la hoja del almacén de Recovery Services, donde se le pide que especifique los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.
   
    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)
4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén.
5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles.
6. Haga clic en **Grupo de recursos** para ver la lista de grupos de recursos disponibles o haga clic en **Nuevo** para crear uno.
7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. Esta elección determina la región geográfica a la que se envían los datos de copia de seguridad.
8. Haga clic en **Crear**.
   
    Si una vez que haya terminado no ve el almacén, haga clic en **Actualizar**. Cuando se actualiza la lista, haga clic en el nombre del almacén.

### <a name="to-determine-storage-redundancy"></a>Determinación de la redundancia de almacenamiento
Al crear un almacén de Servicios de recuperación se determina cómo se replica el almacenamiento.

1. Haga clic en el nuevo almacén para abrir el panel.
2. En la hoja **Configuración**, que se abre automáticamente con el panel del almacén, haga clic en **Infraestructura de copia de seguridad**.
3. En la hoja Infraestructura de copia de seguridad, haga clic en **Configuración de copia de seguridad** para ver el **tipo de replicación de almacenamiento**.
   
    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)
4. Elija la opción de replicación del almacenamiento apropiada para su almacén.
   
    ![Lista de almacenes de Servicios de recuperación](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)
   
    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, siga utilizando el almacenamiento con redundancia geográfica. Si usa Azure como punto de conexión del almacenamiento de copia de seguridad no principal, elija un almacenamiento con redundancia local, ya que ello reducirá el costo de almacenamiento de datos en Azure. En esta página de [información general](../storage/storage-redundancy.md), encontrará más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y con [redundancia local](../storage/storage-redundancy.md#locally-redundant-storage).

Ahora que ya ha creado un almacén, prepare su infraestructura para realizar la copia de seguridad de archivos y carpetas mediante la descarga de las credenciales del almacén y agente de Servicios de recuperación de Microsoft Azure.

## <a name="step-3---download-files"></a>Paso 3: Descarga de los archivos
1. Haga clic en **Configuración** en el panel del almacén de Servicios de recuperación.
   
    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/settings-button.png)
2. Haga clic en **Introducción > Copia de seguridad** en la hoja Configuración.
   
    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)
3. Haga clic en **Backup goal** (Objetivo de la copia de seguridad) en la hoja Copia de seguridad.
   
    ![Abrir hoja de objetivo de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)
4. Seleccione **Local** desde el menú ¿Desde dónde se ejecuta la carga de trabajo?
5. Seleccione **Archivos y carpetas** en el menú ¿De qué desea realizar copias de seguridad? y haga clic en **Aceptar**.

### <a name="download-the-recovery-services-agent"></a>Descarga del agente de Servicios de recuperación
1. Haga clic en **Descargar el agente para Windows Server o cliente de Windows** en la hoja **Preparar infraestructura**.
   
    ![Prepare infrastructure](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)
2. Haga clic en **Guardar** en la ventana emergente de descarga. De forma predeterminada, se guarda el archivo **MARSagentinstaller.exe** en la carpeta de descargas.

### <a name="download-vault-credentials"></a>Descarga de las credenciales de almacén
1. Haga clic en **Descargar > Guardar** en la hoja Preparar infraestructura.
   
    ![Prepare infrastructure](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## <a name="step-4--install-and-register-the-agent"></a>Paso 4: Instalación y registro del agente
> [!NOTE]
> La habilitación de la copia de seguridad a través del portal de Azure estará pronto disponible. En este momento, se usa el agente de Servicios de recuperación de Microsoft Azure local para hacer copia de seguridad de archivos y carpetas.
> 
> 

1. Busque y haga doble clic en el **MARSagentinstaller.exe** desde la carpeta de descargas (u otra ubicación guardado).
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

## <a name="step-5-back-up-your-files-and-folders"></a>Paso 5: Realización de las copias de seguridad de los archivos y las carpetas
La copia de seguridad inicial incluye dos tareas clave:

* Programación de la copia de seguridad
* Creación de copias de seguridad de archivos y carpetas por primera vez

Para realizar la copia de seguridad inicial use el agente de Servicios de recuperación de Azure.

### <a name="to-schedule-the-backup"></a>Para programar la copia de seguridad
1. Abra el agente de Servicios de recuperación de Microsoft Azure. Para encontrarlo, busque **Copia de seguridad de Microsoft Azure**en la máquina.
   
    ![Iniciar el agente de los Servicios de recuperación de Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)
2. En el agente de Servicios de recuperación, haga clic en **Programar copia de seguridad**.
   
    ![Programar una copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)
3. En la página de introducción del Asistente para programar copias de seguridad, haga clic en **Siguiente**.
4. En la página Seleccionar elementos de los que realizar copia de seguridad, haga clic en **Agregar elementos**.
5. Seleccione los archivos y las carpetas de los que desea crear la copia de seguridad y, luego, haga clic en **Aceptar**.
6. Haga clic en **Siguiente**.
7. En la página **Especifique la programación de copia de seguridad**, indique la **programación de copia de seguridad** y haga clic en **Siguiente**.
   
    Puede programar copias de seguridad diarias (con una frecuencia máxima de tres veces al día) o semanales.
   
    ![Elementos para la copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)
   
   > [!NOTE]
   > Si desea más información sobre cómo especificar la programación de las copias de seguridad, consulte el artículo [Usar la copia de seguridad de Azure para cambiar su infraestructura de cintas](backup-azure-backup-cloud-as-tape.md).
   > 
   > 
8. En la página **Seleccione la directiva de retención**, elija la **directiva de retención** para la copia de seguridad.
   
    La directiva de retención especifica el tiempo durante el que se almacenará la copia de seguridad. En vez de especificar solo una directiva para todos los puntos de copia de seguridad, puede especificar directivas de retención distintas en función de cuándo se realice la copia de seguridad. Puede modificar las directivas de retención diarias, semanales, mensuales y anuales según sus necesidades.
9. En la página Elija el tipo de copia de seguridad inicial, elija el tipo de copia de seguridad inicial. Deje activada la opción **Automáticamente a través de la red** y, luego, haga clic en **Siguiente**.
   
    Puede hacer una copia de seguridad automáticamente en la red, o puede realizar una copia sin conexión. En el resto de este artículo, se describe el proceso para crear automáticamente una copia de seguridad. Si prefiere crear una copia de seguridad sin conexión, consulte el artículo [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md) para más información.
10. En la página Confirmación, revise la información y, luego, haga clic en **Finalizar**.
11. Cuando el asistente termine de crear la programación de copia de seguridad, haga clic en **Cerrar**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Creación de copias de seguridad de archivos y carpetas por primera vez
1. En el agente de Servicios de recuperación, haga clic en **Back Up Now** (Iniciar copia de seguridad) para completar la propagación inicial a través de la red.
   
    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. En la página Confirmación, revise la configuración que el asistente para iniciar copia de seguridad usará para crear la copia de seguridad de la máquina. Luego, haga clic en **Crear copia de seguridad**.
3. Haga clic en **Cerrar** para cerrar el asistente. Si lo hace antes de que finalice el proceso de copia de seguridad, el asistente se sigue ejecutando en segundo plano.

Una vez que finalice la copia de seguridad inicial, el estado **Trabajo completado** se refleja en la consola de Copia de seguridad.

![IR completado](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de cómo [realizar copias de seguridad de máquinas Windows](backup-configure-vault.md).
* Ahora que ha realizado una copia de seguridad de los archivos y las carpetas, puede [administrar los almacenes y servidores](backup-azure-manage-windows-server.md).
* Si necesita restaurar una copia de seguridad, use este artículo: [Restaurar archivos en una máquina de Windows Server o del Cliente de Windows](backup-azure-restore-windows-server.md).




<!--HONumber=Dec16_HO2-->


