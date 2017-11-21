---
title: Copia de datos del entorno local a la nube con Azure Data Factory | Microsoft Docs
description: "Averigüe cómo puede copiar datos desde un almacén de datos local a la nube de Azure mediante el uso del entorno de ejecución de integración autohospedado en Azure Data Factory."
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 24a4255a23f0b9b9da5d8c3cefeefb8fe250f2f1
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-copy-data-between-on-premises-and-cloud"></a>Tutorial: Copia de datos entre un entorno local y la nube
En este tutorial, usará Azure PowerShell para crear una canalización de Data Factory que copia los datos de una base de datos de SQL Server local a Azure Blob Storage. Creará y usará una instancia de Integration Runtime (IR) autohospedada de Azure Data Factory, que permite la integración de almacenes de datos locales y almacenes de datos en la nube.  Para obtener información sobre el uso de otras herramientas o SDK para crear una factoría de datos, consulte la sección [Inicios rápidos](quickstart-create-data-factory-dot-net.md).

En este artículo no se ofrece una introducción detallada al servicio Data Factory. Para ver una introducción al servicio Azure Data Factory, consulte [Introducción al servicio Azure Data Factory](introduction.md). 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de una instancia de Integration Runtime autohospedada.
> * Creación de los servicios vinculados SQL Server y Azure Storage. 
> * Creación de los conjuntos de datos de SQL Server y Azure Blob.
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de la canalización

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 o 2016. 
Use una base de datos de SQL Server local como almacén de datos de **origen** en este tutorial. Cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla.

