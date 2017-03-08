---
title: "Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio | Microsoft Docs"
description: "En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante Visual Studio."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 7196b4eddc8a00cf2c15e8d8447ef8381db738a3
ms.openlocfilehash: a1ef4ccb6546f011c405a98cab3cae514de813ea
ms.lasthandoff: 03/02/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio
> [!div class="op_single_selector"]
> * [Introducción y requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Asistente para copia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Plantilla de Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API DE REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API de .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Este tutorial muestra cómo crear y supervisar una factoría de datos de Azure mediante Visual Studio. La canalización de la factoría de datos utiliza una actividad de copia para copiar datos desde Almacenamiento de blobs de Azure a Base de datos SQL de Azure.

> [!NOTE]
> La canalización de datos de este tutorial copia datos de un almacén de datos de origen a un almacén de datos de destino. No transforma los datos de entrada para generar datos de salida. Para ver un tutorial acerca de cómo transformar datos mediante Azure Data Factory, consulte [Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop](data-factory-build-your-first-pipeline.md).
> 
> Puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Para más información, consulte [Programación y ejecución en Data Factory](data-factory-scheduling-and-execution.md).

Estos son los pasos que se realizan en este tutorial:

1. Cree dos servicios vinculados: **AzureStorageLinkedService1** y **AzureSqlinkedService1**. 
   
    AzureStorageLinkedService1 vincula un almacenamiento de Azure y AzureSqlLinkedService1 vincula una Base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactoryVS**. Los datos de entrada para la canalización se encuentran en un contenedor de blob en el almacenamiento de blobs de Azure y los datos de salida se almacenan en una tabla en la base de datos SQL de Azure. Por lo tanto, agregue estos almacenes de datos como servicios vinculados en la factoría de datos.
2. Cree dos conjuntos de datos: **InputDataset** y **OutputDataset**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos. 
   
    Para InputDataset, especifique el contenedor de blobs que contiene un blob con los datos de origen. Para OutputDataset, especifique la tabla SQL que almacena los datos de salida. Especifique también otras propiedades como estructura, disponibilidad y directiva.
3. Cree una canalización denominada **ADFTutorialPipeline** en ADFTutorialDataFactoryVS. 
   
    La canalización dispondrá de una opción para la **actividad de copia** que copia datos de entrada del blob de Azure en la tabla de salida de Azure SQL. La actividad de copia realiza el movimiento de datos en Data Factory de Azure. La actividad funciona con un servicio disponible de forma global que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más información sobre la actividad de copia. 
4. Crear una instancia de Data Factory denominada **VSTutorialFactory**. Implemente tanto la instancia de Data Factory como todas las entidades de Data Factory (servicios vinculados, tablas y la canalización).    

## <a name="prerequisites"></a>Requisitos previos
1. Lea el artículo [Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) y complete los pasos de los **requisitos previos** . 
2. Debe ser **administrador de la suscripción de Azure** para poder publicar entidades de Data Factory en Data Factory de Azure.  
3. Debe tener lo siguiente instalado en el equipo: 
   * Visual Studio 2013 o Visual Studio 2015.
   * Descargue el SDK de Azure para Visual Studio 2013 o Visual Studio 2015. Vaya a la [página Descargas de Azure](https://azure.microsoft.com/downloads/) y haga clic en **VS 2013** o **VS 2015** en la sección **.NET**.
   * Descargue el último complemento de Azure Data Factory para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). También puede actualizar el complemento, para lo que debe seguir estos pasos: en el menú, haga clic en **Herramientas** -> **Extensiones y actualizaciones** -> **En línea** -> **Galería de Visual Studio** -> **Herramientas de Factoría de datos de Microsoft Azure para Visual Studio** -> **Actualizar**.

## <a name="create-visual-studio-project"></a>Creación de un proyecto de Visual Studio
1. Inicie **Visual Studio 2013**. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Debería ver el cuadro de diálogo **Nuevo proyecto** .  
2. En el cuadro de diálogo **Nuevo proyecto**, seleccione la plantilla **DataFactory** y haga clic en **Proyecto vacío de factoría de datos**. Si no ve la plantilla DataFactory, cierre Visual Studio, instale el SDK de Azure para Visual Studio 2013 y vuelva a abrir Visual Studio.  
   
    ![Cuadro de diálogo Nuevo proyecto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Escriba un **nombre** para el proyecto, la **ubicación** y un nombre para la **solución**; a continuación, haga clic en **Aceptar**.
   
    ![Explorador de soluciones](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Consulte los [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver todos orígenes y receptores que admite la actividad de copia. Consulte los [servicios vinculados de procesos](data-factory-compute-linked-services.md) para ver la lista de servicios de proceso compatibles con Data Factory. En este tutorial no se usan servicios de proceso. 

En este paso, crea dos servicios vinculados: **AzureStorageLinkedService1** y **AzureSqlLinkedService1**. El servicio vinculado AzureStorageLinkedService1 vincula una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos SQL de Azure a la factoría de datos: **ADFTutorialDataFactory**. 

### <a name="create-the-azure-storage-linked-service"></a>Creación del servicio vinculado de Almacenamiento de Azure
1. Haga clic con el botón derecho en **Servicios vinculados** en el Explorador de soluciones, seleccione **Agregar** y haga clic en **Nuevo elemento**.      
2. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Servicios vinculados de Almacenamiento de Azure** en la lista y haga clic en **Agregar**. 
   
    ![Nuevo servicio vinculado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Reemplace `<accountname>` y `<accountkey>`* por el nombre de la cuenta de Azure Storage y su clave. 
   
    ![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Guarde el archivo **AzureStorageLinkedService1.json** .

> Para más información acerca de las propiedades de JSON, consulte [Movimiento de datos hacia y desde Blob de Azure mediante Azure Data Factory](data-factory-azure-blob-connector.md#azure-storage-linked-service) .
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>Cree el servicio vinculado SQL de Azure.
1. Haga doble clic con el botón derecho en el nodo **Servicios vinculados** en el **Explorador de soluciones** de nuevo, apunte a **Agregar** y haga clic en **Nuevo elemento**. 
2. Esta vez, seleccione **Servicios vinculados de SQL Azure** y haga clic en **Agregar**. 
3. En el archivo **AzureSqlLinkedService1.json**, reemplace `<servername>`, `<databasename>`, `<username@servername>` y `<password>` por los nombres del servidor, la base de datos, la cuenta de usuario y la contraseña de SQL Azure.    
4. Guarde el archivo **AzureSqlLinkedService1.json** . 

> [!NOTE]
> Para más información acerca de las propiedades de JSON, consulte [Movimiento de datos hacia y desde Azure SQL Database mediante Azure Data Factory](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) .
> 
> 

## <a name="create-datasets"></a>Creación de conjuntos de datos
En el paso anterior, creó los servicios vinculados **AzureStorageLinkedService1** y **AzureSqlLinkedService1** para vincular una cuenta de Azure Storage y Azure SQL Database con la factoría de datos: **ADFTutorialDataFactory**. En este paso, defina dos conjuntos de datos (**InputDataset** y **OutputDataset**) que representen los datos de entrada y salida que se almacenan en los almacenes de datos a los que hacen referencia AzureStorageLinkedService1 y AzureSqlLinkedService1, respectivamente. Para InputDataset, especifique el contenedor de blobs que contiene un blob con los datos de origen. Para OutputDataset, especifique la tabla SQL que almacena los datos de salida.

### <a name="create-input-dataset"></a>Creación de un conjunto de datos de entrada
En este paso, se crea un conjunto de datos denominado **InputDataset** que apunta a un contenedor de blobs de Azure Storage que está representado por el servicio vinculado **AzureStorageLinkedService1**. Una tabla es un conjunto de datos rectangular y es el único tipo de conjunto de datos que se admite en este momento. 

1. Haga clic con el botón derecho en **Tablas** en el **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**.
2. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Blob de Azure** y haga clic en **Agregar**.   
3. Reemplace el texto JSON con el texto siguiente y guarde el archivo **AzureBlobLocation1.json** . 

  ```json   
  {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
  }
  ``` 
    Tenga en cuenta los siguientes puntos: 
   
   * **type** de conjunto de datos está establecido en **AzureBlob**.
   * **linkedServiceName** está establecido en **AzureStorageLinkedService**. Este servicio vinculado lo creó en el paso 2.
   * **folderPath** está establecido en el contenedor **adftutorial**. También puede especificar el nombre de un blob en la carpeta mediante la propiedad **fileName** . Puesto que no se especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.  
   * **type** de formato está establecido en **TextFormat**.
   * Hay dos campos en el archivo de texto: **FirstName** y **LastName** separados por un carácter de coma (**columnDelimiter**).    
   * **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** está establecido en **1**). Por consiguiente, Data Factory busca los datos de entrada cada hora en la carpeta raíz del contenedor de blobs (**adftutorial**) que se ha especificado. 
   
   Si no especifica un valor de **fileName** para un conjunto de datos de **entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada.
   
   Si no especifica un valor **fileName** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.&lt;Guid&gt;.txt (ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
   
   Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se está procesando) y fileName usa Hour de SliceStart. Por ejemplo, si se produce un segmento para 2016-09-20T08:00:00, folderName se establece en wikidatagateway/wikisampledataout/2016/09/20 y fileName se establece en 08.csv. 
  
    ```json   
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ```
            
> [!NOTE]
> Para más información acerca de las propiedades de JSON, consulte [Movimiento de datos hacia y desde Blob de Azure mediante Azure Data Factory](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) .
> 
> 

### <a name="create-output-dataset"></a>Creación del conjunto de datos de salida
En este paso se crea un conjunto de datos de salida denominado **OutputDataset**. Este conjunto de datos apunta a una tabla SQL de Azure SQL Database representada por **AzureSqlLinkedService1**. 

1. Haga clic con el botón derecho en **Tablas** en el **Explorador de soluciones** de nuevo, seleccione **Agregar** y haga clic en **Nuevo elemento**.
2. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **SQL de Azure** y haga clic en **Agregar**. 
3. Reemplace el texto JSON con el JSON siguiente y guarde el archivo **AzureSqlTableLocation1.json** .

    ```json
    {
     "name": "OutputDataset",
     "properties": {
       "structure": [
         {
           "name": "FirstName",
           "type": "String"
         },
         {
           "name": "LastName",
           "type": "String"
         }
       ],
       "type": "AzureSqlTable",
       "linkedServiceName": "AzureSqlLinkedService1",
       "typeProperties": {
         "tableName": "emp"
       },
       "availability": {
         "frequency": "Hour",
         "interval": 1
       }
     }
    }
    ```
   
    Tenga en cuenta los siguientes puntos: 
   
   * **type** de conjunto de datos está establecido en **AzureSQLTable**.
   * **linkedServiceName** está establecido en **AzureSqlLinkedService** (este servicio vinculado lo creó en el paso 2).
   * **tablename** está establecido en **emp**.
   * En la tabla emp de la base de datos hay tres columnas: **ID**, **FirstName** y **LastName**. ID es una columna de identidad, por lo que deberá especificar solo **FirstName** y **LastName** aquí.
   * **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** está establecido en **1**).  El servicio Data Factory generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.

> [!NOTE]
> Para más información acerca de las propiedades de JSON, consulte [Movimiento de datos hacia y desde Azure SQL Database mediante Azure Data Factory](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) .
> 
> 

## <a name="create-pipeline"></a>Creación de una canalización
Ya ha creado las tablas y los servicios vinculados de entrada/salida. Ahora, va a crear una canalización con una **actividad de copia** para copiar datos del blob de Azure a Base de datos SQL de Azure. 

1. Haga clic con el botón derecho en **Canalizaciones** en el **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**.  
2. Seleccione **Copiar canalización de datos** en el cuadro de diálogo **Agregar nuevo elemento** y haga clic en **Agregar**. 
3. Reemplace el JSON por el siguiente JSON y guarde el archivo **CopyActivity1.json** .

    ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
             }
           ],
           "typeProperties": {
             "source": {
               "type": "BlobSource"
             },
             "sink": {
               "type": "SqlSink",
               "writeBatchSize": 10000,
               "writeBatchTimeout": "60:00:00"
             }
           },
           "Policy": {
             "concurrency": 1,
             "executionPriorityOrder": "NewestFirst",
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2015-07-12T00:00:00Z",
       "end": "2015-07-13T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   Tenga en cuenta los siguientes puntos:
   
   * En la sección de actividades, solo hay una actividad con **type** establecido en **Copy**.
   * La entrada de la actividad está establecida en **InputDataset**, mientras que la salida está establecida en **OutputDataset**.
   * En la sección **typeProperties**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.
   
   Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Puede especificar solo la parte de fecha y omitir la parte de hora de la fecha y hora. Por ejemplo, "03-02-2016", que es equivalente a "03-02-2016T00:00:00Z"
   
   Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2016-10-14T16:32:41Z. La hora de finalización ( **end** ) es opcional, pero se utilizará en este tutorial. 
   
   Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**.
   
   En el ejemplo anterior hay 24 segmentos de datos, ya que cada segmento de datos se produce cada hora.

## <a name="publishdeploy-data-factory-entities"></a>Publicación e implementación de las entidades de Data Factory
En este paso, se publican las entidades de Data Factory (servicios vinculados, conjuntos de datos y canalización) que se crearon anteriormente. También se especifica el nombre de la nueva instancia de Data Factory que se va a crear para almacenar dichas entidades.  

1. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
2. Si ve el cuadro de diálogo **Iniciar sesión en tu cuenta Microsoft**, escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **Iniciar sesión**.
3. Debería ver el siguiente cuadro de diálogo:
   
   ![Cuadro de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. En la página Configure data factory (Configurar Data Factory), siga estos pasos: 
   
   1. Seleccione la opción **Crear la nueva factoría de datos** .
   2. Escriba **VSTutorialFactory** para **Nombre**.  
      
      > [!IMPORTANT]
      > El nombre del generador de datos de Azure debe ser único global. Si recibe un error sobre el nombre de la factoría de datos cuando se publica, cámbielo (por ejemplo, sunombreVSTutorialFactory) e intente publicar de nuevo. Consulte el tema [Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para conocer las reglas de nomenclatura para los artefactos de Data Factory.        
      > 
      > 
   3. Seleccione su suscripción de Azure en el campo **Suscripción** .
      
      > [!IMPORTANT]
      > Si no ve ninguna suscripción, asegúrese de que ha iniciado sesión con una cuenta que sea administrador o coadministrador de la suscripción.  
      > 
      > 
   4. Seleccione el **grupo de recursos** para la factoría de datos que se va a crear. 
   5. Seleccione la **Región** de la factoría de datos. La lista desplegable solo muestra las regiones que admite el servicio Data Factory.
   6. Haga clic en **Siguiente** para cambiar a la página **Publicar elementos**.
      
       ![Configurar página de Data Factory](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. En la página **Publicar elementos**, asegúrese de que todas las factorías de datos están seleccionadas y haga clic en **Siguiente** para cambiar a la página **Resumen**.
   
   ![Página Publicar elementos](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Revise el resumen y haga clic en **Siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
   
   ![Página Publicar resumen](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. En la página **Estado de implementación** , debería ver el estado del proceso de implementación. Cuando se haya completado la implementación, haga clic en Finalizar.
 
   ![Página Estado de la implementación](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Tenga en cuenta los siguientes puntos: 

* Si recibe el error: "**La suscripción no está registrada para usar el espacio de nombres Microsoft.DataFactory**", realice una de las acciones siguientes e intente publicarla de nuevo: 
  
  * En Azure PowerShell, ejecute el siguiente comando para registrar el proveedor de Data Factory. 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Puede ejecutar el comando siguiente para confirmar que se ha registrado el proveedor de Data Factory. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Inicie sesión mediante la suscripción de Azure en el [Portal de Azure](https://portal.azure.com) y vaya a una hoja de Data Factory (o) cree una factoría de datos en el Portal de Azure. Esta acción registra automáticamente el proveedor.
* El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.

> [!IMPORTANT]
> Para crear instancias de Data Factory, es preciso ser administrador o coadministrador de la suscripción de Azure
> 
> 

## <a name="summary"></a>Resumen
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Ha usado Visual Studio para crear la factoría de datos, los servicios vinculados, los conjuntos de datos y una canalización. Estos son los pasos de alto nivel que realizó en este tutorial:  

1. Ha creado una **factoría de datos**de Azure.
2. Ha creado **servicios vinculados**.
   1. Un servicio vinculado **Almacenamiento de Azure** para vincular la cuenta de almacenamiento de Azure que contiene datos de entrada.     
   2. Un servicio vinculado **SQL Azure** para vincular la base de datos SQL de Azure que contiene los datos de salida. 
3. Ha creado **conjuntos de datos**que describen los datos de entrada y salida para las canalizaciones.
4. Ha creado una **canalización** con una **actividad de copia** con un origen **BlobSource** y un receptor **SqlSink**. 

## <a name="use-server-explorer-to-view-data-factories"></a>Uso del Explorador de servidores para ver las entidades de Data Factory
1. En **Visual Studio** haga clic en **Vista** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y **Factoría de datos**. Si aparece **Iniciar sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **Continuar**. Escriba la **contraseña** y haga clic en **Iniciar sesión**. Visual Studio intenta obtener información acerca de todas las factorías de datos de Azure de su suscripción. Puede ver el estado de esta operación en la ventana **Data Factory Task List** (Lista de tareas de Data Factory).

    ![Explorador de servidores](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Puede hacer clic con el botón derecho en una factoría de datos y seleccionar Exportar Factoría de datos al nuevo proyecto para crear un proyecto de Visual Studio basado en una factoría de datos existente.

    ![Exportar factoría de datos a un proyecto de VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Actualización de herramientas de Factoría de datos para Visual Studio
Para actualizar las herramientas de Azure Data Factory para Visual Studio, siga estos pasos:

1. Haga clic en el menú **Herramientas** y seleccione **Extensiones y actualizaciones**. 
2. Seleccione **Actualizaciones** en el panel izquierdo y, luego, seleccione **Galería de Visual Studio**.
3. Seleccione **Herramientas de Factoría de datos de Azure para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

Consulte [Supervisión de conjuntos de datos y canalizaciones](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para obtener instrucciones sobre cómo usar el Portal de Azure para supervisar la canalización y los conjuntos de datos creados en este tutorial.

## <a name="see-also"></a>Otras referencias
| Tema. | Descripción |
|:--- |:--- |
| [Procesos](data-factory-create-pipelines.md) |Este artículo le ayuda a conocer las canalizaciones y actividades de Data Factory de Azure. |
| [Conjuntos de datos](data-factory-create-datasets.md) |Este artículo le ayuda a comprender los conjuntos de datos de Data Factory de Azure. |
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) |En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) |En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la aplicación de supervisión y administración. |


