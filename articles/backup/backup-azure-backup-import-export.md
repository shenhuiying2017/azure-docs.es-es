---
title: 'Azure Backup: copia de seguridad sin conexión o propagación inicial mediante el servicio Azure Import/Export | Microsoft Docs'
description: Descubra cómo Azure Backup permite enviar datos fuera de la red mediante el servicio Azure Import/Export. Este artículo explica la propagación sin conexión de los datos de copia de seguridad iniciales mediante el servicio de Azure Import/Export.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/8/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 801de343ebb88394f04a65236997f9ec80a2f535
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939724"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flujo de trabajo de copia de seguridad sin conexión en Azure Backup
El servicio Azure Backup presenta varias eficiencias integradas para ahorrar costos de almacenamiento y red durante las copias de seguridad iniciales 'completas' de datos en Azure. Las copias de seguridad iniciales completas transfieren grandes cantidades de datos y requieren un mayor ancho de banda de red en comparación con las copias de seguridad sucesivas que solo transfieren los cambios diferenciales e incrementales. A través del proceso de propagación sin conexión, Azure Backup puede usar discos para cargar los datos de copia de seguridad sin conexión en Azure.

El proceso de propagación sin conexión de Azure Backup se integra estrechamente con el [servicio Azure Import/Export](../storage/common/storage-import-export-service.md) que permite transferir los datos de la copia de seguridad inicial a Azure mediante discos. Si tiene terabytes (TB) de datos de copia de seguridad inicial que se deben transferir a través de una red de latencia alta y ancho de banda de red bajo, puede usar el flujo de trabajo de propagación sin conexión para enviar la copia de seguridad inicial de una o varias unidades de disco duro a un centro de datos de Azure. En la imagen siguiente se proporciona información general de los pasos del flujo de trabajo.

  ![información general del proceso del flujo de trabajo de importación sin conexión](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

El proceso de copia de seguridad sin conexión implica los pasos siguientes:

1. En lugar de enviar los datos de la copia de seguridad a través de la red, escríbalos en una ubicación de almacenamiento provisional.
2. Use la utilidad AzureOfflineBackupDiskPrep para escribir los datos de la ubicación de almacenamiento provisional en uno o más discos SATA.
3. Como parte del trabajo de preparación, la utilidad AzureOfflineBackupDiskPrep crea un trabajo de importación de Azure. Envíe las unidades de disco SATA al centro de datos de Azure más cercano y haga referencia al trabajo de importación para conectar las actividades.
4. En el centro de datos de Azure, los datos de los discos se copian en una cuenta de almacenamiento de Azure.
5. Azure Backup copia los datos de la copia de seguridad de la cuenta de almacenamiento al almacén de Recovery Services y se programan copias de seguridad incrementales.

## <a name="supported-configurations"></a>Configuraciones admitidas 
Las características o cargas de trabajo de Azure Backup siguientes admiten el uso de la copia de seguridad sin conexión.

> [!div class="checklist"]
> * Copia de seguridad de archivos y carpetas con el agente de Microsoft Azure Recovery Services (MARS), también conocido como el agente de Azure Backup. 
> * Copia de seguridad de todas las cargas de trabajo y archivos con System Center Data Protection Manager (SC DPM). 
> * Copia de seguridad de todas las cargas de trabajo y archivos con Microsoft Azure Backup Server. <br/>

   > [!NOTE]
   > No se admite la copia de seguridad sin conexión para copias de seguridad de estado del sistema realizadas mediante el agente de Azure Backup. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>requisitos previos

  > [!NOTE]
  > El flujo de trabajo y los requisitos previos siguientes solo se aplican a la copia de seguridad sin conexión de archivos y carpetas con el [agente MARS más reciente](https://aka.ms/azurebackup_agent). Para crear copias de seguridad sin conexión de las cargas de trabajo con System Center DPM o Azure Backup Server, consulte [este artículo](backup-azure-backup-server-import-export-.md). 

Antes de iniciar el flujo de trabajo de copia de seguridad sin conexión, complete estos requisitos previos: 
* Cree un [almacén de Recovery Services](backup-azure-recovery-services-vault-overview.md). Para crear uno, consulte los pasos de [este artículo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Asegúrese de que solo la [versión más reciente del agente de Azure Backup](https://aka.ms/azurebackup_agent) se instaló en el cliente Windows Server/Windows, según corresponda, y que el equipo está registrado con el almacén de Recovery Services.
* Se requiere Azure PowerShell 3.7.0 o una versión superior en el equipo que ejecuta el agente de Azure Backup. Se recomienda que [instale la versión más reciente de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).
* En el equipo donde se ejecuta el agente de Azure Backup, asegúrese de que esté instalado Microsoft Edge o Internet Explorer 11 y que JavaScript esté habilitado. 
* Cree una cuenta de Azure Storage en la misma suscripción que el almacén de Recovery Services. 
* Asegúrese de tener los [permisos necesarios](../azure-resource-manager/resource-group-create-service-principal-portal.md) para crear la aplicación de Azure Active Directory. El flujo de trabajo de la copia de seguridad sin conexión crea una aplicación de Azure Active Directory en la suscripción asociada con la cuenta de Azure Storage. El objetivo de la aplicación es proporcionar Azure Backup con el acceso seguro y limitado al servicio Azure Import que se requiere para el flujo de trabajo de la copia de seguridad sin conexión. 
* Registre el proveedor de recursos Microsoft.ImportExport con la suscripción que contiene la cuenta de Azure Storage. Para registrar el proveedor de recursos:
    1. En el menú principal, haga clic en **Suscripciones**.
    2. Si está suscrito a varias suscripciones, seleccione la suscripción que usa para la copia de seguridad sin conexión. Si solo usa una, aparecerá esa suscripción.
    3. En el menú de la suscripción, haga clic en **Proveedores de recursos** para ver la lista de proveedores.
    4. En la lista de proveedores, desplácese hacia abajo hasta Microsoft.ImportExport. Si el estado es NotRegistered, haga clic en **Registrar**.
    ![registro del proveedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)
* Se crea una ubicación de almacenamiento provisional, que puede ser un recurso compartido de red o cualquier unidad adicional en el equipo, interna o externa, con suficiente espacio en disco para almacenar la copia inicial. Por ejemplo, si intenta realizar una copia de seguridad en un servidor de archivos de 500 GB, asegúrese de que el área de ensayo es de al menos 500 GB. (Se utilizará una cantidad menor gracias a la compresión).
* Cuando envíe los discos a Azure, use solo SSD de 2,5 pulgadas o discos duros internos SATA II/III de 2,5 o 3,5 pulgadas. Puede utilizar unidades de disco duro de hasta 10 TB. Vea la [documentación del servicio Azure Import/Export](../storage/common/storage-import-export-service.md#hard-disk-drives) para conocer el conjunto más reciente de unidades de disco que admite el servicio.
* Las unidades de disco SATA deben estar conectadas a un equipo (llamado *equipo de copia*) desde donde se realiza la copia de los datos de copia de seguridad de la *ubicación de almacenamiento provisional* a SATA. Asegúrese de que BitLocker está habilitado en el *equipo de copia*.

## <a name="workflow"></a>Flujo de trabajo
En esta sección se describe el flujo de trabajo de la copia de seguridad sin conexión, por lo que los datos se pueden entregar a un centro de datos de Azure y cargarse en Azure Storage. Si tiene alguna pregunta sobre el servicio Import o cualquier aspecto del proceso, consulte la documentación sobre la [información general del servicio de importación](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Inicio de la copia de seguridad sin conexión
1. Cuando programe una copia de seguridad en el agente MARS, verá la pantalla siguiente.

    ![Pantalla de importación](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  La descripción de las entradas es la siguiente:

    * **Ubicación de ensayo**: la ubicación de almacenamiento temporal en la que se escribe la copia de seguridad inicial. La ubicación de almacenamiento provisional podría estar en un recurso compartido de red o en un equipo local. Si el equipo de copia y el equipo de origen son diferentes, se recomienda especificar la ruta de acceso completa de red de la ubicación de ensayo.
    * **Cuenta de almacenamiento de Azure Resource Manager**: nombre de la cuenta de almacenamiento de tipo Resource Manager en cualquier suscripción de Azure.
    * **Contenedor de Azure Storage**: nombre del blob de almacenamiento de destino de la cuenta de Azure Storage donde se importan los datos de copia de seguridad antes de copiarlos en el almacén de Recovery Services.
    * **Identificador de suscripción de Azure**: identificador de la suscripción de Azure donde se crea la cuenta de Azure Storage.
    * **Nombre del trabajo de importación de Azure**: el nombre único mediante el cual los servicios Azure Import y Azure Backup realizan el seguimiento de la transferencia de los datos enviados mediante discos a Azure. 
  
  Proporcione las entradas en la pantalla y haga clic en **Siguiente**. Guarde la *ubicación de almacenamiento provisional* y el *nombre del trabajo de importación de Azure* proporcionados, ya que esta información es necesaria para preparar los discos.

2. Cuando se le solicite, inicie sesión en su suscripción de Azure. Debe iniciar sesión para que Azure Backup pueda crear la aplicación de Azure Active Directory y proporcionar los permisos necesarios para acceder al servicio Azure Import.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/azurelogin.png)

3. Complete el flujo de trabajo y, en la consola del agente de Azure Backup, haga clic en **Realizar copia de seguridad ahora**.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/backupnow.png)

4. En la página de confirmación del asistente, haga clic en **Back Up** (Hacer una copia de seguridad). La copia de seguridad inicial se escribe en el área de ensayo como parte de esta configuración.

   ![Confirmación de que está listo para realizar la copia de seguridad ahora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Una vez finalizada la operación, la ubicación de ensayo estará lista para usarse en la preparación del disco.

   ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparación de unidades SATA y envío a Azure
La utilidad *AzureOfflineBackupDiskPrep* prepara las unidades de disco SATA que se envían al centro de datos de Azure más próximo. Esta utilidad está disponible en el directorio de instalación del agente de Azure Backup (en la ruta de acceso siguiente):

   *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Vaya al directorio y copie el directorio **AzureOfflineBackupDiskPrep** en otro equipo donde estén conectadas las unidades de disco SATA. En el equipo con las unidades de disco SATA conectadas, asegúrese de lo siguiente:

    * El equipo de copia puede acceder a la ubicación de ensayo del flujo de trabajo de propagación sin conexión mediante la misma ruta de acceso de red proporcionada durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión** .
    * BitLocker está habilitado en el equipo de copia.
    * Está instalado Azure PowerShell 3.7.0 o una versión superior.
    * Los exploradores compatibles más recientes (Edge o Internet Explorer 11) están instalados y JavaScript, habilitado. 
    * El equipo de copia puede acceder a Azure Portal. Si es necesario, el equipo de copia puede ser el mismo que el equipo de origen.
    
    > [!IMPORTANT] 
    > Si el equipo de origen es una máquina virtual, el equipo de copia debe estar en una máquina cliente o un servidor físico distinto del equipo de origen.

2. Abra un símbolo del sistema con privilegios elevados en el equipo de copia con el directorio de la utilidad *AzureOfflineBackupDiskPrep* como directorio actual, y ejecute el siguiente comando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | . | DESCRIPCIÓN |
    | --- | --- |
    | s:&lt;*Ruta de acceso de la ubicación de ensayo*&gt; |Entrada obligatoria que se usa para proporcionar la ruta de acceso a la ubicación de ensayo especificada en el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. |
    | p:&lt;*Ruta de acceso a PublishSettingsFile*&gt; |Entrada obligatoria que se utiliza para proporcionar la ruta de acceso al archivo de **Configuración de publicación de Azure** especificado en el flujo de trabajo de **inicio de la copia de seguridad sin conexión**. |

    Cuando se ejecuta el comando, la utilidad solicita la selección del trabajo de importación de Azure correspondiente a las unidades que deben estar preparadas. Si hay un solo trabajo de importación asociado con la ubicación de ensayo proporcionada, aparece una pantalla como esta.

    ![Entrada de la herramienta de preparación de discos de Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Escriba la letra de la unidad del disco montado (sin los dos puntos finales) que quiera preparar para la transferencia a Azure. 
4. Proporcione la confirmación del formato de la unidad cuando se le solicite.
5. Se le indica que inicie sesión en su suscripción de Azure. Proporcione sus credenciales.

    ![Entrada de la herramienta de preparación de discos de Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    La herramienta comienza entonces a preparar el disco y a copiar los datos de copia de seguridad. Puede que deba conectar discos adicionales si se lo pide la herramienta, si el disco proporcionado no tiene espacio suficiente para los datos de copia de seguridad. <br/>

    Al terminar la ejecución correcta de la herramienta, el símbolo del sistema proporciona tres informaciones distintas:
    1. Uno o varios de los discos que proporcionó están preparados para enviarlos a Azure. 
    2. Recibe la confirmación de que se creó el trabajo de importación. El trabajo de importación usa el nombre que proporcionó.
    3. La herramienta muestra la dirección de envío del centro de datos de Azure.

    ![Preparación de discos de Azure finalizada](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Cuando se complete la ejecución del comando, puede actualizar la información de envío.

7. Envíe los discos a la dirección proporcionada por la herramienta y guarde el número de seguimiento como referencia futura.

   > [!IMPORTANT] 
   > El número de seguimiento no puede ser el mismo para distintos trabajos de importación de Azure. Asegúrese de que las unidades preparadas con la utilidad en un trabajo de importación de Azure se envían juntas en un único paquete y de que el paquete tenga un número de seguimiento exclusivo. No combine unidades preparadas como parte de trabajos de importación de Azure independientes en un solo paquete.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Actualización de los detalles de envío del trabajo de importación de Azure

El procedimiento siguiente actualiza los detalles del envío del trabajo de importación de Azure. Esta información incluye detalles sobre:
* el nombre del operador que entrega los discos a Azure
* los detalles de envío en caso de devolución de los discos

1. Inicie sesión en la suscripción de Azure.
2. En el menú principal, haga clic en **Todos los servicios** y, en el cuadro de diálogo Todos los servicios, haga clic en Importar. Haga clic en **Trabajos de Import/Export** cuando lo vea.
    ![Especificación de la información de envío](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Se abre el menú de la lista de **Trabajos de Import/Export** y aparecen todos los trabajos de Import/Export de la suscripción seleccionada. 

3. Si tiene varias suscripciones, asegúrese de seleccionar la suscripción que se usó para importar los datos de la copia de seguridad. Luego, seleccione el trabajo de importación recién creado para abrir sus detalles.

    ![Revisión de la información de envío](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. En el menú de configuración del trabajo de importación recién creado, haga clic en **Manage Shipping Info** (Administrar información de envío) y escriba los detalles para el envío en caso de devolución.

    ![Almacenamiento de la información de envío](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Cuando tenga el número de seguimiento del transportista de envío, haga clic en el banner en la página de información general del trabajo de importación de Azure y escriba estos detalles:

   > [!IMPORTANT] 
   > Asegúrese de que la información del transportista y el número de seguimiento se actualicen en dos semanas a contar de la creación del trabajo de importación de Azure. No comprobar esta información dentro de dos semanas puede generar que se elimine el trabajo y que no se procese.

   ![Almacenamiento de la información de envío](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Almacenamiento de la información de envío](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tiempo para procesar las unidades 
El tiempo que se tarda en procesar un trabajo de importación de Azure varía en función de determinados factores, como el tiempo de envío, el tipo de trabajo, el tipo y el tamaño de los datos copiados o el tamaño de los discos proporcionados. El servicio Azure Import/Export no tiene un Acuerdo de Nivel de Servicio pero, después de que se reciban los discos, se esfuerza por completar la copia de los datos de copia de seguridad en su cuenta de almacenamiento de Azure en un período de tiempo de entre 7 y 10 días. 

### <a name="monitoring-azure-import-job-status"></a>Supervisión del estado del trabajo de importación de Azure
Para supervisar el estado del trabajo de importación en Azure Portal, vaya a la página **Trabajos de Import/Export** y seleccione el trabajo. Para más información sobre el estado de los trabajos de importación, consulte el artículo sobre el [servicio de Import/Export de Storage](../storage/common/storage-import-export-service.md).

### <a name="complete-the-workflow"></a>Finalización del flujo de trabajo
Una vez que el trabajo de importación se completa correctamente, los datos de la copia de seguridad inicial están disponibles en la cuenta de almacenamiento. En la siguiente copia de seguridad programada, Azure Backup copia el contenido de los datos de la cuenta de almacenamiento en el almacén de Recovery Services, tal y como se muestra a continuación: 

   ![Copia de datos en el almacén de Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

En la siguiente copia de seguridad programada, Azure Backup realiza una copia de seguridad incremental.

### <a name="cleaning-up-resources"></a>Limpieza de recursos
Una vez que se completa la copia de seguridad inicial, puede eliminar de manera segura los datos importados al contenedor de Azure Storage y los datos de la copia de seguridad de la ubicación de almacenamiento provisional.

## <a name="next-steps"></a>Pasos siguientes
* Si tiene dudas acerca del flujo de trabajo de Azure Import/Export, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../storage/common/storage-import-export-service.md).
* Vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre la copia de seguridad sin conexión de Azure si tiene alguna pregunta sobre el flujo de trabajo.
