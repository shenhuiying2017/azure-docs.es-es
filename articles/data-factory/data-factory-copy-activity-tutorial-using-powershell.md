---
title: "Tutorial: Creación de una canalización con la actividad de copia para mover datos con Azure PowerShell | Microsoft Docs"
description: "En este tutorial, creará una canalización de Azure Data Factory con una actividad de copia mediante Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8b5cb66ea958cf6643fa34abb8d484b97b212373
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Tutorial: Creación de una canalización de Data Factory para mover datos mediante Azure PowerShell
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

En este tutorial, creará y supervisará una instancia de Azure Data Factory con cmdlets de Azure PowerShell. La canalización en la factoría de datos que cree en este tutorial usa una actividad de copia para copiar los datos de un blob de Azure a una base de datos SQL de Azure.

La función de actividad de copia realiza el movimiento de datos en Data Factory. La actividad funciona con un servicio disponible de forma global que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Consulte [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para más información sobre la actividad de copia.   

> [!NOTE]
> Este artículo no abarca todos los cmdlets de Factoría de datos. Vea [Referencia de cmdlets de Data Factory](/powershell/module/azurerm.datafactories) para obtener la documentación completa sobre estos cmdlets.
>
> La canalización de datos de este tutorial copia datos de un almacén de datos de origen a un almacén de datos de destino. No transforma los datos de entrada para generar datos de salida. Para ver un tutorial acerca de cómo transformar datos mediante Azure Data Factory, consulte [Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Requisitos previos
- Para obtener información general del tutorial y completar los pasos de los [requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , consulte **Copia de datos de Almacenamiento de blobs en Base de datos SQL mediante Data Factory** .
- Instale Azure PowerShell. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

## <a name="in-this-tutorial"></a>Apartados de este tutorial
En la tabla siguiente se enumeran los pasos que se llevarán a cabo como parte de este tutorial.

| Paso | Descripción |
| --- | --- |
| [Crear una factoría de datos de Azure](#create-data-factory) |En este paso, creará una factoría de datos de Azure denominada **ADFTutorialDataFactoryPSH**. |
| [Crear servicios vinculados](#create-linked-services) |En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. StorageLinkedService vincula el servicio Azure Storage y AzureSqlLinkedService vincula una base de datos SQL de Azure a ADFTutorialDataFactoryPSH. |
| [Crear conjuntos de datos de entrada y salida](#create-datasets) |En este paso, se definen dos conjuntos de datos (EmpTableFromBlob y EmpSQLTable). Estos conjuntos de datos se usan como tablas de entrada y salida de la **actividad de copia** en la canalización ADFTutorialPipeline que creará en el siguiente paso. |
| [Creación y ejecución de una canalización](#create-pipeline) |En este paso, creará una canalización denominada **ADFTutorialPipeline** en la factoría de datos ADFTutorialDataFactoryPSH. La canalización utiliza una actividad de copia para copiar datos de un blob de Azure en una tabla de base de datos de Azure de salida. |
| [Supervisión de conjuntos de datos y canalización](#monitor-pipeline) |En este paso, supervisará los conjuntos de datos y la canalización con Azure PowerShell. |

## <a name="create-a-data-factory"></a>Crear una factoría de datos
En este paso, use Azure PowerShell para crear una instancia de Azure Data Factory Azure llamada **ADFTutorialDataFactoryPSH**.

1. Inicie **PowerShell**. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

    Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña que utiliza para iniciar sesión en Azure Portal:

    ```PowerShell
    Login-AzureRmAccount
    ```   
   
    Ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```PowerShell
    Get-AzureRmSubscription
    ```

    Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **&lt;NameOfAzureSubscription**&gt; por el nombre de su suscripción de Azure:

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
2. Cree un grupo de recursos de Azure con el nombre: **ADFTutorialResourceGroup** mediante la ejecución del siguiente comando:

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado **ADFTutorialResourceGroup**. Si usa un otro grupo de recursos, deberá usarlo en lugar de ADFTutorialResourceGroup en este tutorial.
3. Ejecute el cmdlet **New-AzureRmDataFactory** para crear una instancia de Data Factory denominada **ADFTutorialDataFactoryPSH**:  

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
Tenga en cuenta los siguientes puntos:

* El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre (por ejemplo, yournameADFTutorialDataFactoryPSH). Use este nombre en lugar de ADFTutorialFactoryPSH mientras lleva a cabo los pasos de este tutorial. Consulte [Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Data Factory.

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Para crear instancias de Data Factory, debe ser administrador o colaborador en la suscripción de Azure.
* El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, que sea visible públicamente.
* Es posible que reciba el siguiente error: "**La suscripción no está registrada para usar el espacio de nombres Microsoft.DataFactory**". Realice una de las siguientes acciones e intente publicar de nuevo:

  * En Azure PowerShell, ejecute el siguiente comando para registrar el proveedor de Data Factory:

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Ejecute el comando siguiente para confirmar que se ha registrado el proveedor de Data Factory:

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Inicie sesión en [Azure Portal](https://portal.azure.com) mediante la suscripción de Azure. Vaya a una hoja de Data Factory o cree una instancia de Data Factory en Azure Portal. Esta acción registra automáticamente el proveedor.

## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un servicio Azure Storage, una instancia de Azure SQL Database o una base de datos de SQL Server local que contenga datos de entrada o almacene datos de salida de una canalización de Data Factory. Un servicio de proceso es el servicio que procesa datos de entrada y genera datos de salida.

En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. StorageLinkedService vincula una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos SQL de Azure a la factoría de datos **ADFTutorialDataFactoryPSH**. Más adelante en este tutorial creará una canalización que copia datos de un contenedor de blobs de StorageLinkedService en una tabla SQL de AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Creación de un servicio vinculado para una cuenta de almacenamiento de Azure
1. Cree un archivo JSON con el nombre **StorageLinkedService.json** en la carpeta **C:\ADFGetStartedPSH** con el siguiente contenido (si todavía no existe, cree la carpeta ADFGetStartedPSH).

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ```
   Reemplace **accountname** y **accountkey** por el nombre y la clave de su cuenta de almacenamiento de Azure.
2. En **Azure PowerShell**, cambie a la carpeta **ADFGetStartedPSH**.
3. Use el cmdlet **New-AzureRmDataFactoryLinkedService** para crear un servicio vinculado. Tanto este cmdlet como otros cmdlets de Data Factory que usa en este tutorial requieren que se pasen los valores de los parámetros **ResourceGroupName** y **DataFactoryName**. Como alternativa, puede usar **Get-AzureRmDataFactory** para obtener un objeto DataFactory y pasarlo sin necesidad de escribir ResourceGroupName y DataFactoryName cada vez que ejecuta un cmdlet. Ejecute el comando siguiente para asignar el resultado del cmdlet **Get-AzureRmDataFactory** a una variable: **$df**:

    ```PowerShell   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. Ahora, ejecute el cmdlet **New-AzureRmDataFactoryLinkedService** para crear el servicio vinculado: **StorageLinkedService**.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    Si no hubiera ejecutado el cmdlet **Get-AzureRmDataFactory** y asignado la salida a la variable **$df**, tendría que especificar valores para los parámetros ResourceGroupName y DataFactoryName de la siguiente forma.   

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

Si cierra Azure PowerShell durante el tutorial, tendrá que ejecutar el cmdlet Get-AzureRmDataFactory la próxima vez que ejecute Azure PowerShell para completar el tutorial.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Creación de un servicio vinculado para una instancia de Azure SQL Database
1. Cree un archivo JSON denominado AzureSqlLinkedService.json con el siguiente contenido:

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
   Reemplace **servername**, **databasename**, **username@servername** y **password** por los nombres del servidor SQL Azure, de la base de datos, de la cuenta de usuario y de la contraseña.
2. Ejecute el siguiente comando para crear un servicio vinculado:

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   Confirme que la configuración **Permitir el acceso a los servicios de Azure** esté activada para el servidor de la base de datos SQL. Para comprobarla y activarla, siga estos pasos:

   1. Haga clic en el concentrador **EXAMINAR** a la izquierda y haga clic en **Servidores SQL Server**.
   2. Seleccione un servidor y haga clic en **CONFIGURACIÓN** en la hoja **SQL SERVER**.
   3. En la hoja **CONFIGURACIÓN**, haga clic en **Firewall**.
   4. En la hoja **Configuración de firewall**, haga clic en **ACTIVADA** para **Permitir el acceso a los servicios de Azure**.
   5. Haga clic en el concentrador **ACTIVO** a la izquierda para cambiar a la hoja **Factoría de datos** en la que está.

## <a name="create-datasets"></a>Creación de conjuntos de datos
En el paso anterior, creó servicios para vincular una cuenta de almacenamiento de Azure y una base de datos SQL de Azure a la factoría de datos. En este paso, creará conjuntos de datos que representan los datos de entrada y salida de la actividad de copia en la canalización que va a crear en el paso siguiente.

Una tabla es un conjunto de datos rectangular. Es el único tipo de conjunto de datos admitido en este momento. La tabla de entrada de este tutorial hace referencia a un contenedor de blobs de Azure Storage. La tabla de salida hace referencia a una tabla SQL de la base de datos SQL de Azure.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Preparación de Azure Blob Storage y Azure SQL Database para el tutorial
Omita este paso si ha realizado el tutorial de [Copia de datos de Blob Storage a SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Realice los siguientes pasos para preparar Blob Storage y SQL Database para este tutorial.

1. Cree un contenedor de blobs con el nombre **adftutorial** en la instancia de Blob Storage a la que señala **StorageLinkedService**.
2. Cree y cargue un archivo de texto, llamado **emp.txt**, como un blob para el contenedor **adftutorial**.
3. Cree una tabla llamada **emp** en SQL Database a la que apunta **AzureSqlLinkedService**.

4. Inicie el Bloc de notas. Pegue el texto siguiente y guárdelo como **emp.txt** en la carpeta **C:\ADFGetStartedPSH** de su disco duro.

    ```
    John, Doe
    Jane, Doe
    ```
5. Use herramientas como el [Explorador de Azure Storage](https://azurestorageexplorer.codeplex.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

    ![Explorador de Azure Storage](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. Use el siguiente script de SQL para crear la tabla **emp** en la base de datos SQL.  

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    Si tiene SQL Server 2014 instalado en el equipo, siga las instrucciones de [Paso 2: Conexión con la base de datos SQL de la base de datos de administración de SQL de Azure con SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) para conectarse al servidor de base de datos SQL y ejecutar el script de SQL.

    Si el cliente no tiene permiso para acceder al servidor de base de datos SQL, tendrá que configurar el firewall de su servidor de base de datos SQL para permitir el acceso desde su máquina (dirección IP). Para conocer los pasos, consulte [este artículo](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-an-input-dataset"></a>Creación de un conjunto de datos de entrada
Una tabla es un conjunto de datos rectangular y tiene un esquema. En este paso, creará una tabla, denominada **EmpBlobTable**. Esta tabla apunta a un contenedor de blobs en Azure Storage, representado por el servicio vinculado **StorageLinkedService**. Este contenedor de blobs (adftutorial) contiene los datos de entrada en el archivo **emp.txt**.

1. Cree un archivo JSON llamado **EmpBlobTable.json** en la carpeta **C:\ADFGetStartedPSH** con el siguiente contenido:

    ```json
    {
        "name": "EmpTableFromBlob",
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
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
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
   * **linkedServiceName** está establecido en **StorageLinkedService**.
   * **folderPath** está establecido en el contenedor **adftutorial**.
   * **fileName** se establece en **emp.txt**. Si no especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.  
   * **type** de formato está establecido en **TextFormat**.
   * Hay dos campos en el archivo de texto: **FirstName** y **LastName** separados por un carácter de coma (columnDelimiter).    
   * **availability** está establecido en **hourly** (frequency está establecido en hour e interval está establecido en 1). Por consiguiente, Data Factory busca los datos de entrada cada hora en la carpeta raíz del contenedor de blobs (adftutorial).

   Si no especifica un valor de **fileName** para una **tabla de entrada**, todos los archivos y blobs de la carpeta de entrada (folderPath) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada.

   Si no especifica un valor **fileName** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid\>.txt (ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se está procesando) y fileName usa Hour de SliceStart. Por ejemplo, si se produce un segmento para 2016-10-20T08:00:00, folderName se establece en wikidatagateway/wikisampledataout/2016/10/20 y fileName se establece en 08.csv.

    ```json
     "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
     "fileName": "{Hour}.csv",
     "partitionedBy":
     [
         { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
         { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
         { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
         { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
     ],
    ```

   Para más información sobre las propiedades JSON, consulte [Referencia de scripting JSON](data-factory-data-movement-activities.md).
2. Ejecute el comando siguiente para crear el conjunto de datos de Factoría de datos.

    ```PowerShell  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida
En este paso se crea un conjunto de datos de salida denominado **EmpSQLTable**. Este conjunto de datos apunta a una tabla SQL (emp) de Azure SQL Database representada por **AzureSqlLinkedService**. La canalización copia los datos del blob de entrada a la tabla **emp** .

1. Cree un archivo JSON denominado **EmpSQLTable.json** en la carpeta **C:\ADFGetStartedPSH** con el siguiente contenido:

    ```json
    {
        "name": "EmpSQLTable",
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

   Tenga en cuenta los siguientes puntos:

   * **type** de conjunto de datos está establecido en **AzureSqlTable**.
   * **linkedServiceName** está establecido en **AzureSqlLinkedService**.
   * **tablename** está establecido en **emp**.
   * En la tabla emp de la base de datos hay tres columnas: **ID**, **FirstName** y **LastName**. ID es una columna de identidad, por lo que deberá especificar solo **FirstName** y **LastName** aquí.
   * **availability** está establecido en **hourly** (frequency está establecido en hour e interval está establecido en 1). El servicio Data Factory generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.
2. Ejecute el comando siguiente para crear el conjunto de datos de factoría de datos.

    ```PowerShell   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>Crear una canalización
En este paso, crea una canalización con **actividad de copia**. La canalización usa **EmpTableFromBlob** como entrada y **EmpSQLTable** como salida.

1. Cree un archivo JSON llamado **ADFTutorialPipeline.json** en la carpeta **C:\ADFGetStartedPSH** con el siguiente contenido:

    ```json
    {
        "name": "ADFTutorialPipeline",
        "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
                {
                    "name": "CopyFromBlobToSQL",
                    "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                    "type": "Copy",
                    "inputs": [{ "name": "EmpTableFromBlob" }],
                    "outputs": [{ "name": "EmpSQLTable" }],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "SqlSink"
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
            "start": "2016-08-09T00:00:00Z",
            "end": "2016-08-10T00:00:00Z",
            "isPaused": false
        }
     }
    ```
   Tenga en cuenta los siguientes puntos:

   * En la sección de actividades, solo hay una actividad con **type** establecido en **Copy**.
   * La entrada de la actividad está establecida en **EmpTableFromBlob** y la salida en **EmpSQLTable**.
   * En la sección **transformación**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.

   Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2016-10-14T16:32:41Z. La hora **end** se usa en este tutorial, pero es opcional.

   Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de la propiedad **end**.

   En el ejemplo, hay 24 segmentos de datos, ya que se produce un segmento de datos a la hora.

   Para más información sobre las propiedades JSON, vea [Referencia de scripting JSON](data-factory-data-movement-activities.md).
2. Ejecute el comando siguiente para crear la tabla de factoría de datos.

    ```PowerShell   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

¡Enhorabuena! Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización. Además, ha programado la canalización.

## <a name="monitor-the-pipeline"></a>Supervisar la canalización
En este paso, se usa Azure PowerShell para supervisar lo que ocurre en una Data Factory de Azure.

1. Ejecute **Get-AzureRmDataFactory** y asigne el resultado a la variable $df.

    ```PowerShell  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. Ejecute **Get-AzureRmDataFactorySlice** para obtener la información sobre todos los segmentos de **EmpSQLTable**, que es la tabla de salida de la canalización.  

    ```PowerShell   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Reemplace la parte del año, mes y fecha del parámetro **StartDateTime** por el año, mes y fecha actuales. Esta configuración debe coincidir con el valor de **Inicio** del JSON de canalización.

   Debe ver 24 segmentos, uno para cada hora de 12 A.M. del día actual hasta las 12 A.M. del día siguiente.

   **Salida de ejemplo:**

    ``` 
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. Ejecute **Get-AzureRmDataFactoryRun** para obtener la información de la actividad que se ejecuta para un segmento **específico**. Cambie el valor del parámetro **StartDateTime** para que coincida con la hora de **Inicio** del segmento desde la salida. El valor de **StartDateTime** debe estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601).

    ```PowerShell  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Debería ver una salida similar al siguiente ejemplo:

    ```  
    Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH
    TableName           : EmpSQLTable
    ProcessingStartTime : 8/9/2016 11:03:28 PM
    ProcessingEndTime   : 8/9/2016 11:04:36 PM
    PercentComplete     : 100
    DataSliceStart      : 8/9/2016 10:00:00 PM
    DataSliceEnd        : 8/9/2016 11:00:00 PM
    Status              : Succeeded
    Timestamp           : 8/9/2016 11:03:28 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy
    ```

Vea [Referencia de cmdlets de Data Factory][cmdlet-reference] para obtener la documentación completa sobre los cmdlets de Data Factory.

## <a name="summary"></a>Resumen
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Ha usado PowerShell para crear la factoría de datos, los servicios vinculados, los conjuntos de datos y una canalización. Estos son los pasos de alto nivel que realizó en este tutorial:  

1. Ha creado una **factoría de datos**de Azure.
2. Ha creado **servicios vinculados**.

   a. Un servicio vinculado **Azure Storage** para vincular la cuenta de almacenamiento de Azure que contiene datos de entrada.     
   b. Un servicio vinculado **Azure SQL** para vincular la base de datos SQL que contiene los datos de salida.
3. Ha creado **conjuntos de datos** que describen los datos de entrada y salida de las canalizaciones.
4. Ha creado una **canalización** con una **actividad de copia** con un origen **BlobSource** y un receptor **SqlSink**.

## <a name="see-also"></a>Consulte también
| Tema. | Descripción |
|:--- |:--- |
| [Referencia para cmdlets de Factoría de datos](/powershell/module/azurerm.datafactories) | Esta sección proporciona información acerca de todos los cmdlets de Data Factory |
| [Procesos](data-factory-create-pipelines.md) |Este artículo le ayuda a conocer las canalizaciones y actividades de Data Factory de Azure. |
| [datasets](data-factory-create-datasets.md) |Este artículo le ayuda a comprender los conjuntos de datos de Data Factory de Azure. |
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) |En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Azure Data Factory. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md

