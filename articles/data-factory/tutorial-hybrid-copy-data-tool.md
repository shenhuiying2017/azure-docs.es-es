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
ms.openlocfilehash: 77090d9a61945c9edc42cde7d647c75e91f54dd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copia de datos de una base de datos de SQL Server local en Azure Blob Storage con la herramienta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1: ya está disponible con carácter general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](tutorial-hybrid-copy-data-tool.md)

En este tutorial, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copy Data para crear una canalización que copia datos de una instancia de base de datos de SQL Server local en una instancia de Azure Blob Storage.

> [!NOTE]
> - Si no está familiarizado con Azure Data Factory, consulte [Introducción a Data Factory](introduction.md).
>
> - Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>requisitos previos
### <a name="azure-subscription"></a>Suscripción de Azure
Antes de empezar, si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, a la cuenta de usuario que use para iniciar sesión en Azure debe se le deben asignar los roles *colaborador* o *propietario*, o bien debe de ser de un *administrador* de la suscripción a Azure. 

Para ver los permisos que tiene en la suscripción, vaya a Azure Portal. Seleccione su nombre de usuario en la esquina superior derecha y luego seleccione **Permisos**. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte el artículo acerca de la [incorporación de roles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 y 2017
En este tutorial se usa una base de datos de SQL Server local como almacén de datos de *origen*. La canalización de la factoría de datos que crea en este tutorial, copia los datos de esta base de datos de SQL Server local (origen) a Blob Storage (receptor). Luego, cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla. 

1. Inicie SQL Server Management Studio. Si no está instalada en su máquina, vaya a [Descarga de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Conéctese a una instancia de SQL Server con sus credenciales. 

3. Cree una base de datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en **Bases de datos** y, luego, seleccione **Nueva base de datos**. 
 
4. En el cuadro de diálogo **Nueva base de datos**, escriba el nombre de la base de datos y haga clic en **Aceptar**. 

5. Para crear la tabla **emp** e insertar en ella algunos datos de ejemplo, ejecute el siguiente script de consulta en la base de datos. En la vista de árbol, haga clic con el botón derecho en la base de datos que ha creado y, después, haga clic en **Nueva consulta**.

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

### <a name="azure-storage-account"></a>Cuenta de almacenamiento de Azure
En este tutorial, use una cuenta de almacenamiento de Azure (en concreto Blob Storage) de uso general como almacén de datos de destino o receptor. Si no dispone de una cuenta de almacenamiento de uso general, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla. La canalización de la factoría de datos que crea en este tutorial copia datos de la base de datos de SQL Server local (origen) en esta instancia de Blob Storage (receptor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obtención de un nombre y una clave de cuenta de almacenamiento
En este tutorial, use el nombre y la clave de su cuenta de almacenamiento. Para obtener el nombre y la clave de la cuenta de almacenamiento, siga estos pasos: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con el nombre de usuario y la contraseña de Azure. 

2. En el panel izquierdo, seleccione **Más servicios**. Use la palabra clave **Almacenamiento** para filtrar el resultado y, luego, seleccione **Cuentas de almacenamiento**.

    ![Búsqueda de cuenta de almacenamiento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. En la lista de cuentas de almacenamiento, filtre por su cuenta de almacenamiento, si fuera necesario. Después, seleccione su cuenta de almacenamiento. 

4. En la ventana **Cuenta de almacenamiento**, seleccione **Claves de acceso**.

    ![Claves de acceso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. En los cuadros **Nombre de la cuenta de almacenamiento** y **key1**, copie los valores y péguelos en el Bloc de notas, u otro editor, para su uso posterior en el tutorial. 

#### <a name="create-the-adftutorial-container"></a>Creación del contenedor adftutorial 
En esta sección se crea un contenedor de blobs denominado **adftutorial** en la instancia de Blob Storage. 

1. En la ventana **Cuenta de almacenamiento**, cambie a **Información general** y, después, seleccione **Blobs**. 

    ![Selección de la opción Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. En la ventana **Blob service**, seleccione **Contenedor**. 

    ![Botón Contenedor](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. En la ventana **Nuevo contenedor**, en el cuadro **Nombre**, escriba **adftutorial**y, después, seleccione **Aceptar**. 

    ![Nuevo contenedor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. En la lista de contenedores, seleccione **adftutorial**.

    ![Selección de contenedor](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenga abierta la ventana **Contenedor** de **adftutorial**. Úselo para comprobar la salida al final de este tutorial. Data Factory crea automáticamente la carpeta de salida de este contenedor, por lo que no es necesario que el usuario la cree.

    ![Ventana Contenedor](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú izquierdo, seleccione **Nuevo** > **Datos y análisis** > **Data Factory**. 
   
   ![Creación de nueva factoría de datos](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**. 
      
     ![Nueva factoría de datos](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   El nombre de la factoría de datos tiene que ser *único a nivel global*. Si ve el siguiente mensaje de error en el campo de nombre, cambie el nombre de la factoría de datos (por ejemplo, suNombreADFTutorialDataFactory). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   ![Nombre de la nueva factoría de datos](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Seleccione la **suscripción** de Azure en la que quiere crear la factoría de datos. 
4. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable.

      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos. 
         
      Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).
5. En **Versión**, seleccione **V2 (versión preliminar)**.
6. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa Data Factory pueden estar en otras ubicaciones o regiones.
7. Seleccione **Anclar al panel**. 
8. Seleccione **Crear**.
9. En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory):

    ![Icono de implementación de una factoría de datos](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. Una vez finalizada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    ![Página principal Factoría de datos](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página **Let's get started** (Introducción), seleccione **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Icono de la herramienta Copy Data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades) de la herramienta Copy Data, en **Task name** (Nombre de la tarea), especifique **CopyFromOnPremSqlToAzureBlobPipeline**. Luego, seleccione **Siguiente**. La herramienta Copy Data crea una canalización con el nombre que especificó para este campo. 
    
   ![Nombre de tarea](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **SQL Server** y luego seleccionen **Next** (Siguiente). Puede que necesite desplazarse hacia abajo para ver **SQL Server** en la lista. 

   ![Selección de SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. En **Connection name** (Nombre de la conexión), escriba **SqlServerLinkedService**. Seleccione el vínculo **Create Integration Runtime** (Crear entorno de ejecución de integración). Tiene que crear un entorno de ejecución de integración, descargarlo en la máquina y registrarlo en Data Factory. El entorno de ejecución de integración autohospedado copia datos entre el entorno local y la nube.

   ![Creación de un entorno de ejecución de integración autohospedado](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. En el cuadro de diálogo **Create Integration Runtime** (Crear entorno de ejecución de integración), en el campo **Name** (Nombre) escriba **TutorialIntegration Runtime**. Seleccione **Crear**. 

   ![Nombre del entorno de ejecución de integración](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Seleccione **Launch express setup on this computer** (Iniciar configuración rápida en este equipo). Esta acción instala el entorno de ejecución de integración en la máquina y la registra en Data Factory. Como alternativa, puede usar la instalación manual para descargar el archivo de instalación, ejecutarlo y registrar la instancia de Integration Runtime con la clave. 

    ![Vínculo Launch express setup on this computer (Iniciar configuración rápida en este equipo)](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Ejecute la aplicación descargada. Verá el estado de la configuración rápida en la ventana. 

    ![Estado de la configuración rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Confirme que **TutorialIntegrationRuntime** está seleccionado en el campo **Integration Runtime**.

    ![Integration Runtime seleccionado](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. En **Specify the on-premises SQL Server database** (Especificar la base de datos de SQL Server local), realice los pasos siguientes: 

    a. En **Connection name** (Nombre de la conexión), escriba **OnPremSqlLinkedService**.

    b. Escriba el nombre de la instancia de SQL Server local en **Server name** (Nombre del servidor).

    c. Escriba el nombre de la base de datos local en **Database name** (Nombre de la base de datos).

    d. Seleccione la autenticación adecuada en **Authentication type** (Tipo de autenticación).

    e. Escriba el nombre de usuario con acceso a la instancia de SQL Server local en **User name** (Nombre de usuario).

    f. Escriba la **contraseña** del usuario. 
10. En la página **Select tables from which to copy the data or use a custom query** (Seleccionar tablas de donde copiar los datos o usar una consulta personalizada), seleccione la tabla **[dbo].[emp]** de la lista y luego seleccione **Next** (Siguiente). 

    ![Selección de la tabla emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure Blob Storage** y después **Next** (Siguiente).

    ![Selección de Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. En la página **Specify the Azure Blob storage account** (Especificar la cuenta de Azure Blob Storage), realice los pasos siguientes: 

    a. En **Connection name** (Nombre de la conexión), escriba **AzureStorageLinkedService**.

    b. Seleccione la cuenta de almacenamiento en la lista desplegable **Storage account name** (Nombre de la cuenta de almacenamiento). 

    c. Seleccione **Siguiente**.

    ![Especificar la cuenta de almacenamiento](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. En la página **Choose the output file or folder** (Elegir el archivo o la carpeta de salida), en **Folder path** (Ruta de acceso a la carpeta) escriba **adftutorial/fromonprem**. El contenedor **adftutorial** se creó como parte de los requisitos previos. Si no existe la carpeta de salida, Data Factory la crea automáticamente. También puede usar el botón **Browse** (Examinar) para examinar Blob Storage y sus contenedores o carpetas. Tenga en cuenta que el nombre del archivo de salida se establece en **dbo.emp** de forma predeterminada.
        
    ![Elección del archivo o la carpeta de salida](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. En la página **File format settings** (Configuración de formato de archivo), seleccione **Next** (Siguiente). 

    ![Página File format settings (Configuración de formato de archivo)](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. En la página **Settings** (Configuración), seleccione **Next** (Siguiente). 

    ![Página Configuración](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. En la página **Summary** (Resumen), revise los valores de configuración y seleccione **Next** (Siguiente). 

    ![Página de resumen](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización o la tarea que ha creado.

    ![Página Deployment (Implementación)](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. En la pestaña **Monitor** (Supervisión), puede ver el estado de la canalización que ha creado. Puede usar los vínculos de la columna **Action** (Acción) para ver las ejecuciones de actividad asociadas a la de la canalización y volver a ejecutar la canalización. 

    ![La supervisión de la canalización se ejecuta](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones) para ver las ejecuciones de actividad asociadas a las de la canalización. Para ver detalles acerca de la operación de copia, seleccione el vínculo **Details** (Detalles) (icono de gafas) en la columna **Actions** (Acciones). Para volver a la vista **Pipeline Runs** (Ejecuciones de canalización) seleccione **Pipelines** (Canalizaciones) en la parte superior.

    ![Supervisión de las ejecuciones de actividad](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Confirme que ve un archivo de salida en la carpeta **fromonprem** del contenedor **adftutorial**. 
 
    ![Blob de salida](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Seleccione la pestaña **Edit** (Editar) de la izquierda para cambiar al modo de edición. Con el editor puede actualizar los servicios vinculados, los conjuntos de datos y las canalizaciones creados mediante la herramienta. Seleccione **Code** (Código) para ver el código JSON asociado con la entrada abierta en el editor. Para más información sobre cómo editar estas entidades en la interfaz de usuario de Data Factory, consulte [la versión de Azure Portal de este tutorial](tutorial-copy-data-portal.md).

    ![Pestaña Edit (Editar)](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia datos de la base de datos SQL Server local en Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

Para ver una lista de los almacenes de datos compatibles con Data Factory, consulte los [almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).

Para informarse acerca de cómo copiar datos de forma masiva de un origen a un destino, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy-portal.md)
