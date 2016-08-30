<properties
   pageTitle="Copia de seguridad de Azure: copia de seguridad sin conexión o propagación inicial mediante el servicio Importación/Exportación de Azure"
   description="Descubra cómo la copia de seguridad de Azure permite enviar datos fuera de la red mediante el servicio de importación y exportación de Azure. Este artículo explica la propagación sin conexión de los datos de copia de seguridad iniciales mediante el servicio de importación y exportación de Azure."
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/16/2016"
   ms.author="jimpark;saurabhsensharma;nkolli;trinadhk"/>

# Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure
El servicio Copia de seguridad de Azure incorpora varias eficiencias para ahorrar costos de almacenamiento y red durante las copias de seguridad iniciales 'completas' en Azure. Las copias de seguridad iniciales "completas" transfieren grandes cantidades de datos, de ahí que sea necesario un mayor ancho de banda de red en comparación con las copias de seguridad sucesivas que solo transfieren los cambios diferenciales e incrementales. Copia de seguridad de Azure no solo comprime las copias de seguridad iniciales, sino que mediante el proceso de propagación sin conexión proporciona un mecanismo para cargar los datos comprimidos de la copia de seguridad inicial sin conexión en Azure mediante discos.

El proceso de propagación sin conexión de Copia de seguridad de Azure se integra estrechamente con el [servicio de importación y exportación de Azure](../storage/storage-import-export-service.md) que permite transferir datos a Azure rápidamente mediante discos. Si tiene TB de datos de copia de seguridad inicial que se deben transferir a través de una red de latencia alta y ancho de banda bajo, puede usar el flujo de trabajo de propagación sin conexión para enviar la copia de seguridad inicial de una o varias unidades de disco duro a un centro de datos de Azure. Este artículo proporciona información general de los pasos necesarios para completar este flujo de trabajo.

## Información general

Con la funcionalidad de propagación sin conexión de Copia de seguridad de Azure y la característica Importación/Exportación de Azure, cargar los datos sin conexión en Azure mediante discos es un proceso sencillo. En lugar de transferir la copia inicial completa a través de la red, los datos de copia de seguridad se escriben en una *ubicación de ensayo*. Una vez finalizada la copia inicial en la *ubicación de ensayo*, los datos se escriben en una o varias unidades SATA, según el tamaño de la copia de seguridad inicial, mediante la *herramienta Importación/Exportación de Azure*. Estas unidades se envían finalmente al centro de datos de Azure más cercano.

