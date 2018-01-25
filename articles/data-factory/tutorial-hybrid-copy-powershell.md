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
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 221af59c211cc6ce0471718908db1544ca2d75ed
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Tutorial: copia de datos de una base de datos de SQL Server local a Azure Blob Storage
En este tutorial, use Azure PowerShell para crear una canalización de Data Factory que copie los datos de una base de datos de SQL Server local a Azure Blob Storage. Cree y use una instancia de Integration Runtime autohospedado, que mueve los datos entre almacenes locales y en la nube. 

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
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
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

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Instalar Azure Powershell
Si no está en el equipo, instale la versión más reciente de Azure PowerShell. 

1. Vaya a [Descargas de Azure SDK](https://azure.microsoft.com/downloads/). 

2. En **Herramientas de línea de comandos**, en la sección **PowerShell**, seleccione **Instalación de Windows**. 

3. Para instalar Azure PowerShell, ejecute el archivo MSI. 

Para obtener instrucciones detalladas, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Inicio de sesión en PowerShell

1. Inicie PowerShell en su equipo y manténgalo abierto hasta que finalice este tutorial de inicio rápido. Si lo cierra y vuelve a abrirlo, tendrá que volver a ejecutar estos comandos.

    ![Iniciar PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)

2. Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña de Azure que utiliza para iniciar sesión en Azure Portal:
       
    ```powershell
    Login-AzureRmAccount
    ```        

3. Si tiene varias suscripciones de Azure, ejecute el siguiente comando para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Defina una variable para el nombre del grupo de recursos que usará en los comandos de PowerShell más adelante. Copie el comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) (entre comillas dobles; por ejemplo, `"adfrg"`) y ejecute el comando. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

2. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y ejecute el comando de nuevo.

3. Defina una variable para el nombre de la factoría de datos que pueda usar en los comandos de PowerShell más adelante. El nombre deben comenzar por una letra o un número y solo pueden contener letras, números y el carácter de guion (-).

    > [!IMPORTANT]
    >  Actualice el nombre de la factoría de datos por otro que sea globalmente único. Un ejemplo es ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

4. Defina una variable para la ubicación de la factoría de datos: 

    ```powershell
    $location = "East US"
    ```  

