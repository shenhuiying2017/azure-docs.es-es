---
title: "Tutorial: Creación de una canalización de Azure Data Factory para copiar datos (Azure Portal) | Microsoft Docs"
description: "En este tutorial, usará Azure Portal para crear una canalización de Azure Data Factory con una actividad de copia para copiar datos de una instancia de Azure Blob Storage a una instancia de Azure SQL Database."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 91c9f6d495655dd4ae267125836881d0948c13b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Tutorial: Uso de Azure Portal para crear una canalización de Data Factory para copiar datos 
> [!div class="op_single_selector"]
> * [Introducción y requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Asistente para copia](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Plantilla de Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API DE REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API de .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

En este artículo, aprenderá a usar [Azure Portal](https://portal.azure.com) para crear una factoría de datos con una canalización que copia datos desde Azure Blob Storage a Azure SQL Database. Si no está familiarizado con Azure Data Factory, lea el artículo [Introducción a Azure Data Factory](data-factory-introduction.md) antes de realizar este tutorial.   

En este tutorial, creará una canalización con una actividad en ella: la actividad de copia. La actividad de copia realiza la copia de los datos de un almacén de datos admitido en un almacén de datos receptor. Para obtener una lista de almacenes de datos que se admiten como orígenes y receptores, consulte los [almacenes de datos admitidos](data-factory-data-movement-activities.md#supported-data-stores-and-formats). La actividad funciona con un servicio disponible de forma global que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Para más información acerca de la actividad de copia, consulte las [actividades de movimiento de datos](data-factory-data-movement-activities.md).

pero se puede tener más de una actividad en una canalización. También puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Para más información, consulte [Varias actividades en una canalización](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> La canalización de datos de este tutorial copia datos de un almacén de datos de origen a un almacén de datos de destino. Para ver un tutorial acerca de cómo transformar datos mediante Azure Data Factory, consulte [Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Requisitos previos
Antes de realizar este tutorial, complete los requisitos previos que se enumeran en el artículo [Requisitos previos del tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="steps"></a>Pasos
Estos son los pasos que se realizan en este tutorial:

1. Cree una **factoría de datos** de Azure. En este paso, creará una factoría de datos llamada ADFTutorialDataFactory. 
2. Cree **servicios vinculados** en la factoría de datos. En este paso, se crean dos servicios vinculados del tipo Azure Storage y Azure SQL Database. 
    
    AzureStorageLinkedService vincula una cuenta de Azure Storage a la factoría de datos. Como parte de los [requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), se creó un contenedor y se cargaron datos en esta cuenta de almacenamiento.   

    AzureSqlLinkedService vincula la base de datos SQL de Azure con la factoría de datos. Los datos que se copian desde Blob Storage se almacenan en esta base de datos. Como parte de los [requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), se creó una tabla SQL en esta base de datos.   
3. Cree **conjuntos de datos** de entrada y salida en la factoría de datos.  
    
    El servicio vinculado Azure Storage especifica la cadena de conexión que el servicio Data Factory utiliza en tiempo de ejecución para conectarse a su cuenta de Azure Storage. Además, el conjunto de datos de blobs de entrada especifica el contenedor y la carpeta que contiene los datos de entrada.  

    De forma similar, el servicio vinculado Azure SQL Database especifica la cadena de conexión que el servicio Data Factory utiliza en tiempo de ejecución para conectarse a Azure SQL Database. Además, el conjunto de datos de la tabla SQL de salida especifica la tabla de la base de datos en la que se copian los datos de Blob Storage.
4. Cree una **canalización** en la factoría de datos. En este paso, se crea una canalización con una actividad de copia.   
    
    Esta actividad copia los datos desde un blob de Azure Blob Storage a una tabla de la base de datos SQL de Azure. Puede utilizar una actividad de copia en una canalización para copiar datos desde cualquier origen admitido a cualquier destino admitido. Para ver una lista de los almacenes de datos admitidos, consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Supervise la canalización. En este paso, **supervisará** los segmentos de los conjuntos de datos de entrada y salida con Azure Portal. 

## <a name="create-data-factory"></a>Creación de Data Factory
> [!IMPORTANT]
> Complete los [requisitos previos del tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) si aún no lo ha hecho.   

Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos desde un origen a un almacén de datos de destino o una actividad de Hive de HDInsight para ejecutar un script de Hive que transforme los datos de entrada para generar datos de salida. Comencemos con la creación de la factoría de datos en este paso.

1. Después de iniciar sesión en [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** en el menú izquierdo, haga clic en **Datos y análisis**, y haga clic en **Factoría de datos**. 
   
   ![New->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. En la hoja **Nueva factoría de datos** :
   
   1. Escriba **ADFTutorialDataFactory** como **nombre**. 
      
         ![Hoja Nueva Factoría de datos](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       El nombre de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el tema [Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para conocer las reglas de nomenclatura para los artefactos de Data Factory.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Nombre de Factoría de datos no disponible](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
   3. Para el **grupo de recursos**, realice uno de los siguientes pasos:
      
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
          En algunos de los pasos de este tutorial se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../../azure-resource-manager/resource-group-overview.md).  
   4. Seleccione la **ubicación** de Data Factory. La lista desplegable solo muestra las regiones que admite el servicio Data Factory.
   5. Seleccione **Anclar al panel**.     
   6. Haga clic en **Crear**.
      
      > [!IMPORTANT]
      > Para crear instancias de Data Factory, es preciso ser miembro del rol [Colaborador de Data Factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) en el nivel de grupo de recursos o suscripción.
      > 
      > El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.                
      > 
      > 
3. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Una vez completada la creación, puede ver la hoja **Data Factory** , tal como se muestra en la imagen.
   
   ![Página principal de Factoría de datos](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En este tutorial, no se usa ningún servicio de proceso, como Azure HDInsight o Azure Data Lake Analytics. Se usan dos almacenes de datos del tipo Azure Storage (origen) y Azure SQL Database (destino). 

Por lo tanto, se crean dos servicios vinculados llamados AzureStorageLinkedService y AzureSqlLinkedService del tipo AzureStorage y AzureSqlDatabase.  

AzureStorageLinkedService vincula una cuenta de Azure Storage a la factoría de datos. Esta cuenta de almacenamiento es la que se usó para crear un contenedor y con la que se cargaron los datos como parte de los [requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

AzureSqlLinkedService vincula la base de datos SQL de Azure con la factoría de datos. Los datos que se copian desde Blob Storage se almacenan en esta base de datos. Como parte de los [requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), se creó la tabla emp en esta base de datos.  

### <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Azure Storage con su factoría de datos. Especifique el nombre y la clave de la cuenta de almacenamiento de Azure en esta sección.  

1. En la hoja **Factoría de datos**, haga clic en el icono **Crear e implementar**.
   
   ![Mosaico Crear e implementar](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Verá el **Editor de factorías de datos**, tal y como se muestra en la siguiente imagen: 

    ![Editor de la Factoría de datos](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. En el editor, haga clic en el botón **Nuevo almacén de datos**, en la barra de herramientas, y seleccione **Azure Storage** en el menú desplegable. Debería ver la plantilla JSON para crear un servicio vinculado de almacenamiento de Azure en el panel derecho. 
   
    ![Botón Nuevo almacén de datos del Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Reemplace `<accountname>` y `<accountkey>` por los valores de clave y nombre de la cuenta de Azure Storage. 
   
    ![JSON de almacenamiento de blobs del Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Haga clic en **Implementar** en la barra de herramientas. Debería ver que **AzureStorageLinkedService** está implementado en la vista de árbol. 
   
    ![Implementar almacenamiento de blobs del editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Para más información sobre las propiedades JSON en la definición de servicio vinculado, vea el artículo [Conector de Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Crear un servicio vinculado para la base de datos SQL de Azure
En este paso, vinculará su cuenta de Base de datos SQL de Azure con su factoría de datos. Especifique el nombre del servidor Azure SQL, nombre de base de datos, nombre de usuario y contraseña del usuario en esta sección. 

1. En **Data Factory Editor**, haga clic en el botón **Nuevo almacén de datos** de la barra de herramientas y seleccione **Azure SQL Database** en el menú desplegable. Verá la plantilla JSON para crear un servicio vinculado SQL de Azure en el panel derecho.
2. Reemplace `<servername>`, `<databasename>`, `<username>@<servername>` y `<password>` por los nombres del servidor, la base de datos, la cuenta de usuario y la contraseña de SQL Azure. 
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar **AzureSqlLinkedService**.
4. Confirme que **AzureSqlLinkedService** aparece en la vista de árbol, en **Servicios vinculados**.  

    Para más información acerca de estas propiedades JSON, consulte [Conector de Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Creación de conjuntos de datos
En el paso anterior, creó servicios vinculados para vincular una cuenta de Azure Storage y una base de datos SQL de Azure con la factoría de datos. En este paso, defina dos conjuntos de datos llamados InputDataset y OutputDataset, que representan los datos de entrada y salida que se almacenan en los almacenes de datos a los que hacen referencia AzureStorageLinkedService y AzureSqlLinkedService, respectivamente.

El servicio vinculado Azure Storage especifica la cadena de conexión que el servicio Data Factory utiliza en tiempo de ejecución para conectarse a su cuenta de Azure Storage. Además, el conjunto de datos de blobs de entrada (InputDataset) especifica el contenedor y la carpeta que contiene los datos de entrada.  

De forma similar, el servicio vinculado Azure SQL Database especifica la cadena de conexión que el servicio Data Factory utiliza en tiempo de ejecución para conectarse a Azure SQL Database. Además, el conjunto de datos de la tabla SQL de salida (OutputDataset) especifica la tabla de la base de datos en la que se copian los datos de Blob Storage. 

### <a name="create-input-dataset"></a>Creación de un conjunto de datos de entrada
En este paso, se crea un conjunto de datos llamado InputDataset, que apunta a un archivo de blobs (emp.txt) en la carpeta raíz de un contenedor de blobs (adftutorial), en la instancia de Azure Storage representada por el servicio vinculado AzureStorageLinkedService. Si no especifica un valor para fileName (o puede omitirlo), los datos de todos los blobs en la carpeta de entrada se copian en el destino. En este tutorial, especifique un valor para fileName. 

1. En el **Editor** en la instancia de Data Factory, haga clic en **... Más**, luego en **Nuevo conjunto de datos** y, finalmente, en **Azure Blob Storage** en el menú desplegable. 
   
    ![Menú Conjunto de datos nuevo](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Reemplace JSON en el panel derecho por el siguiente fragmento JSON: 
   
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
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
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

    En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

    | Propiedad | Descripción |
    |:--- |:--- |
    | type | La propiedad type se establece en **AzureBlob** porque los datos residen en una instancia de Azure Blob Storage. |
    | linkedServiceName | Hace referencia al servicio **AzureStorageLinkedService** que creó anteriormente. |
    | folderPath | Especifica el **contenedor** de blobs y la **carpeta** que contiene los blobs de entrada. En este tutorial, adftutorial es el contenedor de blobs y folder es la carpeta raíz. | 
    | fileName | Esta propiedad es opcional. Si omite esta propiedad, se seleccionan todos los archivos de folderPath. En este tutorial, se especifica **emp.txt** en fileName, por lo que solo se selecciona ese archivo para su procesamiento. |
    | formato -> tipo |El archivo de entrada tiene formato de texto, por lo que usamos **TextFormat**. |
    | columnDelimiter | Las columnas del archivo de entrada están delimitadas por **coma (`,`)**. |
    | frecuencia/intervalo | La frecuencia está establecida en **Hour** y el intervalo es **1**, lo que significa que los segmentos de entrada estarán disponibles **cada hora**. En otras palabras, el servicio Data Factory busca los datos de entrada cada hora en la carpeta raíz del contenedor de blobs (**adftutorial**) que se ha especificado. Busca los datos entre las horas de inicio y finalización de la canalización, no antes ni después de esas horas.  |
    | external | Esta propiedad se establece en **true** si esta canalización no ha generado los datos. Los datos de entrada de este tutorial están en el archivo emp.txt, que no lo generó esta canalización, por lo que establecemos esta propiedad en true. |

    Para más información acerca de estas propiedades JSON, consulte el artículo sobre el [conector de Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).      
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar el conjunto de datos **InputDataset**. Confirme que **InputDataset** aparece en la vista de árbol.

### <a name="create-output-dataset"></a>Creación del conjunto de datos de salida
El servicio vinculado Azure SQL Database especifica la cadena de conexión que el servicio Data Factory usa en tiempo de ejecución para conectarse a su instancia de Azure SQL Database. El conjunto de datos de la tabla SQL de salida (OutputDataset) que crea en este paso especifica la tabla de la base de datos en la que se copian los datos de Blob Storage.

1. En el **Editor** en la instancia de Data Factory, haga clic en **... Más**, luego en **Nuevo conjunto de datos** y, finalmente, en **Azure SQL** en el menú desplegable. 
2. Reemplace JSON en el panel derecho por el siguiente fragmento JSON:

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
        "linkedServiceName": "AzureSqlLinkedService",
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

    En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

    | Propiedad | Descripción |
    |:--- |:--- |
    | type | La propiedad type se establece en **AzureSqlTable** porque los datos se copian en una tabla de una base de datos de Azure SQL Database. |
    | linkedServiceName | Hace referencia al servicio **AzureSqlLinkedService** que creó anteriormente. |
    | tableName | Especifica la **tabla** en la que se copian los datos. | 
    | frecuencia/intervalo | La frecuencia se establece en **Hour** y el intervalo es **1**, lo que significa que los sectores de salida se producen **cada hora** entre las horas de inicio y finalización de la canalización, no antes ni después de esas horas.  |

    En la tabla emp de la base de datos hay tres columnas: **ID**, **FirstName** y **LastName**. ID es una columna de identidad, por lo que deberá especificar solo **FirstName** y **LastName** aquí.

    Para más información acerca de estas propiedades JSON, consulte el artículo sobre el [conector de Azure SQL Database](data-factory-azure-sql-connector.md#dataset-properties).
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar el conjunto de datos **OutputDataset**. Confirme que **OutputDataset** aparece en la vista de árbol, en **Conjuntos de datos**. 

## <a name="create-pipeline"></a>Creación de una canalización
En este paso, creará una canalización con una **actividad de copia** que utiliza **InputDataset** como entrada y **OutputDataset** como salida.

Actualmente, el conjunto de datos de salida es lo que impulsa la programación. En este tutorial, el conjunto de datos de salida está configurado para generar un segmento una vez cada hora. La canalización tiene una hora de inicio y una hora de finalización con un día de diferencia, es decir, 24 horas. Por lo tanto, la canalización produce 24 segmentos del conjunto de datos de salida. 

1. En el **Editor** en la instancia de Data Factory, haga clic en **... Más** y, luego, en **Nueva canalización**. O bien, haga clic con el botón derecho **Canalizaciones** en la vista de árbol y elija **Nueva canalización**.
2. Reemplace JSON en el panel derecho por el siguiente fragmento JSON: 

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Tenga en cuenta los siguientes puntos:
   
    - En la sección de actividades, solo hay una actividad con **type** establecido en **Copy**. Para más información acerca de la actividad de copia, consulte las [actividades de movimiento de datos](data-factory-data-movement-activities.md). En las soluciones de Data Factory, también puede usar [actividades de transformación de datos](data-factory-data-transformation-activities.md).
    - La entrada de la actividad está establecida en **InputDataset**, mientras que la salida está establecida en **OutputDataset**. 
    - En la sección **typeProperties**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor. Consulte la lista de [almacenes de datos que se admiten](data-factory-data-movement-activities.md#supported-data-stores-and-formats) como orígenes y receptores de la actividad de copia. Para más información sobre cómo usar un almacén de datos admitido específico como receptor de origen, haga clic en el vínculo en la tabla.
    - Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2016-10-14T16:32:41Z. La hora de finalización ( **end** ) es opcional, pero se utilizará en este tutorial. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**.
     
    En el ejemplo anterior hay 24 segmentos de datos, ya que cada segmento de datos se produce cada hora.

    Para obtener descripciones de las propiedades JSON en una definición de canalización, consulte cómo [crear canalizaciones](data-factory-create-pipelines.md). Para obtener descripciones de las propiedades JSON en una definición de actividad de copia, consulte las [actividades de movimiento de datos](data-factory-data-movement-activities.md). Para ver las descripciones de las propiedades JSON admitidas por BlobSource, consulte el artículo sobre el [conector de Azure Blob](data-factory-azure-blob-connector.md). Para ver las descripciones de las propiedades JSON admitidas por SqlSink, consulte el artículo sobre el [conector de Azure SQL Database](data-factory-azure-sql-connector.md).
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar **ADFTutorialPipeline**. Confirme que la canalización aparece en la vista de árbol. 
4. Ahora, para cerrar la hoja **Editor**, haga clic en la **X**. Vuelva a hacer clic en la **X** para ver la página principal de **Data Factory** de **ADFTutorialDataFactory**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, con una canalización para copiar datos desde Azure Blob Storage a Azure SQL Database. 


## <a name="monitor-pipeline"></a>Supervisión de la canalización
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Supervisión de una canalización con la Aplicación de supervisión y administración
Los pasos siguientes muestran cómo supervisar las canalizaciones de la factoría de datos mediante la aplicación de supervisión y administración: 

1. Haga clic en el icono **Supervisión y administración** en la página principal de Data Factory.
   
    ![Icono Supervisión y administración](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Verá la opción **Aplicación de supervisión y administración** en una pestaña aparte. 

    > [!NOTE]
    > Si ve que el explorador web está bloqueado en "Autorizando...", desactive la casilla **Bloquear cookies y datos de sitios de terceros**, o cree una excepción para **login.microsoftonline.com** e intente volver a abrir la aplicación.

    ![Aplicación de supervisión y administración](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Cambie la **hora de inicio** y la **hora de finalización** para que incluyan las horas de inicio (11-05-2017) y de finalización (12-05-2017) de la canalización y haga clic en **Aplicar**.       
3. En la lista, en el panel central, verá las **ventanas de actividad** asociadas con cada hora entre la hora de inicio y de finalización de la canalización. 
4. Seleccione una ventana en la lista de **ventanas de actividad** para ver información sobre ella. 
    ![Detalles de ventana de actividad](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    En el Explorador de ventanas de actividad, a la derecha, verá que todos los segmentos hasta la hora UTC actual (8:12 PM) están procesados (en color verde). Los segmentos 8-9 PM, 9-10 PM, 10-11 PM, 11 PM-12 AM no están procesados aún.

    La sección **Intentos** del panel derecho proporciona información sobre la actividad ejecutada para el segmento de datos. Si se produjo un error, proporciona detalles sobre el mismo. Por ejemplo, si la carpeta de entrada o el contenedor no existen y se produce un error al procesar el segmento, verá un mensaje de error que indica que el contenedor o la carpeta no existen.

    ![Intentos de ejecución de la actividad](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Inicie **SQL Server Management Studio**, conéctese a Azure SQL Database y compruebe que las filas se han insertado en la tabla **emp** de la base de datos.
    
    ![resultados de la consulta SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Para más información acerca del uso de esta aplicación, consulte [Supervisión y administración de canalizaciones de Azure Data Factory mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md).

### <a name="monitor-pipeline-using-diagram-view"></a>Supervisión de una canalización desde la vista de diagrama
También puede supervisar las canalizaciones de datos mediante la vista de diagrama.  

1. En la hoja **Data Factory**, haga clic en **Diagrama**.
   
    ![Módulo de la factoría de datos: ventana de diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Debería ver un diagrama similar a la siguiente imagen: 
   
    ![Vista de diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. En la vista de diagrama, haga doble clic en **InputDataset** para ver los segmentos del conjunto de datos.  
   
    ![Conjuntos de datos con InputDataset seleccionado](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Haga clic en el vínculo **Ver más** para ver todos los segmentos de datos. Verá 24 segmentos de una hora entre las horas de inicio y finalización de la canalización. 
   
    ![Todos los segmentos de datos de entrada](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Observe que todos los segmentos de datos hasta la hora actual tienen el estado **Ready**, porque el archivo **emp.txt** existe todo el tiempo en el contenedor de blobs: **adftutorial\input**. Los segmentos de los tiempos futuros aún no tienen un estado Listo. Confirme que no aparecen segmentos en la sección **Segmentos que han fallado recientemente** de la parte inferior.
6. Cierre las hojas hasta que se muestre la vista de diagrama, o desplácese a la izquierda para ver la vista de diagrama. A continuación, haga doble clic en **OutputDataset**. 
8. Haga clic en el vínculo **Ver más**, en la hoja **Tabla** de **OutputDataset**, para ver todos los segmentos.

    ![hoja de segmentos de datos](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Observe que todos los segmentos hasta la hora UTC actual cambian del estado **Pendiente de ejecución** = > **En curso** ==> estado **Listo**. Los segmentos del pasado (antes de la hora actual) se procesan del más reciente al más antiguo, de forma predeterminada. Por ejemplo, si la hora actual es 8:12 PM UTC, el segmento 7 PM - 8 PM se procesa antes que el segmento 6 PM - 7 PM. El segmento 8 PM - 9 PM se procesa al final del intervalo de tiempo de forma predeterminada, que es después de las 9 PM.  
10. Haga clic en cualquier segmento de datos de la lista y debería ver la hoja **Segmento de datos** . Los elementos de datos asociados a una ventana de actividad se llaman segmentos. Un segmento puede ser uno o varios archivos.  
    
     ![hoja de segmento de datos](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.
11. En la hoja **SEGMENTO DE DATOS** , verá todas las ejecuciones de actividades de la lista en la parte inferior. Haga clic en una **ejecución de actividad** para ver la hoja **Detalles de la ejecución de actividad**. 
    
    ![Detalles de ejecución de actividad](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    En esta hoja, puede ver cuánto tiempo tardó la operación de copia, cuál es su rendimiento, cuántos bytes de datos se leyeron y escribieron, las horas de inicio y finalización de la ejecución, etc.  
12. Haga clic en la **X** para cerrar todas las hojas hasta que regrese a la hoja inicial de **ADFTutorialDataFactory**.
13. (Opcional) Haga clic en el icono **Conjuntos de datos** o en el icono **Canalizaciones** para obtener las hojas que ha visto en los pasos anteriores. 
14. Inicie **SQL Server Management Studio**, conéctese a Azure SQL Database y compruebe que las filas se han insertado en la tabla **emp** de la base de datos.
    
    ![resultados de la consulta SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Resumen
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Ha usado el Portal de Azure para crear la factoría de datos, los servicios vinculados, los conjuntos de datos y una canalización. Estos son los pasos de alto nivel que realizó en este tutorial:  

1. Ha creado una **factoría de datos**de Azure.
2. Ha creado **servicios vinculados**.
   1. Un servicio vinculado **Almacenamiento de Azure** para vincular la cuenta de almacenamiento de Azure que contiene datos de entrada.     
   2. Un servicio vinculado **SQL Azure** para vincular la base de datos SQL de Azure que contiene los datos de salida. 
3. Ha creado **conjuntos de datos** que describen los datos de entrada y salida de las canalizaciones.
4. Ha creado una **canalización** con una **actividad de copia** con un origen **BlobSource** y un receptor **SqlSink**.  

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha usado Azure Blob Storage como almacén de datos de origen y una base de datos SQL de Azure como almacén de datos de destino en una operación de copia. En la tabla siguiente se proporciona una lista de almacenes de datos que se admiten como orígenes y destinos de la actividad de copia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para aprender a copiar datos hacia y desde un almacén de datos, haga clic en el vínculo del almacén de datos en la tabla.