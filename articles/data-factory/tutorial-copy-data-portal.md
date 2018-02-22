---
title: "Uso de Azure Portal para crear una canalización de factoría de datos | Microsoft Docs"
description: "En este tutorial se proporcionan instrucciones paso a paso para usar Azure Portal para crear una factoría de datos con una canalización. La canalización usa la actividad de copia para copiar datos de Azure Blob Storage a SQL Database."
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
ms.openlocfilehash: 116832175a4b7e4497c9005be7841cb56c1d235b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copia de datos de Azure Blob Storage a SQL Database mediante Azure Data Factory
En este tutorial, creará una factoría de datos mediante la interfaz de usuario (UI) de Azure Data Factory. La canalización de esta factoría de datos copia los datos de Azure Blob Storage a Azure SQL Database. El patrón de configuración de este tutorial se aplica a la copia de un almacén de datos basado en archivos a un almacén de datos relacional. Para obtener una lista de los almacenes de datos que se admiten como orígenes y receptores, consulte la tabla de [almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Si no está familiarizado con Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).
>
> - Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de una canalización con una actividad de copia.
> * Realización de la serie de pruebas de la canalización.
> * Desencadenamiento manual de la canalización.
> * Desencadenamiento de la canalización de forma programada.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>requisitos previos
* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de Azure Storage**. Usará Blob Storage como almacén de datos de *origen*. Si no tiene una cuenta de almacenamiento, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para crear una.
* **Azure SQL Database**. Usará la base de datos como un almacén de datos *receptor*. Si no tiene una base de datos SQL, consulte [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para crear una.

### <a name="create-a-blob-and-a-sql-table"></a>Creación de un blob y una tabla SQL

Ahora, prepare su almacenamiento de blobs y su base de datos SQL para el tutorial mediante los pasos siguientes:

#### <a name="create-a-source-blob"></a>Creación de un blob de origen

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como un archivo **emp.txt** en el disco:

    ```
    John,Doe
    Jane,Doe
    ```

2. Cree un contenedor denominado **adftutorial** en su instancia de Blob Storage. Cree una carpeta denominada **input** en este contenedor. A continuación, cargue el archivo **emp.txt** en la carpeta **input**. Use Azure Portal o herramientas como [Explorador de Azure Storage](http://storageexplorer.com/) para realizar estas tareas.

#### <a name="create-a-sink-sql-table"></a>Creación de una tabla SQL receptora

1. Use el siguiente script de SQL para crear la tabla **dbo.emp** en la base de datos SQL:

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

2. Permita que los servicios de Azure accedan a SQL Server. Asegúrese de que **Permitir el acceso a servicios de Azure** esté **Activado** para SQL Server de forma que Data Factory pueda escribir datos en su instancia de SQL Server. Para comprobar y activar esta configuración, realice los siguientes pasos:

    a. En el lado izquierdo, seleccione **Más servicios** > **Servidores SQL Server**.

    b. Seleccione el servidor y, en **Configuración**, seleccione **Firewall**.

    c. En la página **Configuración de firewall**, seleccione **Activado** en **Permitir el acceso a servicios de Azure**.

## <a name="create-a-data-factory"></a>Crear una factoría de datos
En este paso, creará una factoría de datos e iniciará la interfaz de usuario de Data Factory para crear una canalización en la factoría de datos. 

1. Abra el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. En el menú izquierdo, seleccione **Nuevo** > **Datos y análisis** > **Data Factory**. 
  
   ![Creación de nueva factoría de datos](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**. 
      
     ![Nueva factoría de datos](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser *único de forma global*. Si ve el siguiente mensaje de error en el campo de nombre, cambie el nombre de la factoría de datos (por ejemplo, suNombreADFTutorialDataFactory). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   ![Mensaje de error](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Seleccione la **suscripción** de Azure en la que quiere crear la factoría de datos. 
5. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
    a. Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable.

    b. Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos. 
         
    Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md). 
6. En **Versión**, seleccione **V2 (versión preliminar)**.
7. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa la factoría de datos pueden estar en otras regiones.
8. Seleccione **Anclar al panel**. 
9. Seleccione **Crear**. 
10. En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory): 

    ![Icono de implementación de una factoría de datos](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. Una vez finalizada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    ![Página principal Factoría de datos](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente.

## <a name="create-a-pipeline"></a>Crear una canalización
En este paso, creará una canalización con una actividad de copia en la factoría de datos. La actividad de copia realiza la copia de los datos de Blob Storage a SQL Database. En el [tutorial de inicio rápido](quickstart-create-data-factory-portal.md),creó una canalización mediante estos pasos:

1. Creación del servicio vinculado. 
2. Creación del conjunto de datos de entrada y salida.
3. Creación de una canalización

En este tutorial, comenzará a crear la canalización. A continuación, creará servicios vinculados y conjuntos de datos cuando los necesite para configurar la canalización. 

1. En la página **Let's get started** (Introducción) seleccione **Create pipeline** (Crear canalización). 

   ![Creación de una canalización](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. En la ventana **Properties** (Propiedades) de la canalización, en **Name** (Nombre), escriba **CopyPipeline** como nombre de la canalización.

    ![Nombre de la canalización](./media/tutorial-copy-data-portal/pipeline-name.png)
3. En el cuadro de herramientas **Activities** (Actividades), expanda la categoría **Data Flow** (Flujo de datos) y arrastre y suelte la actividad **Copy** (Copia) desde el cuadro de herramientas hasta la superficie de diseño de la canalización. 

    ![Actividad de copia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. En la pestaña **General** de la ventana **Properties** (Propiedades), especifique **CopyFromBlobToSql** como nombre de la actividad.

    ![Nombre de la actividad](./media/tutorial-copy-data-portal/activity-name.png)
5. Vaya a la pestaña **Source** (Origen). Haga clic en **+ New** (+ Nuevo) para crear un conjunto de datos de origen. 

    ![Pestaña Source (Origen)](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y después **Finish** (Finalizar). Los datos de origen están en Blob Storage, así que seleccionará **Azure Blob Storage** como conjunto de datos de origen. 

    ![Selección de almacenamiento](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. Verá que se abre una nueva pestaña en la aplicación que se llama **AzureBlob1**.

    ![Pestaña AzureBlob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. En la pestaña **General** de la parte inferior de la ventana **Properties** (Propiedades), en **Name** (Nombre), escriba **SourceBlobDataset**.

    ![Nombre del conjunto de datos](./media/tutorial-copy-data-portal/dataset-name.png)
9. Vaya a la pestaña **Connection** (Conexión) de la ventana **Properties** (Propiedades). Junto al cuadro de texto **Linked service** (Servicio vinculado), seleccione **+ New** (+Nuevo). 

    Un servicio vinculado enlaza un almacén de datos o un proceso a la factoría de datos. En este caso, creará un servicio vinculado de almacenamiento para vincular la cuenta de almacenamiento al almacén de datos. El servicio vinculado tiene la información de conexión que usa Data Factory para conectarse a Blob Storage en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) que contiene los datos de origen. 

    ![Botón New linked service (Nuevo servicio vinculado)](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

    a. En **Name** (Nombre), escriba **AzureStorageLinkedService**. 

    b. En **Storage account name** (Nombre de la cuenta de almacenamiento), seleccione la cuenta de almacenamiento.

    c. Seleccione **Test connection** (Prueba de conexión) para probar la conexión a la cuenta de almacenamiento.

    d. Seleccione **Save** (Guardar) para guardar el servicio vinculado.

    ![Nuevo servicio vinculado](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. Junto a **File path** (Ruta de acceso del archivo), seleccione **Browse** (Examinar).

    ![Botón Browse (Examinar) de la ruta de acceso del archivo](./media/tutorial-copy-data-portal/file-browse-button.png)
12. Vaya a la carpeta **adftutorial/input**, seleccione el archivo **emp.txt** y, luego, **Finish** (Finalizar). Como alternativa, puede hacer doble clic en **emp.txt**. 

    ![Seleccionar archivo de entrada](./media/tutorial-copy-data-portal/select-input-file.png)
13. Confirme que el **formato de archivo** está establecido en **Text format** (Formato de texto) y que el **delimitador de columna** está establecido en **Comma (Coma) (`,`)**. Si el archivo de origen usa diferentes delimitadores de fila y columna, puede seleccionar **Detect Text Format** (Detectar formato de texto) en **File format** (Formato de archivo). La herramienta Copy Data detecta automáticamente el formato de archivo y los delimitadores. Todavía puede invalidar estos valores. Para obtener una vista previa de los datos de esta página, seleccione **Preview data** (Vista previa de los datos).

    ![Detectar el formato de texto](./media/tutorial-copy-data-portal/detect-text-format.png)
14. Vaya a la pestaña **Schema** (Esquema) de la ventana **Properties** (Propiedades) y seleccione **Import Schema**. (Importar esquema). Tenga en cuenta que la aplicación detectó dos columnas en el archivo de origen. Aquí importará el esquema, así que puede asignar columnas del almacén de datos de origen al almacén de datos receptor. Si no es necesario asignar columnas, puede omitir este paso. En este tutorial, importará el esquema.

    ![Detectar el esquema de origen](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. Ahora, vaya a la pestaña con la canalización o seleccione la canalización a la izquierda.

    ![Pestaña Pipeline (Canalización)](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. En **Source Dataset** (Conjunto de datos de origen) en la ventana **Properties** (Propiedades), confirme que se ha seleccionado **SourceBlobDataset**. Para obtener una vista previa de los datos de esta página, seleccione **Preview data** (Vista previa de los datos). 
    
    ![Conjunto de datos de origen](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. Vaya a la pestaña **Sink** (Receptor) y seleccione **+ New** (+Nuevo) para crear un conjunto de datos del receptor. 

    ![Conjunto de datos del receptor](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y luego seleccione **Finish** (Finalizar). En este tutorial, copiará los datos en una base de datos SQL. 

    ![Selección de base de datos SQL](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. En la pestaña **General** de la ventana **Properties** (Propiedades), en **Name** (Nombre), escriba **OutputSqlDataset**. 
    
    ![Nombre del conjunto de datos de salida](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. Vaya a la pestaña **Connection** (Conexión) y, junto a **Linked service** (Servicio vinculado), seleccione **+ New** (+Nuevo). Un conjunto de datos debe estar asociado con un servicio vinculado. El servicio vinculado tiene la cadena de conexión que usa Data Factory para conectarse a la base de datos SQL en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) donde se copian los datos. 
    
    ![Servicio vinculado](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

    a. En **Name** (Nombre), escriba **AzureSqlDatabaseLinkedService**.

    b. En **Server name** (Nombre del servidor), seleccione su instancia de SQL Server.

    c. En **Database name** (Nombre de la base de datos), seleccione la base de datos SQL.

    d. En **User name** (Nombre de usuario), escriba el nombre del usuario.

    e. En **Password** (Contraseña), escriba la contraseña del usuario.

    f. Seleccione **Test connection** (Prueba de conexión) para probar la conexión.

    g. Seleccione **Save** (Guardar) para guardar el servicio vinculado. 
    
    ![Guardar nuevo servicio vinculado](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. En **Table** (Tabla), seleccione **[dbo].[emp]**. 

    ![Tabla](./media/tutorial-copy-data-portal/select-emp-table.png)
23. Vaya a la pestaña **Schema** (Esquema) y seleccione **Import Schema** (Importar esquema). 

    ![Seleccionar Import schema (Importar esquema)](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. Seleccione la columna **ID** (Id.) y luego seleccione **Delete** (Eliminar). La columna **ID** (Id.) es una columna de identidad de la base de datos SQL, por lo que no es necesario insertar datos en esta columna durante la actividad de copia.

    ![Eliminar columna ID (Identificador)](./media/tutorial-copy-data-portal/delete-id-column.png)
25. Vaya a la pestaña con la canalización y, en **Sink Dataset** (Conjunto de datos del receptor), confirme que se ha seleccionado **OutputSqlDataset**.

    ![Pestaña Pipeline (Canalización)](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. Vaya a la pestaña **Mapping** (Asignación) en la parte inferior de la ventana **Properties** (Propiedades) y seleccione **Import Schemas** (Importar esquemas). Tenga en cuenta que las columnas primera y segunda del archivo de origen se asignan a **FirstName** y **LastName** de la base de datos SQL.

    ![Esquemas de asignación](./media/tutorial-copy-data-portal/map-schemas.png)
27. Para validar la canalización, seleccione **Validate** (Validar). En la esquina superior derecha, seleccione la flecha derecha para cerrar la ventana de validación.

    ![Salida de comprobación de canalización](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. En la esquina superior derecha, seleccione **Code** (Código). Verá el código JSON asociado con la canalización. 

    ![Botón Code (Código)](./media/tutorial-copy-data-portal/code-button.png)
29. El código JSON será similar al fragmento de código siguiente: 

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
Puede realizar la serie de pruebas de una canalización antes de publicar artefactos (servicios vinculados, conjuntos de datos y canalizaciones) en Data Factory o en su propio repositorio Git de Visual Studio Team Services. 

1. Para realizar la serie de pruebas de la canalización, seleccione **Test Run** (Serie de pruebas) en la barra de herramientas. Verá el estado de ejecución de la canalización en la pestaña **Output** (Salida) en la parte inferior de la ventana. 

    ![Probar canalización](./media/tutorial-copy-data-portal/test-run-output.png)
2. Compruebe que los datos del archivo de origen se insertan en la base de datos SQL de destino. 

    ![Comprobar salida de SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. En el panel izquierdo, seleccione **Publish All** (Publicar todo). Esta acción publica las entidades (servicios vinculados, conjuntos de datos y canalizaciones) que creó para Data Factory.

    ![Publicar](./media/tutorial-copy-data-portal/publish-button.png)
4. Espere a que aparezca el mensaje **Successfully published** (Publicado correctamente). Para ver los mensajes de notificación, haga clic en la pestaña **Show Notifications** (Mostrar notificaciones) en la barra lateral izquierda. Para cerrar la ventana de notificaciones, seleccione **Close** (Cerrar).

    ![Show Notifications (Mostrar notificaciones)](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configuración del repositorio de código
Puede publicar el código asociado a los artefactos de la factoría de datos en un repositorio de código de Visual Studio Team Services. En este paso, creará el repositorio de código. 

Si no quiere trabajar con el repositorio de código de Visual Studio Team Services, puede omitir este paso. Puede seguir publicando en Data Factory como hizo en el paso anterior. 

1. En la esquina superior izquierda, seleccione **Data Factory** o use la flecha abajo que hay al lado y seleccione **Configure Code Repository** (Configurar repositorio de código). 

    ![Configuración del repositorio de código](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. En la página **Repository Settings** (Configuración del repositorio), realice los siguientes pasos:

    a. En **Repository Type** (Tipo de repositorio), seleccione **Visual Studio Team Services Git** (Git de Visual Studio Team Services).

    b. En **Visual Studio Team Services Account** (Cuenta de Visual Studio Team Services), seleccione su cuenta de Visual Studio Team Services.

    c. En **Project Name** (Nombre del proyecto), seleccione un proyecto en su cuenta de Visual Studio Team Services.

    d. En **Git repository name** (Nombre del repositorio Git), escriba **Tutorial2** como el repositorio Git que se va a asociar con la factoría de datos.

    e. Confirme que está activada la casilla **Import existing Data Factory resources to repository** (Importar recursos existentes de Data Factory en el repositorio).

    f. Haga clic en **Save** (Guardar) para guardar la configuración. 

    ![Configuración del repositorio](./media/tutorial-copy-data-portal/repository-settings.png)
3. Confirme que se selecciona **VSTS GIT** (GIT de VSTS) como repositorio.

    ![Seleccionar VSTS GIT](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. En una pestaña independiente del explorador web, vaya al repositorio **Tutorial2**. Verá dos ramas: **adf_publish** y **master**.

    ![Ramas master y adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Compruebe que los archivos JSON de las entidades de Data Factory están en la rama **master**.

    ![Archivos de la rama master](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Compruebe que los archivos JSON no están aún en la rama **adf_publish**. 

    ![Archivos de la rama adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. En **Description** (Descripción), agregue una descripción para la canalización y seleccione **Save** (Guardar) en la barra de herramientas. 

    ![Descripción de una canalización](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Ahora, verá una rama con el nombre de usuario en el repositorio **Tutorial2**. El cambio que se realiza está en su propia rama, no en la rama master. Solo puede publicar entidades de la rama master.

    ![Su rama](./media/tutorial-copy-data-portal/your-branch.png)
9. Mueva el mouse sobre el botón **Sync** (Sincronizar) (no lo seleccione aún), active la casilla **Commit Changes** (Confirmar cambios) y seleccione **Sync** (Sincronizar) para sincronizar los cambios con la rama master. 

    ![Confirmar y sincronizar cambios](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. En la ventana **Sync your changes** (Sincronizar cambios), realice las siguientes acciones: 

    a. Confirme que se muestra **CopyPipeline** en la lista de **canalizaciones** actualizada.

    b. Confirme que la opción **Publish changes after sync** (Publicar los cambios después de la sincronización) está seleccionada. Si desactiva esta casilla, solo se sincronizan los cambios de la rama con la rama master. No se publican en Data Factory. Puede publicarlos más adelante con el botón **Publish** (Publicar). Si selecciona esta casilla, los cambios se sincronizan primero con la rama master y luego se publican en Data Factory.

    c. Seleccione **Sincronizar**. 

    ![Sincronizar los cambios](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. Ahora, puede ver los archivos de la rama **adf_publish** del repositorio **Tutorial2**. También puede encontrar en esta rama la plantilla de Azure Resource Manager de su solución de Data Factory. 

    ![Lista de archivos de la rama adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Desencadenamiento manual de la canalización
En este paso, desencadenará manualmente la canalización que publicó en el paso anterior. 

1. Seleccione **Trigger** (Desencadenar) en la barra de herramientas y, después, seleccione **Trigger Now** (Desencadenar ahora). En la página **Pipeline Run** (Ejecución de canalización), seleccione **Finish** (Finalizar).  

    ![Desencadenar canalizaciones](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Vaya a la pestaña **Monitor** (Supervisar) de la izquierda. Verá una ejecución de canalización que se desencadena de forma manual. Puede usar los vínculos de la columna **Actions** (Acciones) para ver los detalles de la actividad y volver a ejecutar la canalización.

    ![La supervisión de la canalización se ejecuta](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Para ver las ejecuciones de actividad asociadas con la ejecución de canalización, seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en la columna **Actions** (Acciones). En este ejemplo, solo hay una actividad, así que solo verá una entrada en la lista. Para ver detalles sobre la operación de copia, seleccione el vínculo **Details** (Detalles) (icono de gafas) en la columna **Actions** (Acciones). Seleccione **Pipelines** (Canalizaciones) en la parte superior para volver a la vista **Pipeline Runs** (Ejecuciones de canalización). Para actualizar la vista, seleccione **Refresh** (Actualizar).

    ![Supervisión de las ejecuciones de actividad](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Compruebe que se agregan dos filas más a la tabla **emp** de la base de datos SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Desencadenamiento de la canalización de forma programada
En esta programación, creará un desencadenador de programación para la canalización. El desencadenador ejecuta la canalización de acuerdo con la programación especificada, como diariamente o cada hora. En este ejemplo, establecerá el desencadenador para que se ejecute cada minuto hasta la fecha y hora de finalización especificadas. 

1. Vaya a la pestaña **Edit** (Editar) de la izquierda. 

    ![Pestaña Edit (Editar)](./media/tutorial-copy-data-portal/edit-tab.png)
2. Seleccione **Trigger** (Desencadenador) y luego **New/Edit** (Nuevo/Editar). Si la canalización no está activa, vaya a ella. 

    ![Opción de desencadenador](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. En la página **Add Triggers** (Agregar desencadenadores), seleccione **Choose trigger** (Elegir desencadenador) y, después, seleccione **+New** (+Nuevo). 

    ![New button](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. En la ventana **New Trigger** (Nuevo desencadenador), lleve a cabo los siguientes pasos: 

    a. En **Name** (Nombre), escriba **RunEveryMinute**.

    b. En **End** (Fin), seleccione **On Date** (El día).

    c. En **End On** (Finaliza el), seleccione la lista desplegable.

    d. Seleccione la opción **current day** (día actual). De forma predeterminada, el día de finalización se establece en el día siguiente.

    e. Actualice la sección de **minutos** con unos cuantos minutos que pasen de la fecha y hora actuales. El desencadenador se activa únicamente después de publicar los cambios. Si lo establece solo en un par de minutos de diferencia y no lo publica para entonces, no verá una ejecución de desencadenador.

    f. Seleccione **Aplicar**. 

    ![Propiedades de desencadenador](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Seleccione la opción **Activated** (Activado). Puede desactivarla y activarla más adelante mediante esta casilla.

    h. Seleccione **Siguiente**.

    ![Botón Activated (Activado)](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Con cada ejecución de canalización se asocia un costo, así que establezca la fecha de finalización correctamente. 
5. En la página **Trigger Run Parameters** (Parámetros de ejecución de desencadenador), revise la advertencia y luego seleccione **Finish** (Finalizar). La canalización de este ejemplo no toma ningún parámetro. 

    ![Parámetros de ejecución de desencadenador](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. Seleccione **Sync** (Sincronizar) para sincronizar los cambios en la rama con la rama master. De forma predeterminada, se selecciona **Publish changes after sync** (Publicar los cambios después de sincronizarlos). Por lo tanto, cuando se selecciona **Sync** (Sincronizar), también se publican las entidades actualizadas en Data Factory desde la rama master. El desencadenador no se activa realmente hasta que la publicación se realiza correctamente.

    ![Sincronizar los cambios](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. Vaya a la pestaña **Monitor** (Supervisión) de la izquierda para ver las ejecuciones de canalización desencadenadas. 

    ![Ejecuciones de canalización desencadenadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. Para cambiar de la vista **Pipeline Runs** (Ejecuciones de canalización) a la vista **Trigger Runs** (Ejecuciones de desencadenador), seleccione **Pipeline Runs** (Ejecuciones de canalización) y seleccione **Trigger Runs** (Ejecuciones de desencadenador).
    
    ![Ejecuciones de desencadenador](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. Verá las ejecuciones de desencadenador en una lista. 

    ![Lista de ejecuciones de desencadenador](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. Compruebe que se insertan dos filas por minuto (para cada ejecución de canalización) en la tabla **emp** hasta la hora de finalización especificada. 

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra de Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de una canalización con una actividad de copia.
> * Realización de la serie de pruebas de la canalización.
> * Desencadenamiento manual de la canalización.
> * Desencadenamiento de la canalización de forma programada.
> * Supervisión de las ejecuciones de canalización y actividad.


Para aprender a copiar datos desde el entorno local a la nube, avance al tutorial siguiente: 

> [!div class="nextstepaction"]
>[Copia de datos del entorno local a la nube](tutorial-hybrid-copy-portal.md)
