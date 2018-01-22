---
title: "Creación de una factoría de datos de Azure con la interfaz de usuario de Azure Data Factory | Microsoft Docs"
description: "Este tutorial muestra cómo crear una factoría de datos con una canalización que copia datos de una carpeta a otra carpeta en Azure Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: ebce4e0d137d2e56cc914efad357593af7abb255
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-data-factory-using-the-azure-data-factory-ui"></a>Creación de una factoría de datos con la interfaz de usuario de Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](quickstart-create-data-factory-portal.md)

En esta guía de inicio rápido se describe cómo usar la interfaz de usuario de Azure Data Factory para crear y supervisar una factoría de datos. La canalización que ha creado en esta factoría de datos **copia** los datos de una carpeta a otra en Azure Blob Storage. Para ver un tutorial acerca de cómo **transformar** datos mediante Azure Data Factory, consulte [Transformación de datos en la nube mediante la actividad de Spark en Azure Data Factory](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introduction to Azure Data Factory](data-factory-introduction.md) antes de seguir los pasos de esta guía de inicio rápido. 
>
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte el [tutorial de Data Factory versión 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Ver este vídeo le ayudará a conocer la interfaz de usuario de Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Acceda a [Azure Portal](https://portal.azure.com). 
2. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Error de nombre no disponible](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
    Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista desplegable solo se muestran las ubicaciones que admite Data Factory. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) utilizados por la factoría de datos pueden encontrarse en otras ubicaciones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    ![Página principal Factoría de datos](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente. 
11. En la página de introducción, cambie a la pestaña **Edit** (Editar) del panel izquierdo tal como se muestra en la siguiente imagen: 

    ![Página de introducción](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage
En este paso, creará un servicio vinculado para vincular la cuenta de Azure Storage con la factoría de datos. El servicio vinculado tiene la información de conexión que usa el servicio Data Factory en el entorno de tiempo de ejecución para conectarse a él.

2. Haga clic en **Conexiones** y, a continuación, haga clic en el botón **Nueva** en la barra de herramientas. 

    ![Nueva conexión](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
3. En la página **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure Blob Storage** y haga clic en **Continue** (Continuar). 

    ![Selección de Azure Storage](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
4. En la página **New Linked**  (Nuevo servicio vinculado), realice los pasos siguientes: 

    1. Escriba **AzureStorageLinkedService** en **Name** (Nombre).
    2. Seleccione el nombre de la cuenta de Azure Storage en **Storage account name** (Nombre de la cuenta de Storage).
    3. Haga clic en **Test connection** (Probar conexión) para confirmar que el servicio Data Factory puede conectarse a la cuenta de almacenamiento. 
    4. Haga clic en **Save** (Guardar) para guardar el servicio vinculado. 

        ![Servicio vinculado de Azure Storage: configuración](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
5. Confirme que **AzureStorageLinkedService** aparece en la lista de servicios vinculados. 

    ![Servicio vinculado de Azure Storage en la lista](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará dos conjuntos de datos: **InputDataset** y **OutputDataset**. Estos conjuntos de datos son de tipo **AzureBlob**. Hacen referencia al **servicio vinculado de Azure Storage** que creó en el paso anterior. 

El conjunto de datos de entrada representa los datos de origen en la carpeta de entrada. En la definición del conjunto de datos de entrada, se especifica el contenedor de blobs (**adftutorial**), la carpeta (**input**) y el archivo (**emp.txt**) que contiene los datos de origen. 

El conjunto de datos de salida representa los datos que se copian en el destino. En la definición del conjunto de datos de salida, se especifica el contenedor de blobs (**adftutorial**), la carpeta (**output**) y el archivo en el que se copian los datos. Cada ejecución de una canalización tiene un identificador único asociado al que se puede acceder mediante el uso de la variable del sistema **RunId**. El nombre del archivo de salida se evalúa dinámicamente según el identificador de ejecución de la canalización.   

En la configuración del servicio vinculado se especifica la cuenta de Azure Storage que contiene los datos de origen. En la configuración del conjunto de datos de origen se especifica dónde residen exactamente los datos de origen (contenedor de blobs, carpeta y archivo). En la configuración del conjunto de datos receptor se especifica dónde se copian los datos (contenedor de blobs, carpeta y archivo). 
 
1. Haga clic en el botón **+ (Más)** y seleccione **Dataset** (Conjunto de datos).

    ![Menú Conjunto de datos nuevo](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. En la página **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y haga clic en **Finish** (Finalizar). 

    ![Seleccionar Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. En la ventana **Properties** (Propiedades) del conjunto de datos, escriba **InputDataset** en **Name** (Nombre). 

    ![Configuración general del conjunto de datos](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Cambie a la pestaña **Connection** (Conexión) y realice los pasos siguientes: 

    1. Seleccione **AzureStorageLinkedService** como servicio vinculado. 
    2. Haga clic en el botón **Browse** (Examinar) en **File path** (Ruta de acceso del archivo). 
        ![Búsqueda del archivo de entrada](./media/quickstart-create-data-factory-portal/file-path-browse-button.png)
    3. En la ventana **Choose a file or folder** (Elegir un archivo o carpeta), vaya a la carpeta **input** del contenedor **adftutorial**, seleccione el archivo **emp.txt** y haga clic en **Finish** (Finalizar).

        ![Búsqueda del archivo de entrada](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    4. (opcional) Haga clic en **Preview data** (Vista previa de los datos) para obtener una vista previa de los datos del archivo emp.txt.     
5. Repita los pasos para crear el conjunto de datos de salida.  

    1. Haga clic en el botón **+ (Más)** en el panel izquierdo y seleccione **Dataset** (Conjunto de datos).
    2. En la página **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y haga clic en **Finish** (Finalizar).
    3. Escriba **OutputDataset** para el nombre.
    4. Escriba **adftutorial/output** para la carpeta. La actividad de copia crea la carpeta de salida si no existe. 
    5. Escriba `@CONCAT(pipeline().RunId, '.txt')` como nombre de archivo. Cada vez que se ejecuta una canalización, la ejecución de la canalización tiene un identificador único asociado. La expresión concatena el identificador de ejecución de la canalización con **.txt** para evaluar el nombre del archivo de salida. Para la lista de las expresiones y variables del sistema admitidas, consulte las [variables del sistema](control-flow-system-variables.md) y el [lenguaje de expresiones](control-flow-expression-language-functions.md).

        ![Configuración del conjunto de datos de salida](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Crear una canalización 
En este paso, creará y comprobará una canalización con una actividad de **copia** que utiliza los conjuntos de datos de entrada y de salida. La actividad de Copia realiza una copia de los datos desde el archivo especificado en la configuración del conjunto de datos de entrada al archivo especificado en la configuración del conjunto de datos de salida. Si el conjunto de datos de entrada especifica solo una carpeta (no el nombre de archivo), la actividad de copia realiza una copia de todos los archivos de la carpeta de origen al destino. 

1. Haga clic en el botón **+ (Más)** y seleccione **Pipeline** (Canalización). 

    ![Menú New pipeline (Nueva canalización)](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. Especifique **CopyPipeline** en **Name** (Nombre) en la ventana **Properties** (Propiedades). 

    ![Configuración general de la canalización](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. En el cuadro de herramientas **Activities** (Actividades), expanda **Data Flow** (Flujo de datos) y arrastre la actividad **Copy** (Copia) desde el cuadro de herramientas **Activities** (Actividades) hasta la superficie del diseñador de canalizaciones. También puede buscar actividades en el cuadro de herramientas **Activities** (Actividades). Especifique **CopyFromBlobToBlob** para el **nombre**.

    ![Configuración general de la actividad de copia](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Cambie a la pestaña **Source** (Origen) en la configuración de la actividad de copia y seleccione **InputDataset** para **source dataset** (conjunto de datos de origen).

    ![Actividad de copia: configuración del origen](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Cambie a la pestaña **Sink** (Receptor) en la configuración de la actividad de copia y seleccione **OutputDataset** para **sink dataset** (conjunto de datos receptor).

    ![Actividad de copia: configuración del receptor](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Para comprobar la configuración de la canalización, haga clic en **Validate** (Comprobar). Confirme que la canalización se ha comprobado correctamente. Haga clic en el botón **flecha derecha** (>>) para cerrar la salida de la comprobación. 

    ![Comprobación de la canalización](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Realización de la serie de pruebas de la canalización
En este paso se realiza una serie de pruebas de la canalización antes de implementarla en Data Factory. 

1. Haga clic en **Test Run** (Serie de pruebas) en la barra de herramientas de la canalización. 
    
    ![Serie de pruebas de la canalización](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Confirme que ve el estado de ejecución de la canalización en la pestaña **Output** (Salida) de la configuración de la canalización. 

    ![Salida de la serie de pruebas](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Confirme que ve un archivo de salida en la carpeta **output** del contenedor **adftutorial**. Si no existe la carpeta de salida, el servicio Data Factory la crea automáticamente. 
    
    ![Comprobar salida](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Desencadenamiento manual de la canalización
En este paso se implementan las entidades (servicios vinculados, conjuntos de datos, canalizaciones) en Azure Data Factory. A continuación, desencadenará manualmente una ejecución de la canalización. También puede publicar entidades en su propio repositorio de GIT de VSTS, lo cual se trata en [otro tutorial](tutorial-copy-data-portal.md?#configure-code-repository).

1. Antes de desencadenar una canalización, debe publicar las entidades en Data Factory. Haga clic en **Publish** (Publicar) en el panel izquierdo para realizar la publicación. 

    ![Botón Publicar](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Para desencadenar la canalización de forma manual, haga clic en **Trigger** (Desencadenar) en la barra de herramientas y seleccione **Trigger Now** (Desencadenar ahora). 
    
    ![Trigger now (Desencadenar ahora)](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Supervisar la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Use el botón **Refresh** (Actualizar) para actualizar la lista.

    ![Supervisar la canalización](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en **Actions** (Acciones). En esta página puede ver el estado de la ejecución de la actividad de copia. 

    ![Ejecuciones de actividad de canalización](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Para más información sobre la operación de copia, haga clic en el vínculo **Details** (Detalles) (imagen de gafas) de la columna **Actions** (Acciones). Para más información sobre las propiedades, consulte [Introducción a la actividad de copia](copy-activity-overview.md). 

    ![Detalles de la operación de copia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Confirme que ve un archivo nuevo en la carpeta **output** (salida). 
5. Puede volver a la vista **Pipeline Runs** (Ejecuciones de la canalización) desde la vista **Activity Runs** (Ejecuciones de la actividad). Para ello, haga clic en el vínculo **Pipelines** (Canalizaciones). 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Desencadenamiento de la canalización de forma programada
Este paso es opcional en este tutorial. Puede crear un **programador de desencadenador** para programar la ejecución de la canalización periódicamente (cada hora, diariamente, etc.). En este paso, creará un desencadenador que se ejecutará cada minuto hasta la fecha y hora que se especifique como fecha de finalización. 

1. Cambie a la pestaña **Edit** (Editar). 

    ![Cambio a la pestaña Edit (Editar)](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Haga clic en **Trigger** (Desencadenador) en el menú y haga clic en **New/Edit** (Nuevo/Editar). 

    ![Menú Nuevo desencadenador](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. En la página **Add Triggers** (Agregar desencadenadores), haga clic en **Choose trigger...** (Elegir desencadenador) y luego en **New** (Nuevo). 

    ![Add Triggers (Agregar desencadenadores): nuevo desencadenador](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. En la página **New Trigger** (Nuevo desencadenador), para el campo **End** (Final), seleccione **On Date** (En fecha), especifique la hora de finalización unos minutos después de la hora actual y haga clic en **Apply** (Aplicar). Hay un costo asociado a cada ejecución de la canalización. Por lo tanto, especifique la hora de finalización tan solo unos minutos después de la hora de inicio. Asegúrese de que sea el mismo día. No obstante, asegúrese de que hay tiempo suficiente para que la canalización se ejecute entre la hora de publicación y la hora de finalización. El desencadenador entra en vigor después de publicar la solución en Data Factory, no cuando se guarda el desencadenador en la interfaz de usuario. 

    ![Configuración del desencadenador](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Marque la opción **Activated** (Activado) en la página **New Trigger** (Nuevo desencadenador) y haga clic en **Next** (Siguiente) 

    ![Configuración del desencadenador: botón Siguiente](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. En la página **New Trigger** (Nuevo desencadenador), revise el mensaje de advertencia y haga clic en **Finish** (Finalizar).

    ![Configuración del desencadenador: botón Finalizar](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Haga clic en **Publish** (Publicar) para publicar los cambios en Data Factory. 

    ![Botón Publicar](./media/quickstart-create-data-factory-portal/publish-2.png)
8. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Haga clic en **Refresh** (Actualizar) para actualizar la lista. Verá que la canalización se ejecuta una vez cada minuto desde la hora de publicación hasta la hora de finalización. Observe los valores de la columna **Triggered By** (Desencadenado por). La ejecución manual del desencadenador se realizó en el paso (**Trigger Now** [Desencadenar ahora]) que llevó a cabo antes. 

    ![Supervisión de las ejecuciones desencadenadas](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Haga clic en la flecha hacia abajo junto a **Pipeline Runs** (Ejecuciones de la canalización) para cambiar a la vista **Trigger Runs** (Ejecuciones del desencadenador). 

    ![Supervisión de las ejecuciones del desencadenador](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Confirme que se crea un **archivo de salida** para cada ejecución de la canalización hasta la fecha y hora de finalización especificadas en la carpeta **output** (salida). 

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-portal.md) para obtener información acerca del uso de Data Factory en otros escenarios. 