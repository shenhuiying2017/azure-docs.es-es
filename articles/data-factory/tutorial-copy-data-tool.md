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
ms.openlocfilehash: 5b636128d0df5a404df7aa6b2cfdce016e36681f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copia de datos de Azure Blob Storage a SQL Database mediante la herramienta Copy Data
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Version 1: ya está disponible con carácter general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](tutorial-copy-data-tool.md)

En este tutorial, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copy Data para crear una canalización que copia datos de una instancia de Azure Blob Storage a una instancia de SQL Database. 

> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).
>
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>requisitos previos

* **Suscripción de Azure**: si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de almacenamiento de Azure**: use Blob Storage como almacén de datos de _origen_. Si no dispone de una cuenta de almacenamiento de Azure, consulte las instrucciones de [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database**: use una base de datos SQL como almacén de datos _receptor_. Si no tiene una base de datos SQL, consulte las instrucciones de [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Creación de un blob y una tabla SQL

Prepare su almacenamiento de blobs y su base de datos SQL para el tutorial mediante los pasos siguientes.

#### <a name="create-a-source-blob"></a>Creación de un blob de origen

1. Inicie el **Bloc de notas**. Copie el texto siguiente y guárdelo en un archivo llamado **inputEmp.txt** en el disco:

    ```
    John|Doe
    Jane|Doe
    ```

2. Cree un contenedor denominado **adfv2tutorial** y cargue el archivo inputEmp.txt en el contenedor. Puede usar varias herramientas para realizar estas tareas, como el [Explorador de Azure Storage](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Creación de una tabla SQL receptora

1. Use el siguiente script de SQL para crear una tabla llamada **dbo.emp** en la base de datos SQL:

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

2. Permita que los servicios de Azure accedan a SQL Server. Compruebe que la configuración **Permitir el acceso a servicios de Azure** está habilitado para el servidor que ejecuta SQL Server. Esta configuración permite que Data Factory escriba datos en su instancia de SQL Server. Para comprobar y activar esta configuración, realice los siguientes pasos:

    a. A la izquierda, seleccione **Más servicios** y, después, seleccione **Servidores SQL Server**.

    b. Seleccione su servidor y elija **CONFIGURACIÓN** > **Firewall**.

    c. En la página **Configuración de firewall**, **seleccione Activado** en **Permitir el acceso a servicios de Azure**.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú izquierdo, seleccione **+ Nuevo** > **Data + Analytics** > **Data Factory**: 
   
   ![Creación de nueva factoría de datos](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**. 
      
     ![Nueva factoría de datos](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   El nombre de la factoría de datos debe ser _globalmente único_. Puede aparecer el siguiente mensaje de error:
   
   ![Mensaje de error de nueva factoría de datos](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Si recibe un mensaje de error sobre el valor de nombre, escriba un nombre diferente para la factoría de datos. Por ejemplo, utilice _**suNombre**_**ADFTutorialDataFactory**. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Data Factory: reglas de nomenclatura](naming-rules.md).
3. Seleccione la **suscripción** de Azure en la que quiere crear la nueva factoría de datos. 
4. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
    a. Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable.

    b. Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos. 
         
    Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).

5. En **Versión**, seleccione **V2 (versión preliminar)** como versión.
6. En **Ubicación**, seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa la factoría de datos pueden estar en otras ubicaciones o regiones.
7. Seleccione **Anclar al panel**. 
8. Seleccione **Crear**.
9. En el panel, el icono **Deploying Data Factory** (Implementando Data Factory) muestra el estado del proceso.

    ![Icono de implementación de una factoría de datos](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Una vez finalizada la creación, se muestra la página principal de **Data Factory**.
   
    ![Página principal Factoría de datos](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Para abrir la interfaz de usuario de Azure Data Factory en otra pestaña, seleccione el icono **Author & Monitor** (Creación y supervisión). 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página **Let's get started** (Introducción), seleccione el icono **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Icono de la herramienta Copy Data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades), en **Task name** (Nombre de la tarea), escriba **CopyFromBlobToSqlPipeline**. Luego, seleccione **Siguiente**. La interfaz de usuario de Data Factory crea una canalización con el nombre de la tarea especificado. 

    ![Página de propiedades](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **Azure Blob Storage** y después seleccione **Next** (Siguiente). Los datos de origen están en una instancia de Blob Storage. 

    ![Página Source data store (Almacén de datos de origen)](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. En la página **Specify the Azure Blob storage account** (Especificar la cuenta de Azure Blob Storage), realice los pasos siguientes:

    a. En **Connection name** (Nombre de la conexión), escriba **AzureStorageLinkedService**.

    b. Seleccione el nombre de la cuenta de almacenamiento en la lista desplegable **Storage account name** (Nombre de la cuenta de almacenamiento).

    c. Seleccione **Siguiente**. 

    ![Especificar la cuenta de almacenamiento](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    Un servicio vinculado enlaza un almacén de datos o un proceso a la factoría de datos. En este caso, creará un servicio vinculado de almacenamiento para vincular la cuenta de almacenamiento al almacén de datos. El servicio vinculado tiene la información de conexión que usa Data Factory para conectarse a Blob Storage en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) que contiene los datos de origen. 

5. En la página **Choose the input file or folder** (Elegir el archivo o la carpeta de entrada), lleve a cabo los siguientes pasos:
    
    a. Navegue hasta la carpeta **adfv2tutorial/input**.

    b. Seleccione el archivo **inputEmp.txt**.

    c. Seleccione **Choose**(Elegir). Como alternativa, puede hacer doble clic en el archivo **inputEmp.txt**.

    d. Seleccione **Siguiente**. 

    ![Seleccione el archivo o la carpeta de entrada.](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. En la página **File format settings** (Configuración de formato de archivo), observe que la herramienta detecta automáticamente los delimitadores de columna y fila. Seleccione **Siguiente**. También puede obtener una vista previa de los datos y ver el esquema de los datos de entrada en esta página. 

    ![Configuración del formato de archivo](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure SQL Database** y después **Next** (Siguiente).

    ![Almacén de datos de destino](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. En la página **Specify the Azure SQL database** (Especificar la instancia de Azure SQL Database), lleve a cabo los siguientes pasos: 

    a. En **Connection name** (Nombre de conexión), escriba **AzureSqlDatabaseLinkedService**.

    b. En **Server name** (Nombre del servidor), seleccione su instancia de SQL Server.

    c. En **Database name** (Nombre de la base de datos), seleccione la base de datos SQL.

    d. En **User name** (Nombre de usuario), escriba el nombre del usuario.

    e. En **Password** (Contraseña), escriba la contraseña del usuario.

    f. Seleccione **Siguiente**. 

    ![Especifique la base de datos SQL.](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    Un conjunto de datos debe estar asociado con un servicio vinculado. El servicio vinculado tiene la cadena de conexión que usa Data Factory para conectarse a la base de datos SQL en tiempo de ejecución. El conjunto de datos especifica el contenedor, la carpeta y el archivo (opcional) donde se copian los datos.

9. En la página **Table mapping** (Asignación de tabla), seleccione la tabla **[dbo].[ emp]** y, a continuación, seleccione **Next** (Siguiente). 

    ![Asignación de tabla](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. En la página **Schema mapping** (Asignación de esquema), observe que las columnas primera y segunda del archivo de entrada se asignan a las columnas **FirstName** y **LastName** de la tabla **emp**.

    ![Página Schema mapping (Asignación de esquema)](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. En la página **Settings** (Configuración), seleccione **Next** (Siguiente). 

    ![Página Configuración](./media/tutorial-copy-data-tool/settings-page.png)
12. En la página **Summary** (Resumen), revise la configuración y seleccione **Next** (Siguiente).

    ![Página de resumen](./media/tutorial-copy-data-tool/summary-page.png)
13. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización (tarea).

    ![Página Deployment (Implementación)](./media/tutorial-copy-data-tool/deployment-page.png)
14. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. La columna **Actions** (Acciones) incluye los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización. Seleccione **Refresh** (Actualizar) para actualizar la lista. 

    ![La supervisión de la canalización se ejecuta](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Para ver las ejecuciones de actividad asociadas con la ejecución de canalización, seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en la columna **Actions** (Acciones). Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para ver detalles sobre la operación de copia, seleccione el vínculo **Details** (Detalles) (icono de gafas) en la columna **Actions** (Acciones). Para volver a la vista **Pipeline Runs** (Ejecuciones de canalización), seleccione el vínculo **Pipelines** (Canalizaciones) de la parte superior. Para actualizar la vista, seleccione **Refresh** (Actualizar). 

    ![Supervisión de las ejecuciones de actividad](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Seleccione la pestaña **Edit** (Editar) de la izquierda para cambiar al modo de edición. Con el editor puede actualizar los servicios vinculados, los conjuntos de datos y las canalizaciones creados mediante la herramienta. Seleccione **Code** (Código) para ver el código JSON para la entidad abierta actualmente en el editor. Para más información sobre la edición de estas entidades en la interfaz de usuario de Data Factory, consulte [la versión de Azure Portal de este tutorial](tutorial-copy-data-portal.md).

    ![Pestaña Editor](./media/tutorial-copy-data-tool/edit-tab.png)
17. Compruebe que los datos se insertan en la tabla **emp** de la base de datos SQL.

    ![Comprobar salida de SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una instancia de Blob Storage en una instancia de SQL Database. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

Para aprender a copiar datos desde el entorno local a la nube, avance al tutorial siguiente: 

> [!div class="nextstepaction"]
>[Copia de datos del entorno local a la nube](tutorial-hybrid-copy-data-tool.md)
