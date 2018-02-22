---
title: Copia de datos de SQL Server a Blob Storage mediante Azure Data Factory | Microsoft Docs
description: "Aprenda a copiar datos de un almacén de datos local a la nube mediante un entorno de ejecución de integración autohospedado en Azure Data Factory."
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
ms.openlocfilehash: ced708febe848d4555429b78c0227a35b7f0c79f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copia de datos de una base de datos de SQL Server local en Azure Blob Storage
En este tutorial se utiliza la interfaz de usuario (IU) de Azure Data Factory para crear una canalización de Data Factory que copie los datos de una base de datos de SQL Server local en Azure Blob Storage. Cree y use una instancia de Integration Runtime autohospedado, que mueve los datos entre almacenes locales y en la nube.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte la [documentación para la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> En este artículo no se ofrece una introducción detallada a Data Factory. Para más información, consulte [Introducción a Data Factory](introduction.md). 

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
Para crear instancias de Data Factory, tiene que asignar los roles *colaborador* o *propietario* a la cuenta de usuario que use para iniciar sesión en Azure, o tiene que ser un *administrador* de la suscripción a Azure. 

Para ver los permisos que tiene en la suscripción, vaya a Azure Portal. En la esquina superior derecha, seleccione su nombre de usuario y luego seleccione **Permisos**. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte el artículo acerca de la [incorporación de roles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 y 2017
En este tutorial se usa una base de datos de SQL Server local como almacén de datos de *origen*. La canalización de la factoría de datos que crea en este tutorial, copia los datos de esta base de datos de SQL Server local (origen) a Blob Storage (receptor). Luego, cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla. 

1. Inicie SQL Server Management Studio. Si no está instalada en su máquina, vaya a [Descarga de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

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

### <a name="azure-storage-account"></a>Cuenta de almacenamiento de Azure
En este tutorial, use una cuenta de almacenamiento de Azure (en concreto Blob Storage) de uso general como almacén de datos de destino o receptor. Si no dispone de una cuenta de Azure Storage de uso general, consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account). La canalización de la factoría de datos que crea en este tutorial, copia los datos de la base de datos de SQL Server local (origen) en Blob Storage (receptor). 

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

1. En la ventana **Cuenta de almacenamiento**, vaya a **Información general** y, después, seleccione **Blobs**. 

    ![Selección de la opción Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. En la ventana **Blob service**, seleccione **Contenedor**. 

    ![Botón Contenedor](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. En la ventana **Nuevo contenedor** en **Nombre**, escriba **adftutorial**. Después seleccione **Aceptar**. 

    ![Ventana Nuevo contenedor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. En la lista de contenedores, seleccione **adftutorial**.

    ![Selección de contenedor](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenga abierta la ventana **contenedor** de **adftutorial**. Úselo para comprobar la salida al final de este tutorial. Data Factory crea automáticamente la carpeta de salida de este contenedor, por lo que no es necesario que el usuario la cree.

    ![Ventana Contenedor](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Crear una factoría de datos
En este paso, creará una factoría de datos e iniciará la interfaz de usuario de Data Factory para crear una canalización en la factoría de datos. 

1. Abra el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. En el menú izquierdo, seleccione **Nuevo** > **Datos y análisis** > **Data Factory**.
   
   ![Creación de nueva factoría de datos](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
3. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**. 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   El nombre de la factoría de datos tiene que ser *único a nivel global*. Si ve el siguiente mensaje de error en el campo de nombre, cambie el nombre de la factoría de datos (por ejemplo, suNombreADFTutorialDataFactory). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   ![Nombre de la nueva factoría de datos](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
4. Seleccione la **suscripción** de Azure en la que quiere crear la factoría de datos.
5. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable.

      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.
         
    Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).
6. En **Versión**, seleccione **V2 (versión preliminar)**.
7. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa Data Factory pueden estar en otras regiones.
8. Seleccione **Anclar al panel**. 
9. Seleccione **Crear**.
10. En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory):

    ![Icono de implementación de una factoría de datos](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
11. Una vez finalizada la creación, verá la página **Data Factory** tal como se muestra en la imagen:
   
    ![Página principal Factoría de datos](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
12. Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente. 


## <a name="create-a-pipeline"></a>Crear una canalización

1. En la página **Let's get started** (Introducción) seleccione **Create pipeline** (Crear canalización). Se crea automáticamente una canalización. Aparece la canalización en la vista de árbol y se abre su editor. 

   ![Página de introducción](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. En la pestaña **General** de la parte inferior de la ventana **Properties** (Propiedades), en **Name** (Nombre), escriba **SQLServerToBlobPipeline**.

   ![Nombre de la canalización](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
3. En el cuadro de herramientas **Activities** (Actividades), expanda **Data Flow** (Flujo de datos). Arrastre y coloque la actividad **Copy** (Copiar) en la superficie de diseño de la canalización. Establezca el nombre de la actividad en **CopySqlServerToAzureBlobActivity**.

   ![Nombre de la actividad](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
4. En la ventana **Properties** (Propiedades), cambie a la pestaña **Source** (Origen) y haga clic en **+ New** (+ Nuevo).

   ![Pestaña Source (Origen)](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
5. En la ventana **New Dataset** (nuevo conjunto de datos), busque **SQL Server**. Seleccione **SQL Server** y, luego, **Finish** (Finalizar). Verá una nueva pestaña titulada **SqlServerTable1**. También verá el conjunto de datos **SqlServerTable1** en la vista de árbol a la izquierda. 

   ![Selección de SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
6. En la pestaña **General** de la parte inferior de la ventana **Properties** (Propiedades), en **Name** (Nombre), escriba **SqlServerDataset**.
    
   ![Nombre del conjunto de datos de origen](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
7. Cambie a la pestaña **Connection** (Conexión) y haga clic en **+ New** (+ Nuevo). En este paso se crea una conexión con el almacén de datos de origen (base de datos de SQL Server). 

   ![Conexión con el conjunto de datos de origen](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
8. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **New integration runtime** (Nuevo entorno de ejecución de integración). En esta sección se crea una instancia de Integration Runtime autohospedada y se asocia con un equipo local con la base de datos de SQL Server. El entorno de ejecución de integración autohospedado es el componente que copia los datos de la base de datos de SQL Server de la máquina en Blob Storage. 

   ![Nuevo entorno de ejecución de integración](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
9. En la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione **Private Network** (Red privada) y haga clic en **Next** (Siguiente). 

   ![Selección de red privada](./media/tutorial-hybrid-copy-portal/select-private-network.png)
10. Escriba un nombre para el entorno de ejecución de integración y seleccione **Next** (Siguiente).
    
    ![Nombre del entorno de ejecución de integración](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
11. En la sección **Option 1: Express setup** (Opción 1: configuración rápida) seleccione **Click here to launch the express setup for this computer** (Haga clic aquí para iniciar la configuración rápida en este equipo). 

    ![Vínculo a configuración rápida](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
12. En la ventana **Integration Runtime (Self-hosted) Express Setup** (Configuración rápida de Integration Runtime [autohospedado]), seleccione **Close** (Cerrar). 

    ![Configuración rápida del entorno de ejecución de integración (autohospedado)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
13. En el explorador web, en la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), haga clic en **Finish** (Finalizar). 

    ![Configuración de Integration Runtime](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
14. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    a. En **Name** (Nombre), escriba **SqlServerLinkedService**.

    b. En **Connect via integration runtime** (Conectar a través del entorno de ejecución de integración) compruebe que aparece el entorno de ejecución de integración autohospedado que creó anteriormente.

    c. Escriba el nombre de la instancia de SQL Server en **Server name** (Nombre del servidor). 

    d. En el campo **Database name** (Nombre de la base de datos) especifique el nombre de la base de datos con la tabla **emp**.

    e. En **Authentication type** (Tipo de autenticación) seleccione el tipo de autenticación adecuado que Data Factory debe usar para conectarse a la base de datos de SQL Server.

    f. En **User name** (Nombre de usuario) y **Password** (Contraseña), escriba el nombre de usuario y la contraseña. Si necesita usar una barra diagonal inversa (\\) en la cuenta del usuario o en el nombre del servidor, utilice el carácter de escape (\\) antes. Por ejemplo, use *mydomain\\\\myuser*.

    g. Seleccione **Test connection** (Probar conexión). Realice este paso para confirmar que el servicio Data Factory puede conectarse a la base de datos de SQL Server mediante el entorno de ejecución de integración autohospedado que ha creado.

    h. Seleccione **Save** (Guardar) para guardar el servicio vinculado.

       ![Configuración del nuevo servicio vinculado](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
15. Debería volver a la ventana con el conjunto de datos de origen abierto. En la pestaña **Connection** (Conexión) en la ventana **Properties** (Propiedades), realice los pasos siguientes: 

    a. En **Linked service** (Servicio vinculado) confirme que ve **SqlServerLinkedService**.

    b. En **Table** (Tabla), seleccione **[dbo].[emp]**.

    ![Información de conexión de conjunto de datos de origen](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
16. Vaya a la pestaña con **SQLServerToBlobPipeline** o seleccione **SQLServerToBlobPipeline** en la vista de árbol. 

    ![Pestaña Pipeline (Canalización)](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
17. Vaya a la pestaña **Sink** (Receptor) en la parte inferior de la ventana **Properties** (Propiedades) y seleccione **+ New** (+ Nuevo). 

    ![Pestaña Sink (Receptor)](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
18. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage**. A continuación, seleccione **Finish** (Finalizar). Verá que se abre una nueva pestaña para el conjunto de datos. También verá el conjunto de datos en la vista de árbol. 

    ![Selección de Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
19. En **Name** (Nombre) escriba **AzureBlobDataset**.

    ![Nombre del conjunto de datos receptor](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
20. Vaya a la pestaña **Connection** (Conexión) al final de la ventana **Properties** (Propiedades). Junto a **Linked service** (servicio vinculado), seleccione **+ New** (+ Nuevo). 

    ![Botón New linked service (Nuevo servicio vinculado)](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
21. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    a. En **Name** (Nombre), escriba **AzureStorageLinkedService**.

    b. En **Storage account name** (Nombre de la cuenta de almacenamiento), seleccione la cuenta de almacenamiento.

    c. Seleccione **Test connection** (Prueba de conexión) para probar la conexión a la cuenta de almacenamiento.

    d. Seleccione **Guardar**.

    ![Configuración del servicio vinculado de Storage](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
22.  Debería volver a la ventana con el conjunto de datos receptor abierto. En la pestaña **Connection** (Conexión), realice los pasos siguientes: 

        a. En **Linked service** (Servicio vinculado) confirme que **AzureStorageLinkedService** está seleccionado.

        b. En la parte de la **carpeta** del campo **File path** (Ruta de acceso del archivo), escriba **adftutorial/fromonprem**. Si no existe la carpeta de salida en el contenedor adftutorial, Data Factory la crea automáticamente.

        c. En la parte de **nombre del archivo** del campo **File path** (Ruta de acceso del archivo), escriba `@CONCAT(pipeline().RunId, '.txt')`.

     ![Conexión del conjunto de datos receptor](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
23. Vaya a la pestaña con la canalización abierto o seleccione la canalización en la vista de árbol. En **Sink Dataset** (Conjunto de datos receptor) confirme que **AzureBlobDataset** está seleccionado. 

    ![Conjunto de datos receptor seleccionado](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
24. Para comprobar la configuración de la canalización, seleccione **Validate** (Validar) en la barra de herramientas de la canalización. Para cerrar **Pipeline Validation Report** (Informe de validación de la canalización), seleccione **Close** (Cerrar). 

    ![Comprobar la canalización](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
25. Para publicar las entidades que creó en Data Factory, seleccione **Publish All** (Publicar todo).

    ![Botón Publicar](./media/tutorial-hybrid-copy-portal/publish-button.png)
26. Espere hasta ver el mensaje emergente **Publishing succeeded** (Publicación correcta). Para comprobar el estado de la publicación seleccione el vínculo **Show Notifications** (Mostrar notificaciones) de la izquierda. Para cerrar la ventana de notificaciones, seleccione **Close** (Cerrar). 

    ![Publicación correcta](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización
Seleccione **Trigger** (Desencadenar) en la barra de herramientas para la canalización y, después, seleccione **Trigger Now** (Desencadenar ahora).

![Desencadenar la ejecución de la canalización](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Vaya a la pestaña **Monitor** (Supervisar). Verá la canalización que ha desencadenado manualmente en el paso anterior. 

    ![La supervisión de la canalización se ejecuta](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Para ver las ejecuciones de actividad asociadas con la ejecución de canalización, seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en la columna **Actions** (Acciones). Como solo hay una actividad en la canalización, solo se ven ejecuciones de actividad. Para ver detalles acerca de la operación de copia, seleccione el vínculo **Details** (Detalles) (icono de gafas) en la columna **Actions** (Acciones). Para volver a la vista **Pipeline Runs** (Ejecuciones de canalización) seleccione **Pipelines** (Canalizaciones) en la parte superior.

    ![Supervisión de las ejecuciones de actividad](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Comprobación del resultado
La canalización automáticamente la carpeta de salida *fromonprem* en el contenedor de blobs `adftutorial`. Confirme que ve el archivo *dbo.emp.txt* en la carpeta de salida. 

1. En Azure Portal, en la ventana Contenedor **adftutorial**, seleccione **Actualizar** para ver la carpeta de salida.

    ![Carpeta de salida creada](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Seleccione `fromonprem` en la lista de carpetas. 
3. Confirme que ve un archivo denominado `dbo.emp.txt`.

    ![Archivo de salida](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en Blob Storage. Ha aprendido a:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Creación de los servicios vinculados SQL Server y Storage. 
> * Creación de conjuntos de datos de SQL Server y Blob Storage.
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de la canalización

Para ver una lista de los almacenes de datos compatibles con Data Factory, consulte los [almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).

Para informarse acerca de cómo copiar datos de forma masiva de un origen a un destino, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy-portal.md)
