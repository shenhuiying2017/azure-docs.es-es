<properties
  pageTitle="Preparación del entorno para realizar copias de seguridad de cargas de trabajo con el servidor de Copia de seguridad de Azure | Microsoft Azure"
  description="Asegúrese de que el entorno está preparado correctamente para hacer copias de seguridad de las cargas de trabajo con el servidor de Copia de seguridad de Azure"
  services="backup"
  documentationCenter=""
  authors="trinadhk"
  manager="shreeshd"
  editor=""
  keywords="servidor de copia de seguridad de Azure; almacén de copia de seguridad"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/10/2016"
  ms.author="jimpark;trinadhk;pullabhk"/>

# Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure

> [AZURE.SELECTOR]
- [Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Servidor de copia de seguridad de Azure (clásico)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (clásico)](backup-azure-dpm-introduction-classic.md)

En este artículo, se trata cómo preparar el entorno para hacer copias de seguridad de las cargas de trabajo con el servidor de Copia de seguridad de Azure.

> [AZURE.NOTE] Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). En este artículo se proporcionan información y procedimientos para restaurar las máquinas virtuales implementadas mediante el modelo de Resource Manager.

Con el servidor de Copia de seguridad de Azure, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows desde una única consola:

>[AZURE.WARNING] El servidor de Copia de seguridad de Azure hereda la funcionalidad de Data Protection Manager (DPM) para copias de seguridad de cargas de trabajo. Encontrará punteros para la documentación de DPM para algunas de estas capacidades. Sin embargo, el servidor de Copia de seguridad de Azure no ofrece protección en cinta o integración con System Center.

## 1\. Máquina de Windows Server

El primer paso para que funcione el servidor de Copia de seguridad de Azure es tener una máquina Windows Server.

| Ubicación | Requisitos mínimos | Instrucciones adicionales |
| -------- | -------------------- | ----------------------- |
| Azure | Máquina virtual de Azure IaaS<br><br>A2 Standard: 2 núcleos, 3,5 GB de RAM | Puede comenzar por una galería de imágenes sencilla de Windows Server 2012 R2 Datacenter. [La protección de cargas de trabajo de IaaS con el Servidor de Copia de seguridad de Azure (DPM)](https://technet.microsoft.com/library/jj852163.aspx) presenta numerosos matices. Asegúrese de leer el artículo completo antes de implementar la máquina. |
| Local | Máquina virtual de Hyper-V,<br> máquina virtual de VMWare<br> o un host físico<br><br>2 núcleos y 4 GB de RAM | Puede desduplicar el almacenamiento de DPM con la desduplicación de Windows Server. Más información sobre cómo funcionan juntos [DPM y la desduplicación](https://technet.microsoft.com/library/dn891438.aspx) al implementarlos en máquinas virtuales de Hyper-V. |

> [AZURE.NOTE] Se recomienda instalar el servidor de Copia de seguridad de Azure en una máquina con Windows Server 2012 R2 Datacenter. Muchos de los requisitos previos quedan resueltos automáticamente con la versión más reciente del sistema operativo Windows.

Si planea unir este servidor a un dominio en algún momento, se recomienda que la actividad de unión a dominio se realice antes de la instalación del servidor de Copia de seguridad de Azure. *No se permite* mover una máquina del Servidor de Copia de seguridad de Azure existente a un dominio nuevo después de la implementación.

## 2\. Almacén de Servicios de recuperación

Tanto si envía datos de copia de seguridad a Azure como si los mantiene localmente, el software debe estar conectado a Azure. Más concretamente, la máquina del Servidor de Copia de seguridad de Azure debe estar registrada en un almacén de Servicios de recuperación.

Para crear un almacén de Servicios de recuperación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Servicios de recuperación**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Servicios de recuperación**.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    Se muestra la lista de almacenes de Servicios de recuperación.

3. En el menú **Almacenes de Servicios de recuperación**, haga clic en **Agregar**.

    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    Se abre la hoja del almacén de Servicios de recuperación, que le pide que proporcione los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)

4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.

6. Haga clic en **Grupo de recursos** para ver la lista disponible de grupos de recursos o en **Nuevo** para crear uno. Para una información completa sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../resource-group-overview.md)

7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén.

8. Haga clic en **Crear**. La creación del almacén de Servicios de recuperación puede tardar unos minutos. Supervise las notificaciones de estado en la parte superior derecha del portal. Una vez creado el almacén, se abre en el portal.

### Configuración de la replicación de almacenamiento

