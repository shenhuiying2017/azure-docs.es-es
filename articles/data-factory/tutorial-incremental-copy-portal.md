---
title: Copia incremental de una tabla utilizando Azure Data Factory | Microsoft Docs
description: "En este tutorial, creará una canalización de Azure Data Factory que copia datos de forma incremental de una base de datos SQL de Azure a Azure Blob Storage."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: af01fac2c552e038377f5b394fecbe3ad9fd3acc
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Carga de datos de forma incremental de Azure SQL Database a Azure Blob Storage
En este tutorial, creará una instancia de Azure Data Factory con una canalización que carga los datos diferenciales de una tabla en una base de datos SQL de Azure en Azure Blob Storage. 


> [!NOTE]
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación del almacén de datos para almacenar el valor de marca de agua
> * Creación de una factoría de datos.
> * Cree servicios vinculados. 
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización 
> * Revisión de los resultados
> * Adición de más datos al origen
> * Repetición de la ejecución de la canalización
> * Supervisión de la segunda ejecución de la canalización
> * Revisión de los resultados de la segunda ejecución


## <a name="overview"></a>Información general
Este es el diagrama de solución de alto nivel: 

![Cargar datos de forma incremental](media\tutorial-Incremental-copy-portal\incrementally-load.png)

Estos son los pasos importantes para crear esta solución: 

1. **Seleccione la columna de marca de agua**.
    Seleccione una columna en el almacén de datos de origen, que pueda usarse para segmentar los registros nuevos o actualizados para cada ejecución. Normalmente, los datos de esta columna seleccionada (por ejemplo, last_modify_time o id.) siguen aumentando cuando se crean o se actualizan las filas. El valor máximo de esta columna se utiliza como una marca de agua.

2. **Prepare el almacén de datos para almacenar el valor de marca de agua**. En este tutorial, el valor de marca de agua se almacena en una base de datos SQL.
    
