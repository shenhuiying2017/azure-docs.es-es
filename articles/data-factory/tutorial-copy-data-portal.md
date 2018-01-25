---
title: "Uso de Azure Portal para crear una canalización de Data Factory | Microsoft Docs"
description: "En este tutorial se proporcionan instrucciones paso a paso para usar Azure Portal para crear una factoría de datos con una canalización. La canalización usa la actividad de copia para copiar los datos de una instancia de Azure Blob Storage a una instancia de Azure SQL Database. "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 424a5ec49018e969edbf90c374a9da7e1d22395d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copia de datos del blob de Azure a Azure SQL Database mediante Azure Data Factory
En este tutorial, creará una factoría de datos mediante la interfaz de usuario (UI) de Azure Data Factory. La canalización de esta factoría de datos copia los datos de Azure Blob Storage a Azure SQL Database. El patrón de configuración de este tutorial se aplica a la copia de un almacén de datos basado en archivos a un almacén de datos relacional. Para obtener una lista de los almacenes de datos que se admiten como orígenes y receptores, consulte la tabla [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) (Almacenes de datos admitidos).

> [!NOTE]
> - Si no está familiarizado con Azure Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).
>
> - Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de una canalización con la actividad de copia
> * Realización de la serie de pruebas de la canalización
> * Desencadenamiento manual de la canalización
> * Desencadenamiento de la canalización de forma programada
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>requisitos previos
* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de Azure Storage**. Blob Storage se puede usar como almacén de datos de **origen**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.
* **Azure SQL Database**. La base de datos se puede usar como almacén de datos **receptor**. Si no tiene ninguna instancia de Azure SQL Database, consulte el artículo [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación.

### <a name="create-a-blob-and-a-sql-table"></a>Creación de un blob y una tabla SQL

Ahora, prepare su blob de Azure y su instancia de Azure SQL Database para el tutorial. Para ello, siga los pasos siguientes:

#### <a name="create-a-source-blob"></a>Creación de un blob de origen

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como un archivo **emp.txt** en el disco.

    ```
    John,Doe
    Jane,Doe
    ```

2. Cree un contenedor denominado **adftutorial** en su instancia de Azure Blob Storage. Cree una carpeta denominada **input** en este contenedor. A continuación, cargue el archivo **emp.txt** en la carpeta **input**. Use Azure Portal o herramientas como [Explorador de Azure Storage](http://storageexplorer.com/) para realizar estas tareas.

#### <a name="create-a-sink-sql-table"></a>Creación de una tabla SQL receptora

1. Use el siguiente script de SQL para crear la tabla **dbo.emp** en su instancia de Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que los servicios de Azure accedan a SQL Server. Asegúrese de que la configuración **Permitir el acceso a los servicios de Azure** está **Activada** para SQL Server de Azure para que el servicio Data Factory pueda acceder a SQL Server de Azure. Para comprobar y activar esta configuración, realice los siguientes pasos:

    1. Haga clic en el concentrador **Más servicios** a la izquierda y haga clic en **Servidores SQL**.
    2. Seleccione el servidor y haga clic en **Firewall** en **CONFIGURACIÓN**.
    3. En la hoja **Configuración de firewall**, haga clic en **Activar** para **Permitir el acceso a los servicios de Azure**.

## <a name="create-a-data-factory"></a>Crear una factoría de datos
En este paso, creará una factoría de datos e iniciará la interfaz de usuario de Azure Data Factory para crear una canalización de la factoría de datos. 

1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
        Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).      
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Azure Data Factory en una pestaña independiente.

## <a name="create-a-pipeline"></a>Crear una canalización
En este paso, creará una canalización con una actividad de copia en la factoría de datos. La actividad de copia realiza la copia los datos de Azure Blob Storage en Azure SQL Database. En el [tutorial de inicio rápido](quickstart-create-data-factory-portal.md),creó una canalización mediante estos pasos:

1. Creación del servicio vinculado. 
2. Creación del conjunto de datos de entrada y salida.
3. Y, luego, creación de una canalización.

En este tutorial, empiece con la creación de la canalización, y cree conjuntos de datos y servicios vinculados cuando necesite configurar la canalización. 

1. En la página de introducción, haga clic en el icono **Create Pipeline** (Crear canalización). 

   ![Icono Create pipeline (Crear canalización)](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. En la ventana **Properties** (Propiedades) de la canalización, establezca el **nombre** de la canalización en **CopyPipeline**.

    ![Nombre de la canalización](./media/tutorial-copy-data-portal/pipeline-name.png)
4. En el cuadro de herramientas **Activities** (Actividades), expanda la categoría **DataFlow** y arrastre y suelte la actividad **Copy** (Copia) desde el cuadro de herramientas hasta la superficie de diseño de la canalización. 

    ![Arrastrar y colocar la actividad de copia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. En la pestaña **General** de la ventana **Properties** (Propiedades), especifique **CopyFromBlobToSql** como nombre de la actividad.

    ![Nombre de la actividad](./media/tutorial-copy-data-portal/activity-name.png)
6. Cambie a la pestaña **Source** (Origen). Haga clic en **+ New** (+ Nuevo) para crear un conjunto de datos de origen. 

    ![Menú de nuevo conjunto de datos de origen](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y haga clic en **Finish** (Finalizar). Los datos de origen están en una instancia de Azure Blob Storage, así que seleccionará Azure Blob Storage como conjunto de datos de origen. 

    ![Seleccionar Azure Blob Storage](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. Verá que se abre una nueva **pestaña** en la aplicación que se llama **AzureBlob1**.

    ![Pestaña Azure Blob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. En la pestaña **General** de la ventana **Properties** (Propiedades) de la parte inferior, especifique **SourceBlobDataset** como **nombre**.

    ![Nombre del conjunto de datos](./media/tutorial-copy-data-portal/dataset-name.png)
10. Cambie a la pestaña **Connection** (Conexión) en la ventana de propiedades.   

    ![Pestaña Connection (Conexión)](./media/tutorial-copy-data-portal/source-dataset-connection-tab.png)
11. Haga clic en **+ New** (+ Nuevo) junto al cuadro de texto **Linked service** (Servicio vinculado). Un servicio vinculado enlaza un almacén de datos o un proceso a la factoría de datos. En este caso, creará un servicio vinculado de Azure Storage para vincular su cuenta de Azure Storage al almacén de datos. El servicio vinculado tiene la información de conexión que usa el servicio Data Factory para conectarse a la instancia de Blob Storage en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) que contiene los datos de origen. 

    ![Botón New linked service (Nuevo servicio vinculado)](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

    1. Especifique **AzureStorageLinkedService** en el campo **Name** (Nombre). 
    2. Seleccione la cuenta de Azure Storage en el campo **Storage account name** (Nombre de la cuenta de Storage).
    3. Haga clic en **Test connection** (Prueba de conexión) para probar la conexión a la cuenta de Azure Storage.  
    4. Haga clic en **Save** (Guardar) para guardar el servicio vinculado.

        ![Nuevo servicio vinculado de Azure Storage](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. Haga clic en **Browse** (Examinar) en el campo **File path** (Ruta de acceso del archivo).  

    ![Botón Browse (Examinar) del archivo](./media/tutorial-copy-data-portal/file-browse-button.png)
14. Vaya a la carpeta **adftutorial/input**, seleccione el archivo **emp.txt** y haga clic en **Finish** (Finalizar). Como alternativa, puede hacer doble clic en emp.txt. 

    ![Seleccionar archivo de entrada](./media/tutorial-copy-data-portal/select-input-file.png)
15. Confirme que el **formato de archivo** está establecido en **Text format** (Formato de texto) y que el **delimitador de columna** está establecido en **Comma (Coma) (`,`)**. Si el archivo de origen usa diferentes delimitadores de fila y columna, haga clic en **Detect Text Format** (Detectar formato de texto) en el campo **File format** (Formato de archivo). La herramienta Copy Data detecta automáticamente el formato de archivo y los delimitadores. Todavía puede invalidar estos valores. Puede obtener una vista previa de los datos de esta página si hace clic en **Preview data** (Vista previa de los datos).

    ![Detectar el formato de texto](./media/tutorial-copy-data-portal/detect-text-format.png)
17. Cambie a la pestaña **Schema** (Esquema) de la ventana de propiedades y haga clic en **Import Schema** (Importar esquema). Tenga en cuenta que la aplicación detectó dos columnas en el archivo de origen. Aquí va a importar el esquema, así que puede asignar columnas del almacén de datos de origen al almacén de datos receptor. Si no es necesario asignar columnas, puede omitir este paso. En este tutorial, importará el esquema.

    ![Detectar el esquema de origen](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. Ahora, cambie a la **pestaña con la canalización** o haga clic en la canalización en la **vista de árbol** a la izquierda.  

    ![Pestaña Pipeline (Canalización)](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. Confirme que **SourceBlobDataset** se ha seleccionado en el campo del conjunto de datos de origen en la ventana de propiedades. Puede obtener una vista previa de los datos de esta página si hace clic en **Preview data** (Vista previa de los datos). 
    
    ![Conjunto de datos de origen](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. Cambie a la pestaña **Sink** (Receptor) y haga clic en **New** (Nuevo) para crear un conjunto de datos receptor. 

    ![Menú New sink dataset (Nuevo conjunto de datos receptor)](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar). En este tutorial, copiará datos en una base de datos de Azure SQL. 

    ![Seleccionar Azure SQL Database](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. En la pestaña **General** de la ventana de propiedades, establezca el nombre en **OutputSqlDataset**. 
    
    ![Nombre del conjunto de datos de salida](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. Cambie a la pestaña **Connection** (Conexión) y haga clic en **New** (Nuevo) en **Linked service** (Servicio vinculado). Un conjunto de datos debe estar asociado con un servicio vinculado. El servicio vinculado tiene la cadena de conexión que usa el servicio Data Factory para conectarse a la base de datos SQL de Azure en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) donde se copian los datos. 
    
    ![Botón New linked service (Nuevo servicio vinculado)](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

    1. Escriba **AzureSqlDatabaseLinkedService** en el campo **Name** (Nombre). 
    2. Seleccione el servidor de Azure SQL Server en el campo **Server name** (Nombre del servidor).
    4. Seleccione la base de datos SQL de Azure en el campo **Database name** (Nombre de la base de datos). 
    5. Escriba el nombre del usuario en el campo **User name** (Nombre de usuario). 
    6. Escriba la contraseña del usuario en el campo **Password** (Contraseña). 
    7. Haga clic en **Test connection** (Prueba de conexión) para probar la conexión.
    8. Haga clic en **Save** (Guardar) para guardar el servicio vinculado. 
    
        ![Nuevo servicio vinculado de Azure SQL Database](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. Seleccione **[dbo].[emp]** en **Table** (Tabla). 

    ![Seleccionar la tabla emp](./media/tutorial-copy-data-portal/select-emp-table.png)
27. Cambie a la pestaña **Schema** (Esquema) y haga clic en Import Schema (Importar un esquema). 

    ![Importar el esquema de destino](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. Seleccione la columna **ID** (Identificador) y haga clic en **Delete** (Eliminar). La columna ID (Identificador) es una columna de identidad de la base de datos SQL, por lo que no es necesario insertar datos en esta columna durante la actividad de copia.

    ![Eliminar columna ID (Identificador)](./media/tutorial-copy-data-portal/delete-id-column.png)
30. Cambie a la pestaña con la **canalización** y confirme que **OutputSqlDataset** se ha seleccionado en **Sink Dataset** (Conjunto de datos receptor).

    ![Pestaña Pipeline (Canalización)](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. Cambie a la pestaña **Mapping** (Asignación) en la ventana de propiedades de la parte inferior y haga clic en **Import Schemas** (Importar esquemas). Tenga en cuenta que las columnas primera y segunda del archivo de origen se asignan a los campos **FirstName** y **LastName** de la base de datos SQL.

    ![Esquemas de asignación](./media/tutorial-copy-data-portal/map-schemas.png)
33. Para comprobar la canalización, haga clic en el botón **Validate** (Comprobar). Haga clic en la **flecha derecha** para cerrar la ventana de comprobación.

    ![Salida de comprobación de canalización](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. Haga clic en el botón **Code** (Código) en la esquina derecha. Verá el código JSON asociado con la canalización. 

    ![Botón Code (Código)](./media/tutorial-copy-data-portal/code-button.png)
35. El código JSON será similar al fragmento de código siguiente:  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>Realización de la serie de pruebas de la canalización
Puede realizar la serie de pruebas de una canalización antes de publicar artefactos (servicios vinculados, conjuntos de datos y canalizaciones) en Data Factory (o) en su propio repositorio GIT de VSTS. 

1. Para realizar la serie de pruebas de la canalización, haga clic en **Test Run** (Serie de pruebas) en la barra de herramientas. Verá el estado de ejecución de la canalización en la pestaña **Output** (Salida) de la ventana en la parte inferior. 

    ![Botón Test Run (Serie de pruebas)](./media/tutorial-copy-data-portal/test-run-output.png)
2. Compruebe que los datos del archivo de origen se insertan en la base de datos SQL de destino. 

    ![Comprobar salida de SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Haga clic en **Publish** (Publicar) en el panel izquierdo. Esta acción publica las entidades (servicios vinculados, conjuntos de datos y canalizaciones) que creó para Azure Data Factory.

    ![Botón Publicar](./media/tutorial-copy-data-portal/publish-button.png)
4. Espere a que aparezca el mensaje **Successfully published** (Publicado correctamente). Para ver los mensajes de notificación, haga clic en **Show Notifications** (Mostrar notificaciones) en la barra lateral izquierda. Para cerrar la ventana de notificaciones, haga clic en la **X**.

    ![Show Notifications (Mostrar notificaciones)](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configuración del repositorio de código
Puede publicar el código asociado a los artefactos de la factoría de datos en un repositorio de código de Visual Studio Team System (VSTS). En este paso, creará el repositorio de código. 

Si no desea trabajar con el repositorio de código de VSTS, puede omitir este paso y continuar la publicación en Data Factory como lo hizo en el paso anterior. 

1. Haga clic en **Data Factory** en la esquina izquierda (o) en la flecha abajo que hay al lado, y haga clic en **Configure Code Repository** (Configurar repositorio de código). 

    ![Botón Code (Código)](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. En la página **Repository Settings** (Configuración del repositorio), realice los siguientes pasos: 
    1. Seleccione **Visual Studio Team Services Git** (Git de Visual Studio Team Services) en el campo **Repository Type** (Tipo de repositorio).
    2. Seleccione su cuenta de VSTS en el campo **Visual Studio Team Services Account** (Cuenta de Visual Studio Team Services).
    3. Seleccione un proyecto en su cuenta de VSTS en el campo **Project Name** (Nombre del proyecto).
    4. Escriba **Tutorial2** como **nombre del repositorio Git** que se asociará a la factoría de datos. 
    5. Confirme que está seleccionada la opción **Import existing Data Factory resources to repository** (Importar recursos existentes de Data Factory en el repositorio). 
    6. Haga clic en **Guardar** para guardar la configuración. 

        ![Configuración del repositorio](./media/tutorial-copy-data-portal/repository-settings.png)
3. Confirme que se selecciona **VSTS GIT** (GIT de VSTS) como repositorio.

    ![VSTS GIT (GIT de VSTS) seleccionado](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. En una pestaña independiente del explorador web, vaya al repositorio **Tutorial2**; verá dos ramas: **master** y **adf_publish**.

    ![Ramas master y adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Compruebe que los **archivos JSON** de las entidades de Data Factory están en la rama **master**.

    ![Archivos de la rama master](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Compruebe que los **archivos JSON** no están aún en la rama **adf_publish**. 

    ![Archivos de la rama adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Agregue una **descripción** para la **canalización** y haga clic en el botón **Save** (Guardar) de la barra de herramientas. 

    ![Agregar una descripción para la canalización](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Ahora, debería ver una **rama** con su nombre de usuario en el repositorio **Tutorial2**. El cambio que se realiza está en su propia rama, no en la rama master. Solo puede publicar entidades de la rama master.

    ![Su rama](./media/tutorial-copy-data-portal/your-branch.png)
9. Mueva el puntero sobre el botón **Sync** (Sincronizar) (no haga clic aún), seleccione la opción **Commit Changes** (Confirmar cambios) y haga clic en el botón **Sync** (Sincronizar) para sincronizar los cambios con la rama **master**. 

    ![Confirmar y sincronizar los cambios](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. En la ventana de sincronización de los cambios, realice las siguientes acciones: 

    1. Confirme que se muestra **CopyPipeline** en la lista de canalizaciones actualizada.
    2. Confirme que la opción **Publish changes after sync** (Publicar los cambios después de la sincronización) está seleccionada. Si desactiva esta opción, solo se sincronizarán los cambios de la rama con la rama master, pero no se publicaran en el servicio Data Factory. Puede publicarlos más adelante con el botón **Publish** (Publicar). Si selecciona esta opción, los cambios se sincronizan primero con la rama master y luego se publican en el servicio Data Factory.
    3. Haga clic en **Sync** (Sincronizar). 

    ![Ventana Sync your changes (Sincronizar los cambios)](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. Ahora, puede ver los archivos de la rama **adf_publish** del repositorio **Tutorial2**. También puede encontrar en esta rama la plantilla de Azure Resource Manager de su solución de Data Factory.  

    ![Archivos de la rama adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Desencadenamiento manual de la canalización
En este paso, desencadenará manualmente la canalización que publicó en el paso anterior. 

1. Haga clic en **Trigger** (Desencadenar) en la barra de herramientas y en **Trigger Now** (Desencadenar ahora). 

    ![Menú Trigger now (Desencadenar ahora)](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Verá una ejecución de canalización que se desencadena de forma manual. Puede usar los vínculos de la columna Actions (Acciones) para ver los detalles de la actividad y volver a ejecutar la canalización.

    ![Supervisar ejecución de canalización](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Para ver las ejecuciones de actividad asociadas con la ejecución de esta canalización, haga clic en la acción **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). En este ejemplo, solo hay una actividad, así que solo verá una entrada en la lista. Para ver detalles sobre la operación de copia, haga clic en el vínculo **Details** (Detalles) (icono de gafas) de la columna **Actions** (Acciones). Puede hacer clic en **Pipelines** (Canalizaciones) en la parte superior para volver a la vista **Pipeline Runs** (Ejecuciones de canalización). Para actualizar la vista, haga clic en **Refresh** (Actualizar).

    ![Ver ejecuciones de actividad](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Compruebe que se agregan dos filas más a la tabla **emp** de la base de datos SQL de Azure. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Desencadenamiento de la canalización de forma programada
En esta programación, creará un desencadenador de programación para la canalización. El desencadenador ejecuta la canalización de acuerdo con la programación especificada (diariamente, cada hora, etc.). En este ejemplo, establecerá el desencadenador para que se ejecute cada minuto hasta la fecha y hora de finalización especificadas. 

1. Cambie a la pestaña **Edit** (Editar) de la izquierda. 

    ![Pestaña Edit (Editar)](./media/tutorial-copy-data-portal/edit-tab.png)
2. Haga clic en **Trigger** (Desencadenador) y seleccione **New/Edit** (Nuevo/Editar). Si la canalización no está activa, actívela. 

    ![Menú New/edit (Nuevo/Editar) del desencadenador](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. En la ventana **Add Triggers** (Agregar desencadenadores), haga clic en **Choose trigger...** (Elegir desencadenador) y luego en **+ New** (+Nuevo). 

    ![Add Triggers (Agregar desencadenadores): nuevo desencadenador](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. En la ventana **New Trigger** (Nuevo desencadenador), lleve a cabo los siguientes pasos: 

    1. Establezca el **nombre** en **RunEveryMinute**.
    2. Seleccione **On Date** (El día) en **End** (Fin). 
    3. Haga clic en la lista desplegable en **End On** (Finaliza el).
    4. Seleccione el **día actual**. De forma predeterminada, el día de finalización se establece en el día siguiente. 
    5. Actualice la sección de **minutos** con unos cuantos minutos que pasen de la fecha y hora actuales. El desencadenador se activa únicamente después de publicar los cambios. Por lo tanto, si lo establece solo en un par de minutos de diferencia y no lo publica para entonces, no verá una ejecución de desencadenador.  
    6. Haga clic en **Aplicar**. 

        ![Establecer propiedades de desencadenador](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. Active la opción **Activated** (Activado). Puede desactivarla y activarla más adelante mediante esta casilla.
    8. Haga clic en **Next**.

        ![Desencadenador activado: siguiente](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Hay un costo asociado a cada ejecución de canalización. Por consiguiente, establezca correctamente la fecha de finalización. 
6. En la página **Trigger Run Parameters** (Parámetros de ejecución de desencadenador), revise la advertencia y haga clic en **Finish** (Finalizar). La canalización de este ejemplo no toma ningún parámetro. 

    ![Parámetros de canalización](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. Haga clic en **Publish** (Publicar) para publicar los cambios en el repositorio. El desencadenador no se activa realmente hasta que la publicación se realiza correctamente. 

    ![Publicar desencadenador](./media/tutorial-copy-data-portal/publish-trigger.png) 
8. Cambie a la pestaña **Monitor** (Supervisión) de la izquierda para ver las ejecuciones de canalización desencadenadas. 

    ![Ejecuciones de canalización desencadenadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. Para cambiar de la vista de ejecuciones de canalización a la vista de ejecuciones de desencadenador, haga clic en Pipeline Runs (Ejecuciones de canalización) y seleccione Trigger Runs (Ejecuciones de desencadenador).
    
    ![Menú Trigger runs (Ejecuciones de desencadenador)](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. Verá las ejecuciones de desencadenador en una lista. 

    ![Lista de ejecuciones de desencadenador](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. Compruebe que se insertan dos filas por minuto (para cada ejecución de canalización) en la tabla **emp** hasta la hora de finalización especificada. 

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de una canalización con la actividad de copia
> * Realización de la serie de pruebas de la canalización
> * Desencadenamiento manual de la canalización
> * Desencadenamiento de la canalización de forma programada
> * Supervisión de las ejecuciones de canalización y actividad.


Pase al tutorial siguiente para obtener información sobre cómo copiar datos desde el entorno local a la nube: 

> [!div class="nextstepaction"]
>[Copiar datos del entorno local a la nube](tutorial-hybrid-copy-portal.md)