La [Copia de seguridad de Azure de agosto de 2016 (y versiones posteriores)](http://go.microsoft.com/fwlink/?LinkID=229525&clcid=0x409), incluye una *herramienta de preparación de discos de Azure* llamada **AzureOfflineBackupDiskPrep** que:

   - Le facilita la preparación de las unidades para la importación de Azure mediante la herramienta Importación/Exportación de Azure.
   - Crea automáticamente un trabajo de importación de Azure para el servicio Importación/Exportación de Azure en el [Portal de Azure clásico](https://manage.windowsazure.com) en lugar de crearlo manualmente con versiones anteriores de Copia de seguridad de Azure.

Cuando finaliza la carga de los datos de copia de seguridad en Azure, el servicio Copia de seguridad de Azure copia los datos de copia de seguridad en el almacén de copia de seguridad y se programan las copias de seguridad incrementales.

  > [AZURE.NOTE] Para utilizar la herramienta de preparación de discos de Azure, asegúrese de haber instalado la actualización de Copia de seguridad de Azure de agosto de 2016 (o una versión superior), y realice con ella los pasos del flujo de trabajo. Si va a utilizar una versión anterior de Copia de seguridad de Azure, puede preparar la unidad SATA mediante la *herramienta Importación/Exportación de Azure*, tal y como se detalla en las secciones posteriores de este artículo.

## Requisitos previos

1. Es importante familiarizarse con el flujo de trabajo de exportación de importación de Azure que aparece [aquí](../storage/storage-import-export-service.md).
2. Antes de iniciar el flujo de trabajo, asegúrese de que se haya creado un almacén de Copia de seguridad de Azure, de que las credenciales de almacén se hayan descargado, el agente de Copia de seguridad de Azure se haya instalado en el servidor/cliente de Windows o en System Center Data Protection Manager (SCDPM) y la máquina esté registrada con el almacén de Copia de seguridad de Azure.
3. Descargue la configuración del archivo de publicación de Azure [aquí](https://manage.windowsazure.com/publishsettings) en la máquina desde la que piensa hacer copia de seguridad de los datos.
4. Prepare una *ubicación provisional* que podría ser un recurso compartido de red o una unidad de disco adicional en la máquina. La ubicación de ensayo es 'almacenamiento transitorio', y se utiliza temporalmente durante este flujo de trabajo. Asegúrese de que la ubicación de ensayo tiene suficiente espacio en disco para almacenar la copia inicial. Por ejemplo, si intenta realizar una copia de seguridad en un servidor de archivos de 500 GB, asegúrese de que el área de ensayo sea de al menos 500 GB (aunque se usará una cantidad menor debido a la compresión).
5. Escritor de unidad SATA externo y una unidad SATA externa de 3,5 pulgadas. El servicio de importación y exportación solo admite unidades de disco duro SATA II/III de 3,5 pulgadas. No se admiten unidades de disco duro con un tamaño superior a 8 TB. Es posible conectar un disco SATA II/III externo a la mayoría de los equipos con un adaptador USB SATA II/III. Consulte la documentación de la herramienta Importación/Exportación de Azure para conocer el conjunto más reciente de unidades de disco que admite el servicio.
6. Habilite BitLocker en la máquina a la que está conectado el sistema de escritura de la unidad SATA.
7. Descargue la herramienta de importación y exportación [aquí](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) en la máquina a la que está conectado el sistema de escritura de la unidad SATA. Este paso no es necesario si ha descargado e instalado la actualización de Copia de seguridad de Azure de agosto de 2016 (o superior).

## Flujo de trabajo
La información proporcionada en esta sección es para completar el flujo de trabajo de **Copia de seguridad sin conexión**, por lo que los datos se pueden entregar a un centro de datos de Azure y cargarse en el almacenamiento de Azure. Si tiene alguna pregunta sobre el servicio de importación o cualquier aspecto del proceso, vea la documentación sobre la [Información general del servicio de importación](../storage/storage-import-export-service.md) a la que se ha hecho referencia anteriormente.

### Iniciar la copia de seguridad sin conexión

1. Como parte de la programación de una copia de seguridad, aparecerá la pantalla siguiente (en Windows Server, el cliente Windows o SCDPM).

    ![ImportScreen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)
    
    La pantalla correspondiente en SCDPM tiene el siguiente aspecto. <br/> ![Pantalla de importación de DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    La descripción de las entradas es la siguiente:

    - **Ubicación de ensayo**: hace referencia a la ubicación de almacenamiento temporal en la que se escribe la copia de seguridad inicial. Podría tratarse de un recurso compartido de red o en la máquina local. Si la *máquina de copia* y la *máquina de origen* son diferentes, se recomienda especificar la ruta de acceso completa de red de la ubicación de ensayo.
    - **Nombre del trabajo de importación de Azure**: hace referencia al nombre único mediante el cual los servicios Importación de Azure y Copia de seguridad de Azure realizan el seguimiento de la transferencia de los datos enviados mediante discos a Azure.
    - **Configuración de publicación de Azure**: es un archivo XML que contiene información sobre el perfil de suscripción. También contiene credenciales seguras asociadas a su suscripción. Es posible descargar el archivo [aquí](https://manage.windowsazure.com/publishsettings). Proporcione la ruta de acceso local al archivo de configuración de publicación.
    - **Identificador de suscripción de Azure**: es el identificador de la suscripción de Azure en la que planea iniciar el trabajo de importación de Azure. Si tiene varias suscripciones de Azure, use el identificador al que quiere asociar el trabajo de importación.
    - **Cuenta de almacenamiento de Azure**: la cuenta de almacenamiento de tipo "clásico" de la suscripción proporcionada, que se asociará al trabajo de importación de Azure.
    - **Contenedor de Almacenamiento de Azure**: el nombre del blob de almacenamiento de destino de la cuenta de Almacenamiento de Azure donde se importan los datos de este trabajo.
    
    > [AZURE.NOTE] Aunque haya registrado el servidor en un almacén de Servicios de recuperación del [nuevo Portal de Azure](https://ms.portal.azure.com) para sus copias de seguridad y no utilice una suscripción del proveedor de servicios en la nube (CSP), puede crear una cuenta de almacenamiento de tipo "clásico" desde el nuevo Portal de Azure y usarla en el flujo de trabajo de copia de seguridad sin conexión.
    
    Guarde toda esta información, la necesitará más tarde en los pasos siguientes. Solo se necesita la *ubicación de ensayo* si la *herramienta de preparación de discos de Azure* se usa para preparar los discos.
    

2. Complete el flujo de trabajo y seleccione **Back Up Now** (Iniciar copia de seguridad) en la consola de administración de Copia de seguridad de Azure para iniciar la copia de seguridad sin conexión. La copia de seguridad inicial se escribe en el área de ensayo como parte de este paso.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/backupnow.png)
    
    El flujo de trabajo correspondiente en SCDPM se habilita haciendo clic con el botón derecho en **Grupo de protección** y eligiendo la opción **Crear punto de recuperación**. Este paso va seguido de la elección de la opción **Protección en línea**.

    ![Realizar copia de seguridad de DPM ahora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Cuando finalice la operación, la ubicación de ensayo estará lista para usarse en la preparación del disco

    ![Salida](./media/backup-azure-backup-import-export/opbackupnow.png)

###Preparación de la unidad SATA y creación de un trabajo de importación de Azure mediante la *herramienta de preparación de discos de Azure*
La *herramienta de preparación de discos de Azure* está disponible en el directorio de instalación del agente de Servicios de recuperación de Microsoft Azure (actualización de agosto de 2016 y superior) en la siguiente ruta de acceso.

   *\\Microsoft* *Azure* *Recovery* *Services* *Agent\\Utils*

1. Desplácese al directorio anterior y copie el directorio **AzureOfflineBackupDiskPrep** en una *máquina de copia* en la que se montarán las unidades de disco que se van a preparar. Compruebe que se cumplen los siguientes requisitos en la *máquina de copia*:

      - La *ubicación de ensayo* proporcionada para el flujo de trabajo de inicialización sin conexión es accesible desde la *máquina de copia* usando la misma ruta de acceso de red proporcionada durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión**.
      
      - BitLocker está habilitado en la máquina.
      
      - La máquina tiene acceso al Portal de Azure.
      
      Si es necesario, la *máquina de copia* puede ser la misma que la *máquina de origen*.

2. Abra un símbolo del sistema con privilegios elevados en la máquina de copia, donde el directorio de la herramienta de preparación de discos de Azure será el directorio actual, y ejecute el siguiente comando:

      *.\\AzureOfflineBackupDiskPrep.exe* s:<*Ruta de acceso de la ubicación de ensayo*> [p:<*Ruta de acceso a PublishSettingsFile*>]

| Parámetro | Description
|-------------|-------------|
|s:<*Ruta de acceso de la ubicación de ensayo*> | Entrada obligatoria, que se utiliza para proporcionar la ruta de acceso a la ubicación de ensayo especificada durante el inicio de la copia de seguridad sin conexión. |
|p:<*Ruta de acceso a PublishSettingsFile*> | Entrada opcional, que se utiliza para proporcionar la ruta de acceso al archivo de configuración de publicación especificado durante el inicio de la copia de seguridad sin conexión. |

   > [AZURE.NOTE] El archivo de configuración de publicación es una entrada obligatoria cuando la *máquina de copia* y la *máquina de origen* son diferentes

Cuando se ejecuta el comando, la herramienta solicita la selección del trabajo de importación de Azure correspondiente a las unidades que deben estar preparadas. Si hay un solo trabajo de importación asociado con la ubicación de ensayo proporcionada, aparece una pantalla como esta:

   ![Entrada de la herramienta de preparación de discos de Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Escriba la **letra de unidad** del disco montado (sin los dos puntos finales) que quiera preparar para transferirlo a Azure. A continuación, proporcione la confirmación del formato de la unidad cuando se le solicite.

La herramienta comienza a preparar entonces el disco con los datos de copia de seguridad. Puede que deba conectar discos adicionales si se lo pide la herramienta, si el disco proporcionado no tiene espacio suficiente para acomodar los datos de copia de seguridad. <br/>

Al final de la ejecución correcta de la herramienta, uno o varios discos que ha proporcionado están preparados para enviarse a Azure, y se crea un trabajo de importación con el nombre que proporcionó durante el flujo de trabajo de **inicio de la copia de seguridad sin conexión** en el Portal de Azure clásico. Además, la herramienta muestra la dirección de envío al centro de datos de Azure adonde es necesario enviar los discos, junto con el vínculo para encontrar el trabajo de importación en el Portal de Azure clásico.

   ![Preparación de discos de Azure finalizada](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
4. Envíe los discos a la dirección proporcionada por la herramienta y guarde el número de seguimiento como referencia futura.<br/>
5. Al navegar al vínculo mostrado por la herramienta, va a parar a la **cuenta de almacenamiento de Azure** especificada durante el flujo de trabajo de copia de seguridad sin conexión. Aquí podrá ver el trabajo de importación recién creado en la pestaña **IMPORTACIÓN/EXPORTACIÓN** de la cuenta de almacenamiento.

   ![Trabajo de importación creado](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>
6. Haga clic en **INFORMACIÓN DE ENVÍO** en la parte inferior de la página para actualizar los **detalles de contacto**, tal como se muestra a continuación. Microsoft usa esta información para devolverle los discos una vez finalizado el trabajo de importación.

   ![Información de contacto](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>
7. Escriba los **detalles de envío** en la siguiente pantalla, tal como se muestra a continuación. Proporcione los detalles del transportista y el número de seguimiento correspondientes a los discos enviados al centro de datos de Azure.
   
   ![Información de envío](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### Completar el flujo de trabajo
Una vez completado el trabajo de importación, los datos de la copia de seguridad inicial están disponibles en la cuenta de almacenamiento. El agente de Servicios de recuperación de Microsoft Azure copia entonces el contenido de los datos de esta cuenta en el Almacén de copia de seguridad o en el almacén de Servicios de recuperación, según corresponda. En la siguiente copia de seguridad programada, el agente de Copia de seguridad de Azure realiza la copia de seguridad incremental sobre la copia de seguridad inicial.


> [AZURE.NOTE] Las secciones siguientes se aplican a los usuarios de versiones anteriores de Copia de seguridad de Azure que no tienen acceso a la *herramienta de preparación de discos de Azure*

### Preparar la unidad de disco SATA

1. Descargue la [herramienta de importación y exportación](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) de Microsoft Azure en la *máquina de copia*. Asegúrese de que la ubicación de ensayo (en el paso anterior) es accesible desde la máquina e la que tiene previsto ejecutar el siguiente conjunto de comandos. Si es necesario, la máquina de copia puede ser la misma que la máquina de origen.

2. Descomprima el archivo *WAImportExport.zip*. Ejecute la herramienta *WAImportExport* que formatea la unidad SATA, escriba los datos de copia de seguridad en esa unidad y cífrelos. Antes de ejecutar el siguiente comando, asegúrese de que BitLocker esté habilitado en la máquina. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*ubicación de ensayo*> /dstdir: <*DestinationBlobVirtualDirectory*>/*
    
    > [AZURE.NOTE] Si ha instalado la actualización de Copia de seguridad de Azure de agosto de 2016 (o versiones posteriores), asegúrese de que la *ubicación de ensayo* especificada sea la misma que la mostrada en la pantalla **Back Up Now** (Iniciar copia de seguridad) y que contenga los archivos *AIB* y *Base Blob*.

| Parámetro | Description
|-------------|-------------|
| /j:<*JournalFile*>| La ruta de acceso al archivo de diario. Cada unidad debe tener exactamente un archivo de diario. El archivo de diario no debe residir en la unidad de destino. La extensión de archivo de diario es .jrn y se crea como parte de la ejecución de este comando.|
|/id:<*SessionId*> | El identificador de sesión identifica una *sesión de copia*. Se utiliza para asegurar la recuperación precisa de una sesión de copia interrumpida. Los archivos que se copian en una sesión de copia se almacenan en un directorio con el mismo nombre que el identificador de sesión en la unidad de destino.|
| /sk:<*StorageAccountKey*> | La clave de cuenta para la cuenta de almacenamiento en la que se importan los datos. La clave tiene que ser igual a la que se especificó durante la creación del grupo de protección o directiva de copia de seguridad.|
| /BlobType | Especifique **PageBlob**; este flujo de trabajo solo tendrá éxito si se especifica la opción PageBlob. Esta no es la opción predeterminada y se debería mencionar en este comando. |
|/t:<*TargetDriveLetter*> | La letra de unidad del disco duro de destino para la sesión de copia actual, sin los dos puntos finales.|
|/format | Especifique este parámetro cuando la unidad debe tener formato; de lo contrario, omítalo. Antes de que la herramienta aplique formato a la unidad, se le pedirá una confirmación desde la consola. Para suprimir la confirmación, especifique el parámetro /silentmode.|
|/encrypt | Este parámetro se especifica cuando la unidad todavía no se ha cifrado con BitLocker y debe cifrarse mediante la herramienta. Si la unidad ya se ha cifrado con BitLocker, a continuación, omita este parámetro y especifique el parámetro /bk, proporcionando la clave de BitLocker existente. Si especifica el parámetro/format, también debe especificar el parámetro /encrypt. |
|/srcdir:<*SourceDirectory*> | El directorio de origen que contiene archivos que se copiarán en la unidad de destino. Asegúrese de que el nombre de directorio especificado aquí es la ruta de acceso completa (no una ruta de acceso relativa).|
|/dstdir:<*DestinationBlobVirtualDirectory*> | La ruta de acceso al directorio virtual de destino en la cuenta de almacenamiento de Microsoft Azure. Asegúrese de utilizar nombres de contenedor válidos al especificar los directorios virtuales de destino o blobs. Tenga en cuenta que los nombres de contenedor deben estar en minúsculas. Este nombre de contenedor tiene que ser el mismo que el especificado durante la creación del grupo de protección o directiva de copia de seguridad.|

  > [AZURE.NOTE] Un archivo de diario se creará en la carpeta WAImportExport, que captura la información completa del flujo de trabajo. Necesitará este archivo al crear un trabajo de importación en el Portal de Azure.

  ![Salida de PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Crear trabajo de importación en el portal de Azure
1. Vaya a su cuenta de almacenamiento en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Importación/Exportación** y, luego, en **Crear trabajo de importación** en el panel de tareas.

    ![Portal](./media/backup-azure-backup-import-export/azureportal.png)

2. En el paso 1 del asistente, indique que ha preparado su unidad y que tiene el archivo de diario de la unidad disponible. En el paso 2 del asistente, proporcione la información de contacto de la persona responsable de este trabajo de importación.
3. En el paso 3, cargue los archivos de diario de unidad que haya obtenido durante la sección anterior.
4. En el paso 4, escriba un nombre descriptivo para el trabajo de importación que se especificó durante la creación del grupo de protección o directiva de copia de seguridad. El nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido se usa para realizar el seguimiento de los trabajos mientras están en curso y cuando se han completado.
5. A continuación, seleccione en la lista la región de su centro de datos. La región del centro de datos indicará el centro de datos y la dirección donde debe enviar el paquete.

    ![CD](./media/backup-azure-backup-import-export/dc.png)

6. En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft usa esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación.

7. Envíe el disco y escriba el número de seguimiento para realizar un seguimiento del estado del envío. Una vez que el disco llega al centro de datos, se copia en la cuenta de almacenamiento y el estado se actualiza.

    ![Estado completo](./media/backup-azure-backup-import-export/complete.png)

### Completar el flujo de trabajo
Cuando los datos de la copia de seguridad inicial están disponibles en la cuenta de almacenamiento, el agente de Servicios de recuperación de Microsoft Azure copia el contenido de los datos de esta cuenta en el Almacén de copia de seguridad o en el almacén de Servicios de recuperación, según corresponda. En la siguiente copia de seguridad de programación, el agente de Copia de seguridad de Azure realiza la copia de seguridad incremental sobre la copia de seguridad inicial.

## Pasos siguientes
- Si tiene alguna pregunta sobre el flujo de trabajo de importación y exportación de Azure, consulte este [artículo](../storage/storage-import-export-service.md).
- Vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre la copia de seguridad sin conexión de Azure si tiene alguna pregunta sobre el flujo de trabajo.

<!---HONumber=AcomDC_0817_2016-->