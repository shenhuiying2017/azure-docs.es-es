---
title: Copia de datos de SQL Server a Blob Storage mediante Azure Data Factory | Microsoft Docs
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Tutorial: Copia de datos de un servidor SQL Server local a Azure Blob Storage
En este tutorial, usará Azure PowerShell para crear una canalización de Data Factory que copia los datos de una base de datos de SQL Server local a Azure Blob Storage. Cree y use una instancia de Integration Runtime autohospedado, que mueve los datos entre almacenes locales y en la nube. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> En este artículo no se ofrece una introducción detallada al servicio Data Factory. Para ver una introducción al servicio Azure Data Factory, consulte [Introducción al servicio Azure Data Factory](introduction.md). 

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Creación de los servicios vinculados SQL Server y Azure Storage. 
> * Creación de los conjuntos de datos de SQL Server y Azure Blob.
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de la canalización

## <a name="prerequisites"></a>Requisitos previos
### <a name="azure-subscription"></a>Suscripción de Azure
Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure. En Azure Portal, haga clic en el **nombre de usuario**, en la esquina superior derecha, y seleccione **Permisos** para ver los permisos que tiene en la suscripción. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte el artículo sobre la [adición de roles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-201420162017"></a>SQL Server 2014/2016/2017
Use una base de datos de SQL Server local como almacén de datos de **origen** en este tutorial. La canalización de la factoría de datos que crea en este tutorial copia los datos de una base de datos de SQL Server local (origen) a Azure Blob Storage (receptor). Cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla. 

1. Inicie **SQL Server Management Studio** en su máquina. Si no tiene SQL Server Management Studio en su máquina, instálelo desde el [centro de descarga de](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Conéctese a su servidor de SQL Server con sus credenciales. 
3. Cree una base de datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en **Bases de datos** y luego en **Nueva base de datos**. En el cuadro de diálogo **Nueva base de datos**, escriba el **nombre** de la base de datos y haga clic en **Aceptar**. 
4. Ejecute el siguiente script de consulta contra la base de datos, que crea la tabla **emp** e inserta en ella algunos datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en la **base de datos** que creó y, después, haga clic en **Nueva consulta**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de **destino o receptor** en este tutorial. Si no dispone de una cuenta de Azure Storage de uso general, consulte el artículo [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla. La canalización de la factoría de datos que crea en este tutorial copia los datos de la base de datos de SQL Server local (origen) a esta instancia de Azure Blob Storage (receptor). 

#### <a name="get-storage-account-name-and-account-key"></a>Obtener un nombre y una clave de cuenta de Storage
En este tutorial, use el nombre y la clave de su cuenta de Azure Storage. El siguiente procedimiento especifica los pasos necesarios para obtener el nombre y la clave de una cuenta de Storage. 

1. Inicie un explorador web y navegue hasta [Azure Portal](https://portal.azure.com). Inicie sesión con su nombre de usuario y contraseña de Azure. 
2. Haga clic en **Más servicios>** en el menú de la izquierda, filtre por la palabra clave **Almacenamiento** y seleccione **Cuentas de almacenamiento**.

    ![Buscar cuenta de Storage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. En la lista de cuentas de Storage, filtre por su cuenta de almacenamiento (si fuera necesario) y, después, seleccione **su cuenta de Storage**. 
4. En la página **Cuenta de almacenamiento**, seleccione **Claves de acceso** en el menú.

    ![Obtener nombre y clave de la cuenta de Storage](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copie los valores de los campos **Nombre de cuenta de almacenamiento** y **clave1** en el Portapapeles. Péguelos en el Bloc de notas o en cualquier otro editor y guárdelo. Utilice el nombre y la clave de la cuenta de almacenamiento en el tutorial. 

#### <a name="create-the-adftutorial-container"></a>Creación del contenedor adftutorial 
En esta sección se crea un contenedor de blobs denominado **adftutorial** en la instancia de Azure Blob Storage. 

1. En la página **Cuenta de almacenamiento**, cambie a **Información general** y, a continuación, haga clic en **Blobs**. 

    ![Selección de la opción Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. En la página **Blob service**, haga clic en **+ Contenedor** en la barra de herramientas. 

    ![Botón Agregar contenedor](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. En el cuadro de diálogo **Nuevo contenedor**, escriba **adftutorial** para el nombre y haga clic en **Aceptar**. 

    ![Especificación de un nombre de contenedor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. Haga clic en **adftutorial** en la lista de contenedores.  

    ![Selección del contenedor](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. Mantenga la página **contenedor** para que **adftutorial** se abra. Úselo para comprobar la salida al final de este tutorial. Data Factory crea automáticamente la carpeta de salida de este contenedor, por lo que no es necesario que el usuario la cree.

    ![Página Contenedor](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Instale PowerShell.
Si no está aún en la máquina, instale la versión más reciente de PowerShell. 

1. En el explorador web, navegue hasta la página [SDK y descargas de Azure SDK](https://azure.microsoft.com/downloads/). 
2. Haga clic en **Instalación de Windows** en la sección **Herramientas de línea de comandos** -> **PowerShell**. 
3. Para instalar Azure PowerShell, ejecute el archivo **MSI**. 

Para obtener instrucciones detalladas, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Inicio de sesión en PowerShell

1. Inicie **PowerShell** en su equipo. Mantenga abierto PowerShell hasta el final de esta guía de inicio rápido. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

    ![Inicie PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña de Azure que utiliza para iniciar sesión en Azure Portal:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Si tiene varias suscripciones de Azure, ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Si tiene varias suscripciones de Azure, ejecute el siguiente comando para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. Por ejemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y ejecute el comando de nuevo.
3. Defina una variable para el nombre de la factoría de datos que pueda usar en los comandos de PowerShell más adelante. El nombre deben comenzar por una letra o un número, y pueden contener solo letras, números y el carácter de guion (-).

    > [!IMPORTANT]
    >  Actualice el nombre de la factoría de datos para que sea único global. Por ejemplo, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. Defina una variable para la ubicación de la factoría de datos: 

    ```powershell
    $location = "East US"
    ```  
5. Para crear la factoría de datos, ejecute el siguiente cmdlet, **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

## <a name="create-a-self-hosted-ir"></a>Creación de una instancia de IR autohospedada

En esta sección, creará una instancia de Integration Runtime autohospedado y la asociará a una máquina local con la base de datos de SQL Server. Integration Runtime autohospedado es el componente que copia los datos de SQL Server de la máquina a Azure Blob Storage. 

1. Cree una variable para el nombre del runtime de integración. Utilice un nombre único y anótelo. Lo usará más adelante en este tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Cree una instancia de Integration Runtime autohospedada. 

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

3. Ejecute el siguiente comando para recuperar las **claves de autenticación** para registrar la instancia de Integration Runtime autohospedado con el servicio Data Factory en la nube. 

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
4. Copie una de las claves (excluya las comillas dobles) para registrar la instancia de Integration Runtime autohospedado que instalará en el equipo en el paso siguiente.  

## <a name="install-integration-runtime"></a>Instalación de Integration Runtime
1. [Descargue](https://www.microsoft.com/download/details.aspx?id=39717) la versión de Integration Runtime autohospedado en un equipo Windows local y ejecute la instalación. 
2. En la **página de bienvenida del Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Siguiente**.  
3. En la página del **Contrato de licencia para el usuario final**, acepte los términos del Contrato de licencia y haga clic en **Siguiente**. 
4. En la página **Carpeta de destino**, haga clic en **Siguiente**. 
5. En **Preparado para instalar Microsoft Integration Runtime**, haga clic en **Instalar**. 
6. Si aparece un mensaje de advertencia que indica que el equipo está configurado para entrar en modo de suspensión o hibernación si deja de usarse, haga clic en **Aceptar**. 
7. Si ve la ventana **Opciones de energía**, ciérrela y cambie a la ventana de instalación. 
8. En la página **Ha completado el Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Finalizar**.
9. En la página **Registro de Integration Runtime (autohospedado)**, pegue la clave guardada en la sección anterior y haga clic en **Registrar**. 

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
7. Ahora, pruebe la conectividad a la base de datos de SQL Server.

    ![Ficha Diagnóstico](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - En la ventana **Configuration Manager**, cambie a la pestaña **Diagnósticos**.
    - Seleccione **SqlServer** como **Tipo de origen de datos**.
    - Escriba el **nombre del servidor**.
    - Escriba el **nombre de la base de datos**. 
    - Seleccione el **modo de autenticación**. 
    - Escriba el **nombre de usuario**. 
    - Escriba la **contraseña** del nombre de usuario.
    - Haga clic en **Probar** para confirmar que el entorno de tiempo de ejecución de la integración puede conectarse a SQL Server. Verá una marca de verificación verde si la conexión es correcta. De lo contrario, verá un mensaje de error asociado al error. Solucione los problemas y asegúrese de que Integration Runtime puede conectarse a SQL Server.
    - Anote estos valores (tipo de autenticación, servidor, base de datos, usuario, contraseña). Los usará más adelante en este tutorial. 
    
      
## <a name="create-linked-services"></a>Crear servicios vinculados
Cree servicios vinculados en una factoría de datos para vincular los almacenes de datos y los servicios de proceso a la factoría de datos. En este tutorial, vincula su cuenta de Azure Storage y SQL Server local al almacén de datos. Los servicios vinculados tienen la información de conexión que usa el servicio Data Factory en el entorno de tiempo de ejecución para conectarse a ellos. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creación de un servicio vinculado a Azure Storage (destino/receptor)
En este paso, vincula su cuenta de Azure Storage a la factoría de datos.

1. Cree un archivo JSON llamado **AzureStorageLinkedService.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: cree la carpeta C:\ADFv2Tutorial si aún no existe.  

    > [!IMPORTANT]
    > Reemplace &lt;accountName&gt; y &lt;accountKey&gt; por el nombre y la clave de su **cuenta de Azure Storage** antes de guardar el archivo. Los anotó en la parte de [requisitos previos](#get-storage-account-name-and-account-key).

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

    Si usa Bloc de notas, seleccione **Todos los archivos** en **Guardar como tipo** en el cuadro de diálogo **Guardar como**. Si no lo hace, puede que se agregue la extensión `.txt` al archivo. Por ejemplo: `AzureStorageLinkedService.json.txt`. Si crea el archivo en el Explorador de archivos antes de abrirlo en el Bloc de notas, puede que no vea la extensión `.txt`, ya que la opción **Ocultar las extensiones de archivo para tipos de archivo conocidos** está establecida de forma predeterminada. Quite la extensión `.txt` antes de continuar con el paso siguiente. 
2. En **Azure PowerShell**, cambie a la carpeta **C:\ADFv2Tutorial**.

   Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureStorageLinkedService**. 

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

    Si recibe un error "archivo no encontrado". Ejecute el comando `dir` para confirmar que el archivo existe. Si el nombre del archivo tiene la extensión `.txt` (por ejemplo, AzureStorageLinkedService.json.txt), quítela y luego ejecute de nuevo el comando de PowerShell. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creación y cifrado de un servicio vinculado de SQL Server (origen)
En este paso, vincula la instancia de SQL Server local a la factoría de datos.

1. Cree un archivo JSON denominado **SqlServerLinkedService.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: seleccione la sección derecha según la **autenticación** que use para conectarse a SQL Server.  

    > [!IMPORTANT]
    > Seleccione la sección derecha según la **autenticación** que use para conectarse a SQL Server.

    **Si usa la autenticación de SQL (sa), copie la siguiente definición de JSON:**

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
    **Si usa la autenticación de Windows, copie la siguiente definición de JSON:**

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
    > - Seleccione la sección derecha según la **autenticación** que use para conectarse a SQL Server.
    > - Reemplace **&lt;integration** **runtime** **name>** por el nombre de su instancia de Integration Runtime.
    > - Reemplace **&lt;servername>**, **&lt;databasename>**, **&lt;username>** y **&lt;password>** por los nombres de su instancia de SQL Server antes de guardar el archivo.
    > - Si necesita usar un carácter de barra diagonal (`\`) en el nombre de servidor o en la cuenta de usuario, utilice el carácter de escape (`\`). Por ejemplo: `mydomain\\myuser`. 

2. Para cifrar los datos confidenciales (nombre de usuario, contraseña, etc.), ejecute el cmdlet **AzureRmDataFactoryV2LinkedServiceEncryptedCredential New**. Esto garantiza que las credenciales se cifran mediante la interfaz de programación de aplicaciones de protección de datos (DPAPI). Las credenciales cifradas se almacenan de manera local en el nodo de Integration Runtime autohospedado (máquina local). La carga de salida se puede redirigir a otro archivo JSON (en este caso, "encryptedLinkedService.json"), que contiene las credenciales cifradas.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Ejecute el comando siguiente, que crea el **EncryptedSqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => Azure Blob Storage).

### <a name="create-a-dataset-for-source-sql-database"></a>Creación de un conjunto de datos de la instancia de SQL Database de origen
En este paso, definirá un conjunto de datos que representa los datos en la base de datos de SQL Server. El conjunto de datos es de tipo **SqlServerTable**. Hace referencia al **servicio vinculado de SQL Server** que creó en el paso anterior. El servicio vinculado tiene la **información de conexión** que usa el servicio Data Factory para conectarse a su instancia de SQL Server en el entorno de tiempo de ejecución. Este conjunto de datos especifica la **tabla SQL** en la base de datos que contiene los datos. En este tutorial, es la tabla `emp` que contiene los datos de origen. 

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
En este paso, definirá un conjunto de datos que representa los datos que se van a copiar en Azure Blob Storage. El conjunto de datos es de tipo **AzureBlob**. Hace referencia al **servicio vinculado Azure Storage** que creó anteriormente en este tutorial. El servicio vinculado tiene la **información de conexión** que Data Factory usa en el entorno de tiempo de ejecución para conectarse a su instancia de Azure Storage. Este **conjunto de datos** especifica la **carpeta** en el almacenamiento de Azure a la que los datos se copian desde la base de datos de SQL Server. En este tutorial, la carpeta es: `adftutorial/fromonprem` donde `adftutorial` es el contenedor de blob y `fromonprem` es la carpeta. 

1. Cree un archivo JSON denominado **AzureBlobDataset.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido:

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

## <a name="create-a-pipeline"></a>Crear una canalización
En este tutorial, creará una canalización con una actividad de copia. La actividad de copia usa **SqlServerDataset** como el conjunto de datos de entrada y **AzureBlobDataset** como el de salida. El tipo de origen se establece en **SqlSource** y el tipo de receptor en **BlobSink**.

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


## <a name="create-a-pipeline-run"></a>Creación de una ejecución de canalización
Inicie una ejecución para la canalización "SQLServerToBlobPipeline" y capture el identificador de ejecución de la canalización a fin de poder realizar una supervisión en el futuro.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Ejecute el siguiente script para comprobar continuamente el estado de ejecución de la canalización **SQLServerToBlobPipeline** e imprima el resultado final. Copie y pegue el siguiente script en la ventana de PowerShell y presione ENTRAR.

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

3. Puede obtener el identificador de ejecución de la canalización **SQLServerToBlobPipeline** y comprobar el resultado detallado de la ejecución de actividad ejecutando el comando siguiente: 

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
El conducto crea automáticamente la carpeta de salida `fromonprem` en el contenedor de blobs `adftutorial`. Confirme que ve el archivo **dbo.emp.txt** en la carpeta de salida. 

1. En Azure Portal, en la página de contenedor **adftutorial**, haga clic en **Actualizar** para ver la carpeta de salida.

    ![carpeta de salida creada](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Haga clic en `fromonprem` en la lista de carpetas. 
3. Confirme que ve un archivo denominado `dbo.emp.txt`.

    ![archivo de salida](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


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
