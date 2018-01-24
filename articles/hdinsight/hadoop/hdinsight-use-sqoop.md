---
title: "Ejecución de trabajos de Apache Sqoop con Azure HDInsight (Hadoop) | Microsoft Docs"
description: "Aprenda a utilizar Azure PowerShell desde una estación de trabajo para ejecutar la importación y exportación en Sqoop entre un clúster de Hadoop y una base de datos SQL de Azure."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: e2f4601daf9aa3537f9170c3516d62ab1bd602e5
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Uso de Sqoop con Hadoop en HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a utilizar Sqoop en HDInsight  para importar y exportar entre un clúster de HDInsight y una base de datos de SQL Server o la base de datos SQL de Azure.

A pesar de que Hadoop es una opción natural para procesar datos no estructurados y datos semiestructurados, como registros y archivos, es posible que también sea necesario procesar datos estructurados almacenados en bases de datos relacionales.

[Sqoop][sqoop-user-guide-1.4.4] es una herramienta diseñada para transferir datos entre clústeres de Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS. En este tutorial, usará una base de datos de SQL Server como base de datos relacional.

Para ver las versiones de Sqoop compatibles con los clústeres de HDInsight, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][hdinsight-versions].

## <a name="understand-the-scenario"></a>Descripción del escenario

El clúster de HDInsight incluye algunos datos de ejemplo. Utilice los dos ejemplos siguientes:

* Un archivo registro log4j, ubicado en */example/data/sample.log*. Los registros siguientes se extraen del archivo:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Una tabla de Hive denominada *hivesampletable* que hace referencia al archivo de datos ubicado en */hive/warehouse/hivesampletable*. La tabla contiene algunos datos del dispositivo móvil. 
  
  | Campo | Tipo de datos |
  | --- | --- |
  | clientid |cadena |
  | querytime |cadena |
  | market |cadena |
  | deviceplatform |cadena |
  | devicemake |cadena |
  | devicemodel |cadena |
  | state |cadena |
  | country |cadena |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

En este tutorial, usará estos dos conjuntos de datos para probar los procesos de importación y exportación de Sqoop.