5. Para crear la factoría de datos, ejecute el siguiente cmdlet `Set-AzureRmDataFactoryV2`: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * El nombre de la factoría de datos debe ser globalmente único. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Para crear instancias de Data Factory, a la cuenta de usuario que use para iniciar sesión en Azure debe se le deben asignar los roles *colaborador* o *propietario*, o bien debe de ser de un *administrador* de la suscripción a Azure.
> * Actualmente, mediante la versión 2 de Data Factory se pueden crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (Azure HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Creación de una instancia de Integration Runtime autohospedada

En esta sección se crea una instancia de Integration Runtime autohospedada y se asocia con un equipo local con la base de datos de SQL Server. Integration Runtime autohospedado es el componente que copia los datos de la base de datos de SQL Server del equipo a Azure Blob Storage. 

1. Cree una variable para el nombre del runtime de integración. Use un nombre único y anótelo. Lo usará más adelante en este tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

2. Cree una instancia de Integration Runtime autohospedada. 

    Este es la salida de ejemplo:

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```

3. Para recuperar el estado de la instancia de Integration Runtime creada, ejecute el siguiente comando:

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

4. Para recuperar las *claves de autenticación* para registrar la instancia de Integration Runtime autohospedado con el servicio Data Factory en la nube, ejecute el siguiente comando. Copie una de las claves (sin las comillas dobles) para registrar la instancia de Integration Runtime autohospedado que instalará en el equipo en el paso siguiente. 

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    Este es la salida de ejemplo:
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Instalación de Integration Runtime
1. Descargue [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) en un equipo Windows local y ejecute la instalación. 

2. En el **Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Siguiente**.  

3. En la ventana **Contrato de licencia para el usuario final**, acepte los términos del Contrato de licencia y haga clic en **Siguiente**. 

4. En la ventana **Carpeta de destino**, seleccione **Siguiente**. 

5. En la ventana **Preparado para instalar Microsoft Integration Runtime**, haga clic en **Instalar**. 

6. Si aparece un mensaje de advertencia que indica que el equipo está configurado para entrar en modo de suspensión o hibernación si deja de usarse, haga clic en **Aceptar**. 

7. Si se muestra la ventana **Opciones de energía**, ciérrela y cambie a la ventana de instalación. 

8. En **Ha completado el Asistente para la instalación de Microsoft Integration Runtime Setup**, seleccione **Finalizar**.

9. En la ventana **Registro de Integration Runtime (autohospedado)**, pegue la clave que guardó en la sección anterior y haga clic en **Registrar**. 

    ![Registro de Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    Cuando Integration Runtime autohospedado se haya registrado correctamente, se muestra el siguiente mensaje: 

    ![Se registró correctamente](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

10. En la página **Nuevo nodo de Integration Runtime (autohospedado)**, haga clic en **Siguiente**. 

    ![Ventana Nuevo nodo de Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

11. En la ventana **Canal de comunicaciones de intranet**, haga clic en **Omitir**.  
    Puede seleccionar una certificación TLS/SSL para proteger la comunicación entre nodos en un entorno de Integration Runtime con varios nodos.

    ![Ventana Canal de comunicaciones de intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

12. En la ventana **Registro de Integration Runtime (autohospedado)**, haga clic en 
**Iniciar Configuration Manager**. 

13. Cuando el nodo se conecte al servicio en la nube, se mostrará el servicio en la nube:

    ![El nodo está conectado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

14. Para probar la conectividad a la base de datos de SQL Server, siga estos pasos:

    ![Ficha Diagnóstico](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. En la ventana **Configuration Manager**, cambie a la pestaña **Diagnósticos**.

    b. En el cuadro **Tipo de origen de datos**, seleccione **SqlServer**.

    c. Escriba el nombre del servidor.

    d. Escriba el nombre de la base de datos. 

    e. Seleccione el modo de autenticación. 

    f. Escriba el nombre de usuario. 

    g. Escriba la contraseña asociada con el nombre de usuario.

    h. Para confirmar que Integration Runtime puede conectarse a SQL Server, seleccione **Probar**.  
    Si la conexión se ha realizado correctamente, verá una marca de verificación verde. De lo contrario, recibirá un mensaje de error asociado al error. Solucione los problemas y asegúrese de que Integration Runtime puede conectarse a la instancia de SQL Server.

    Anote todos los valores anteriores, ya que los usará más adelante en este mismo tutorial.
    
## <a name="create-linked-services"></a>Crear servicios vinculados
Para vincular los almacenes de datos y los servicios de proceso a la factoría de datos, cree servicios vinculados en una factoría de datos. En este tutorial, vincula su cuenta de Azure Storage y una instancia de SQL Server local al almacén de datos. Los servicios vinculados tienen la información de conexión que usa el servicio Data Factory en el entorno de tiempo de ejecución para conectarse a ellos. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creación de un servicio vinculado a Azure Storage (destino/receptor)
En este paso, vinculará su cuenta de Azure Storage a la factoría de datos.

1. Cree un archivo JSON llamado *AzureStorageLinkedService.json* en la carpeta *C:\ADFv2Tutorial* con el siguiente código. Si la carpeta *ADFv2Tutorial* no existe, créela.  

    > [!IMPORTANT]
    > Antes de guardar el archivo, sustituya \<accountName> y \<accountKey> por el nombre y la clave de su cuenta de Azure Storage. Los anotó en la sección [Requisitos previos](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. En PowerShell, cambie a la carpeta *C:\ADFv2Tutorial*.

3. Para crear el servicio vinculado, AzureStorageLinkedService, ejecute el siguiente cmdlet `Set-AzureRmDataFactoryV2LinkedService`: 

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

    Si recibe un error de "archivo no encontrado", confirme que existe el archivo, para lo que debe ejecutar el comando `dir`. Si el nombre del archivo tiene la extensión *.txt* (por ejemplo, AzureStorageLinkedService.json.txt), quítela y vuelva a ejecutar el comando de PowerShell. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creación y cifrado de un servicio vinculado de SQL Server (origen)
En este paso, vincula la instancia de SQL Server local a la factoría de datos.

1. Cree un archivo JSON llamado *SqlServerLinkedService.json* en la carpeta *C:\ADFv2Tutorial* mediante el siguiente código:

    > [!IMPORTANT]
    > Seleccione la sección que se basa en la autenticación que usa para conectarse a SQL Server.

    **Uso de la autenticación de SQL (sa):**

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

    **Uso de la autenticación de Windows:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
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

    > [!IMPORTANT]
    > - Seleccione la sección que se basa en la autenticación que usa para conectarse a su instancia de SQL Server.
    > - Reemplace **\<integration runtime name>** por el nombre de su instancia de Integration Runtime.
    > - Antes de guardar el archivo, reemplace **\<servername>**, **\<databasename>**, **\<username>** y **\<password>** por los valores de su instancia de SQL Server.
    > - Si necesita usar una barra diagonal inversa (\\) en la cuenta del usuario o en el nombre del servidor, utilice el carácter de escape (\\) antes. Por ejemplo, use *mydomain\\\\myuser*. 

2. Para cifrar los datos confidenciales (nombre de usuario, contraseña, etc.), ejecute el cmdlet `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential`.  
    Este cifrado que las credenciales se cifran mediante la API de protección de datos (DPAPI). Las credenciales cifradas se almacenan de manera local en el nodo de Integration Runtime autohospedado (máquina local). La carga de salida se puede redirigir a otro archivo JSON (en este caso, *encryptedLinkedService.json*) que contiene las credenciales cifradas.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Ejecute el comando siguiente, que crea EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, crea conjuntos de datos de entrada y salida. Estos representan los datos de entrada y de salida para la operación de copia, en la que se copian datos de la base de datos de SQL Server local a Azure Blob Storage.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Creación de un conjunto de datos para la base de datos de origen SQL Server
En este paso, defina un conjunto de datos que represente los datos de la instancia de la base de datos de SQL Server. El conjunto de datos es del tipo SqlServerTable. Hace referencia al servicio vinculado de SQL Server que creó en el paso anterior. El servicio vinculado tiene la información de conexión que el servicio Data Factory usa para conectarse a su instancia de SQL Server en el runtime. Este conjunto de datos especifica la tabla SQL de la base de datos que contiene los datos. En este tutorial, la tabla **emp** contiene los datos de origen. 

1. Cree un archivo JSON denominado *SqlServerDataset.json* en la carpeta *C:\ADFv2Tutorial* con el siguiente código:  

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

2. Para crear el conjunto de datos SqlServerDataset, ejecute el cmdlet `Set-AzureRmDataFactoryV2Dataset`.

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

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Creación de un conjunto de datos para Azure Blob Storage (receptor)
En este paso, se define un conjunto de datos que representa los datos que se van a copiar a Azure Blob Storage. El conjunto de datos es del tipo AzureBlob. Hace referencia al servicio vinculado Azure Storage que creó anteriormente en este tutorial. 

El servicio vinculado tiene la información de conexión que Data Factory usa en el runtime para conectarse a su cuenta de Azure Storage. Este conjunto de datos especifica la carpeta del almacenamiento de Azure a la que se copian los datos desde la base de datos de SQL Server. En este tutorial, la carpeta es: *adftutorial/fromonprem*, donde `fromonprem` es el contenedor de blobs y `adftutorial` es la carpeta. 

1. Cree un archivo JSON denominado *AzureBlobDataset.json* en la carpeta *C:\ADFv2Tutorial* con el siguiente código:

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

2. Para crear el conjunto de datos AzureBlobDataset, ejecute el cmdlet `Set-AzureRmDataFactoryV2Dataset`.

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

## <a name="create-a-pipeline"></a>Crear una canalización
En este tutorial, creará una canalización con una actividad de copia. La actividad de copia usa SqlServerDataset como el conjunto de datos de entrada y AzureBlobDataset como conjunto de datos de salida. El tipo de origen se establece en *SqlSource* y el tipo de receptor en *BlobSink*.

1. Cree un archivo JSON denominado *SqlServerToBlobPipeline.json* en la carpeta *C:\ADFv2Tutorial* con el siguiente código:

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

2. Para crear la canalización SqlServerToBlobPipeline, ejecute el cmdlet `Set-AzureRmDataFactoryV2Pipeline`.

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

## <a name="create-a-pipeline-run"></a>Creación de una ejecución de canalización
Inicie la ejecución de la canalización SQLServerToBlobPipeline y capture el identificador de dicha ejecución para poder supervisarla en el futuro.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Para comprobar continuamente el estado de ejecución de la canalización SQLServerToBlobPipeline, ejecute el siguiente script en PowerShell e imprima el resultado final:

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

2. Mediante la ejecución del comando siguiente puede obtener el identificador de la ejecución de la canalización SQLServerToBlobPipeline y comprobar el resultado detallado de la ejecución de la actividad: 

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
La canalización automáticamente la carpeta de salida *fromonprem* en el contenedor de blobs `adftutorial`. Confirme que ve el archivo *dbo.emp.txt* en la carpeta de salida. 

1. En Azure Portal, en la ventana Contenedor **adftutorial**, seleccione **Actualizar** para ver la carpeta de salida.

    ![Carpeta de salida creada](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Seleccione `fromonprem` en la lista de carpetas. 
3. Confirme que ve un archivo denominado `dbo.emp.txt`.

    ![Archivo de salida](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


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
>[Copiar datos de forma masiva](tutorial-bulk-copy.md)
