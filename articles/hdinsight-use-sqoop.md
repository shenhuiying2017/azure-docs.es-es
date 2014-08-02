<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="Use Sqoop with HDInsight Samples" pageTitle="Use Sqoop with HDInsight | Azure" metaKeywords="" description="Learn how to use Azure PowerShell from a workstation to run Sqoop import and export between an HDInsight cluster and an Azure SQL database." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Sqoop with HDInsight" authors="jgao" />

Uso de Sqoop con HDInsight
==========================

Aprenda a utilizar Azure PowerShell y el SDK .NET de HDInsight desde una estación de trabajo para ejecutar la importación y exportación en Sqoop entre un clúster de HDInsight y una base de datos SQL de Azure.

**Duración aproximada:** 30 minutos

En este artículo
----------------

-   [¿Qué es Sqoop?](#whatissqoop)
-   [Requisitos previos](#prerequisites)
-   [Comprensión del escenario del tutorial](#scenario)
-   [Preparación del tutorial](#prepare)
-   [Uso de PowerShell para ejecutar la exportación en Sqoop](#export)
-   [Uso del SDK de HDInsight para ejecutar la exportación en Sqoop](#export-sdk)
-   [Uso de PowerShell para ejecutar la importación en Sqoop](#import)
-   [Pasos siguientes](#nextsteps)

¿Qué es Sqoop?
--------------

A pesar de que Hadoop es una opción natural para procesar datos no estructurados y datos semiestructurados, como registros y archivos, es posible que también sea necesario procesar datos estructurados almacenados en bases de datos relacionales.

[Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) es una herramienta diseñada para transferir datos entre clústeres de Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como SQL, MySQL u Oracle en el sistema de archivos distribuidos Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS. En este tutorial, utilizará una base de datos SQL como base de datos relacional.

Para ver las versiones de Sqoop compatibles con los clústeres de HDInsight, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](../hdinsight-component-versioning/).

Requisitos previos
------------------

Antes de empezar este tutorial, debe tener lo siguiente:

-   Una **estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell). Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts](http://technet.microsoft.com/en-us/library/ee176949.aspx).

-   **Un clúster de HDInsight de Azure**. Para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight de Azure](../hdinsight-get-started/) o [Aprovisionamiento de clústeres de HDInsight](../hdinsight-provision-clusters/). Para completar el tutorial, necesitará los datos siguientes:

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propiedad del cl&uacute;ster</th><th data-morhtml="true">Nombre de variable de PowerShell</th><th data-morhtml="true">Valor</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre del cl&uacute;ster de HDInsight</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">Este es el nombre del cl&uacute;ster de HDInsight.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de la cuenta de almacenamiento de Azure</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">Cuenta de almacenamiento de Azure disponible para el cl&uacute;ster de HDInsight. Para este tutorial, use la cuenta de almacenamiento predeterminada especificada durante el proceso de aprovisionamiento del cl&uacute;ster.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre del contenedor de blobs de Azure</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">Para este ejemplo, use el contenedor de almacenamiento de blobs de Azure utilizado para el sistema de archivos predeterminado del cl&uacute;ster de HDInsight. De manera predeterminada, tiene el mismo nombre que el del cl&uacute;ster de HDInsight.</td></tr>
  </table>
-   Una **Base de datos SQL de Azure**. Debe configurar una regla de firewall para que el servidor de Base de datos SQL permita el acceso desde la estación de trabajo. Para obtener instrucciones acerca de cómo crear una base de datos SQL y configurar el firewall, consulte [Introducción al uso de la base de datos SQL de Microsoft Azure](../sql-database-get-started/). En este artículo se proporciona un script de PowerShell para crear la tabla de base de datos SQL requerida para este tutorial.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propiedad de la base de datos SQL</th><th data-morhtml="true">Nombre de variable de PowerShell</th><th data-morhtml="true">Valor</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre del servidor de base de datos SQL</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Servidor de Base de datos SQL al que Sqoop exportar&aacute; datos o desde el que los importar&aacute;. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de inicio de sesi&oacute;n de la base de datos SQL</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">Nombre de inicio de sesi&oacute;n de la base de datos SQL.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Contrase&ntilde;a de inicio de sesi&oacute;n de la base de datos SQL</td><td data-morhtml="true">$sqlDatabasePassword</td><td data-morhtml="true"></td><td data-morhtml="true">Contrase&ntilde;a de inicio de sesi&oacute;n de la base de datos SQL.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de la base de datos SQL</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Base de datos SQL de Azure a la que Sqoop exportar&aacute; datos o desde la que los importar&aacute;. </td></tr>
  </table>

    > [WACOM.NOTE] De forma predeterminada, una base de datos SQL de Azure permite realizar conexiones desde servicios de Azure como HDInsight. Si la configuración del firewall está deshabilitada, debe habilitarla en el Portal de administración de Azure. Para obtener instrucciones acerca de la creación de una base de datos SQL y la configuración de las reglas de firewall, consulte [Creación y configuración de una base de datos SQL](../sql-database-create-configure/).

> [WACOM.NOTE] Complete los valores de las tablas, le resultará útil para completar el tutorial.

Comprensión del escenario
-------------------------

El clúster de HDInsight incluye algunos datos de ejemplo. Usará los dos siguientes:

-   Un archivo de registro log4j ubicado en */example/data/sample.log*. Los registros siguientes se extraen del archivo:

          2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
          2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
          2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
          ...

-   Una tabla de Hive denominada *hivesampletable* que hace referencia al archivo de datos ubicado en */hive/warehouse/hivesampletable*. La tabla contiene algunos datos del dispositivo móvil. El esquema de dicha tabla es el siguiente:

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Campo</th><th data-morhtml="true">Tipo de datos</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">clientid</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">querytime</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">market</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">deviceplatform</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">devicemake</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">devicemodel</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">state</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">country</td><td data-morhtml="true">cadena</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">querydwelltime</td><td data-morhtml="true">double</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">sessionid</td><td data-morhtml="true">bigint</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">sessionpagevieworder</td><td data-morhtml="true">bigint</td></tr>
  </table>

En primer lugar, exportará tanto sample.log como hivesampletable a la base de datos SQL y, a continuación, importará la tabla que contiene los datos del dispositivo móvil de nuevo en HDInsight con la ruta de acceso siguiente:

    /tutorials/usesqoop/importeddata

### Descripción del almacenamiento de HDInsight

HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage/).

Cuando se aprovisiona un clúster de HDInsight, se designan una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs específico de dicha cuenta como sistema de archivos predeterminado, de la misma forma que en HDFS. Además de esta cuenta de almacenamiento, puede agregar más cuentas de almacenamiento desde la misma suscripción de Azure o desde otras diferentes durante el proceso de aprovisionamiento. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight](../hdinsight-provision-clusters/). Para simplificar el script de PowerShell que se utiliza en este tutorial, todos los archivos se almacenan en el contenedor del sistema de archivos predeterminado, ubicado en */tutorials/usesqoop*. De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster de HDInsight. La sintaxis de WASB es la siguiente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] La sintaxis *wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La sintaxis *asv://* anterior es compatible con los clústeres de las versiones 2.1 y 1.6 de HDInsight, pero no con los de la versión 3.0, y no será compatible con versiones posteriores.

> [WACOM.NOTE] La ruta WASB es una ruta de acceso virtual. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage/).

Para tener acceso a un archivo almacenado en el contenedor del sistema de archivos predeterminado desde HDInsight se puede usar cualquiera de los URI siguientes (use sample.log como ejemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

    example/data/sample.log

Preparación del tutorial
------------------------

Va a crear dos tablas de bases de datos SQL que se utilizarán en la exportación de Sqoop más adelante en este tutorial. Asimismo, tendrá que realizar tareas de procesamiento previo en el archivo sample.log para que Sqoop pueda procesarlo.

**Para crear una tabla de base de datos SQL**

1.  Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell\_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Start Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).

2.  Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras cuatro variables:

         #Variables de base de datos SQL
         $sqlDatabaseServer = "<SQLDatabaseServerName>" 
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseName = "<SQLDatabaseName>" 

         $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

3.  Anexe el script siguiente en el panel de scripts. Estas son las instrucciones SQL que definen las dos tablas y sus índices agrupados. La Base de datos SQL requiere un índice agrupado.

         # Cadenas de consulta de SQL para crear tablas e índices agrupados
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

4.  Anexe el script siguiente en el panel de scripts para ejecutar los comandos SQL:

         Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
         $conn = New-Object System.Data.SqlClient.SqlConnection
         $conn.ConnectionString = $sqlDatabaseConnectionString
         $conn.Open()
            
         Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
         $cmd = New-Object System.Data.SqlClient.SqlCommand
         $cmd.Connection = $conn
         $cmd.CommandText = $cmdCreateLog4jTable
         $ret = $cmd.ExecuteNonQuery()
         $cmd.CommandText = $cmdCreateLog4jClusteredIndex
         $cmd.ExecuteNonQuery()
            
         Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
         $cmd.CommandText = $cmdCreateMobileTable
         $cmd.ExecuteNonQuery()
         $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
         $cmd.ExecuteNonQuery()
            
         Write-Host "Close connection ..." -ForegroundColor Green     
         $conn.close()
            
         Write-Host "Done" -ForegroundColor Green

5.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6.  Use el [Portal de administración de Azure](https://manage.windowsazure.com/) para examinar las tablas y los índices agrupados.

En este tutorial, exportará un archivo de registro log4j (un archivo delimitado) y una tabla de Hive a una Base de datos SQL. El archivo delimitado es */example/data/sample.log*. En este tutorial se han mostrado algunos ejemplos de registros log4j. El archivo de registro tiene algunas líneas vacías y otras similares a las siguientes:

    java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
        at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Puede que sea conveniente quitar *java.lang.Exception:* de la fila para poder analizar el registro correctamente.

La exportación de Sqoop producirá un error si hay una cadena vacía o una línea con un número de elementos inferior al número de campos definido en la tabla de base de datos SQL. La tabla log4jlogs tiene 7 campos de tipo cadena.

**Para preprocesar el archivo sample.log**

1.  Abra Windows PowerShell ISE.
2.  En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure. Este método de agregar una conexión de suscripción expira y, transcurridas 12 horas, tendrá que volver a iniciar sesión.

    > [WACOM.NOTE] Si tiene varias suscripciones de Azure y no desea usar la predeterminada, utilice el cmdlet **Select-AzureSubscription** para seleccionar la suscripción actual.

3.  Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras dos variables:

         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<BlobContainerName>"
            
         $sourceBlobName = "example/data/sample.log"
         $destBlobName = "tutorials/usesqoop/data/sample.log"

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4.  Anexe el script siguiente en el panel de scripts:

         # Definir la cadena de conexión
         $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
            
         # Crear objetos de blob en bloques que hagan referencia al blob de origen y de destino.
         $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
         $storageClient = $storageAccount.CreateCloudBlobClient();
         $storageContainer = $storageClient.GetContainerReference($containerName)
         $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
         $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
            
         # Definir un objeto MemoryStream y StreamReader para leer del archivo de origen
         $stream = New-Object System.IO.MemoryStream
         $stream = $sourceBlob.OpenRead()
         $sReader = New-Object System.IO.StreamReader($stream)
            
         # Definir un objeto MemoryStream y StreamWriter para escribir en el archivo de destino
         $memStream = New-Object System.IO.MemoryStream
         $writeStream = New-Object System.IO.StreamWriter $memStream
            
         # Procesar el blob de origen
         $exString = "java.lang.Exception:"
         while(-Not $sReader.EndOfStream){
             $line = $sReader.ReadLine()
             $split = $line.Split(" ")
            
             # Quitar "java.lang.Exception" del primer elemento de la matriz
             # Por ejemplo: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
             if ($split[0] -eq $exString){
                 # Crear un nuevo elemento ArrayList para quitar $split[0]
                 $newArray = [System.Collections.ArrayList] $split
                 $newArray.Remove($exString)
            
                 # Actualizar $split y $line
                 $split = $newArray
                 $line = $newArray -join(" ")
             }
            
             # Quitar las líneas con menos de 7 elementos
             if ($split.count -ge 7){
                 write-host $line
                 $writeStream.WriteLine($line)
             }
         }
            
         # Escribir en el blob de destino
         $writeStream.Flush()
         $memStream.Seek(0, "Begin")
         $destBlob.UploadFromStream($memStream)

5.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6.  Para examinar el archivo de datos modificado, puede usar el Portal de administración de Azure, una herramienta del explorador de almacenamiento de Azure o Azure PowerShell. [Introducción a HDInsight](../hdinsight-get-started/) incluye código de ejemplo sobre cómo usar PowerShell para descargar un archivo y mostrar el contenido del mismo.

Uso de PowerShell para ejecutar la exportación en Sqoop
-------------------------------------------------------

En esta sección, usará Azure PowerShell para ejecutar el comando de exportación de Sqoop a fin de exportar una tabla de Hive y un archivo de datos a una base de datos SQL de Azure. En la siguiente sección se ofrece un ejemplo de .NET de HDInsight.

**Para exportar el archivo de registro log4j**

1.  Abra Windows PowerShell ISE.
2.  En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras siete variables:

         # Definir las variables del clúster
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Definir las variables de la base de datos SQL
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "<SQLDatabaseName>"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_log4j = "/tutorials/usesqoop/data"

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

    Observe que \$exportDir\_log4j no tiene especificado el nombre del archivo sample.log. Sqoop exportará los datos de todos los archivos ubicados en esa carpeta.

4.  Anexe el script siguiente en el panel de scripts:

         # Enviar un trabajo de Sqoop
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by  # Enviar un trabajo de Sqoop
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput
        x20 -m 1"
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

    Observe que el delimitador de campo es **\\0x20**, que equivale a un espacio. El delimitador se define en el script de PowerShell de preprocesamiento del archivo sample.log. Para obtener información sobre el valor **-m 1**, consulte el [manual del usuario de Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) (en inglés).

5.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6.  Use el [Portal de administración de Azure](https://manage.windowsazure.com/) para examinar los datos exportados.

**Para exportar la tabla de Hive hivesampletable**

1.  Abra Windows PowerShell ISE.
2.  En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras siete variables:

         # Definir las variables del clúster
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Definir las variables de la base de datos SQL
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_mobile = "mobiledata"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_mobile = "/hive/warehouse/hivesampletable"

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4.  Anexe el script siguiente en el panel de scripts:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
            
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6.  Use el [Portal de administración de Azure](https://manage.windowsazure.com/) para examinar los datos exportados.

Uso del SDK .NET de HDInsight para ejecutar la exportación en Sqoop
-------------------------------------------------------------------

A continuación se muestra un ejemplo de C\# en el que se usa el SDK .NET de HDInsight para ejecutar la exportación en Sqoop. Si desea obtener información general acerca de cómo usar el SDK .NET de HDInsight, consulte [Envío de trabajos de Hadoop mediante programación](../hdinsight-submit-hadoop-jobs-programmatically/).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;
    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace sqoopSDKSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Establecer las variables
                string subscriptionID = "<WindowsAzureSubscriptionID>";
                string clusterName = "<HDInsightClusterName>";
                string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
                string sqlDatabaseServerName = "<SQLDatabaseServerName>";
                string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
                string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                string sqlDatabaseDatabaseName = "hdisqoop";
                string sqlDatabaseTableName = "log4jlogs";

                cmdExport = @"export";
                cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
                cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
                cmdExport = cmdExport + @" --input-fields-terminated-by using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;
    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace sqoopSDKSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Establecer las variables
                string subscriptionID = "<WindowsAzureSubscriptionID>";
                string clusterName = "<HDInsightClusterName>";
                string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
                string sqlDatabaseServerName = "<SQLDatabaseServerName>";
                string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
                string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                string sqlDatabaseDatabaseName = "hdisqoop";
                string sqlDatabaseTableName = "log4jlogs";

                cmdExport = @"export";
                cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
                cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
                cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

                SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
                {
                    Command = cmdExport,
                    StatusFolder = "/tutorials/usesqoop/jobStatus"
                };

                // Obtener el objeto de certificado del almacén de certificados usando el nombre descriptivo para identificarlo
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Enviar el trabajo de Hive
                var jobClient = JobSubmissionClientFactory.Connect(creds);
                JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

                // Esperar a que se haya completado el trabajo
                WaitForJobCompletion(jobResults, jobClient);

                // Imprimir el resultado del trabajo de Hive
                System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

                StreamReader reader = new StreamReader(stream);
                Console.WriteLine(reader.ReadToEnd());

                Console.WriteLine("Press ENTER to continue.");
                Console.ReadLine();
            }

            private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
            {
                JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                {
                    jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));
                }
            }
        }
    }
    x20 -m 1";

                SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
                {
                    Command = cmdExport,
                    StatusFolder = "/tutorials/usesqoop/jobStatus"
                };

                // Obtener el objeto de certificado del almacén de certificados usando el nombre descriptivo para identificarlo
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Enviar el trabajo de Hive
                var jobClient = JobSubmissionClientFactory.Connect(creds);
                JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

                // Esperar a que se haya completado el trabajo
                WaitForJobCompletion(jobResults, jobClient);

                // Imprimir el resultado del trabajo de Hive
                System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

                StreamReader reader = new StreamReader(stream);
                Console.WriteLine(reader.ReadToEnd());

                Console.WriteLine("Press ENTER to continue.");
                Console.ReadLine();
            }

            private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
            {
                JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                {
                    jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));
                }
            }
        }
    }