1. Inicie **SQL Server Management Studio**. Si usa SQL Server 2016, es posible que tenga que instalar SQL Server Management Studio fuera del [centro de descarga](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Conéctese a su servidor de SQL Server con sus credenciales. 
3. Cree una base de datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en **Bases de datos** y luego en **Nueva base de datos**. En el cuadro de diálogo **Nueva base de datos**, escriba el **nombre** de la base de datos y haga clic en **Aceptar**. 
4. Ejecute el siguiente script de consulta en la base de datos, lo que crea la tabla **emp**. En la vista de árbol, haga clic con el botón derecho en la **base de datos** que creó y, después, haga clic en **Nueva consulta**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Ejecute los siguientes comandos en la base de datos que inserta datos de ejemplo en la tabla:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de **destino o receptor** en este tutorial. Si no dispone de una cuenta de Azure Storage de uso general, consulte el artículo [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla.

#### <a name="get-storage-account-name-and-account-key"></a>Obtener un nombre y una clave de cuenta de Storage
En esta guía de inicio rápido use el nombre y la clave de su cuenta de Azure Storage. El siguiente procedimiento especifica los pasos necesarios para obtener el nombre y la clave de una cuenta de Storage. 

1. Inicie un explorador web y navegue hasta [Azure Portal](https://portal.azure.com). Inicie sesión con su nombre de usuario y contraseña de Azure. 
2. Haga clic en **Más servicios>** en el menú de la izquierda, filtre por la palabra clave **Almacenamiento** y seleccione **Cuentas de almacenamiento**.

    ![Buscar cuenta de Storage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. En la lista de cuentas de Storage, filtre por su cuenta de almacenamiento (si fuera necesario) y, después, seleccione **su cuenta de Storage**. 
4. En la página **Cuenta de almacenamiento**, seleccione **Claves de acceso** en el menú.

    ![Obtener nombre y clave de la cuenta de Storage](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copie los valores de los campos **Nombre de cuenta de almacenamiento** y **clave1** en el Portapapeles. Péguelos en el Bloc de notas o en cualquier otro editor y guárdelo. Utilice el nombre y la clave de la cuenta de almacenamiento en el tutorial. 

#### <a name="create-the-adftutorial-container"></a>Creación del contenedor adftutorial 
En esta sección se crea un contenedor de blobs denominado: adftutorial en Azure Blob Storage. 

1. Instale el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) si no lo tiene en su equipo. 
2. Inicie el **Explorador de Microsoft Azure Storage** en el equipo.   
3. En la ventana **Conectar a Azure Storage**, seleccione **Use a storage account name and key** (Usar un nombre y una clave de cuenta de almacenamiento) y haga clic en **Siguiente**. Si no ve la ventana **Conectar a Azure Storage**, haga clic con el botón derecho en **Cuentas de almacenamiento** en la vista de árbol y haga clic en **Conectar a Azure Storage**. 

    ![Conectar a Azure Storage](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. En la ventana **Attach using Name and Key** (Adjuntar mediante nombre y clave), pegue el **nombre de la cuenta** y la **clave de la cuenta** que guardó en el paso anterior. A continuación, haga clic en **Siguiente**. 
5. En la ventana **Resumen de conexiones**, haga clic en **Conectar**.
6. Confirme que ve la cuenta de almacenamiento en la vista de árbol en **(locales y adjuntas)** -> **Cuentas de almacenamiento**. 
7. Expanda **Contenedores de blob** y confirme que no existe un contenedor de blobs denominado **adftutorial**. Si ya existe. omita los pasos siguientes para crear el contenedor. 
8. Haga clic con el botón derecho en **Contenedores de blob** y seleccione 
**Crear contenedor de blobs**.

    ![Creación de un contenedor de blobs](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Escriba **adftutorial** como nombre y presione **ENTRAR**. 
10. Confirme que el contenedor **adftutorial** está seleccionado en la vista de árbol. Data Factory crea automáticamente la carpeta de salida de este contenedor, por lo que no es necesario que el usuario la cree. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell
Si no está en el equipo, instale la versión más reciente de Azure PowerShell. 

1. En el explorador web, navegue hasta la página [SDK y descargas de Azure SDK](https://azure.microsoft.com/downloads/). 
2. Haga clic en **Instalación de Windows** en la sección **Herramientas de línea de comandos** -> **PowerShell**. 
3. Para instalar Azure PowerShell, ejecute el archivo **MSI**. 

Para obtener instrucciones detalladas, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Iniciar sesión en Azure PowerShell
Inicie **PowerShell** en su equipo. Mantenga Azure PowerShell abierto hasta el final de esta guía de inicio rápido. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

1. Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña de Azure que utiliza para iniciar sesión en Azure Portal:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Si tiene varias suscripciones de Azure, ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Defina una variable para el nombre de la factoría de datos que pueda usar en los comandos de PowerShell más adelante. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Defina una variable para la ubicación de la factoría de datos: 

    ```powershell
    $location = "East US"
    ```
4. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` e inténtelo de nuevo. Si desea compartir el grupo de recursos con otro usuario, vaya al paso siguiente.  
5. Para crear la factoría de datos, ejecute el siguiente cmdlet, **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para crear instancias de Data Factory, hay que ser **colaborador** o **administrador** de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

## <a name="create-a-self-hosted-ir"></a>Creación de una instancia de IR autohospedada

En esta sección, puede crear una instancia de Integration Runtime autohospedada y asociarla a un nodo local (máquina).

1. Cree una variable para el nombre del runtime de integración. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Cree una instancia de Integration Runtime autohospedada. Use un nombre único en caso de que exista otra instancia de Integration Runtime con el mismo nombre.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Este es la salida de ejemplo:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Ejecute el comando siguiente para recuperar el estado de la instancia de Integration Runtime creada.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Este es la salida de ejemplo:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Ejecute el siguiente comando para recuperar las claves de autenticación para registrar la instancia de Integration Runtime autohospedada con el servicio Data Factory en la nube. Copie una de las claves para registrar la instancia de Integration Runtime autohospedada.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Este es la salida de ejemplo:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Instalación de Integration Runtime
1. [Descargue](https://www.microsoft.com/download/details.aspx?id=39717) la versión de Integration Runtime autohospedada en un equipo Windows local y ejecute la instalación. 
2. En la **página de bienvenida del Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Siguiente**.  
3. En la página del **Contrato de licencia para el usuario final**, acepte los términos del Contrato de licencia y haga clic en **Siguiente**. 
4. En la página **Carpeta de destino**, haga clic en **Siguiente**. 
5. En **Preparado para instalar Microsoft Integration Runtime**, haga clic en **Instalar**. 
6. Si aparece un mensaje de advertencia que indica que el equipo está configurado para entrar en modo de suspensión o hibernación si deja de usarse, haga clic en **Aceptar**. 
7. En la página **Ha completado el Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Finalizar**.
8. En la página **Registro de Integration Runtime (autohospedado)**, pegue la clave guardada en la sección anterior y haga clic en **Registrar**. 

   ![Registro de Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Verá el siguiente mensaje cuando la instancia de Integration Runtime autohospedada se haya registrado correctamente:

   ![Se registró correctamente](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. En la página **Nuevo nodo de Integration Runtime (autohospedado)**, haga clic en **Siguiente**. 

    ![Página Nuevo nodo de Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. En **Canal de comunicaciones de intranet**, haga clic en **Omitir**. Puede seleccionar una certificación TLS/SSL para proteger la comunicación entre nodos en un entorno de Integration Runtime con varios nodos. 

    ![Página Canal de comunicaciones de intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. En la página **Registro de Integration Runtime (autohospedado)**, haga clic en 
**Iniciar Configuration Manager**. 
6. Verá la siguiente página cuando el nodo esté conectado al servicio en la nube:

   ![El nodo está conectado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Crear servicios vinculados

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creación de un servicio vinculado a Azure Storage (destino/receptor)

1. Cree un archivo JSON llamado **AzureStorageLinkedService.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: cree la carpeta C:\ADFv2Tutorial si aún no existe.  

    > [!IMPORTANT]
    > Reemplace &lt;accountName&gt; y &lt;accountKey&gt; por el nombre y la clave de su cuenta de Azure Storage antes de guardar el archivo.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```
2. En **Azure PowerShell**, cambie a la carpeta **ADFv2Tutorial**.

   Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureStorageLinkedService**. Los cmdlets usados en este tutorial toman valores para los parámetros **ResourceGroupName** y **DataFactoryName**. Como alternativa, puede pasar el objeto **DataFactory** devuelto por el cmdlet Set-AzureRmDataFactoryV2 sin escribir ResourceGroupName ni DataFactoryName cada vez que ejecute un cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Este es una salida de ejemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creación y cifrado de un servicio vinculado de SQL Server (origen)

1. Cree un archivo JSON denominado **SqlServerLinkedService.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: reemplace **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** y **&lt;password>** por valores de su instancia de SQL Server antes de guardar el archivo. 

    > [!IMPORTANT]
    > Reemplace **&lt;integration** **runtime** **name>** por el nombre de su instancia de Integration Runtime.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Para cifrar la información confidencial de la carga de JSON en la instancia de Integration Runtime autohospedada local, ejecute **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** y pase la carga anterior. Esto garantiza que las credenciales se cifran mediante la interfaz de programación de aplicaciones de protección de datos (DPAPI). Las credenciales cifradas se almacenan en el nodo de Integration Runtime autohospedado de manera local (máquina local). La carga de salida se puede redirigir a otro archivo JSON (en este caso, "encryptedLinkedService.json"), que contiene las credenciales cifradas.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Ejecute el siguiente comando con JSON en el paso anterior para crear **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => Azure Blob Storage).

### <a name="create-a-dataset-for-source-sql-database"></a>Creación de un conjunto de datos de la instancia de SQL Database de origen

1. Cree un archivo JSON denominado **SqlServerDataset.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Para crear el conjunto de datos **SqlServerDataset**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Este es la salida de ejemplo:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Creación de un conjunto de datos para la instancia de Azure Blob Storage receptora

1. Cree un archivo JSON denominado **AzureBlobDataset.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido:

    > [!IMPORTANT]
    > Con este código de ejemplo se da por supuesto que tiene un contenedor denominado **adftutorial** en Azure Blob Storage.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Para crear el conjunto de datos **AzureBlobDataset**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Este es la salida de ejemplo:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Creación de canalizaciones

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Creación de la canalización "SqlServerToBlobPipeline"

1. Cree un archivo JSON denominado **SqlServerToBlobPipeline.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para crear la canalización **SQLServerToBlobPipeline**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Este es la salida de ejemplo:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Inicio y supervisión de una ejecución de la canalización

1. Inicie una ejecución para la canalización "SQLServerToBlobPipeline" y capture el identificador de ejecución de la canalización a fin de poder realizar una supervisión en el futuro.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Ejecute el siguiente script para comprobar continuamente el estado de ejecución de la canalización "**SQLServerToBlobPipeline**" e imprimir el resultado final.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Este es el resultado de la ejecución de ejemplo:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Puede obtener el identificador de ejecución de la canalización "**SQLServerToBlobPipeline**" y comprobar el resultado de la ejecución de actividad detallado como se muestra a continuación.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Este es el resultado de la ejecución de ejemplo:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Comprobación del resultado
El conducto crea automáticamente la carpeta de salida `fromonprem` en el contenedor de blobs `adftutorial`. Confirme que ve el archivo **dbo.emp.txt** en la carpeta de salida. Use el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para comprobar se crea la salida. 

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Ha aprendido a:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Creación de los servicios vinculados SQL Server y Azure Storage. 
> * Creación de los conjuntos de datos de SQL Server y Azure Blob.
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de la canalización

Para ver una lista de los almacenes de datos compatibles con Azure Data Factory, consulte los [almacenes de datos disponibles](copy-activity-overview.md#supported-data-stores-and-formats).

Vaya al tutorial siguiente para obtener información sobre cómo copiar datos de forma masiva de un origen a un destino:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy.md)
