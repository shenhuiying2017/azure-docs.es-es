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
ms.date: 2/6/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 306c4c7498601cf3ab7e918ba6ce6bfef173236a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flujo de trabajo de copia de seguridad sin conexión en Azure Backup
El servicio Azure Backup presenta varias eficiencias integradas para ahorrar costos de almacenamiento y red durante las copias de seguridad iniciales 'completas' de datos en Azure. Las copias de seguridad iniciales completas transfieren grandes cantidades de datos y requieren un mayor ancho de banda de red en comparación con las copias de seguridad sucesivas que solo transfieren los cambios diferenciales e incrementales. Azure Backup permite comprimir las copias de seguridad iniciales. A través del proceso de propagación sin conexión, Azure Backup puede usar discos para cargar los datos comprimidos iniciales de copia de seguridad sin conexión en Azure.  

El proceso de propagación sin conexión de Azure Backup se integra estrechamente con el [servicio Azure Import/Export](../storage/common/storage-import-export-service.md) que permite transferir datos a Azure mediante discos. Si tiene terabytes (TB) de datos de copia de seguridad inicial que se deben transferir a través de una red de latencia alta y ancho de banda de red bajo, puede usar el flujo de trabajo de propagación sin conexión para enviar la copia de seguridad inicial de una o varias unidades de disco duro a un centro de datos de Azure. Este artículo proporciona información general y pasos detallados para completar este flujo de trabajo.


## <a name="overview"></a>Información general
Con la funcionalidad de propagación sin conexión de Azure Backup y Azure Import/Export, cargar los datos sin conexión en Azure mediante discos es un proceso sencillo. El proceso de copia de seguridad sin conexión implica los pasos siguientes:

> [!div class="checklist"]
> * Los datos de copia de seguridad, en lugar de enviarse a través de la red, se escriben en una *ubicación de almacenamiento provisional*.
> * A continuación, los datos de la *ubicación de almacenamiento provisional* se escriben en uno o más discos SATA mediante la utilidad *AzureOfflineBackupDiskPrep*.
> * La utilidad crea automáticamente un trabajo de importación de Azure.
> * Las unidades SATA se envían entonces al centro de datos de Azure más próximo.
> * Cuando finaliza la carga de los datos de copia de seguridad en Azure, el servicio Azure Backup copia los datos de copia de seguridad en el almacén de copia de seguridad y se programan las copias de seguridad incrementales.

## <a name="supported-configurations"></a>Configuraciones admitidas 
La copia de seguridad sin conexión es compatible con todos los modelos de implementación de Azure Backup que realizan copias de seguridad de datos sin conexión desde entornos locales a Microsoft Cloud. Esto incluye:

> [!div class="checklist"]
> * Copia de seguridad de archivos y carpetas con el agente de Microsoft Azure Recovery Services (MARS) o el agente de Azure Backup. 
> * Copia de seguridad de todas las cargas de trabajo y archivos con System Center Data Protection Manager (SC DPM). 
> * Copia de seguridad de todas las cargas de trabajo y archivos con Microsoft Azure Backup Server. <br/>

   > [!NOTE]
   > No se admite la copia de seguridad sin conexión para copias de seguridad de estado del sistema realizadas mediante el agente de Azure Backup. 