Para ejecutar un archivo de script, puede reemplazar:

    Command = cmdExport,

por:

    File = "/tutorials/usesqoop/sqoopexport.txt",

El archivo de script debe estar ubicado en WASB.

Uso de PowerShell para ejecutar la importación en Sqoop
-------------------------------------------------------

En esta sección, volverá a importar los registros log4j (que ha exportado a la Base de datos SQL) en HDInsight.

1.  Abra Windows PowerShell ISE.
2.  En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras siete variables:

         # Definir las variables del clúster
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Definir las variables de la base de datos SQL
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $tableName_mobile = "mobiledata"
         $targetDir_mobile = "/tutorials/usesqoop/importeddata/"

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4.  Anexe el script siguiente en el panel de scripts:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6.  Para examinar el archivo de datos modificado, puede usar el Portal de administración de Azure, una herramienta del explorador de almacenamiento de Azure o Azure PowerShell. [Introducción a HDInsight](../hdinsight-get-started/) incluye código de ejemplo sobre cómo usar PowerShell para descargar un archivo y mostrar el contenido del mismo.

Pasos siguientes
----------------

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

-   [Uso de Oozie con HDInsight](../hdinsight-use-oozie/): Use una acción de Sqoop en un flujo de trabajo de Oozie.
-   [Análisis de la información de retraso de vuelos usando HDInsight](../hdinsight-analyze-flight-delay-data/): Use Hive para analizar la información de retraso de los vuelos y, a continuación, use Sqoop para exportar los datos a una base de datos SQL.
-   [Carga de datos en HDInsight](../hdinsight-upload-data/): Busque otros métodos para cargar datos en HDInsight (almacenamiento de blobs de Azure).

