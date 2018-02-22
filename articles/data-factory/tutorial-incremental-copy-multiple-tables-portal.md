---
title: Copia incremental de varias tablas mediante Azure Data Factory | Microsoft Docs
description: "En este tutorial, creará una canalización de Azure Data Factory que copia los datos diferenciales de forma incremental de varias tablas de una base de datos local de SQL Server a una base de datos SQL de Azure."
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
ms.date: 01/20/2018
ms.author: jingwang
ms.openlocfilehash: 11dedc8866fcc0239fd4a34b7ed73af34c6d5a4e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Carga incremental de datos de varias tablas de SQL Server a Azure SQL Database
En este tutorial, creará una factoría de datos de Azure con una canalización que carga los datos diferenciales de varias tablas de una instancia local de SQL Server a una base de datos SQL de Azure.    

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación de los almacenes de datos de origen y de destino
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Instalación del entorno de ejecución de integración 
> * Cree servicios vinculados. 
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación, ejecución y supervisión de una canalización
> * Consulte los resultados.
> * Adición o actualización de datos en tablas de origen
> * Nueva ejecución y supervisión de la canalización
> * Revisión de los resultados finales

> [!NOTE]
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Información general
Estos son los pasos importantes para crear esta solución: 

1. **Seleccione la columna de marca de agua**.
    
    Seleccione una columna de cada tabla del almacén de datos de origen que pueda usarse para identificar los registros nuevos o actualizados de cada ejecución. Normalmente, los datos de esta columna seleccionada (por ejemplo, last_modify_time o id.) siguen aumentando cuando se crean o se actualizan las filas. El valor máximo de esta columna se utiliza como una marca de agua.

2. **Prepare el almacén de datos para almacenar el valor de marca de agua**.   
    
    En este tutorial, el valor de marca de agua se almacena en una base de datos SQL.

