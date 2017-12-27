---
title: Uso de DPM para realizar copias de seguridad de cargas de trabajo en Azure Portal | Microsoft Docs
description: "Una introducción a la copia de seguridad de servidores DPM mediante el servicio Azure Backup"
services: backup
documentationcenter: 
author: adigan
manager: nkolli
editor: 
keywords: System Center Data Protection Manager, Data Protection Manager, copia de seguridad de DPM
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: c22e6fc85e88d89007107c8c3bad142ac91e9d12
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparación para la copia de seguridad de cargas de trabajo en Azure con DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

En este artículo se proporciona una introducción al uso de Microsoft Azure Backup con el objeto de proteger los servidores y las cargas de trabajo de System Center Data Protection Manager (DPM). Cuando lo lea, comprenderá:

* Cómo funciona la copia de seguridad de servidor DPM de Azure
* Los requisitos previos para lograr una experiencia fluida de la copia de seguridad
* Los errores típicos y cómo resolverlos
* Escenarios admitidos

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se proporcionan información y procedimientos para restaurar las máquinas virtuales implementadas mediante el modelo de Resource Manager.
>
>

Datos de aplicación y archivo de copia de seguridad de [System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview). Se puede encontrar más información sobre las cargas de trabajo admitidas [aquí](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Los datos con copia de seguridad en DPM se pueden almacenar en una cinta o en un disco, o se puede crear una copia de seguridad de ellos en Azure con Microsoft Azure Backup. DPM interactúa con Azure Backup de la manera siguiente:

* **DPM implementado como un servidor físico o una máquina virtual local** : si DPM se implementa como un servidor físico o una máquina virtual de Hyper-V local, además de en disco y cinta, puede crear una copia de seguridad de los datos en un almacén de Recovery Services.
* **DPM implementado como una máquina virtual de Azure** : desde System Center 2012 R2 con Update 3, DPM puede implementarse como una máquina virtual de Azure. Si DPM se implementa como una máquina virtual de Azure, puede crear una copia de seguridad de los datos en discos de Azure conectados a la máquina virtual de DPM Azure, o puede descargar el almacenamiento de datos creando una copia de seguridad de ellos en un almacén de Recovery Services.

## <a name="why-backup-from-dpm-to-azure"></a>¿Por qué hacer una copia de seguridad de DPM a Azure?
Algunas de las ventajas empresariales del uso de Azure Backup para la copia de seguridad de servidores DMP son las siguientes:

* Para la implementación de DPM local, puede usar Azure como alternativa a la implementación a largo plazo en cinta.
* Para las implementaciones de DPM en Azure, Azure Backup le permite descargar el almacenamiento del disco de Azure, de forma que es posible escalar verticalmente gracias a que los datos más antiguos se almacenan en Recovery Services y los nuevos en el disco.

## <a name="prerequisites"></a>Requisitos previos
Prepare Azure Backup para crear copias de seguridad de los datos de DPM de la manera siguiente:

1. **Crear un almacén de Recovery Services**: cree un almacén en Azure Portal.
2. **Descargar credenciales de almacén** : descargue las credenciales que va a usar para registrar el servidor DPM en el almacén de Recovery Services.
3. **Instalar el agente de Azure Backup**: desde Azure Backup, instale el agente en cada servidor DPM.
4. **Registrar el servidor** : registre el servidor DPM en el almacén de Recovery Services.

## <a name="key-definitions"></a>Definiciones clave
Estas son algunas definiciones clave para copia de seguridad de Azure para DPM:

1. **Credenciales de almacén**: las credenciales de almacén son necesarias para autenticar la máquina para enviar datos de copia de seguridad a un almacén identificado en el servicio Azure Backup. Se puede descargar desde el almacén y son válidas durante 48 horas.
2. **Frase de contraseña**: la frase de contraseña se utiliza para cifrar las copias de seguridad en la nube. Guarde el archivo en una ubicación segura, ya que puede ser necesario durante una operación de recuperación.
3. **PIN de seguridad**: si ha habilitado la [configuración de seguridad](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) del almacén, el PIN de seguridad es necesario para realizar operaciones de copia de seguridad críticas. Esta Multi-Factor Authentication agrega otra capa de seguridad. 
4. **Carpeta de recuperación**: es la frase en la que se descargan temporalmente las copias de seguridad de la nube durante las recuperaciones de la nube. Su tamaño debe ser aproximadamente igual al tamaño de los elementos de copia de seguridad que desea recuperar en paralelo.


### <a name="1-create-a-recovery-services-vault"></a>1. Creación de un almacén de Servicios de recuperación
Para crear un almacén de Servicios de recuperación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Recovery Services**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Recovery Services**.

    ![Creación del almacén de Recovery Services, paso 1](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    Se muestra la lista de almacenes de Recovery Services.
3. En el menú **Almacenes de Recovery Services**, haga clic en **Agregar**.

    ![Creación del almacén de Recovery Services, paso 2](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    Se abre la hoja del almacén de Recovery Services, donde se le pide que especifique los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Creación del almacén de Recovery Services, paso 5](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.
5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.
6. Haga clic en **Grupo de recursos** para ver la lista de grupos de recursos disponibles o haga clic en **Nuevo** para crear uno. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén.
8. Haga clic en **Crear**. La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones de estado en la parte superior derecha del portal.
   Una vez creado el almacén, se abre en el portal.

### <a name="set-storage-replication"></a>Configuración de la replicación de almacenamiento
La opción de replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si esta es su copia de seguridad principal, elija el almacenamiento con redundancia geográfica. Elija el almacenamiento con redundancia local si desea una opción más económica que no sea tan duradera. Para más información sobre las opciones de almacenamiento [con redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) y [con redundancia local](../storage/common/storage-redundancy.md#locally-redundant-storage), consulte [Replicación de Azure Storage](../storage/common/storage-redundancy.md).

Para editar la configuración de replicación de almacenamiento:

1. Seleccione el almacén para abrir su panel y la hoja de configuración. Si la hoja **Configuración** no se abre, haga clic en la opción **Toda la configuración** del panel del almacén.
2. En la hoja **Configuración**, haga clic en **Infraestructura de copia de seguridad** > **Configuración de copia de seguridad** para abrir la hoja **Configuración de copia de seguridad**. En la hoja **Configuración de copia de seguridad** , elija la opción de replicación de almacenamiento para su almacén.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

### <a name="2-download-vault-credentials"></a>2. Descarga de las credenciales de almacén
El archivo de credenciales de almacén es un certificado generado por el portal para cada almacén de copia de seguridad. Luego el portal carga la clave pública en Access Control Service (ACS). La clave privada del certificado estará disponible para el usuario como parte del flujo de trabajo que se proporciona como entrada del flujo de trabajo de registro de máquina. Esto autentica la máquina para enviar datos de copia de seguridad a un almacén identificado en el servicio de Azure Backup.

El archivo de credenciales de almacén se usa solo durante el flujo de trabajo de registro. Es responsabilidad del usuario asegurarse de que el archivo de credenciales de almacén no se ve comprometido. Si cae en manos de un usuario no autorizado, el archivo de credenciales de almacén puede utilizarse para registrar otras máquinas en el mismo almacén. Sin embargo, como los datos de copia de seguridad se cifran mediante una frase de contraseña que pertenece al cliente, los datos de copia de seguridad existentes no pueden estar en peligro. Para mitigar este problema, se establece la expiración de las credenciales de almacén en 48 horas. Puede descargar las credenciales de almacén de un almacén de Servicios de recuperación cualquier número de veces, pero solo el último archivo es aplicable durante el flujo de trabajo de registro.

El archivo de credenciales de almacén se descarga a través de un canal seguro desde el Portal de Azure. El servicio Azure Backup no conoce la clave privada del certificado, y la clave privada no se conserva en el portal o el servicio. Siga estos pasos para descargar el archivo de credenciales de almacén a una máquina local.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Abra el almacén de Recovery Services en el que quiere registrar la máquina DPM.
3. La hoja Configuración se abre de forma predeterminada. Si está cerrada, haga clic en **Configuración** en el panel de almacén para abrirla. En dicha hoja, haga clic en **Propiedades**.

    ![Hoja del almacén abierta](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. En la página Propiedades, en **Credenciales de copia de seguridad**, haga clic en **Descargar**. El portal genera el archivo de credenciales del almacén, que puede descargarse.

    ![Descargar](./media/backup-azure-dpm-introduction/vault-credentials.png)

El portal generará una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. Haga clic en **Guardar** para descargar las credenciales de almacén en la carpeta de descargas de la cuenta local, o seleccione Guardar como desde el menú Guardar para especificar una ubicación para las credenciales de almacén. El archivo tarda un minuto en generarse.

### <a name="note"></a>Nota:
* Asegúrese de que el archivo de credenciales de almacén se guarde en una ubicación a la que se pueda acceder desde la máquina. Si se almacenan en un recurso compartido/SMB de archivo, compruebe los permisos de acceso.
* El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro.
* El archivo de credenciales de almacén caduca después de 48 horas y puede descargarse desde el portal.

### <a name="3-install-backup-agent"></a>3. Instalación del agente de copia de seguridad
Después de crear el almacén de Azure Backup, se debe instalar un agente en cada una de las máquinas de Windows (servidor de Windows Server, cliente de Windows, servidor de System Center Data Protection Manager o la máquina de Azure Backup Server) que habilite la copia de seguridad de los datos y las aplicaciones en Azure.

1. Abra el almacén de Recovery Services en el que quiere registrar la máquina DPM.
2. La hoja Configuración se abre de forma predeterminada. Si está cerrada, haga clic en **Configuración** para abrirla. En dicha hoja, haga clic en **Propiedades**.

    ![Hoja del almacén abierta](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. En la página Configuración, en **Agente de Azure Backup**, haga clic en **Descargar**.

    ![Descargar](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Después de instalar el agente, ejecute MARSAgentInstaller.exe para iniciar la instalación del agente de Azure Backup. Elija la carpeta de instalación y la carpeta temporal requerido para el agente. La ubicación de caché especificada debe tener un espacio libre de al menos el 5% de los datos de copia de seguridad.
4. Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy** , especifique los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, escriba los detalles de nombre y la contraseña del usuario en esta pantalla.
5. El agente de Azure Backup instala .NET Framework 4.5 y Windows PowerShell (si aún no está disponible) para completar la instalación.
6. Una vez instalado el agente, **cierre** la ventana.

   ![cierre](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. Para **registrar el servidor DPM** en el almacén, en la pestaña **Administración**, haga clic en **En línea**. Después, seleccione **Registrar**. Se abrirá al Asistente para registrar el programa de instalación.
8. Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy** , especifique los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, escriba los detalles de nombre y la contraseña del usuario en esta pantalla.

    ![Configuración de proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. En la pantalla de credenciales del almacén, busque y seleccione el archivo de credenciales del almacén que se descargó anteriormente.

    ![Credenciales de almacén](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    El archivo de almacén de credenciales solo es válido durante 48 horas (después de descargarlo desde el portal). Si encuentra algún error en esta pantalla (por ejemplo "El archivo de credenciales de almacén especificado expiró"), inicie sesión en el Portal de Azure y vuelva a descargar el archivo de credenciales de almacén.

    Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de almacén de credenciales en una ubicación temporal en esta máquina y vuelva a intentar la operación.

    Si se produce un error de credenciales de almacén no válidas (por ejemplo, "Las credenciales del almacén no son válidas"), el archivo está dañado o no tiene asociadas las credenciales más recientes con el servicio de recuperación. Vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error suele aparecer si el usuario hace clic en la opción **Descargar credenciales de almacén** en el Portal de Azure, en sucesión rápida. En este caso, solo es válido el segundo archivo de credenciales de almacén.
10. Para controlar el uso de ancho de banda de red durante el trabajo y las horas no laborables, en la pantalla **Configuración de límite** , puede establecer los límites de uso de ancho de banda y definir las horas de trabajo y no laborables.

    ![Configuración de límite](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. En la pantalla **Recovery Folder Setting** (Configuración de la carpeta de recuperación), examine la carpeta donde los archivos descargados de Azure se almacenarán temporalmente.

    ![Recovery Folder Setting](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. En la pantalla **Configuración de cifrado** , puede generar una frase de contraseña o proporcionarla (mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.

    ![Cifrado](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Si la frase de contraseña se pierde u olvida; Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no puede ver la frase de contraseña que usa el usuario final. Guarde el archivo en una ubicación segura, ya que puede ser necesario durante una operación de recuperación.
    >
    >
13. Al hacer clic en el botón **Registrar** , la máquina se ha registrado correctamente en el almacén y ahora está lista para iniciar la copia de seguridad en Microsoft Azure.
14. Cuando use Data Protection Manager, puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción **Configurar** y seleccionando **En línea** en la pestaña **Administración**.

## <a name="requirements-and-limitations"></a>Requisitos y limitaciones
* DPM se puede ejecutar como un servidor físico o como una máquina virtual de Hyper-V, instalado en System Center 2012 SP1 o System Center 2012 R2. También se puede ejecutar como una máquina virtual de Azure que ejecuta System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 3 para DPM 2012 R2, o como una máquina virtual de Windows en VMWare que se ejecuta en System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 5.
* Si ejecuta DPM con System Center 2012 SP1, debe instalar el paquete acumulativo de actualizaciones 2 para System Center Data Protection Manager SP1. Esto es necesario para poder instalar Azure Backup Agent.
* El servidor DPM debe tener instalado Windows PowerShell y .Net Framework 4.5.
* DPM puede realizar una copia de seguridad de la mayoría de las cargas de trabajo en Azure Backup. Para obtener una lista completa de compatibilidad, vea a continuación los elementos admitidos en Azure Backup.
* Los datos almacenados en Azure Backup no se pueden recuperar con la opción "copiar en cinta".
* Necesitará una cuenta de Azure con la característica Azure Backup habilitada. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Lea acerca de los [Precios de Backup](https://azure.microsoft.com/pricing/details/backup/).
* El uso de Copia de seguridad de Azure requiere la instalación de Azure Backup Agent en los servidores de los que desee realizar una copia de seguridad. Cada servidor debe tener disponible como espacio de almacenamiento local al menos un 5 % del tamaño de los datos de los que se va a realizar la copia de seguridad. Por ejemplo, realizar una copia de seguridad de 100 GB de datos requiere un mínimo de 5 GB de espacio libre en la ubicación temporal.
* Los datos se almacenarán en el almacenamiento de almacén de Azure. No hay ningún límite en la cantidad de datos de los que puede realizar una copia de seguridad en el almacén de Azure Backup; sin embargo, el tamaño de un origen de datos (por ejemplo, una máquina virtual o una base de datos) no debe superar los 54.400 GB.

Los siguientes tipos de archivo se admiten para la copia de seguridad en Azure:

* Cifrados (solo copias de seguridad completas)
* Comprimidos (copias de seguridad incrementales compatibles)
* Dispersos (copias de seguridad incrementales compatibles)
* Comprimidos y dispersos (tratados como dispersos)

No se admiten los siguientes:

* Servidores de sistemas de archivos que distinguen entre mayúsculas y minúsculas.
* Vínculos físicos (omitidos)
* Puntos de reanálisis (omitidos)
* Cifrados y comprimidos (omitidos)
* Cifrados y dispersos (omitidos)
* Flujo comprimido
* Flujo disperso

> [!NOTE]
> A partir de System Center 2012 DPM con SP1 en adelante, puede realizar una copia de seguridad de las cargas protegidas por DPM en Azure con Microsoft Azure Backup.
>
>