## <a name="create-cluster-and-sql-database"></a>Creación del clúster y la base de datos SQL
En esta sección se muestra cómo crear un clúster, una instancia de SQL Database y los esquemas de SQL Database para ejecutar el tutorial con Azure Portal y una plantilla de Azure Resource Manager. La plantilla se puede encontrar en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). La plantilla de Resource Manager llama a un paquete de bacpac para que implemente los esquemas de tabla en SQL Database.  El paquete de bacpac se encuentra en un contenedor de blobs público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Si desea usar un contenedor privado para los archivos bacpac, utilice los siguientes valores en la plantilla:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Si prefiere usar Azure PowerShell para crear el clúster y SQL Database; consulte el [Apéndice A](#appendix-a---a-powershell-sample).

> [!NOTE]
> La importación mediante Azure Portal solo admite la importación de un archivo BACPAC desde Azure Blob Storage.

**Configurar el entorno con una plantilla de administración de recursos**
1. Haga clic en la imagen siguiente para abrir una plantilla de Resource Manager en Azure Portal.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Especifique las siguientes propiedades:

    - **Suscripción**: escriba su suscripción de Azure.
    - **Grupo de recursos**: cree un nuevo grupo de recursos de Azure o seleccione uno existente.  Un grupo de recursos tiene fines administrativos.  Es un contenedor de objetos.
    - **Ubicación**: seleccione una región.
    - **Nombre del clúster**: escriba el nombre del clúster de Hadoop.
    - **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es admin.
    - **Nombre de usuario y contraseña de SSH**.
    - **Nombre y contraseña de inicio de sesión en el servidor de la base de datos SQL**.
    - **_artifacts Location** (Ubicación de _artefactos): use el valor predeterminado a no ser que quiera usar su propio archivo backpac en una ubicación diferente.
    - **_artifacts Location Sas Token** (Token Sas de ubicación de _artefactos): deje este valor en blanco.
    - **Bacpac File Name** (Nombre de archivo bacpac): use el valor predeterminado a no ser que quiera usar su propio archivo backpac.
     
        Los siguientes valores están codificados de forma rígida en la sección de variables:
        
        |NOMBRE|Valor|
        |----|-----|
        | Nombre de la cuenta de almacenamiento predeterminada | &lt;ClusterName>store |
        | Nombre del servidor de base de datos SQL de Azure | &lt;ClusterName>dbserver |
        | Nombre de la base de datos SQL de Azure | &lt;ClusterName>db |
     
3. Seleccione **Acepto los términos y condiciones indicados anteriormente**.
4. Haga clic en **Comprar**. Verá un icono nuevo llamado Envío de implementación para la implementación de plantilla. Tarda aproximadamente 20 minutos en crear un clúster y la base de datos SQL.

Si opta por usar la base de datos SQL de Azure existente o Microsoft SQL Server

* **Base de datos SQL de Azure**: debe configurar una regla de firewall para que el servidor de base de datos SQL de Azure permita el acceso desde la estación de trabajo. Para obtener instrucciones sobre cómo crear una base de datos SQL de Azure y configurar el firewall, consulte [Introducción al uso de Azure SQL Database][sqldatabase-get-started]. 
  
  > [!NOTE]
  > De forma predeterminada, una base de datos SQL de Azure permite realizar conexiones desde servicios de Azure, como HDInsight de Azure. Si la configuración del firewall está deshabilitada, debe habilitarla en Azure Portal. Para obtener instrucciones sobre la creación de una base de datos de Azure SQL Database y la configuración de las reglas de firewall, consulte [Creación y configuración de SQL Database][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: si el clúster de HDInsight se encuentra en la misma red virtual de Azure que un SQL Server, puede seguir los pasos indicados en este artículo para importar y exportar datos a una base de datos de SQL Server.
  
  > [!NOTE]
  > HDInsight solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.
  > 
  > 
  
  * Para crear y configurar una red virtual, consulte [Creación de una red virtual mediante Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    
    * Cuando use SQL Server en el centro de datos, debe configurar la red virtual como de *sitio a sitio* o de *punto a sitio*.
      
      > [!NOTE]
      > En el caso de las redes virtuales de **punto a sitio**, SQL Server debe ejecutarse en la aplicación de configuración de clientes VPN, que se encuentra disponible en el **Panel** de la configuración de red virtual de Azure.
      > 
      > 
    * Si usa SQL Server en una máquina virtual de Azure, se puede usar cualquier configuración de red virtual si la máquina virtual que hospeda SQL Server es miembro de la misma red virtual que HDInsight.
  * Para crear un clúster de HDInsight en una red virtual, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server también debe permitir la autenticación. Debe usar un inicio de sesión de SQL Server para completar los pasos de este artículo.
    > 
    > 

**Validar la configuración**

1. En Azure Portal, abra el grupo de recursos. Verá cuatro recursos en el grupo:

    - el clúster
    - el servidor de bases de datos
    - la base de datos
    - la cuenta de almacenamiento predeterminada

2. Abra la base de datos en Microsoft SQL Server Management Studio.  Verá que se implementan dos bases de datos:

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Ejecución de trabajos de Sqoop
HDInsight puede ejecutar trabajos de Sqoop mediante una variedad de métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Utilícelo** si desea... | ...un shell **interactivo** | ...procesamiento por**lotes** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux o Windows |Windows (por ahora) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux o Windows |Windows |

## <a name="limitations"></a>Limitaciones
* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se utiliza para exportar datos a Microsoft SQL Server o SQL Database Azure no es compatible actualmente con las inserciones masivas.
* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realiza varias inserciones en lugar de procesar por lotes las operaciones de inserción.

## <a name="next-steps"></a>pasos siguientes
Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

* [Uso de Hive con HDInsight](../hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](../hdinsight-use-pig.md)
* [Carga de datos en HDInsight][hdinsight-upload-data]: busque otros métodos para cargar datos en HDInsight o Azure Blob Storage.

## <a name="appendix-a---a-powershell-sample"></a>Apéndice A - un ejemplo de PowerShell
El ejemplo de PowerShell lleva a cabo los siguientes pasos:

1. Conéctese a Azure.
2. Cree un grupo de recursos de Azure. Para obtener más información, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Cree un servidor de Azure SQL Database, una instancia de Azure SQL Database y dos tablas. 
   
    Si usa SQL Server en su lugar, use las siguientes instrucciones para crear las tablas:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    La manera más sencilla de examinar la base de datos y las tablas es usar Visual Studio. El servidor de bases de datos y la base de datos se pueden examinar con el portal de Azure.
4. Cree un clúster de HDInsight.
   
    Para examinar el clúster, puede usar el portal de Azure o Azure PowerShell.
5. Preprocese el archivo de datos de origen.
   
    En este tutorial, se exporta un archivo de registro log4j (un archivo delimitado) y una tabla de Hive a una base de datos SQL de Azure. El archivo delimitado se llama */example/data/sample.log*. Anteriormente en este tutorial, se han mostrado algunos ejemplos de registros log4j. El archivo de registro tiene algunas líneas vacías y otras similares a las siguientes:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Esto es válido para otros ejemplos que usan estos datos, pero debemos quitar estas excepciones para poder importarlos a la base de datos SQL de Azure o a SQL Server. La exportación de Sqoop producirá un error si hay una cadena vacía o una línea con menos elementos que el total de campos definido en la tabla de bases de datos de Azure SQL. La tabla "log4jlogs" tiene siete campos de tipo cadena.
   
    Este procedimiento crea un archivo nuevo en el clúster: tutorials/usesqoop/data/sample.log. Para examinar el archivo de datos modificado, puede usar Azure Portal, una herramienta del explorador de Azure Storage o Azure PowerShell. [Introducción a HDInsight][hdinsight-get-started] incluye código de ejemplo para usar Azure PowerShell para descargar un archivo y mostrar su contenido.
6. Exportar un archivo de datos a la base de datos SQL de Azure.
   
    El archivo de origen es tutorials/usesqoop/data/sample.log. La tabla donde se exportan los datos se denomina log4jlogs.
   
   > [!NOTE]
   > Aparte de la información de la cadena de conexión, los pasos indicados en esta sección deben funcionar para una base de datos SQL de Azure o para SQL Server. Estos pasos se probaron con la siguiente configuración:
   > 
   > * **Configuración de punto a sitio de la red virtual de Azure**: red virtual que conecta el clúster de HDInsight a SQL Server en un centro privado de datos. Para obtener más información, consulte [Configuración de una VPN de punto a sitio en el Portal de administración](../../vpn-gateway/vpn-gateway-point-to-site-create.md) .
   > * **Azure HDInsight:**consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) para obtener más información sobre cómo crear un clúster en una red virtual.
   > * **SQL Server 2014**: configurado para permitir la autenticación y la ejecución del paquete de configuración de clientes VPN para conectarse de forma segura a la red virtual.
   > 
   > 
7. Exportar una tabla de Hive a la base de datos SQL de Azure.
8. Importe la tabla mobiledata al clúster de HDInsight.
   
    Para examinar el archivo de datos modificado, puede usar Azure Portal, una herramienta del explorador de Azure Storage o Azure PowerShell.  [Introducción a HDInsight][hdinsight-get-started] incluye código de ejemplo para usar Azure PowerShell para descargar un archivo y mostrar su contenido.

### <a name="the-powershell-sample"></a>El ejemplo de PowerShell

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "http://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

#region - Create Azure resouce group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
