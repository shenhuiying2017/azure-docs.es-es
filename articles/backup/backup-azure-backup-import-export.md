---
title: "Azure Backup: copia de seguridad sin conexión o propagación inicial mediante el servicio Azure Import/Export | Microsoft Docs"
description: "Descubra cómo Azure Backup permite enviar datos fuera de la red mediante el servicio Azure Import/Export. Este artículo explica la propagación sin conexión de los datos de copia de seguridad iniciales mediante el servicio de Azure Import/Export."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/18/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 32a48a34711a7f053a74e103deb6853150de3903
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flujo de trabajo de copia de seguridad sin conexión en Azure Backup
El servicio Azure Backup presenta varias eficiencias integradas para ahorrar costos de almacenamiento y red durante las copias de seguridad iniciales 'completas' de datos en Azure. Las copias de seguridad iniciales completas transfieren grandes cantidades de datos y requieren un mayor ancho de banda de red en comparación con las copias de seguridad sucesivas que solo transfieren los cambios diferenciales e incrementales. Azure Backup permite comprimir las copias de seguridad iniciales. A través del proceso de propagación sin conexión, Azure Backup puede usar discos para cargar los datos comprimidos iniciales de copia de seguridad sin conexión en Azure.  

El proceso de propagación sin conexión de Azure Backup se integra estrechamente con el [servicio Azure Import/Export](../storage/common/storage-import-export-service.md) que permite transferir datos a Azure mediante discos. Si tiene terabytes (TB) de datos de copia de seguridad inicial que se deben transferir a través de una red de latencia alta y ancho de banda de red bajo, puede usar el flujo de trabajo de propagación sin conexión para enviar la copia de seguridad inicial de una o varias unidades de disco duro a un centro de datos de Azure. Este artículo proporciona información general de los pasos necesarios para completar este flujo de trabajo.

## <a name="overview"></a>Información general
Con la funcionalidad de propagación sin conexión de Azure Backup y Azure Import/Export, cargar los datos sin conexión en Azure mediante discos es un proceso sencillo. En lugar de transferir la copia inicial completa a través de la red, los datos de copia de seguridad se escriben en una *ubicación de ensayo*. Una vez finalizada la copia inicial en la ubicación de ensayo mediante la herramienta Azure Import/Export, los datos se escriben en una o varias unidades SATA, según el tamaño de la copia de seguridad inicial. Estas unidades se envían finalmente al centro de datos de Azure más cercano.