## <a name="prerequisites"></a>requisitos previos
Asegúrese de que se cumplen los siguientes requisitos previos antes de iniciar el flujo de trabajo de copia de seguridad sin conexión:
* Se ha creado [el almacén de Recovery Services](backup-azure-recovery-services-vault-overview.md). Para crear uno, consulte los pasos de [este artículo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Se ha instalado el agente de Azure Backup, Azure Backup Server o SC DPM en Windows Server o en un cliente de Windows, según corresponda, y el equipo está registrado con el almacén de Recovery Services. Asegúrese de que solo se utiliza la [versión más reciente de Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525). 
* [Descargue el archivo de configuración de publicación de Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) en el equipo desde el que piensa hacer copia de seguridad de los datos. La suscripción desde la que descargue el archivo de configuración de publicación puede ser diferente a la suscripción que contiene el almacén de Recovery Services. Si la suscripción se encuentra en nubes soberanas de Azure, use los vínculos siguientes según corresponda para descargar el archivo de configuración de publicación de Azure.

    | Región de nubes soberanas | Vínculo del archivo de configuración de publicación de Azure |
    | --- | --- |
    | Estados Unidos | [Vínculo](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Vínculo](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Se ha creado una cuenta de Azure Storage con el modelo de implementación *clásica* en la suscripción desde la que descargó el archivo de configuración de publicación, tal y como se muestra a continuación: 

 ![Creación de una cuenta de almacenamiento clásica](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Se crea una ubicación de almacenamiento provisional, que puede ser un recurso compartido de red o cualquier unidad adicional en el equipo, interna o externa, con suficiente espacio en disco para almacenar la copia inicial. Por ejemplo, si intenta realizar una copia de seguridad en un servidor de archivos de 500 GB, asegúrese de que el área de ensayo es de al menos 500 GB. (Se utilizará una cantidad menor gracias a la compresión).
* Con respecto a los discos que se enviarán a Azure, asegúrese de que solo se utilizan unidades de disco duro SSD de 2,5 pulgadas o SATA II/III de 2,5 o 3,5 pulgadas internas. Puede utilizar unidades de disco duro de hasta 10 TB. Vea la [documentación del servicio Azure Import/Export](../storage/common/storage-import-export-service.md#hard-disk-drives) para conocer el conjunto más reciente de unidades de disco que admite el servicio.
* Las unidades SATA deben estar conectadas a un equipo (llamado *equipo de copia*) desde donde se realiza la copia de los datos de copia de seguridad de la *ubicación de almacenamiento provisional* a SATA. Asegúrese de que BitLocker está habilitado en el *equipo de copia*. 

## <a name="workflow"></a>Flujo de trabajo
La información de esta sección le ayuda a completar el flujo de trabajo de copia de seguridad sin conexión, por lo que los datos se pueden entregar a un centro de datos de Azure y cargarse en Azure Storage. Si tiene alguna pregunta sobre el servicio de importación o cualquier aspecto del proceso, consulte la documentación sobre la [Información general del servicio de importación](../storage/common/storage-import-export-service.md) a la que se ha hecho referencia anteriormente.

### <a name="initiate-offline-backup"></a>Inicio de la copia de seguridad sin conexión
1. Cuando se programa una copia de seguridad, aparece la pantalla siguiente (en Windows Server, el cliente de Windows o System Center Data Protection Manager).

    ![Pantalla de importación](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aquí está la pantalla correspondiente en System Center Data Protection Manager:  <br/>
    ![Pantalla de importación de servidor de SC DPM y Azure Backup](./media/backup-azure-backup-import-export/dpmoffline.png)

    La descripción de las entradas es la siguiente:

    * **Ubicación de ensayo**: la ubicación de almacenamiento temporal en la que se escribe la copia de seguridad inicial. La ubicación de almacenamiento provisional podría estar en un recurso compartido de red o en un equipo local. Si el equipo de copia y el equipo de origen son diferentes, se recomienda especificar la ruta de acceso completa de red de la ubicación de ensayo.
    * **Nombre del trabajo de importación de Azure**: el nombre único mediante el cual los servicios Azure Import y Azure Backup realizan el seguimiento de la transferencia de los datos enviados mediante discos a Azure.
    * **Configuración de publicación de Azure**: proporcione la ruta de acceso local al archivo de configuración de publicación.
    * **Identificador de suscripción de Azure**: el identificador de la suscripción de Azure con la que descargó el archivo de configuración de publicación de Azure. 
    * **Cuenta de Azure Storage**: el nombre de la cuenta de almacenamiento de la suscripción de Azure asociada al archivo de configuración de publicación de Azure.
    * **Contenedor de Azure Storage**: el nombre del blob de almacenamiento de destino de la cuenta de Azure Storage donde se importan los datos de copia de seguridad.

     Guarde la *ubicación de almacenamiento provisional* y el *nombre del trabajo de importación de Azure* proporcionados, ya que esto es necesario para preparar los discos.  
     
2. Complete el flujo de trabajo y, para iniciar la copia de seguridad sin conexión, haga clic en **Back Up Now** (Iniciar copia de seguridad) en la consola de administración del agente de Azure Backup. La copia de seguridad inicial se escribe en el área de ensayo como parte de este paso.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/backupnow.png)

    Para completar el flujo de trabajo correspondiente en System Center Data Protection Manager o Azure Backup Server, haga clic con el botón derecho en el **Grupo de protección** y elija la opción **Crear punto de recuperación**. A continuación, elija la opción **Protección en línea** .

    ![Inicio de copia de seguridad en SC DPM y Azure Backup Server](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Una vez finalizada la operación, la ubicación de ensayo estará lista para usarse en la preparación del disco.

    ![Progreso de la copia de seguridad](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparación de unidades SATA y envío a Azure
La utilidad *AzureOfflineBackupDiskPrep* se usa para preparar las unidades SATA que se envían al centro de datos de Azure más próximo. Esta utilidad está disponible en el directorio de instalación del agente de Recovery Services en la ruta de acceso siguiente:

   *\Microsoft**Azure**Recovery**Services* *Agent\Utils\*

1. Vaya al directorio y copie el directorio de **AzureOfflineBackupDiskPrep** en un equipo de copia en el que se conectarán las unidades SATA que se van a preparar. Compruebe que se cumplen los siguientes requisitos en el equipo de copia:

    * El equipo de copia puede acceder a la ubicación de ensayo del flujo de trabajo de propagación sin conexión mediante la misma ruta de acceso de red proporcionada durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión** .
    * BitLocker está habilitado en el equipo de copia.
    * El equipo de copia puede acceder a Azure Portal.

    Si es necesario, el equipo de copia puede ser el mismo que el equipo de origen. 
    
    > [!IMPORTANT] 
    > Si el equipo de origen es una máquina virtual, es obligatorio utilizar un servidor físico o equipo cliente diferentes como equipo de copia.
    
    
2. Abra un símbolo del sistema con privilegios elevados en el equipo de copia con el directorio de la utilidad *AzureOfflineBackupDiskPrep* como directorio actual, y ejecute el siguiente comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | . | DESCRIPCIÓN |
    | --- | --- |
    | s:&lt;*Ruta de acceso de la ubicación de ensayo*&gt; |Entrada obligatoria que se utiliza para proporcionar la ruta de acceso a la ubicación de ensayo especificada en el flujo de trabajo de **inicio de la copia de seguridad sin conexión** . |
    | p:&lt;*Ruta de acceso a PublishSettingsFile*&gt; |Entrada obligatoria que se utiliza para proporcionar la ruta de acceso al archivo de **Configuración de publicación de Azure** especificado en el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. |

    > [!NOTE]
    > El valor de &lt;Ruta de acceso a AzurePublishSettingFile&gt; es obligatorio cuando el equipo de copia y el equipo de origen son diferentes.
    >
    >

    Cuando se ejecuta el comando, la utilidad solicita la selección del trabajo de importación de Azure correspondiente a las unidades que deben estar preparadas. Si hay un solo trabajo de importación asociado con la ubicación de ensayo proporcionada, aparece una pantalla como esta.

    ![Entrada de la herramienta de preparación de discos de Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Escriba la letra de la unidad del disco montado (sin los dos puntos finales) que quiera preparar para la transferencia a Azure. Proporcione la confirmación del formato de la unidad cuando se le solicite.

    La herramienta comienza entonces a preparar el disco y a copiar los datos de copia de seguridad. Puede que deba conectar discos adicionales si se lo pide la herramienta, si el disco proporcionado no tiene espacio suficiente para los datos de copia de seguridad. <br/>

    Al final de la ejecución correcta de la herramienta, uno o varios discos que proporcionó estarán preparados para el envío a Azure. Además, se creará en Azure un trabajo de importación con el nombre que proporcionó durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. Finalmente, la herramienta muestra la dirección de envío al centro de datos de Azure adonde es necesario enviar los discos.

    ![Preparación de discos de Azure finalizada](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. Al final de la ejecución del comando, también verá la opción para actualizar la información de envío, tal y como se muestra a continuación:

    ![Opción para actualizar la información de envío](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Puede escribir los detalles inmediatamente. La herramienta le guía a través del proceso, que requiere que especifique diversos datos. Sin embargo, si no tiene información como el número de seguimiento u otros datos relacionados con el envío, puede finalizar la sesión. Los pasos para actualizar los detalles de envío se describen más adelante en este artículo. 

6. Envíe los discos a la dirección proporcionada por la herramienta y guarde el número de seguimiento como referencia futura.

   > [!IMPORTANT] 
   > El número de seguimiento no puede ser el mismo para distintos trabajos de importación de Azure. Asegúrese de que las unidades preparadas con la utilidad en un trabajo de importación de Azure se envían juntas en un único paquete y de que el paquete tenga un número de seguimiento exclusivo. No combine unidades preparadas como parte de **diferentes** trabajos de importación de Azure en un único paquete. 

5. Si dispone del número de seguimiento, vaya al equipo de origen, que está esperando a la finalización del trabajo de importación, y ejecute el siguiente comando en un símbolo del sistema con privilegios elevados con el directorio de la utilidad *AzureOfflineBackupDiskPrep* como directorio actual: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   También puede ejecutar el siguiente comando desde otro equipo, como el *equipo de copia*, con el directorio de la utilidad *AzureOfflineBackupDiskPrep* como directorio actual:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | . | DESCRIPCIÓN |
    | --- | --- |
    | u: | Entrada obligatoria usada para actualizar los detalles de envío de un trabajo de importación de Azure |
    | s:&lt;*Ruta de acceso de la ubicación de ensayo*&gt; | Entrada obligatoria cuando el comando no se ejecuta en el equipo de origen. Se utiliza para proporcionar la ruta de acceso a la ubicación de almacenamiento provisional especificada en el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. |
    | p:&lt;*Ruta de acceso a PublishSettingsFile*&gt; | Entrada obligatoria cuando el comando no se ejecuta en el equipo de origen. Se utiliza para proporcionar la ruta de acceso al archivo de **Configuración de publicación de Azure** especificado en el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. |
    
    La utilidad detecta automáticamente el trabajo de importación que está esperando el equipo de origen o los trabajos de importación asociados a la ubicación de almacenamiento provisional cuando el comando se ejecuta en un equipo diferente. Después proporciona la opción para actualizar la información de envío a través de una serie de valores que deben especificarse, tal y como se muestra a continuación: 
    
    ![Especificación de la información de envío](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Una vez especificados todos los valores, revise cuidadosamente los detalles y confirme la información de envío que ha proporcionado escribiendo *yes*. 

    ![Revisión de la información de envío](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Al actualizar correctamente la información de envío, la utilidad proporciona una ubicación local donde se almacenan los detalles de envío que ha especificado, tal y como se muestra a continuación. 

    ![Almacenamiento de la información de envío](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Asegúrese de que las unidades lleguen el centro de datos de Azure en el plazo de dos semanas tras proporcionar la información de envío mediante la utilidad *AzureOfflineBackupDiskPrep*. De lo contrario, es posible que las unidades no se procesen.  

Cuando haya completado los pasos anteriores, el centro de datos de Azure estará listo para recibir las unidades y procesarlas para transferir los datos de copia de seguridad de las unidades a la cuenta de almacenamiento de Azure clásica que ha creado. 

### <a name="time-to-process-the-drives"></a>Tiempo para procesar las unidades 
El tiempo que se tarda en procesar un trabajo de importación de Azure varía en función de determinados factores, como el tiempo de envío, el tipo de trabajo, el tipo y el tamaño de los datos copiados o el tamaño de los discos proporcionados. El servicio Azure Import/Export no tiene un Acuerdo de Nivel de Servicio pero, después de que se reciban los discos, se esfuerza por completar la copia de los datos de copia de seguridad en su cuenta de almacenamiento de Azure en un período de tiempo de entre 7 y 10 días. La siguiente sección detalla cómo puede supervisar el estado del trabajo de importación de Azure. 

### <a name="monitoring-azure-import-job-status"></a>Supervisión del estado del trabajo de importación de Azure
Mientras que las unidades están en tránsito o en el centro de datos de Azure para copiarse en la cuenta de almacenamiento, el agente de Azure Backup, SC DPM o la consola de Azure Backup Server en el equipo de origen muestran el estado de trabajo siguiente para las copias de seguridad programadas. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Siga los pasos siguientes para comprobar el estado del trabajo de importación. 
1. Abra un símbolo del sistema con privilegios elevados en el equipo de origen y ejecute el comando siguiente:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  El resultado muestra el estado actual del trabajo de importación, tal y como se muestra a continuación: 

    ![Comprobación del estado del trabajo de importación](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para más información sobre los distintos estados del trabajo de importación de Azure, consulte [este artículo](../storage/common/storage-import-export-service.md#how-does-the-azure-importexport-service-work).

### <a name="complete-the-workflow"></a>Finalización del flujo de trabajo
Una vez completado el trabajo de importación, los datos de la copia de seguridad inicial están disponibles en la cuenta de almacenamiento. En la siguiente copia de seguridad programada, Azure Backup copia el contenido de los datos de la cuenta de almacenamiento en el almacén de Recovery Services, tal y como se muestra a continuación: 

   ![Copia de datos en el almacén de Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

En la siguiente copia de seguridad programada, Azure Backup realiza la copia de seguridad incremental sobre la copia de seguridad inicial.

## <a name="next-steps"></a>pasos siguientes
* Si tiene dudas acerca del flujo de trabajo de Azure Import/Export, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../storage/common/storage-import-export-service.md).
* Vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre la copia de seguridad sin conexión de Azure si tiene alguna pregunta sobre el flujo de trabajo.