3. **Cree una canalización con el siguiente flujo de trabajo**: 
    
    La canalización de esta solución consta de las siguientes actividades:
  
    * Cree dos actividades de búsqueda. Use la primera actividad de búsqueda para recuperar el último valor de marca de agua. y, la segunda actividad, para recuperar el nuevo valor de marca de agua. Estos valores de marca de agua se pasan a la actividad de copia. 
    * Cree una actividad de copia que copie filas del almacén de datos de origen con el valor de la columna de marca de agua que sea mayor que el valor anterior y menor que el nuevo. A continuación, copia los datos diferenciales del almacén de datos de origen en Blob Storage como un archivo nuevo. 
    * Cree un procedimiento almacenado que actualice el valor de marca de agua de la canalización que se ejecute la próxima vez. 


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos
* **Azure SQL Database**. La base de datos se usa como almacén de datos de origen. Si no tiene ninguna, consulte [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación.
* **Azure Storage**. Blob Storage se usa como almacén de datos receptor. Si no tiene una cuenta de almacenamiento, consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación. Cree un contenedor denominado adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Creación de una tabla de origen de datos en la base de datos SQL
1. Abra SQL Server Management Studio. En el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

2. Ejecute el siguiente comando SQL en la base de datos SQL para crear una tabla denominada `data_source_table` como el almacén de origen de datos: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    En este tutorial, utilizará LastModifytime como columna de marca de agua. Los datos del almacén de origen de datos se muestran en la tabla siguiente:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Creación de otra tabla en la base de datos SQL para almacenar el valor de límite máximo
1. Ejecute el siguiente comando SQL en la base de datos SQL para crear una tabla denominada `watermarktable` y almacenar el valor de marca de agua:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Establezca el valor predeterminado de límite máximo con el nombre de la tabla del almacén de datos de origen. En este tutorial, el nombre de tabla es data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Revise los datos de la tabla `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Salida: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Creación de un procedimiento almacenado en la base de datos SQL 

Ejecute el siguiente comando para crear un procedimiento almacenado en la base de datos SQL:

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, escriba **ADFIncCopyTutorialDF** para el **nombre**. 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve un signo de exclamación rojo con el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFIncCopyTutorialDF) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
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

    ![icono implementando factoría de datos](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Azure Data Factory en una pestaña independiente.

## <a name="create-a-pipeline"></a>Crear una canalización
En este tutorial, creará una canalización con dos actividades de búsqueda, una actividad de copia y un procedimiento almacenado encadenada en una canalización. 

1. En la página de **introducción** de la interfaz de usuario de Data Factory, haga clic en el icono **Create pipeline** (Crear canalización). 

   ![Página de introducción de la interfaz de usuario de Data Factory](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. En la página **General** (General) de la ventana **Properties** (Propiedades) de la canalización, escriba el nombre **IncrementalCopyPipeline**. 

   ![Nombre de la canalización](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Agreguemos la primera actividad de búsqueda para recuperar el valor de marca de agua anterior. En el cuadro de herramientas **Activities** (Actividades), expanda **SQL Database**, arrastre la actividad **Lookup** (Búsqueda) y colóquela en la superficie del diseñador de canalizaciones. Cambie el nombre de la actividad a **LookupOldWaterMarkActivity**.

   ![Nombre de la primera actividad de búsqueda](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Cambie a la pestaña **Settings** (Configuración) y haga clic en **+ New** (+ Nuevo) para **Source Dataset** (Conjunto de datos de origen). En este paso, creará conjuntos de datos que representarán los datos de **watermarktable**. Esta tabla contiene la marca de agua que se utilizó anteriormente en la operación de copia anterior. 

   ![Menú New dataset (Nuevo conjunto de datos): marca de agua anterior](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar). Verá que se abre una nueva pestaña para el conjunto de datos. 

   ![Seleccionar Azure SQL Database](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. En la ventana de propiedades del conjunto de datos, escriba **WatermarkDataset** en **Name** (Nombre).

   ![Conjunto de datos Watermark: nombre](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Cambie a la pestaña **Connection** (Conexión) y haga clic en **+ New** (+ Nuevo) para realizar una conexión (crear un servicio vinculado) a la instancia de Azure SQL Database. 

   ![Botón New linked service (Nuevo servicio vinculado)](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureSqlDatabaseLinkedService** en **Name** (Nombre). 
    2. Seleccione el servidor de Azure SQL Server en **Server name** (Nombre del servidor).
    3. Escriba el **nombre del usuario** de acceso al servidor de Azure SQL. 
    4. Escriba la **contraseña** del usuario. 
    5. Para probar la conexión a Azure SQL Database, haga clic en **Test connection** (Prueba de conexión).
    6. Haga clic en **Save**(Guardar).
    7. En la pestaña **Connection** (Conexión), confirme que **AzureSqlDatabaseLinkedService** está seleccionado en **Linked service** (Servicio vinculado).
       
        ![Ventana New Linked Service (Nuevo servicio vinculado)](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Seleccione **[dbo].[watermarktable]** para **Table** (Tabla). Si desea una vista previa de los datos de la tabla, haga clic en **Preview data** (Vista previa de los datos).

    ![Conjunto de datos de marca de agua: configuración de la conexión](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Cambie al editor de canalización; para ello, haga clic en la pestaña de la canalización de la parte superior o en el nombre de esta de la vista de árbol de la izquierda. En la ventana de propiedades de la actividad de **búsqueda**, confirme que **WatermarkDataset** está seleccionado en el campo **Source Dataset** (Conjunto de datos de origen). 

    ![Canalización: conjunto de datos antiguo de marca de agua](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. En el cuadro de herramientas **Activities** (Actividades), expanda **SQL Database**, y arrastre otra actividad **Lookup** (Búsqueda) y colóquela en la superficie del diseñador de canalizaciones; después, establezca el nombre en  **LookupNewWaterMarkActivity** en la pestaña **General** (General) de la ventana de propiedades. Esta actividad de búsqueda obtiene el nuevo valor de marca de agua de la tabla y copia los datos de origen en el destino. 

    ![Nombre de la segunda actividad de búsqueda](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. En la ventana de propiedades de la segunda actividad de **búsqueda**, cambie a la pestaña **Settings** (Configuración) y haga clic en **New** (Nuevo). Creará un conjunto de datos que apuntará a la tabla de origen con el nuevo valor de marca de agua (valor máximo de LastModifyTime). 

    ![Segunda actividad de búsqueda: nuevo conjunto de datos](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar). Verá que se abre una nueva pestaña para este conjunto de datos. También verá el conjunto de datos en la vista de árbol. 
15. En la pestaña **General** (General) de la ventana de propiedades, escriba **SourceDataset** en **Name** (Nombre). 

    ![Conjunto de datos de origen: nombre](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Cambie a la pestaña **Connection** (Conexión) y realice los pasos siguientes: 

    1. Seleccione **AzureSqlDatabaseLinkedService** como **Linked service** (Servicio vinculado).
    2. Seleccione **[dbo].[data_source_table]** en Table (Tabla). Más adelante en este tutorial especificará una consulta en este conjunto de datos. La consulta tiene prioridad sobre la tabla que se especifica en este paso. 

        ![Segunda actividad de búsqueda: nuevo conjunto de datos](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Cambie al editor de canalización; para ello, haga clic en la pestaña de la canalización de la parte superior o en el nombre de esta de la vista de árbol de la izquierda. En la ventana de propiedades de la actividad de **búsqueda**, confirme que **SourceDataset** está seleccionado en el campo **Source Dataset** (Conjunto de datos de origen). 
18. Seleccione **Query** (Consulta) en el campo **Use Query** (Usar consulta) y escriba la siguiente consulta: solo se selecciona el valor máximo de **LastModifytime** de **data_source_table**. Si no tiene esta consulta, el conjunto de datos obtiene todas las filas de la tabla, ya que especificó el nombre de tabla (data_source_table) en la definición del conjunto de datos.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Segunda actividad de búsqueda: consulta](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. En el cuadro de herramientas **Activities** (Actividades), expanda **DataFlow** (Flujo de datos), y arrastre la actividad **Copy** (Copiar) y colóquela en el cuadro de herramientas Activities (Actividades); después, establezca el nombre en  **IncrementalCopyActivity**. 

    ![Actividad de copia: nombre](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Conecte las dos actividades Lookup (Búsqueda) a la actividad Copy (Copiar)**; para ello, arrastre el **botón verde** de las actividades de búsqueda a la actividad de copia. Suelte el botón del mouse cuando vea el color del borde de la actividad de copia cambiar a azul. 

    ![Conexión de las actividades de búsqueda a la actividad de copia](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Seleccione la **actividad de copia** y confirme que ve sus propiedades en la ventana **Properties** (Propiedades). 

    ![Propiedades de la actividad de copia](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Cambie a la pestaña **Source** (Origen) de la ventana **Properties** (Propiedades) y realice los pasos siguientes:

    1. Seleccione **SourceDataset** en el campo **Source Dataset** (Conjunto de datos de origen). 
    2. Seleccione **Query** (Consulta) en el campo **Use Query** (Usar consulta). 
    3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta). 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Actividad de copia: origen](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Cambie a la pestaña **Sink** (Receptor) y haga clic en **+ New** (+ Nuevo) en el campo **Sink Dataset** (Conjunto de datos receptor). 

    ![Botón New Dataset (Conjunto de datos nuevo)](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. En este tutorial, el almacén de datos receptor es de tipo Azure Blob Storage. Por lo tanto, seleccione **Azure Blob Storage** y haga clic en **Finish** (Finalizar) en la ventana **New Dataset** (Nuevo conjunto de datos). 

    ![Seleccionar Azure Blob Storage](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. En la pestaña **General** (General) de la ventana de propiedades del conjunto de datos, escriba **SinkDataset** en **Name** (Nombre). 

    ![Conjunto de datos receptor: nombre](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Cambie a la pestaña **Connection** (Conexión) y haga clic en **+ New** (+ Nuevo). En este paso se crea una conexión (servicio vinculado) y su instancia de **Azure Blob Storage**.

    ![Conjunto de datos receptor: nueva conexión](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

    1. Escriba **AzureStorageLinkedService** en **Name** (Nombre). 
    2. Seleccione la cuenta de Azure Storage de **Storage account name** (Nombre de la cuenta de Storage).
    3. Haga clic en **Save**(Guardar). 

        ![Servicio vinculado de Azure Storage: configuración](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. En la pestaña **Connection** (Conexión), realice los pasos siguientes:

    1. Confirme que **AzureStorageLinkedService** está seleccionado en **Linked service** (Servicio vinculado). 
    2. En la parte de la **carpeta** del campo **File path** (Ruta de acceso de archivo), escriba **adftutorial/incrementalcopy**. **adftutorial** es el nombre del contenedor de blobs; **incrementalcopy**, el de la carpeta. Con este fragmento de código se da por supuesto que tiene un contenedor de blob denominado adftutorial en Blob Storage. Cree el contenedor si no existe o asígnele el nombre de uno existente. Si no existe, Azure Data Factory crea automáticamente la carpeta de salida **incrementalcopy**. También puede usar el botón **Browse** (Examinar) para ir a una carpeta del contenedor de blobs mediante la **ruta de acceso del archivo**. .RunId, '.txt')`.
    3. En la parte del **nombre de archivo** del campo **File path** (Ruta de acceso de archivo), escriba `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. El nombre de archivo se genera dinámicamente mediante la expresión. Cada ejecución de canalización tiene un identificador único. La actividad de copia usa el identificador de ejecución para generar el nombre de archivo. 

        ![Conjunto de datos receptor: configuración de la conexión](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Cambie al editor de **canalización**; para ello, haga clic en la pestaña de la canalización de la parte superior o en el nombre de esta de la vista de árbol de la izquierda. 
29. En el cuadro de herramientas **Activities** (Actividades), expanda **SQL Database**, arrastre la actividad **Stored Procedure** (Procedimiento almacenado) de allí para colocarla en la superficie del diseñador de canalizaciones. **Conecte** el resultado verde (correcto) de la actividad **Copy** (Copiar) con la actividad **Stored Procedure** (Procedimiento almacenado). 
    
    ![Actividad de copia: origen](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Seleccione **Storage Procedure Activity** (Actividad Procedimiento almacenado) en el diseñador de canalizaciones y cámbiele el nombre a **StoredProceduretoWriteWatermarkActivity**. 

    ![Actividad de procedimiento almacenado: nombre](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Cambie a la pestaña **SQL Account** (Cuenta de SQL) y seleccione *AzureSqlDatabaseLinkedService** como **Linked service** (Servicio vinculado). 

    ![Actividad de procedimiento almacenado: cuenta SQL](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Cambie a la pestaña **Stored Procedure** (Procedimiento almacenado) y realice los pasos siguientes: 

    1. Escriba **sp_write_watermark** en **Stored procedure name** (Nombre del procedimiento almacenado). 
    2. Para especificar valores para los parámetros del procedimiento almacenado, haga clic en **+ New** (+ Nuevo) en la sección **Stored procedure parameters** (Parámetros de los procedimientos almacenados) y escriba los valores siguientes: 

        | NOMBRE | type | Valor | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue |
        | TableName | string | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Actividad de procedimiento almacenado: configuración del procedimiento almacenado](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Para comprobar la configuración de canalización, haga clic en **Validate** (Comprobar) en la barra de herramientas. Confirme que no haya errores de comprobación. Para cerrar la ventana **Pipeline Validation Report** (Informe de comprobación de la canalización), haga clic en >>.   

    ![Comprobar la canalización](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Para publicar entidades (servicios vinculados, conjuntos de datos y canalizaciones) en el servicio Azure Data Factory, haga clic en el botón **Publish** (Publicar). Espere hasta que vea un mensaje de que la publicación se completó correctamente. 

    ![Botón Publicar](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización
Haga clic en **Trigger** (Desencadenar) en la barra de herramientas y en **Trigger Now** (Desencadenar ahora). 

![Botón Trigger Now (Desencadenar ahora)](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Puede ver el estado de ejecución de la canalización activado por el desencadenador manual. Haga clic en el botón **Refresh** (Actualizar) para actualizar la lista. 
    
    ![Ejecuciones de la canalización](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Para ver las ejecuciones de actividad asociadas con la de esta canalización, haga clic en el primer vínculo (**View Activity Runs** [Ver ejecuciones de actividad]) de la columna **Actions** (Acciones). Para volver a la vista anterior, haga clic en **Pipelines** (Canalizaciones) de la parte superior. Haga clic en el botón **Refresh** (Actualizar) para actualizar la lista.

    ![Ejecuciones de actividad](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Revisión del resultado
1. Puede conectarse a su cuenta de Azure Storage mediante herramientas como el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Compruebe que se crea un archivo de salida en la carpeta **incrementalcopy** del contenedor **adftutorial**.

    ![Primer archivo de salida](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Abra el archivo de salida y observe que todos los datos se hayan copiado de **data_source_table** en el archivo de blobs. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Compruebe el valor más reciente de `watermarktable`. Verá que se ha actualizado el valor de marca de agua.

    ```sql
    Select * from watermarktable
    ```
    
    Este es el resultado:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Incorporación de más datos al origen

Inserte nuevos datos en la base de datos SQL (almacén de origen de datos).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Los datos actualizados en la base de datos SQL son los siguientes:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Desencadenamiento de otra ejecución de canalización
1. Cambie a la pestaña **Edit** (Editar). Si no está abierta en el diseñador, haga clic en la canalización en la vista de árbol. 

    ![Botón Trigger Now (Desencadenar ahora)](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Haga clic en **Trigger** (Desencadenar) en la barra de herramientas y en **Trigger Now** (Desencadenar ahora). 

    ![Botón Trigger Now (Desencadenar ahora)](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Supervisión de la segunda ejecución de la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Puede ver el estado de ejecución de la canalización activado por el desencadenador manual. Haga clic en el botón **Refresh** (Actualizar) para actualizar la lista. 
    
    ![Ejecuciones de la canalización](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Para ver las ejecuciones de actividad asociadas con la de esta canalización, haga clic en el primer vínculo (**View Activity Runs** [Ver ejecuciones de actividad]) de la columna **Actions** (Acciones). Para volver a la vista anterior, haga clic en **Pipelines** (Canalizaciones) de la parte superior. Haga clic en el botón **Refresh** (Actualizar) para actualizar la lista.

    ![Ejecuciones de actividad](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>Comprobación de la segunda salida

1. En la instancia de Blob Storage, verá que se ha creado otro archivo. En este tutorial, el nombre de archivo nuevo es `Incremental-<GUID>.txt`. Abra ese archivo y verá que contiene dos filas de registros.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Compruebe el valor más reciente de `watermarktable`. Verá que se ha vuelto a actualizar el valor de marca de agua.

    ```sql
    Select * from watermarktable
    ```
    salida de ejemplo: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>pasos siguientes
En este tutorial, realizó los pasos siguientes: 

> [!div class="checklist"]
> * Preparación del almacén de datos para almacenar el valor de marca de agua
> * Creación de una factoría de datos.
> * Cree servicios vinculados. 
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización 
> * Revisión de los resultados
> * Adición de más datos al origen
> * Repetición de la ejecución de la canalización
> * Supervisión de la segunda ejecución de la canalización
> * Revisión de los resultados de la segunda ejecución

En este tutorial, la canalización copió datos de una única tabla de una base de datos SQL a una instancia de Blob Storage. Avance al tutorial siguiente para aprender a copiar datos de varias tablas de una base de datos de SQL Server local a una base de datos SQL. 

> [!div class="nextstepaction"]
>[Carga incremental de datos de varias tablas de SQL Server a Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)