La [actualización de agosto de 2016 de Azure Backup (y versiones posteriores)](http://go.microsoft.com/fwlink/?LinkID=229525) , incluye una *herramienta de preparación de discos de Azure*llamada AzureOfflineBackupDiskPrep que:

* Le facilita la preparación de las unidades para la importación de Azure mediante la herramienta Azure Import/Export.
* Crea automáticamente un trabajo de importación de Azure para el servicio Azure Import/Export en [Azure Portal](https://ms.portal.azure.com).

Cuando finaliza la carga de los datos de copia de seguridad en Azure, el servicio Azure Backup copia los datos de copia de seguridad en el almacén de copia de seguridad y se programan las copias de seguridad incrementales.

> [!NOTE]
> Para utilizar la herramienta de preparación de discos de Azure, asegúrese de haber instalado la actualización de Azure Backup de agosto de 2016 (o una versión superior), y realice con ella todos los pasos del flujo de trabajo. Si va a utilizar una versión anterior de Azure Backup, puede preparar la unidad SATA mediante la herramienta Azure Import/Export, tal y como se detalla en las secciones posteriores de este artículo.
>
>

## <a name="prerequisites"></a>Requisitos previos
* [Familiarícese con el flujo de trabajo de Azure Import/Export](../storage/common/storage-import-export-service.md).
* Antes de iniciar el flujo de trabajo, asegúrese de lo siguiente:
  * Se ha creado un almacén de Azure Backup.
  * Se han descargado las credenciales del almacén.
  * Se ha instalado el agente de Azure Backup en Windows Server o en el cliente de Windows o en el servidor de System Center Data Protection Manager y el equipo está registrado con el almacén de Azure Backup.
* [Descargue la configuración del archivo de publicación de Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) en el equipo desde el que piensa hacer copia de seguridad de los datos.
* Prepare una ubicación de ensayo que podría ser un recurso compartido de red o una unidad de disco adicional del equipo. La ubicación de ensayo es un almacenamiento transitorio y se utiliza temporalmente durante este flujo de trabajo. Asegúrese de que la ubicación de ensayo tiene suficiente espacio en disco para almacenar la copia inicial. Por ejemplo, si intenta realizar una copia de seguridad en un servidor de archivos de 500 GB, asegúrese de que el área de ensayo es de al menos 500 GB. (Se utilizará una cantidad menor gracias a la compresión).
* Asegúrese de que está usando una unidad compatible. El servicio Import/Export solo admite unidades de disco duro internas SSD de 2,5 pulgadas o SATA II o III de 2,5 o 3,5 pulgadas. Puede utilizar unidades de disco duro de hasta 10 TB. Vea la [documentación del servicio Azure Import/Export](../storage/common/storage-import-export-service.md#hard-disk-drives) para conocer el conjunto más reciente de unidades de disco que admite el servicio.
* Habilite BitLocker en el equipo al que está conectado el sistema de escritura de la unidad SATA.
* [Descargue la herramienta de Importación/Exportación](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) en el equipo al que está conectado el sistema de escritura de la unidad SATA. Este paso no es necesario si ha descargado e instalado la actualización de Azure Backup de agosto de 2016 (o posterior).

## <a name="workflow"></a>Flujo de trabajo
La información de esta sección le ayuda a completar el flujo de trabajo de copia de seguridad sin conexión, por lo que los datos se pueden entregar a un centro de datos de Azure y cargarse en Azure Storage. Si tiene alguna pregunta sobre el servicio de importación o cualquier aspecto del proceso, consulte la documentación sobre la [Información general del servicio de importación](../storage/common/storage-import-export-service.md) a la que se ha hecho referencia anteriormente.

### <a name="initiate-offline-backup"></a>Inicio de la copia de seguridad sin conexión
1. Cuando se programa una copia de seguridad, aparece la pantalla siguiente (en Windows Server, el cliente de Windows o System Center Data Protection Manager).

    ![Pantalla de importación](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aquí está la pantalla correspondiente en System Center Data Protection Manager:  <br/>
    ![Pantalla de importación de DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    La descripción de las entradas es la siguiente:

    * **Ubicación de ensayo**: la ubicación de almacenamiento temporal en la que se escribe la copia de seguridad inicial. Esta podría estar en un recurso compartido de red o en un equipo local. Si el equipo de copia y el equipo de origen son diferentes, se recomienda especificar la ruta de acceso completa de red de la ubicación de ensayo.
    * **Nombre del trabajo de importación de Azure**: el nombre único mediante el cual los servicios Azure Import y Azure Backup realizan el seguimiento de la transferencia de los datos enviados mediante discos a Azure.
    * **Configuración de publicación de Azure**: es un archivo XML que contiene información sobre el perfil de suscripción. También contiene credenciales seguras asociadas a su suscripción. Puede [descargar el archivo](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade). Proporcione la ruta de acceso local al archivo de configuración de publicación.
    * **Identificador de suscripción de Azure**: el identificador de la suscripción de Azure en la que planea iniciar el trabajo de importación de Azure. Si tiene varias suscripciones de Azure, use el identificador de la suscripción a la que quiere asociar el trabajo de importación.
    * **Cuenta de Azure Storage**: la cuenta de almacenamiento de la suscripción de Azure asociada al trabajo de importación de Azure.
    * **Contenedor de Azure Storage**: el nombre del blob de almacenamiento de destino de la cuenta de Azure Storage donde se importan los datos de este trabajo.

    > [!NOTE]
    > Aunque haya registrado el servidor en un almacén de Azure Recovery Services de [Azure Portal](https://portal.azure.com) para sus copias de seguridad y no utilice una suscripción del proveedor de soluciones en la nube (CSP), aún puede crear una cuenta de almacenamiento desde Azure Portal y usarla en el flujo de trabajo de copia de seguridad sin conexión.
    >
    >

     Guarde toda esta información porque tendrá que escribirla de nuevo en los siguientes pasos. Solo se necesita la *ubicación de ensayo* si usó la herramienta de preparación de discos de Azure para preparar los discos.    
2. Complete el flujo de trabajo y, después, seleccione **Back Up Now** (Iniciar copia de seguridad) en la consola de administración de Azure Backup para iniciar la copia de seguridad sin conexión. La copia de seguridad inicial se escribe en el área de ensayo como parte de este paso.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/backupnow.png)

    Para completar el flujo de trabajo correspondiente en System Center Data Protection Manager, haga clic con el botón derecho en el **Grupo de protección** y elija la opción **Crear punto de recuperación**. A continuación, elija la opción **Protección en línea** .

    ![Realizar copia de seguridad de DPM ahora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Una vez finalizada la operación, la ubicación de ensayo estará lista para usarse en la preparación del disco.

    ![Progreso de la copia de seguridad](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Preparación de la unidad SATA y creación de un trabajo de importación de Azure mediante la herramienta de preparación de discos de Azure
La herramienta de preparación de discos de Azure está disponible en el directorio de instalación del agente de Recovery Services (actualización de agosto de 2016 y posteriores) en la siguiente ruta de acceso.

   *\Microsoft**Azure**Recovery**Services* *Agent\Utils\*

1. Vaya al directorio y copie el directorio **AzureOfflineBackupDiskPrep** en un equipo de copia en el que se montarán las unidades de disco que se van a preparar. Compruebe que se cumplen los siguientes requisitos en el equipo de copia:

    * El equipo de copia puede acceder a la ubicación de ensayo del flujo de trabajo de propagación sin conexión mediante la misma ruta de acceso de red proporcionada durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión** .
    * BitLocker está habilitado en el equipo.
    * El equipo puede acceder a Azure Portal.

    Si es necesario, el equipo de copia puede ser el mismo que el equipo de origen.
2. Abra un símbolo del sistema con privilegios elevados en el equipo de copia, donde el directorio de la herramienta de preparación de discos de Azure será el directorio actual, y ejecute el siguiente comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | . | DESCRIPCIÓN |
    | --- | --- |
    | s:&lt;*Ruta de acceso de la ubicación de ensayo*&gt; |Entrada obligatoria que se utiliza para proporcionar la ruta de acceso a la ubicación de ensayo especificada en el flujo de trabajo de **inicio de la copia de seguridad sin conexión** . |
    | p:&lt;*Ruta de acceso a PublishSettingsFile*&gt; |Entrada obligatoria que se utiliza para proporcionar la ruta de acceso al archivo de **Configuración de publicación de Azure** especificado en el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. |

    > [!NOTE]
    > El valor de &lt;Ruta de acceso a PublishSettingFile&gt; es obligatorio cuando el equipo de copia y el equipo de origen son diferentes.
    >
    >

    Cuando se ejecuta el comando, la herramienta solicita la selección del trabajo de importación de Azure correspondiente a las unidades que deben estar preparadas. Si hay un solo trabajo de importación asociado con la ubicación de ensayo proporcionada, aparece una pantalla como esta.

    ![Entrada de la herramienta de preparación de discos de Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Escriba la letra de la unidad del disco montado (sin los dos puntos finales) que quiera preparar para la transferencia a Azure. Proporcione la confirmación del formato de la unidad cuando se le solicite.

    La herramienta comienza a preparar entonces el disco con los datos de copia de seguridad. Puede que deba conectar discos adicionales si se lo pide la herramienta, si el disco proporcionado no tiene espacio suficiente para los datos de copia de seguridad. <br/>

    Al final de la ejecución correcta de la herramienta, uno o varios discos que proporcionó estarán preparados para el envío a Azure. Además, se creará en Azure Portal un trabajo de importación con el nombre que proporcionó durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. Finalmente, la herramienta muestra la dirección de envío al centro de datos de Azure adonde es necesario enviar los discos, junto con el vínculo para encontrar el trabajo de importación en Azure Portal.

    ![Preparación de discos de Azure finalizada](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Envíe los discos a la dirección proporcionada por la herramienta y guarde el número de seguimiento como referencia futura.<br/>

5. Cuando vaya al vínculo que mostró la herramienta, verá la cuenta de Azure Storage que especificó en el flujo de trabajo de **inicio de la copia de seguridad sin conexión** . Aquí puede ver el trabajo de importación recién creado en la pestaña **IMPORTAR/EXPORTAR** de la cuenta de almacenamiento.

    ![Trabajo de importación creado](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Haga clic en **INFORMACIÓN DE ENVÍO** en la parte inferior de la página para actualizar los detalles de contacto, tal como se muestra en la siguiente pantalla. Microsoft usa esta información para devolverle los discos una vez finalizado el trabajo de importación.

    ![Información de contacto](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Escriba los detalles de envío en la siguiente pantalla. Proporcione los **detalles del transportista** y el **número de seguimiento** correspondientes a los discos que envió al centro de datos de Azure.

    ![INFORMACIÓN DE ENVÍO](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>Finalización del flujo de trabajo
Una vez completado el trabajo de importación, los datos de la copia de seguridad inicial están disponibles en la cuenta de almacenamiento. El agente de Recovery Services copia entonces el contenido de los datos de esta cuenta en el almacén de Backup o en el almacén de Recovery Services, según corresponda. En la siguiente copia de seguridad programada, el agente de Azure Backup realiza la copia de seguridad incremental sobre la copia de seguridad inicial.

> [!NOTE]
> Las secciones siguientes se aplican a los usuarios de versiones anteriores de Azure Backup que no tienen acceso a la herramienta de preparación de discos de Azure.
>
>

### <a name="prepare-a-sata-drive"></a>Preparación de una unidad de disco SATA
1. Descargue la [herramienta Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) en el equipo de copia. Asegúrese de que la ubicación de ensayo es accesible desde el equipo en el que tiene previsto ejecutar el siguiente conjunto de comandos. Si es necesario, el equipo de copia puede ser el mismo que el equipo de origen.

2. Descomprima el archivo WAImportExport.zip. Ejecute la herramienta WAImportExport que formatea la unidad SATA, escriba los datos de copia de seguridad en esa unidad y cífrelos. Antes de ejecutar el siguiente comando, asegúrese de que BitLocker esté habilitado en el equipo. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Si ha instalado la actualización de Azure Backup de agosto de 2016 (o versiones posteriores), asegúrese de que la ubicación de ensayo especificada sea la misma que la mostrada en la pantalla **Back Up Now** (Iniciar copia de seguridad) y que contenga los archivos AIB y Base Blob.
    >
    >

| . | DESCRIPCIÓN |
| --- | --- |
| /j:<*ArchivoDiario*> |La ruta de acceso al archivo de diario. Cada unidad debe tener exactamente un archivo de diario. El archivo de diario no debe estar en la unidad de destino. La extensión de archivo de diario es .jrn y se crea como parte de la ejecución de este comando. |
| /id:<*IdSesión*> |El identificador de sesión identifica una sesión de copia. Se utiliza para asegurar la recuperación precisa de una sesión de copia interrumpida. Los archivos que se copian en una sesión de copia se almacenan en un directorio con el mismo nombre que el identificador de sesión en la unidad de destino. |
| /sk:<*ClaveCuentaAlmacenamiento*> |La clave de cuenta para la cuenta de almacenamiento en la que se importan los datos. La clave tiene que ser igual a la que se especificó durante la creación del grupo de protección o directiva de copia de seguridad. |
| /BlobType |El tipo de blob. Este flujo de trabajo se realizará correctamente solo si se especifica **PageBlob** . Esta no es la opción predeterminada y se debería mencionar en este comando. |
| /t:<*LetraUnidadDestino*> |La letra de unidad del disco duro de destino para la sesión de copia actual, sin los dos puntos finales. |
| /format |La opción para formatear la unidad. Especifique este parámetro cuando la unidad debe tener formato; de lo contrario, omítalo. Antes de que la herramienta formatee la unidad, se le pedirá una confirmación desde la consola. Para suprimir la confirmación, especifique el parámetro /silentmode. |
| /encrypt |La opción para cifrar la unidad. Especifique este parámetro cuando la unidad todavía no se ha cifrado con BitLocker y debe cifrarse mediante la herramienta. Si la unidad ya se ha cifrado con BitLocker, omita este parámetro y especifique el parámetro /bk y proporcione la clave de BitLocker existente. Si especifica el parámetro /format, también debe especificar el parámetro /encrypt. |
| /srcdir:<*DirectorioOrigen*> |El directorio de origen que contiene archivos que se copiarán en la unidad de destino. Asegúrese de que el nombre de directorio especificado tiene una ruta de acceso completa en lugar de relativa. |
| /dstdir:<*DirectorioVirtualBlobDestino*> |La ruta de acceso al directorio virtual de destino en la cuenta de Azure Storage. Asegúrese de utilizar nombres de contenedor válidos al especificar los directorios virtuales de destino o blobs. Tenga en cuenta que los nombres de contenedor deben estar en minúsculas.  Este nombre de contenedor tiene que ser el mismo que el especificado durante la creación del grupo de protección o directiva de copia de seguridad. |

> [!NOTE]
> Un archivo de diario se creará en la carpeta WAImportExport, que captura la información completa del flujo de trabajo. Necesitará este archivo al crear un trabajo de importación en Azure Portal.
>
>

  ![Salida de PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Creación de un trabajo de importación en Azure Portal
1. Vaya a su cuenta de almacenamiento en [Azure Portal](https://ms.portal.azure.com/), haga clic en **Import/Export** y en **Crear trabajo de importación** en el panel de tareas.

    ![Pestaña Importar/Exportar de Azure Portal](./media/backup-azure-backup-import-export/azureportal.png)

2. En el paso 1 del asistente, indique que ha preparado su unidad y que tiene el archivo de diario de la unidad disponible.

3. En el paso 2 del asistente, proporcione la información de contacto de la persona responsable de este trabajo de importación.

4. En el paso 3, cargue los archivos de diario de unidad que haya obtenido en la sección anterior.

5. En el paso 4, escriba un nombre descriptivo para el trabajo de importación que se especificó durante la creación del grupo de protección o directiva de copia de seguridad. El nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido se usa para realizar el seguimiento de los trabajos mientras están en curso y cuando se han completado.

6. A continuación, seleccione en la lista la región de su centro de datos. La región del centro de datos indicará el centro de datos y la dirección donde debe enviar el paquete.

    ![Selección de la región del centro de datos](./media/backup-azure-backup-import-export/dc.png)

7. En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft usará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación.

8. Envíe el disco y escriba el número de seguimiento para realizar un seguimiento del estado del envío. Una vez que el disco llega al centro de datos, se copia en la cuenta de almacenamiento y el estado se actualiza.

    ![Estado completado](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>Finalización del flujo de trabajo
Cuando los datos de la copia de seguridad inicial están disponibles en la cuenta de almacenamiento, el agente de Microsoft Azure Recovery Services copia el contenido de los datos de esta cuenta en el almacén de Backup o en el almacén de Recovery Services, según corresponda. En la siguiente copia de seguridad de programación, el agente de Azure Backup realiza la copia de seguridad incremental sobre la copia de seguridad inicial.

## <a name="next-steps"></a>pasos siguientes
* Si tiene dudas acerca del flujo de trabajo de Azure Import/Export, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../storage/common/storage-import-export-service.md).
* Vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre la copia de seguridad sin conexión de Azure si tiene alguna pregunta sobre el flujo de trabajo.