3. **Cree una canalización con las siguientes actividades:** 
    
    a. Cree una actividad ForEach que recorra en iteración una lista de nombres de tabla de origen que se pase como parámetro a la canalización. Para cada tabla de origen, invoca las siguientes actividades para realizar la carga diferencial de esa tabla.

    b. Cree dos actividades de búsqueda. Use la primera actividad de búsqueda para recuperar el último valor de marca de agua. y, la segunda actividad, para recuperar el nuevo valor de marca de agua. Estos valores de marca de agua se pasan a la actividad de copia.

    c. Cree una actividad de copia que copie filas del almacén de datos de origen con el valor de la columna de marca de agua que sea mayor que el valor anterior y menor que el nuevo. A continuación, copie los datos diferenciales del almacén de datos de origen a Azure Blob Storage como un archivo nuevo.

    d. Cree un procedimiento almacenado que actualice el valor de marca de agua de la canalización que se ejecute la próxima vez. 

    Este es el diagrama de solución de alto nivel: 

    ![Cargar datos de forma incremental](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
* **SQL Server**. En este tutorial, usará una base de datos local SQL Server como almacén de datos de origen. 
* **Azure SQL Database**. Usará una base de datos SQL como almacén de datos receptor. Si no tiene ninguna, consulte [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Creación de tablas de origen en la base de datos de SQL Server

1. Abra SQL Server Management Studio y conéctese a la base de datos SQL Server local.

2. En el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

3. Ejecute el siguiente comando SQL en la base de datos para crear las tablas denominadas `customer_table` y `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Creación de tablas de destino en Azure SQL Database
1. Abra SQL Server Management Studio y conéctese a Azure SQL Database.

2. En el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

3. Ejecute el siguiente comando SQL en la base de datos SQL para crear las tablas denominadas `customer_table` y `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Creación de otra tabla en la base de datos SQL de Azure para almacenar el valor del límite máximo
1. Ejecute el siguiente comando SQL en la base de datos SQL para crear una tabla denominada `watermarktable` y almacenar el valor de marca de agua: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Inserte los valores del límite inicial de ambas tablas de origen en la tabla de límites.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Creación de un procedimiento almacenado en la base de datos SQL de Azure 

Ejecute el siguiente comando para crear un procedimiento almacenado en la base de datos SQL. Este procedimiento almacenado actualiza el valor de la marca de agua después de cada ejecución de canalización. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Creación de tipos de datos y procedimientos almacenados adicionales en la base de datos SQL de Azure
Ejecute la consulta siguiente para crear dos procedimientos almacenados y dos tipos de datos en la base de datos SQL. Estos procedimientos se usan para combinar los datos de las tablas de origen en las tablas de destino.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, escriba **ADFMultiIncCopyTutorialDF** como **nombre**. 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFMultiIncCopyTutorialDF) e intente crearlo de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
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

    ![icono implementando factoría de datos](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** para iniciar la interfaz de usuario de Azure Data Factory en una pestaña independiente.
11. En la página de introducción de la interfaz de usuario de Azure Data Factory, haga clic en **Create pipeline** (Crear canalización) o cambie a la pestaña **Edit** (Editar). 

   ![Página de introducción](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Creación de un entorno de ejecución de integración autohospedado
Cuando mueva datos de un almacén de datos de una privada red (local) a un almacén de datos de Azure, instale un entorno de ejecución de integración (IR) autohospedado en su entorno local. El entorno de ejecución de integración autohospedado mueve los datos entre la red privada y Azure. 

1. Haga clic en **Connections** (Conexiones) en la parte inferior del panel izquierdo y cambie a **Integration Runtimes** (Runtimes de integración) en la ventana **Connections** (Conexiones). 

   ![Pestaña Connections (Conexiones)](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
2. En la pestaña **Integration Runtimes** (Runtimes de integración), haga clic en **+ New** (+Nuevo). 

   ![Nuevo runtime de integración (botón)](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
3. En la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione la opción **Perform data movement and dispatch activities to external computes** (Realizar movimientos de datos y enviar actividades a procesos externos) y haga clic en **Next** (Siguiente). 

   ![Seleccionar tipo de entorno de ejecución de integración](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
4. Seleccione **Private Network** (Red privada) y haga clic en **Next** (Siguiente). 

   ![Seleccionar red privada](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
5. Escriba **MySelfHostedIR** como **nombre** y haga clic en **Next** (Siguiente). 

   ![Nombre de entorno de ejecución de integración autohospedado](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
10. Haga clic en **Click here to launch the express setup for this computer** (Haga clic aquí para iniciar la configuración rápida en este equipo) en la sección **Option 1: Express setup** (Opción 1: configuración rápida). 

   ![Vínculo a configuración rápida](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
11. En la ventana **Microsoft Integration Runtime (Self-hosted) Express Setup** (Configuración rápida de Integration Runtime [autohospedado]), haga clic en **Close** (Cerrar). 

   ![Configuración correcta de Integration Runtime](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
12. En el explorador web, en la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), haga clic en **Finish** (Finalizar). 

   ![Configuración de Integration Runtime: finalizar](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
17. Confirme que ve **MySelfHostedIR** en la lista de entornos de ejecución de integración.

       ![Entornos de ejecución de integración: lista](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta sección, creará servicios vinculados a la base de datos local SQL Server y a la base de datos SQL. 

### <a name="create-the-sql-server-linked-service"></a>Creación del servicio vinculado de SQL Server
En este paso, vinculará la base de datos SQL Server local a la factoría de datos.

1. En la ventana **Connections** ventana, cambie de la pestaña **Integration Runtimes** (Entornos de ejecución de integración) a la pestaña **Linked Services** (Servicios vinculados) y haga clic en **+ New** (Nuevo).

    ![Botón New Linked Service (Nuevo servicio vinculado)](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **SQL Server** y haga clic en **Continue** (Continuar). 

    ![Selección de SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **SqlServerLinkedService** en **Name** (Nombre). 
    2. Seleccione **MySelfHostedIR** en la opción **Connect via integration runtime** (Conectar mediante IR). Este es un paso **importante**. El entorno de ejecución de integración predeterminado no se puede conectar a un almacén de datos local. Utilice el entorno de ejecución de integración auto-hospedado que creó anteriormente. 
    3. En **Server name** (Nombre de servidor), escriba el nombre del equipo que tiene la base de datos de SQL Server.
    4. En **Database name** (Nombre de base de datos), escriba el nombre de la base de datos de SQL Server que tiene los datos de origen. Como parte de los requisitos previos creó una tabla e insertó datos en esta base de datos. 
    5. En **Authentication type** (Tipo de autenticación), seleccione el **tipo de autenticación** que desea usar para conectarse a la base de datos. 
    6. En **User name** (Nombre de usuario), escriba el nombre del usuario que tiene acceso a la base de datos de SQL Server. Si necesita usar un carácter de barra diagonal (`\`) en el nombre de servidor o en la cuenta de usuario, utilice el carácter de escape (`\`). Un ejemplo es `mydomain\\myuser`.
    7. En **Password** (Contraseña), escriba la **contraseña** del usuario. 
    8. Para comprobar si Data Factory puede conectarse a su base de datos de SQL Server, haga clic en **Test connection** (Probar conexión). Corrija todos los errores que aparezcan hasta que la conexión se realice correctamente. 
    9. Haga clic en **Save** (Guardar) para guardar el servicio vinculado.

        ![Servicio vinculado de SQL Server: configuración](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Creación del servicio vinculado Azure SQL Database
En el último paso, creó un servicio vinculado para vincular su base de datos de Azure SQL Server de origen con la factoría de datos. En este paso, vinculará su base de datos SQL de Azure de destino o receptora con la factoría de datos. 

1. En la ventana **Connections** ventana, cambie de la pestaña **Integration Runtimes** (Entornos de ejecución de integración) a la pestaña **Linked Services** (Servicios vinculados) y haga clic en **+ New** (Nuevo).

    ![Botón New Linked Service (Nuevo servicio vinculado)](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure SQL Database** y haga clic en **Continue** (Continuar). 
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureSqlDatabaseLinkedService** en **Name** (Nombre). 
    3. En **Server name** (Nombre del servidor), seleccione el nombre del servidor de Azure SQL Server en la lista desplegable. 
    4. En **Database name** (Nombre de base de datos), seleccione la base de datos SQL de Azure en la que creó customer_table y project_table como parte de los requisitos previos. 
    6. En **User name** (Nombre de usuario), escriba el nombre del usuario que tiene acceso a la base de datos SQL de Azure. 
    7. En **Password** (Contraseña), escriba la **contraseña** del usuario. 
    8. Para comprobar si Data Factory puede conectarse a su base de datos de SQL Server, haga clic en **Test connection** (Probar conexión). Corrija todos los errores que aparezcan hasta que la conexión se realice correctamente. 
    9. Haga clic en **Save** (Guardar) para guardar el servicio vinculado.

        ![Servicio vinculado SQL de Azure: configuración](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
10. Confirme que ve dos servicios vinculados en la lista. 
   
    ![Dos servicios vinculados](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos para representar el origen de datos, el destino de los datos y el lugar para almacenar la marca de agua.

### <a name="create-a-source-dataset"></a>Creación de un conjunto de datos de origen

1. En el panel izquierdo, haga clic en el signo  **+ (más)** y en **Dataset** (Conjunto de datos).

   ![Menú New Dataset (Nuevo conjunto de datos)](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **SQL Server** y haga clic en **Finish** (Finalizar). 

   ![Selección de SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
3. Verá que se abre una nueva pestaña en el explorador web para configurar el conjunto de datos. También verá un conjunto de datos en la vista de árbol. En la pestaña **General** de la ventana de propiedades de la parte inferior, escriba **SourceDataset** en **Name** (Nombre). 

   ![Conjunto de datos de origen: nombre](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
4. Cambie a la pestaña **Connection** (Conexión) de la ventana de propiedades y seleccione **SqlServerLinkedService** en **Linked service** (Servicio vinculado). No seleccione ninguna tabla aquí. La actividad de copia en la canalización usa una consulta SQL para cargar los datos en lugar de cargar la tabla entera.

   ![Conjunto de datos de origen: conexión](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Creación de un conjunto de datos receptor
1. En el panel izquierdo, haga clic en el signo  **+ (más)** y en **Dataset** (Conjunto de datos).

   ![Menú New Dataset (Nuevo conjunto de datos)](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar). 

   ![Seleccionar Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. Verá que se abre una nueva pestaña en el explorador web para configurar el conjunto de datos. También verá un conjunto de datos en la vista de árbol. En la pestaña **General** de la ventana de propiedades de la parte inferior, escriba **SinkDataset** en **Name** (Nombre).

   ![Conjunto de datos receptor: general](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
4. Cambie a la pestaña **Connection** (Conexión) de la ventana de propiedades y seleccione **AzureSqlLinkedService** en **Linked service** (Servicio vinculado). 

   ![Conjunto de datos receptor: conexión](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
5. Cambie a la pestaña **Parameters** (Parámetros) de la ventana de propiedades y realice los pasos siguientes: 

    1. Haga clic en **+ New** (+ Nuevo) en la sección **Create/update parameters** (Crear o actualizar parámetros). 
    2. Escriba **SinkTableName** en **Name** (Nombre) and **String** en **Type** (Tipo). Este conjunto de datos toma **SinkTableName** como parámetro. El parámetro SinkTableName lo establece la canalización dinámicamente en el runtime. La actividad ForEach de la canalización recorre en iteración una lista de nombres de tabla y pasa el nombre de tabla a este conjunto de datos en cada iteración.
    3. Escriba `@{dataset().SinkTableName}` en la propiedad **tableName** en la sección **Parameterizable properties** (Propiedades parametrizables). Utilice el valor que ha pasado al parámetro **SinkTableName** parámetro para inicializar la propiedad **tableName** del conjunto de datos. 

       ![Conjunto de datos receptor: propiedades](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)

### <a name="create-a-dataset-for-a-watermark"></a>Creación de un conjunto de datos para una marca de agua
En este paso, creará un conjunto de datos para almacenar un valor de límite máximo. 

1. En el panel izquierdo, haga clic en el signo  **+ (más)** y en **Dataset** (Conjunto de datos).

   ![Menú New Dataset (Nuevo conjunto de datos)](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar). 

   ![Seleccionar Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. En la pestaña **General** de la ventana de propiedades de la parte inferior, escriba **WatermarkDataset** en **Name** (Nombre).
4. Cambie a la pestaña **Connection** (Conexión) y realice los pasos siguientes: 

    1. Seleccione **AzureSqlDatabaseLinkedService** como **Linked service** (Servicio vinculado).
    2. Seleccione **[dbo].[watermarktable]** para **Table** (Tabla).

       ![Conjunto de datos de marca de agua: conexión](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Crear una canalización
La canalización toma una lista de tablas como un parámetro. La actividad ForEach recorre en iteración la lista de nombres de tabla y realiza las siguientes operaciones: 

1. Usa la actividad de búsqueda para recuperar el valor de marca de agua antiguo (valor inicial o que se usó en la última iteración).

2. Usa la actividad de búsqueda para recuperar el nuevo valor de marca de agua (valor máximo de la columna de marca de agua en la tabla de origen).

3. Usa la actividad de copia para copiar datos entre estos dos valores de marca de agua desde la base de datos de origen a la base de datos de destino.

4. Usa el procedimiento almacenado para actualizar el valor de marca de agua antiguo que se usará en el primer paso de la iteración siguiente. 

### <a name="create-the-pipeline"></a>Creación de la canalización

1. En el panel izquierdo, haga clic en el **signo + (más)** y en **Pipeline** (Canalización).

    ![New pipeline (Nueva canalización): menú](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
2. En la pestaña **General** de la ventana de **propiedades**, escriba **IncrementalCopyPipeline** en **Name** (Nombre). 

    ![Nombre de la canalización](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
3. En la ventana de **propiedades**, realice los pasos siguientes: 

    1. Haga clic en **+ Nuevo**. 
    2. Escriba **tableList** en el parámetro **name**. 
    3. Seleccione **Object** en el parámetro **type**.

    ![Parámetros de canalización](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
4. En el cuadro de herramientas **Activities** (Actividades), expanda **Iteration & Conditionals** (Iteraciones y condiciones), arrastre la actividad **ForEach** (Para cada uno) y colóquela en la superficie del diseñador de canalizaciones. En la pestaña **General** de la ventana de **propiedades**, escriba **IterateSQLTables**. 

    ![Actividad ForEach: nombre](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
5. Cambie a la pestaña **Settings** (Configuración) de la ventana de **propiedades** y escriba `@pipeline().parameters.tableList` en **Items** (Elementos). La actividad ForEach recorre en iteración una lista de tablas y realiza la operación de copia incremental. 

    ![Actividad ForEach: configuración](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
6. Seleccione la actividad **ForEach** en la canalización, en caso de que aún no esté seleccionada. Haga clic en el botón **Edit (icono del lápiz)**.

    ![Actividad ForEach: editar](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
7. En el cuadro de herramientas **Activities** (Actividades), expanda **General** (General), arrastre la actividad **Lookup** (Búsqueda), colóquela en la superficie del diseñador de canalizaciones y escriba **LookupOldWaterMarkActivity** como **Name** (Nombre).

    ![Primera actividad de búsqueda: nombre](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
8. Cambie a la pestaña **Settings** (Configuración) de la ventana de **propiedades** y realice los pasos siguientes: 

    1. Seleccione **WatermarkDataset** en **Source Dataset** (Conjunto de datos de origen).
    2. Seleccione **Query** (Consulta) en **Use Query** (Usar consulta). 
    3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta). 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Primera actividad de búsqueda: configuración](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
9. Arrastre y coloque la actividad **Lookup** (Búsqueda) del cuadro de herramientas **Activities** (Actividades) y escriba **LookupNewWaterMarkActivity** en **Name** (Nombre).
        
    ![Segunda actividad de búsqueda: nombre](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
10. Cambie a la pestaña **Configuración** .

    1. Seleccione **SourceDataset** como **Source Dataset** (Conjunto de datos de origen). 
    2. Seleccione **Query** (Consulta) en **Use Query** (Usar consulta).
    3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta).

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Segunda actividad de búsqueda: configuración](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
11. Arrastre y coloque la actividad **Copy** (Copia) del cuadro de herramientas **Activities** (Actividades) y escriba **IncrementalCopyActivity** en **Name** (Nombre). 

    ![Actividad de copia: nombre](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
12. Conecte las dos actividades **Lookup **(Búsqueda) con la actividad** Copy** (Copia) una a una. Para conectarse, empiece a arrastrar en el cuadro **verde** adjunto a la actividad **Lookup** (Búsqueda) y colóquela en la actividad **Copy** (Copia). Suelte el botón del mouse cuando el color del borde de la actividad de copia cambie a **azul**.

    ![Conexión de las actividades de búsqueda a la actividad de copia](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
13. Seleccione la actividad **Copy** (Copia) de la canalización. Cambie a la pestaña **Source** (Origen) en la ventana de **propiedades**. 

    1. Seleccione **SourceDataset** como **Source Dataset** (Conjunto de datos de origen). 
    2. Seleccione **Query** (Consulta) en **Use Query** (Usar consulta). 
    3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta).

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Actividad de copia: configuración del origen](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
14. Cambie a la pestaña **Sink** (Receptor) y seleccione **SinkDataset** en **Sink Dataset** (Conjunto de datos receptor). 
        
    ![Actividad de copia: configuración del receptor](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
15. Cambie a la pestaña **Parameters** (Parámetros) y realice los siguientes pasos:

    1. En la propiedad **Sink Stored Procedure Name** (Nombre procedimiento almacenado receptor), escriba `@{item().StoredProcedureNameForMergeOperation}`.
    2. En la propiedad **Sink Table Type** (Tipo tabla receptor), escriba `@{item().TableType}`.
    3. En la sección **Sink Dataset** (Conjunto de datos receptor), en el parámetro **SinkTableName**, escriba `@{item().TABLE_NAME}`.

        ![Actividad de copia: parámetros](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
16. Arrastre la actividad **Stored Procedure** (procedimiento almacenado) del cuadro de herramientas **Activities** (Actividades) y colóquela en la superficie del diseñador de canalizaciones. Conecte la actividad **Copy** (Copia) a la actividad **Stored Procedure** (Procedimiento almacenado). 

    ![Actividad de copia: parámetros](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
17. Seleccione el **Stored Procedure** actividad en la canalización y escriba **StoredProceduretoWriteWatermarkActivity** para **nombre** en la **General** pestaña de la **propiedades** ventana. 

    ![Actividad de procedimiento almacenado: nombre](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
18. Cambie a la pestaña **SQL Account** (Cuenta de SQL) y seleccione **AzureSqlDatabaseLinkedService** en **Linked service** (Servicio vinculado).

    ![Actividad de procedimiento almacenado: cuenta SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
19. Cambie a la pestaña **Stored Procedure** (Procedimiento almacenado) y realice los pasos siguientes:

    1. Como **Stored procedure name** (Nombre de procedimiento almacenado), seleccione `sp_write_watermark`. 
    2. Seleccione **Import parameter** (Importar parámetro). 
    3. Especifique los siguientes valores para los parámetros: 

        | NOMBRE | type | Valor | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | string | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Actividad de procedimiento almacenado: configuración del procedimiento almacenado](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
20. En el panel izquierdo, haga clic en **Publish** (Publicar). Esta acción publica las entidades que creó en el servicio Data Factory. 

    ![Botón Publicar](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
21. Espere a que aparezca el mensaje **Successfully published** (Publicado correctamente). Para ver las notificaciones, haga clic en el vínculo **Show Notifications** (Mostrar notificaciones). Para cerrar la ventana de notificaciones, haga clic en la **X**.

    ![Show Notifications (Mostrar notificaciones)](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Ejecución de la canalización

1. En la barra de herramientas de la canalización, haga clic en **Trigger** (Desencadenar) y en **Trigger Now** (Desencadenar ahora).     

    ![Trigger now (Desencadenar ahora)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
2. En la ventana **Pipeline Run** (Ejecución de canalización), escriba el siguiente valor para el parámetro **tableList** y haga clic en **Finish** (Finalizar). 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![Argumentos de Pipeline Run (Ejecución de canalización)](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Supervisar la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Verá la ejecución de canalización que ha desencadenado el **desencadenador manual**. Haga clic en el botón **Refresh** (Actualizar) para actualizar la lista. Los vínculos de la columna **Action** (Acción) permiten ver las ejecuciones de actividad asociadas a la ejecución de la canalización y volver a ejecutar la canalización. 

    ![Ejecuciones de la canalización](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Verá todas las ejecuciones de actividad asociadas con la ejecución de la canalización seleccionada. 

    ![Ejecuciones de actividad](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Revisión del resultado
En SQL Server Management Studio, ejecute las consultas siguientes contra la base de datos SQL de Azure de destino para comprobar que los datos se copiaron de las tablas de origen a las tablas de destino: 

**Consultar** 
```sql
select * from customer_table
```

**Salida**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consultar**

```sql
select * from project_table
```

**Salida**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consultar**

```sql
select * from watermarktable
```

**Salida**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que se actualizaron los valores de marca de agua de ambas tablas. 

## <a name="add-more-data-to-the-source-tables"></a>Adición de más datos a las tablas de origen

Ejecute la consulta siguiente contra la base de datos SQL Server de origen para actualizar una fila existente en customer_table. Inserte una nueva fila en project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Nueva ejecución de la canalización
1. En la ventana del explorador web, cambie a la pestaña **Edit** (Editar) de la izquierda. 
2. En la barra de herramientas de la canalización, haga clic en **Trigger** (Desencadenar) y en **Trigger Now** (Desencadenar ahora).   

    ![Trigger now (Desencadenar ahora)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. En la ventana **Pipeline Run** (Ejecución de canalización), escriba el siguiente valor para el parámetro **tableList** y haga clic en **Finish** (Finalizar). 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline"></a>Supervisar la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Verá la ejecución de canalización que ha desencadenado el **desencadenador manual**. Haga clic en el botón **Refresh** (Actualizar) para actualizar la lista. Los vínculos de la columna **Action** (Acción) permiten ver las ejecuciones de actividad asociadas a la ejecución de la canalización y volver a ejecutar la canalización. 

    ![Ejecuciones de la canalización](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Verá todas las ejecuciones de actividad asociadas con la ejecución de la canalización seleccionada. 

    ![Ejecuciones de actividad](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Revisión de los resultados finales
En SQL Server Management Studio, ejecute las siguientes consultas en la base de datos de destino para comprobar que los datos nuevos o actualizados se han copiado de las tablas de origen a las tablas de destino. 

**Consultar** 
```sql
select * from customer_table
```

**Salida**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe los nuevos valores de **Nombre** y **LastModifytime** de **PersonID**: 3. 

**Consultar**

```sql
select * from project_table
```

**Salida**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Tenga en cuenta que la entrada **NewProject** se agregó a project_table. 

**Consultar**

```sql
select * from watermarktable
```

**Salida**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que se actualizaron los valores de marca de agua de ambas tablas.
     
## <a name="next-steps"></a>pasos siguientes
En este tutorial, realizó los pasos siguientes: 

> [!div class="checklist"]
> * Preparación de los almacenes de datos de origen y de destino
> * Creación de una factoría de datos.
> * Creación de una instancia de Integration Runtime (IR) autohospedado
> * Instalación del entorno de ejecución de integración
> * Cree servicios vinculados. 
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación, ejecución y supervisión de una canalización
> * Consulte los resultados.
> * Adición o actualización de datos en tablas de origen
> * Nueva ejecución y supervisión de la canalización
> * Revisión de los resultados finales

Pase al tutorial siguiente para obtener información acerca de la transformación de datos mediante el uso de un clúster de Spark en Azure:

> [!div class="nextstepaction"]
>[Incrementally load data from Azure SQL Database to Azure Blob Storage using Change Tracking technology](tutorial-incremental-copy-change-tracking-feature-portal.md) (Carga incremental de datos de Azure SQL Database a Azure Blob Storage mediante la tecnología de control de cambios)


