---
title: Copia de datos de SQL Server a Blob Storage mediante Azure Data Factory | Microsoft Docs
description: "Aprenda a copiar datos de un almacén de datos local a la nube de Azure mediante un runtime de integración autohospedado en Azure Data Factory."
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 64cd758e2f40ff2b18abbff1194a7e57389d8a54
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Tutorial: copia de datos de una base de datos de SQL Server local a Azure Blob Storage
En este tutorial se utiliza la interfaz de usuario (UI) de Azure Data Factory para crear una canalización de Data Factory que copie los datos de una base de datos de SQL Server local a Azure Blob Storage. Cree y use una instancia de Integration Runtime autohospedado, que mueve los datos entre almacenes locales y en la nube. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> En este artículo no se ofrece una introducción detallada al servicio Data Factory. Para más información, consulte [Introducción a Azure Data Factory](introduction.md). 

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Creación de los servicios vinculados SQL Server y Azure Storage. 
> * Creación de los conjuntos de datos de SQL Server y Azure Blob.
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de la canalización

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

5. Para crear la tabla **emp** e insertar en ella algunos datos de ejemplo, ejecute el siguiente script de consulta en la base de datos:

   ```
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

6. En la vista de árbol, haga clic con el botón derecho en la base de datos que ha creado y, después, haga clic en **Nueva consulta**.

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de destino o receptor en este tutorial. Si no dispone de una cuenta de Azure Storage de uso general, consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account). La canalización de la factoría de datos que crea en este tutorial copia los datos de la base de datos de SQL Server local (origen) a esta instancia de Azure Blob Storage (receptor). 

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
En este paso, creará una factoría de datos e iniciará la interfaz de usuario de Azure Data Factory para crear una canalización de la factoría de datos. 

1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![icono implementando factoría de datos](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Azure Data Factory en una pestaña independiente. 


## <a name="create-a-pipeline"></a>Crear una canalización

1. En la página **Get started** (Introducción), haga clic en **Create pipeline** (Crear canalización). Se crea automáticamente una canalización. Aparece la canalización en la vista de árbol y su editor abierto. 

   ![Página de introducción](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. En la pestaña **General** en la parte inferior de la ventana **Properties** (Propiedades), escriba **SQLServerToBlobPipeline** para **Name** (Nombre).

   ![Nombre de la canalización](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. En el cuadro de herramientas **Activities** (Actividades), expanda **Data Flow** (Flujo de datos), arrastre la actividad **Copy** (Copiar) y colóquela en la superficie del diseño de canalizaciones. Establezca el nombre de la actividad en **CopySqlServerToAzureBlobActivity**.

   ![Nombre de la actividad](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. En la ventana Propiedades, cambie a la pestaña **Source** (Origen) y haga clic en **+ New** (+ Nuevo).

   ![Nuevo conjunto de datos de origen: botón](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. En la ventana **New Dataset** (Nuevo conjunto de datos), busque **SQL Server**, seleccione **SQL Server** y haga clic en **Finish** (Finalizar). Verá una nueva pestaña titulada **SqlServerTable1**. También verá el conjunto de datos **SqlServerTable1** en la vista de árbol de la izquierda. 

   ![Selección de SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. En la pestaña **General** (General) de la ventana Propiedades, escriba **SqlServerDataset** en **Name** (Nombre).
    
   ![Conjunto de datos de origen: nombre](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. Cambie a la pestaña **Connections** (Conexiones) y haga clic en **+ New** (+ Nueva). En este paso se crea una conexión con el almacén de datos de origen (base de datos de SQL Server). 

   ![Conjunto de datos de origen: botón nueva conexión](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. En la ventana **New Linked Service** (Nuevo servicio vinculado), haga clic en **New integration runtime** (Nuevo entorno de ejecución de integración). En esta sección se crea una instancia de Integration Runtime autohospedada y se asocia con un equipo local con la base de datos de SQL Server. Integration Runtime autohospedado es el componente que copia los datos de la base de datos de SQL Server del equipo a Azure Blob Storage. 

   ![Nuevo entorno de ejecución de integración](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. En la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione **Private Network** (Red privada) y haga clic en **Next** (Siguiente). 

   ![Seleccionar red privada](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. Escriba un nombre para el entorno de ejecución de integración y haga clic en **Next** (Siguiente).  
    
   ![Entorno de ejecución de integración: nombre](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. Haga clic en **Click here to launch the express setup for this computer** (Haga clic aquí para iniciar la configuración rápida en este equipo) en la sección **Option 1: Express setup** (Opción 1: configuración rápida). 

   ![Vínculo a configuración rápida](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. En la ventana **Microsoft Integration Runtime (Self-hosted) Express Setup** (Configuración rápida de Integration Runtime [autohospedado]), haga clic en **Close** (Cerrar). 

   ![Configuración correcta de Integration Runtime](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. En el explorador web, en la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), haga clic en **Finish** (Finalizar). 

   ![Configuración de Integration Runtime: finalizar](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **SqlServerLinkedService** en **Name** (Nombre).
    2. Confirme que el entorno de ejecución de integración autohospedado que creó anteriormente se muestra en **Connect via integration runtime** (Conectar mediante Integration Runtime).
    3. Especifique el nombre de la instancia de SQL Server en **Server name** (Nombre del servidor). 
    4. Especifique el nombre de la base de datos con la tabla **emp** en el campo **Database name** (Nombre de la base de datos). 
    5. Seleccione el **tipo de autenticación** adecuado que debe usar Data Factory para conectarse a la base de datos de SQL Server. 
    6. Escriba el **nombre de usuario** y la **contraseña**. Si necesita usar una barra diagonal inversa (\\) en la cuenta del usuario o en el nombre del servidor, utilice el carácter de escape (\\) antes. Por ejemplo, use *mydomain\\\\myuser*. 
    7. Haga clic en **Test connection** (Probar conexión). Realice este paso para confirmar que el servicio Data Factory puede conectarse a la base de datos de SQL Server mediante el entorno de ejecución de integración autohospedado que ha creado. 
    8. Haga clic en **Save** (Guardar) para guardar el servicio vinculado.

       ![Servicio vinculado de SQL Server: configuración](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. Debería volver a la ventana con el conjunto de datos de origen abierto. En **Connection** (Conexión) en la ventana **Properties** (Propiedades), realice los pasos siguientes: 

    1. Confirme que ve **SqlServerLinkedService** en **Linked service** (Servicio vinculado). 
    2. Seleccione **[dbo].[emp]** en **Table** (Tabla).

        ![Conjunto de datos de origen: información de conexión](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. Cambie a la pestaña con SQLServerToBlobPipeline o haga clic en **SQLServerToBlobPipeline** en la vista de árbol. 

    ![Pestaña Pipeline (Canalización)](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. Cambie a la pestaña **Sink** (Receptor) en la ventana **Properties** (Propiedades) y haga clic en **+ New** (+ Nuevo). 

    ![Conjunto de datos receptor: botón Nuevo](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y haga clic en **Finish** (Finalizar). Verá que se abre una nueva pestaña para el conjunto de datos. También verá el conjunto de datos en la vista de árbol. 

    ![Seleccionar Azure Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. Escriba **AzureBlobDataset** en **Name** (Nombre).

    ![Conjunto de datos receptor: nombre](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. Cambie a la pestaña **Connection** (Conexión) de la ventana **Properties** (Propiedades) y haga clic en **+ New** (+ Nuevo) en **Linked service** (Servicio vinculado). 

    ![Nuevo servicio vinculado: botón](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureStorageLinkedService** en **Name** (Nombre).
    2. Seleccione la cuenta de Azure Storage de **Storage account name** (Nombre de la cuenta de Storage). 
    3. Haga clic en **Test connection** (Probar conexión) para probar la conexión a la cuenta de Azure Storage.
    4. Haga clic en **Save**(Guardar).

        ![Servicio vinculado de Azure Storage: configuración](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  Debería volver a la ventana con el conjunto de datos receptor abierto. En la pestaña **Connection** (Conexión), realice los pasos siguientes: 

        1. Confirme que **AzureStorageLinkedService** está seleccionado en **Linked service** (Servicio vinculado).
        2. En la parte de la **carpeta** del campo **File path** (Ruta de acceso del archivo), escriba **adftutorial/fromonprem**. Si no existe la carpeta de salida en el contenedor adftutorial, el servicio Data Factory la crea automáticamente.
        3. Escriba `@CONCAT(pipeline().RunId, '.txt')` en la parte **nombre de archivo** del campo **File path** (Ruta de acceso del archivo).

            ![Conjunto de datos receptor: conexión](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. Cambie a la pestaña con la canalización abierto o haga clic en la **canalización** en la **vista de árbol**. Confirme que **AzureBlobDataset** está seleccionado en **Sink Dataset** (Conjunto de datos receptor). 

    ![Conjunto de datos receptor seleccionado ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. Para comprobar la configuración de la canalización, haga clic en Validate (Comprobar) en la barra de herramientas de la canalización. Cierre **Pipe Validation Report** (Informe de validación de la canalización) haciendo clic en la **X** en la esquina derecha. 

    ![Comprobar la canalización](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. Para publicar las entidades que ha creado en el servicio Azure Data Factory, haga clic en **Publish** (Publicar).

    ![Botón Publicar](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. Espere hasta ver el mensaje emergente **Publicación correcta**. También puede comprobar el estado de la publicación haciendo clic en el vínculo **Show Notifications** (Mostrar notificaciones) de la izquierda. Para cerrar la ventana de notificaciones, haga clic en la **X**. 

    ![Publicación correcta](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización
Haga clic en **Trigger** (Desencadenar) en la barra de herramientas de la canalización y en **Trigger Now** (Desencadenar ahora).

![Trigger now (Desencadenar ahora)](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Vaya a la pestaña **Monitor** (Supervisar). Verá la canalización que ha desencadenado manualmente en el paso anterior. 

    ![Ejecuciones de la canalización](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Para ver las ejecuciones de actividad asociadas a la de la canalización, haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Como solo hay una actividad en la canalización, solo se ven ejecuciones de actividad. Para ver detalles sobre la operación de copia, haga clic en el vínculo **Details** (Detalles) (icono de gafas) de la columna **Actions** (Acciones). Para volver a la vista de ejecuciones de canalización, haga clic en **Pipelines** (Canalizaciones) de la parte superior.

    ![Ejecuciones de actividad](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Comprobación del resultado
La canalización automáticamente la carpeta de salida *fromonprem* en el contenedor de blobs `adftutorial`. Confirme que ve el archivo *dbo.emp.txt* en la carpeta de salida. 

1. En Azure Portal, en la ventana Contenedor **adftutorial**, seleccione **Actualizar** para ver la carpeta de salida.

    ![Carpeta de salida creada](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Seleccione `fromonprem` en la lista de carpetas. 
3. Confirme que ve un archivo denominado `dbo.emp.txt`.

    ![Archivo de salida](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en Azure Blob Storage. Ha aprendido a:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Creación de los servicios vinculados SQL Server y Azure Storage. 
> * Creación de los conjuntos de datos de SQL Server y Azure Blob.
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de la canalización

Para ver una lista de los almacenes de datos compatibles con Data Factory, consulte los [almacenes de datos disponibles](copy-activity-overview.md#supported-data-stores-and-formats).

Para obtener información acerca de cómo copiar datos de forma masiva de un origen a un destino, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy-portal.md)
