---
title: Copia de datos mediante la herramienta Copy Data de Azure | Microsoft Docs
description: "Cree una factoría de datos de Azure y, luego, use la herramienta Copy Data para copiar datos de Azure Blob Storage a Azure SQL Database."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>Copia de datos de Azure Blob a Azure SQL Database mediante la herramienta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](tutorial-copy-data-tool.md)

En este tutorial, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copy Data para crear una canalización que copia datos de una instancia de Azure Blob Storage a una instancia de Azure SQL Database. 

> [!NOTE]
> - Si no está familiarizado con Azure Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).
>
> - Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>requisitos previos

* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de Azure Storage**. Blob Storage se puede usar como almacén de datos de **origen**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.
* **Azure SQL Database**. La base de datos se puede usar como almacén de datos **receptor**. Si no tiene ninguna instancia de Azure SQL Database, consulte el artículo [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación.

### <a name="create-a-blob-and-a-sql-table"></a>Creación de un blob y una tabla SQL

Ahora, prepare su blob de Azure y su instancia de Azure SQL Database para el tutorial. Para ello, siga los pasos siguientes:

#### <a name="create-a-source-blob"></a>Creación de un blob de origen

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como archivo **inputEmp.txt** en el disco.

    ```
    John|Doe
    Jane|Doe
    ```

2. Use herramientas como [Explorador de Azure Storage](http://storageexplorer.com/) para crear el contenedor **adfv2tutorial** y cargar el archivo **inputEmp.txt** en el contenedor.

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

2. Permita que los servicios de Azure accedan a SQL Server. Asegúrese de que la opción **Permitir el acceso a servicios de Azure** esté habilitada para su instancia de Azure SQL Server para que el servicio Data Factory pueda escribir datos ahí. Para comprobar y activar esta configuración, realice los siguientes pasos:

    1. Haga clic en el concentrador **Más servicios** a la izquierda y haga clic en **Servidores SQL**.
    2. Seleccione el servidor y haga clic en **Firewall** en **CONFIGURACIÓN**.
    3. En la hoja **Configuración de firewall**, haga clic en **Activar** para **Permitir el acceso a los servicios de Azure**.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
      Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras ubicaciones o regiones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Azure Data Factory en una pestaña independiente. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página de introducción, haga clic en el icono **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Icono de la herramienta Copy Data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades) de la herramienta Copy Data, especifique **CopyFromBlobToSqlPipeline** en **Task name** (Nombre de la tarea) y haga clic **Next** (Siguiente). La interfaz de usuario de Data Factory crea una canalización con el nombre especificado como nombre de la tarea. 

    ![Herramienta Copy Data: página de propiedades](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **Azure Blob Storage** y haga clic en **Next** (Siguiente). Los datos de origen están en una instancia de Azure Blob Storage. 

    ![Página Source data store (Almacén de datos de origen)](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. En la página **Specify the Azure Blob storage account** (Especificar la cuenta de Azure Blob Storage), realice los pasos siguientes: 
    1. Escriba **AzureStorageLinkedService** como **nombre de la conexión**.
    2. Seleccione el **nombre de la cuenta de Storage**  en la lista desplegable.
    3. Haga clic en **Next**. 

        ![Especificar cuenta de Blob Storage](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        Un servicio vinculado enlaza un almacén de datos o un proceso a la factoría de datos. En este caso, creará un servicio vinculado de Azure Storage para vincular su cuenta de Azure Storage al almacén de datos. El servicio vinculado tiene la información de conexión que usa el servicio Data Factory para conectarse a la instancia de Blob Storage en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) que contiene los datos de origen. 
5. En la página **Choose the input file or folder** (Elegir el archivo o la carpeta de entrada), lleve a cabo los siguientes pasos:
    
    1. Navegue hasta la carpeta **adfv2tutorial/input**.
    2. Seleccione **inputEmp.txt**.
    3. Haga clic en **Choose** (Elegir). Como alternativa, puede hacer doble clic en **inputEmp.txt**. 
    4. Haga clic en **Next**. 

    ![Elegir archivo o carpeta de entrada](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. En la página **File format settings** (Configuración de formato de archivo), observe que la herramienta detecta automáticamente los delimitadores de columna y fila; haga clic en **Next** (Siguiente). También puede obtener una vista previa de los datos y ver el esquema de los datos de entrada en esta página. 

    ![Página File format settings (Configuración de formato de archivo)](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure SQL Database** y haga clic en **Next** (Siguiente). 

    ![Página Destination data store (Almacén de datos de destino)](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. En la página **Specify the Azure SQL database** (Especificar la base de datos SQL de Azure), lleve a cabo los siguientes pasos: 

    1. Especifique **AzureSqlDatabaseLinkedService** como **nombre de conexión**. 
    2. Seleccione el servidor de Azure SQL Server en **Server name** (Nombre del servidor).
    3. Seleccione la base de datos SQL de Azure en **Database name** (Nombre de la base de datos).
    4. Especifique el nombre de usuario en **User name** (Nombre de usuario).
    5. Especifique la contraseña del usuario en **Password** (Contraseña).
    6. Haga clic en **Next**. 

        ![Especificar la base de datos SQL de Azure](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        Un conjunto de datos debe estar asociado con un servicio vinculado. El servicio vinculado tiene la cadena de conexión que usa el servicio Data Factory para conectarse a la base de datos SQL de Azure en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) donde se copian los datos.
9.  En la página **Table mapping** (Asignación de tabla), seleccione **[dbo].[emp]** y haga clic en **Next** (Siguiente). 

    ![Página Table mapping (Asignación de tabla)](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. En la página **Schema mapping** (Asignación de esquema), observe que las columnas primera y segunda del archivo de entrada se asignan a las columnas **FirstName** y **LastName** de la tabla **emp**. 

    ![Página Schema mapping (Asignación de esquema)](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. En la página **Settings** (Configuración), haga clic en **Next** (Siguiente). 

    ![Página Configuración](./media/tutorial-copy-data-tool/settings-page.png)
12. En la página **Summary** (Resumen), revise la configuración y haga clic en **Next** (Siguiente).

    ![Página de resumen](./media/tutorial-copy-data-tool/summary-page.png)
13. En la página **Deployment** (Implementación), haga clic en **Monitor** (Supervisión) para supervisar la canalización (tarea).

    ![Página Deployment (Implementación)](./media/tutorial-copy-data-tool/deployment-page.png)
14. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. En la columna **Actions** (Acciones), se muestran los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización. Haga clic en **Refresh** (Actualizar) para actualizar la lista. 

    ![La supervisión de la canalización se ejecuta](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Para ver las ejecuciones de actividad asociadas a la de la canalización, haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para ver detalles sobre la operación de copia, haga clic en el vínculo **Details** (Detalles) (icono de gafas) de la columna **Actions** (Acciones). Para volver a la vista de ejecuciones de canalización, haga clic en el vínculo **Pipelines** (Canalizaciones) de la parte superior. Para actualizar la vista, haga clic en **Refresh** (Actualizar). 

    ![Supervisión de las ejecuciones de actividad](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Haga clic en la pestaña **Edit** (Editar) de la izquierda para cambiar al modo de edición. Con el editor puede actualizar los servicios vinculados, los conjuntos de datos y las canalizaciones creados mediante la herramienta. Haga clic en **Code** (Código) para ver el código JSON asociado con la entrada abierta en el editor. Para más información sobre la edición de estas entidades en la interfaz de usuario de Data Factory, consulte [la versión de Azure Portal de este tutorial](tutorial-copy-data-portal.md).

    ![Pestaña Editor](./media/tutorial-copy-data-tool/edit-tab.png)
17. Compruebe que los datos se insertan en la tabla **emp** de la base de datos SQL de Azure. 

    ![Comprobar salida de SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una instancia de Azure Blob Storage en una instancia de Azure SQL Database. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización
> * Supervisión de las ejecuciones de canalización y actividad.

Pase al tutorial siguiente para obtener información sobre cómo copiar datos desde el entorno local a la nube: 

> [!div class="nextstepaction"]
>[Copiar datos del entorno local a la nube](tutorial-hybrid-copy-data-tool.md)