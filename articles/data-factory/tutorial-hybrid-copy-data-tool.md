---
title: Copia de datos locales con la herramienta Copy Data de Azure | Microsoft Docs
description: Cree una instancia de Azure Data Factory y use la herramienta Copy Data para copiar datos locales de Azure SQL Server Database en Azure Blob Storage.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: aba8b13d47b2b9236a0d5cc868e53780e894c406
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-copy-data-tool"></a>Copia de datos de una base de datos de SQL Server local en Azure Blob Storage con la herramienta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](tutorial-hybrid-copy-data-tool.md)

En este tutorial, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copy Data para crear una canalización que copia datos de una instancia de Azure SQL Database local en una de Azure Blob Storage.

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
### <a name="azure-subscription"></a>Suscripción de Azure
Antes de empezar, si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, a la cuenta de usuario que use para iniciar sesión en Azure debe se le deben asignar los roles *colaborador* o *propietario*, o bien debe de ser de un *administrador* de la suscripción a Azure. 

Para ver los permisos que tiene en la suscripción, vaya a Azure Portal, seleccione su nombre de usuario en la esquina superior derecha y, después, seleccione **Permisos**. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte el artículo sobre la [adición de roles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 y 2017
En este tutorial se usa una base de datos de SQL Server local como almacén de datos de *origen*. La canalización de Data Fatory que crea en este tutorial copia los datos de esta base de datos de SQL Server local (origen) a Azure Blob Storage (receptor). Luego, cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla. 

1. Inicie SQL Server Management Studio. Si no está instalada en su equipo, vaya a [Descarga de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Conéctese a una instancia de SQL Server con sus credenciales. 

3. Cree una base de datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en **Bases de datos** y, luego, seleccione **Nueva base de datos**. 
 
4. En el cuadro de diálogo **Nueva base de datos**, escriba el nombre de la base de datos y haga clic en **Aceptar**. 

5. Para crear la tabla **emp** e insertar datos de ejemplo en ella, ejecute el siguiente script de consulta en la base de datos: en la vista de árbol, haga clic con el botón derecho en la base de datos que creó y seleccione **Nueva consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de destino o receptor en este tutorial. Si no dispone de una cuenta de Azure Storage de uso general, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla. La canalización de la factoría de datos que crea en este tutorial copia los datos de la base de datos de SQL Server local (origen) a esta instancia de Azure Blob Storage (receptor). 

#### <a name="get-storage-account-name-and-account-key"></a>Obtener un nombre y una clave de cuenta de Storage
En este tutorial, use el nombre y la clave de su cuenta de Azure Storage. Para obtener el nombre y la clave de una cuenta de almacenamiento, siga estos pasos: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con el nombre de usuario y la contraseña de Azure. 

2. En el panel izquierdo, seleccione **Más servicios**, use la palabra clave **Almacenamiento** para realizar el filtro y, luego, seleccione **Cuentas de almacenamiento**.

    ![Buscar cuenta de Storage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. En la lista de cuentas de almacenamiento, filtre por su cuenta de almacenamiento (si fuera necesario) y, después, seleccione su cuenta de almacenamiento. 

4. En la ventana **Cuenta de almacenamiento**, seleccione **Claves de acceso**.

    ![Obtener nombre y clave de la cuenta de Storage](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. En los cuadros **Nombre de la cuenta de almacenamiento** y **key1**, copie los valores y péguelos en el Bloc de notas, u otro editor, para su uso posterior en el tutorial. 

#### <a name="create-the-adftutorial-container"></a>Creación del contenedor adftutorial 
En esta sección se crea un contenedor de blobs denominado **adftutorial** en la instancia de Azure Blob Storage. 

1. En la ventana **Cuenta de almacenamiento**, cambie a **Información general** y, después, seleccione **Blobs**. 

    ![Selección de la opción Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. En la ventana **Blob service**, seleccione **Contenedor**. 

    ![Botón Agregar contenedor](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. En la ventana **Nuevo contenedor**, en el cuadro **Nombre**, escriba **adftutorial**y, después, seleccione **Aceptar**. 

    ![Especificación de un nombre de contenedor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. En la lista de contenedores, seleccione **adftutorial**.  

    ![Selección del contenedor](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenga abierta la ventana **contenedor** de **adftutorial**. Úselo para comprobar la salida al final de este tutorial. Data Factory crea automáticamente la carpeta de salida de este contenedor, por lo que no es necesario que el usuario la cree.

    ![Ventana Contenedor](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
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

    ![icono implementando factoría de datos](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Azure Data Factory en una pestaña independiente. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página de introducción, haga clic en el icono **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Icono de la herramienta Copy Data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades) de la herramienta Copy Data, especifique **CopyFromOnPremSqlToAzureBlobPipeline** en **Task name** (Nombre de la tarea) y haga clic **Next** (Siguiente). La herramienta Copy Data crea una canalización con el nombre que especificó para este campo. 
    
   ![Página de propiedades](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **SQL Server** y haga clic en **Next** (Siguiente). Puede que necesite desplazarse hacia abajo para ver **SQL Server** en la lista. 

   ![Página Source data store (Almacén de datos de origen)](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Escriba **SqlServerLinkedService** como **Connection name** (Nombre de la colección) y haga clic en el vínculo **Create Integration Runtime** (Crear instancia de Integration Runtime). Debe crear una instancia de Integration Runtime autohospedada, descargarla en la máquina y registrarla en el servicio Data Factory. La instancia de Integration Runtime autohospedada copia datos entre el entorno local y la nube de Azure.

   ![Creación del vínculo de Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. En el cuadro de diálogo **Create Integration Runtime** (Crear instancia de Integration Runtime), escriba **TutorialIntegration Runtime** en el campo **Name** (Nombre) y haga clic en **Create** (Crear). 

   ![Creación del cuadro de diálogo de Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Haga clic en **Launch express setup on this computer** (Iniciar configuración rápida en este equipo). Esta acción instala la instancia de Integration Runtime en la máquina y la registra en el servicio Data Factory. Como alternativa, puede usar la instalación manual para descargar el archivo de instalación, ejecutarlo y registrar la instancia de Integration Runtime con la clave. 

    ![Creación del vínculo de Integration Runtime](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Ejecute la aplicación descargada. Verá el **estado** de la configuración rápida en la ventana. 

    ![Estado de la configuración rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Confirme que **TutorialIntegrationRuntime** está seleccionado en el campo **Integration Runtime**.

    ![Integration Runtime seleccionado](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. En **Specify the on-premises SQL Server database** (Especificar la base de datos de SQL Server local), realice los pasos siguientes: 

    1. Escriba **OnPremSqlLinkedService** como **Connection name** (Nombre de la conexión).
    2. Escriba el nombre de la instancia de SQL Server local en **Server name** (Nombre del servidor).
    3. Escriba el nombre de la base de datos local en **Database name** (Nombre de la base de datos). 
    4. Seleccione la autenticación adecuada en **Authentication type** (Tipo de autenticación).
    5. Escriba el nombre de usuario con acceso a la instancia de SQL Server local en **User name** (Nombre de usuario).
    6. Escriba la **contraseña** del usuario. 
10. En la página **Select tables from which to copy the data or use a custom query** (Seleccionar tablas de donde copiar los datos o usar una consulta personalizada), seleccione la tabla **[dbo].[emp]** de la lista y haga clic en **Next** (Siguiente). 

    ![Selección de la tabla emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure Blob Storage** y haga clic en **Next** (Siguiente).

    ![Seleccionar Azure Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. En la página **Specify the Azure Blob storage account** (Especificar la cuenta de Azure Blob Storage), realice los pasos siguientes: 

    1. Escriba **AzureStorageLinkedService** como **Connection name** (Nombre de la conexión).
    2. Seleccione la cuenta de Azure Storage de la lista desplegable **Storage account name** (Nombre de la cuenta de Storage). 
    3. Haga clic en **Next**.

        ![Seleccionar Azure Blob Storage](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. En la página **Choose the output file or folder** (Elegir el archivo o la carpeta de salida), realice los pasos siguientes: 
    
    1. Escriba **adftutorial/fromonprem** como **Folder path** (Ruta de acceso de carpeta). El contenedor **adftutorial** se creó como parte de los requisitos previos. Si no existe la carpeta de salida, el servicio Data Factory la crea automáticamente. También puede usar el botón **Browse** (Examinar) para ir al almacenamiento de blobs y sus contenedores o carpetas. Tenga en cuenta que el nombre del archivo de salida se establece en **dbo.emp** de forma predeterminada.
        
        ![Elección del archivo o la carpeta de salida](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. En la página **File format settings** (Configuración de formato de archivo), haga clic en **Next** (Siguiente). 

    ![Página File format settings (Configuración de formato de archivo)](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. En la página **Settings** (Configuración), haga clic en **Next** (Siguiente). 

    ![Página Configuración](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. En la página **Summary** (Resumen), revise los valores de configuración y haga clic en **Next** (Siguiente). 

    ![Página de resumen](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. En la página **Deployment** (Implementación), haga clic en **Monitor** (Supervisión) para supervisar la canalización o la tarea que ha creado.

    ![Página Deployment (Implementación)](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. En la pestaña **Monitor** (Supervisión), puede ver el estado de la canalización que ha creado. Los vínculos de la columna **Action** (Acción) permiten ver las ejecuciones de actividad asociadas a la de la canalización y volver a ejecutar la canalización. 

    ![Ejecuciones de la canalización](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones) para ver las ejecuciones de actividad asociadas a las de la canalización. Para ver detalles sobre la operación de copia, haga clic en el vínculo **Details** (Detalles) (icono de gafas) de la columna **Actions** (Acciones). Para volver a la vista de ejecuciones de canalización, haga clic en **Pipelines** (Canalizaciones) de la parte superior.

    ![Ejecuciones de actividad](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Confirme que ve un archivo de salida en la carpeta **fromonprem** del contenedor **adftutorial**.   
 
    ![Blob de salida](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Haga clic en la pestaña **Edit** (Editar) de la izquierda para cambiar al modo de edición. Con el editor puede actualizar los servicios vinculados, los conjuntos de datos y las canalizaciones creados mediante la herramienta. Haga clic en **Code** (Código) para ver el código JSON asociado con la entrada abierta en el editor. Para más información sobre la edición de estas entidades en la interfaz de usuario de Data Factory, consulte [la versión de Azure Portal de este tutorial](tutorial-copy-data-portal.md).

    ![Pestaña Edit (Editar)](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia datos de la base de datos SQL Server local en una instancia de Azure Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización
> * Supervisión de las ejecuciones de canalización y actividad.

Para ver una lista de los almacenes de datos compatibles con Data Factory, consulte los [almacenes de datos disponibles](copy-activity-overview.md#supported-data-stores-and-formats).

Para obtener información acerca de cómo copiar datos de forma masiva de un origen a un destino, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy-portal.md)