La opción de replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si esta es su copia de seguridad principal, elija el almacenamiento con redundancia geográfica. Elija el almacenamiento con redundancia local si desea una opción más económica que no sea tan duradera. Para más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y [local](../storage/storage-redundancy.md#locally-redundant-storage) consulte [Replicación de almacenamiento de Azure](../storage/storage-redundancy.md).

Para editar la configuración de replicación de almacenamiento:

1. Seleccione el almacén para abrir su panel y la hoja de configuración. Si la hoja **Configuración** no se abre, haga clic en **Toda la configuración** en el panel del almacén.

2. En la hoja **Configuración**, haga clic en **Backup Infrastructure** (Infraestructura de copia de seguridad) > **Configuración de copia de seguridad** para abrir la hoja **Configuración de copia de seguridad**. En la hoja **Configuración de copia de seguridad**, elija la opción de replicación para su almacén.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## 3\. Paquete de software

### Descarga del paquete de software
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Si ya tiene abierto un almacén de Servicios de recuperación, vaya al paso 3. Si no tiene abierto un almacén de Servicios de recuperación, pero está en el Portal de Azure, en el menú del centro, haga clic en **Examinar**.

    - En la lista de recursos, escriba **Servicios de recuperación**.
    - Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de servicios de recuperación** cuando lo vea.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

    Aparece la lista de almacenes de Servicios de recuperación.
    
    - En la lista de almacenes de Servicios de recuperación, seleccione un almacén.

    Se abre el panel del almacén seleccionado.

    ![Hoja del almacén abierta](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

3. La hoja Configuración se abre de forma predeterminada. Si está cerrada, haga clic en **Configuración** para abrirla.

    ![Hoja del almacén abierta](./media/backup-azure-microsoft-azure-backup/vault-setting.png)

4. Haga clic en **Copia de seguridad** en **Introducción** para abrir el Asistente para introducción.

    ![Introducción a la copia de seguridad](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

5. En la introducción que se abre, la pantalla Backup Goals (Objetivos de copia de seguridad) se selecciona automáticamente. ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

    En la sección **Backup goals** (Objetivos de copia de seguridad), en *Where is your workload running?* (¿Desde dónde se ejecuta la carga de trabajo?), seleccione *Local*.

    ![entorno local y cargas de trabajo como objetivos](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

6. Seleccione las cargas de trabajo que quiere proteger mediante el Servidor de Copia de seguridad de Azure en *What workloads you want to protect* (¿Que cargas de trabajo quiere proteger?) y haga clic en **Aceptar**.

    > [AZURE.NOTE] Si va a proteger solo archivos y carpetas, se recomienda utilizar el agente de Copia de seguridad de Azure. Si va a proteger más cargas de trabajo, en lugar de solo archivos y carpetas, o tiene pensado en el futuro expandir las necesidades de protección, seleccione todas esas cargas de trabajo.

    El Asistente para introducción cambiará para preparar la infraestructura para la protección de las cargas de trabajo locales en Azure.

    ![Cambio del Asistente para introducción](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)
  
7. En la hoja **Prepare infrastructure** (Preparar la infraestructura) que se abre, haga clic en **Download Azure Backup Server and Vault credentials** (Descargar las credenciales de almacén y del Servidor de Copia de seguridad) que usará durante el registro del Servidor de Copia de seguridad de Azure en el almacén de Servicios de recuperación. Esto le llevará a la página del Centro de descargas donde podrá descargar el paquete de software.

    ![Preparación de la infraestructura del Servidor de Copia de seguridad de Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

8. Seleccione todos los archivos y haga clic en **Siguiente**. Descargue todos los archivos procedentes de la página de descarga de Copia de seguridad de Microsoft Azure y colóquelos en la misma carpeta.

    ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Puesto que el tamaño de descarga de todos los archivos juntos es de más de 3 GB, con un vínculo de descarga a 10 Mbps, se puede tardar hasta 60 minutos en completarla.


### Extracción del paquete de software

Después de descargar todos los archivos, haga clic en **MicrosoftAzureBackupInstaller.exe**. Se inicia el **Asistente para instalación de Copia de seguridad de Microsoft Azure**, que extraerá los archivos de instalación en una ubicación especificada por el usuario. Siga con el asistente y haga clic en el botón **Extraer** para comenzar el proceso de extracción.

> [AZURE.WARNING] Se requieren al menos 4 GB de espacio libre para extraer los archivos de instalación.


![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Después de completar el proceso de extracción, active la casilla para iniciar el archivo *setup.exe* recién extraído y empezar a instalar el Servidor de Copia de seguridad de Microsoft Azure. Luego, haga clic en el botón **Finalizar**.

### Instalación del paquete de software

1. Haga clic en **Copia de seguridad de Microsoft Azure** para iniciar el asistente para la instalación.

    ![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. En la pantalla de bienvenida, haga clic en **Siguiente**. Irá a la sección *Prerequisite Checks* (Comprobaciones de requisitos previos). En esta pantalla, haga clic en el botón **Comprobar** para determinar si se cumplieron los requisitos previos de hardware y software para el Servidor de Copia de seguridad de Azure. Si se cumplieron todos los requisitos previos, verá un mensaje que indica que la máquina los cumple. Haga clic en el botón **Siguiente**.

    ![Servidor de Copia de seguridad Azure - Bienvenida y requisitos previos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. El servidor de Copia de seguridad de Microsoft Azure requiere SQL Server Standard y en el paquete de instalación del servidor de Copia de seguridad de Azure se incluyen los correspondientes archivos binarios de SQL Server necesarios. Cuando comience una nueva instalación del Servidor de Copia de seguridad de Azure, debe elegir la opción **Install new Instance of SQL Server with this Setup** (Instalar nueva instancia de SQL Server con esta configuración) y hacer clic en el botón **Check and Install** (Comprobar e instalar). Una vez que los requisitos previos se instalen correctamente, haga clic en **Next** (Siguiente).

    ![Servidor de Copia de seguridad de Azure - Comprobación de SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Si se produce un error con una recomendación para reiniciar el equipo, proceda a reiniciar y haga clic en **Check Again** (Comprobar de nuevo).

    > [AZURE.NOTE] El servidor de Copia de seguridad de Azure no funcionará con una instancia remota de SQL Server. La instancia que se utiliza en el servidor de Copia de seguridad de Azure debe ser local.

4. Proporcione una ubicación donde instalar los archivos del servidor de Copia de seguridad de Microsoft Azure y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La ubicación temporal es un requisito para hacer copias de seguridad en Azure. Asegúrese de que la ubicación temporal sea al menos el 5% de los datos cuya copia de seguridad se planea hacer en la nube. Para la protección de disco, deben configurarse discos independientes una vez completada la instalación. Para obtener más información acerca de los grupos de almacenamiento, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

5. Proporcione una contraseña segura para las cuentas de usuario locales con permisos restringidos y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. Seleccione si desea usar *Microsoft Update* para comprobar si hay actualizaciones y haga clic en **Next** (Siguiente).

    >[AZURE.NOTE] Se recomienda que Windows Update se redirija a Microsoft Update, que ofrece actualizaciones importantes y de seguridad para Windows y otros productos como el servidor de Copia de seguridad de Microsoft Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. Revise *Summary of Settings* (Resumen de la configuración) y haga clic en **Install** (Instalar).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. La instalación se realiza en fases. En la primera fase, se instala el agente de Servicios de recuperación de Microsoft Azure en el servidor. El asistente comprueba igualmente la conectividad a Internet. Si la conectividad a Internet está disponible, puede continuar con la instalación; de lo contrario, debe proporcionar los detalles del proxy para conectarse a Internet.

    El siguiente paso es configurar el agente de Servicios de recuperación de Microsoft Azure. Como parte de la configuración, tendrá que proporcionar las credenciales del almacén para registrar la máquina en el almacén de Servicios de recuperación. También proporcionará una frase de contraseña para cifrar y descifrar los datos enviados entre Azure y sus instalaciones. Puede generar una frase de contraseña automáticamente o proporcionar la suya propia, con un mínimo de 16 caracteres. Continúe con el asistente hasta que se haya configurado el agente.

    ![Requisitos previos del servidor de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Una vez que se complete correctamente el registro del servidor de Copia de seguridad de Microsoft Azure, el asistente para instalación global prosigue con la instalación y configuración de los componentes de SQL Server y de Copia de seguridad de Microsoft Azure. Tras completarse la instalación de los componentes de SQL Server, se instalan los componentes del servidor de Copia de seguridad de Azure.

    ![Servidor de Copia de seguridad de Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


Cuando el paso de instalación haya finalizado, se habrán creado también los iconos de escritorio del producto. Haga doble clic en el icono para iniciar el producto.

### Incorporación de almacenamiento de copia de seguridad

La primera copia de seguridad se mantiene en el almacenamiento conectado a la máquina del servidor de Copia de seguridad de Azure. Para obtener más información acerca de los discos, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

> [AZURE.NOTE] Debe agregar el almacenamiento de copia de seguridad incluso si tiene pensado enviar los datos a Azure. En la arquitectura del Servidor de Copia de seguridad de Azure actual, el almacén de Copia de seguridad de Azure contiene la *segunda* copia de los datos, mientras que el almacenamiento local contiene la primera (y obligatoria) copia de seguridad.

## 4\. Conectividad de red

El servidor de Copia de seguridad de Azure requiere conectividad al servicio de Copia de seguridad de Azure para que el producto funcione correctamente. Para validar si la máquina tiene conectividad a Azure, use el cmdlet ```Get-DPMCloudConnection``` en la consola de PowerShell del Servidor de Copia de seguridad de Azure. Si la salida del cmdlet es TRUE, entonces existe conectividad, de lo contrario, no hay conectividad.

Además, la suscripción de Azure debe encontrarse en un estado correcto. Para averiguar el estado de la suscripción y administrarla, inicie sesión en el [portal de suscripción](https://account.windowsazure.com/Subscriptions).

Una vez que conozca el estado de la conectividad y suscripción de Azure, puede usar la tabla siguiente para saber el impacto en la funcionalidad de copia de seguridad y restauración que se ofrece.

| Estado de conectividad | Suscripción de Azure | Copia de seguridad en Azure| Copia de seguridad en disco | Restauración desde Azure | Restauración desde disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Conectado | Active | Permitida | Permitida | Permitida | Permitida |
| Conectado | Expirada | Stopped | Stopped | Permitida | Permitida |
| Conectado | Desaprovisionada | Stopped | Stopped | Detenida y puntos de recuperación de Azure eliminados | Stopped |
| Pérdida de conectividad > 15 días | Active | Stopped | Stopped | Permitida | Permitida |
| Pérdida de conectividad > 15 días | Expirada | Stopped | Stopped | Permitida | Permitida |
| Pérdida de conectividad > 15 días | Desaprovisionada | Stopped | Stopped | Detenida y puntos de recuperación de Azure eliminados | Detenido |

### Recuperación de una pérdida de conectividad
Si tiene un firewall o un proxy que impide el acceso a Azure, deberá permitir primero las siguientes direcciones de dominio en el perfil del firewall/proxy:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

Una vez restaurada la conectividad a Azure en la máquina del servidor de Copia de seguridad de Azure, las operaciones que pueden realizarse dependen del estado de la suscripción de Azure. La tabla anterior incluye detalles acerca de las operaciones permitidas una vez que la máquina esté "Conectada".

### Control de los estados de la suscripción

Es posible llevar una suscripción de Azure desde un estado *Expirado* o *Desaprovisionado* hasta un estado *Activo*. Sin embargo, esto tiene algunos efectos sobre el comportamiento del producto mientras el estado no sea *Activo*:

- Una suscripción con estado *Desaprovisionado* pierde la funcionalidad durante el período en que está desaprovisionada. Al pasar a *Activo*, se reactiva la funcionalidad del producto de copia de seguridad y restauración. Los datos de copia de seguridad del disco local también pueden recuperarse en caso de que se haya mantenido con un período de retención lo suficientemente amplio. No obstante, los datos de copia de seguridad de Azure se pierden irremediablemente una vez que la suscripción pasa al estado *Desaprovisionado*.
- Una suscripción con estado *Expirado* solo pierde la funcionalidad hasta que pase de nuevo al estado *Activo*. Las copias de seguridad programadas para el período en el que la suscripción tenía el estado *Expirado* no se ejecutarán.


## Solución de problemas

Si el servidor de Copia de seguridad de Microsoft Azure produce un error durante la fase de instalación (o copia de seguridad o restauración), consulte este [documento de códigos de error](https://support.microsoft.com/kb/3041338) para obtener más información. También puede consultar [Copia de seguridad de Azure - Preguntas más frecuentes](backup-azure-backup-faq.md).


## Pasos siguientes

Para más información sobre la [preparación del entorno para DPM](https://technet.microsoft.com/library/hh758176.aspx), visite el sitio de Microsoft TechNet. También contiene información sobre las configuraciones admitidas en las que se puede implementar y usar el servidor de Copia de seguridad de Azure.

Puede usar estos artículos para mejorar la comprensión sobre la protección de cargas de trabajo mediante el servidor de Copia de seguridad de Microsoft Azure.

- [Copia de seguridad de SQL Server](backup-azure-backup-sql.md)
- [Copia de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint.md)
- [Copia de seguridad de otro servidor](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0720_2016-->