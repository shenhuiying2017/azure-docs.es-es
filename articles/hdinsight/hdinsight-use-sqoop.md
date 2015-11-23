<properties
	pageTitle="Uso de Sqoop de Hadoop en HDInsight | Microsoft Azure"
	description="Aprenda a utilizar Azure PowerShell desde una estación de trabajo para ejecutar la importación y exportación en Sqoop entre un clúster de Hadoop y una base de datos SQL de Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="jgao"/>

#Uso de Sqoop con Hadoop en HDInsight (Windows)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar Azure PowerShell y el SDK .NET de HDInsight desde una estación de trabajo para ejecutar la importación y exportación en Sqoop entre un clúster de HDInsight y una base de datos SQL de Azure o una base de datos de SQL Server.

> [AZURE.NOTE]Los pasos descritos en este artículo pueden utilizarse con cualquier un clúster de HDInsight basado en Windows o Linux ; sin embargo, estos pasos sólo funcionarán desde un cliente Windows.
>
> Si utiliza un cliente Linux, OS X o Unix, y un servidor HDInsight basado en Linux, consulte [Uso de Sqoop con Hadoop en HDInsight (SSH)](hdinsight-use-sqoop-mac-linux.md)

##¿Qué es Sqoop?

A pesar de que Hadoop es una opción natural para procesar datos no estructurados y datos semiestructurados, como registros y archivos, es posible que también sea necesario procesar datos estructurados almacenados en bases de datos relacionales.

[Sqoop][sqoop-user-guide-1.4.4] es una herramienta diseñada para transferir datos entre clústeres de Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS. En este tutorial, usará una base de datos de SQL Server como base de datos relacional.

Para ver las versiones de Sqoop compatibles con los clústeres de HDInsight, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][hdinsight-versions].

##Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalación y uso de Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).
 Para ejecutar scripts de Azure PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Ejecución de scripts de Windows PowerShell][powershell-script].

- **Clúster de Azure HDInsight**: para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. Para completar el tutorial, necesita los datos siguientes:

	<table border="1">
	<tr><th>Propiedad del clúster</th><th>Nombre de variable de Azure PowerShell</th><th>Valor</th><th>Descripción</th></tr>
	<tr><td>Nombre del clúster de HDInsight</td><td>$clusterName</td><td></td><td>El nombre del clúster de HDInsight.</td></tr>
	<tr><td>Nombre de la cuenta de almacenamiento de Azure</td><td>$storageAccountName</td><td></td><td>Cuenta de almacenamiento de Azure disponible para el clúster de HDInsight. Para este tutorial, use la cuenta de almacenamiento predeterminada especificada durante el proceso de aprovisionamiento del clúster.</td></tr>
	<tr><td>Nombre del contenedor de blobs de Azure</td><td>$containerName</td><td></td><td>Para este ejemplo, use el nombre del blob utilizado para el sistema de archivos predeterminado del clúster de HDInsight. De manera predeterminada, tiene el mismo nombre que el del clúster de HDInsight.</td></tr>
	</table>

- ****Base de datos SQL de Azure o Microsoft SQL Server

	- **Base de datos SQL de Azure**: debe configurar una regla de firewall para que el servidor de base de datos SQL de Azure permita el acceso desde la estación de trabajo. Para obtener instrucciones sobre cómo crear una base de datos SQL de Azure y configurar el firewall, consulte [Introducción al uso de la base de datos SQL de Azure][sqldatabase-get-started]. En este artículo se proporciona un script de Windows PowerShell para crear la tabla de base de datos SQL de Azure requerida para este tutorial.
	
		<table border="1">
<tr><th>Propiedad de la base de datos SQL de Azure</th><th>Nombre de variable de Azure PowerShell</th><th>Valor</th><th>Descripción</th></tr>
<tr><td>Nombre del servidor de base de datos SQL de Azure</td><td>$sqlDatabaseServer</td><td></td><td>Servidor de base de datos SQL de Azure al que Sqoop exportará datos o desde el que los importará. </td></tr>
<tr><td>Nombre de inicio de sesión de la base de datos SQL de Azure</td><td>$sqlDatabaseLogin</td><td></td><td>El nombre de inicio de sesión de la base de datos SQL de Azure.</td></tr>
<tr><td>Contraseña de inicio de sesión de la base de datos SQL de Azure</td><td>$sqlDatabasePassword</td><td></td><td>La contraseña de inicio de sesión de la base de datos SQL de Azure.</td></tr>
<tr><td>Nombre de la base de datos SQL de Azure</td><td>$sqlDatabaseName</td><td></td><td>Base de datos SQL de Azure a la que Sqoop exportará datos o desde la que los importará. </td></tr>
</table>
> [AZURE.NOTE]De forma predeterminada, una base de datos SQL de Azure permite realizar conexiones desde servicios de Azure, como HDInsight de Azure. Si la configuración del firewall está deshabilitada, debe habilitarla en el portal de vista previa de Azure. Para obtener instrucciones sobre la creación de una base de datos SQL de Azure y la configuración de las reglas de firewall, consulte [Creación y configuración de una base de datos SQL][sqldatabase-create-configue].
	
	* **SQL Server**: si el clúster de HDInsight se encuentra en la misma red virtual de Azure que un SQL Server, puede seguir los pasos indicados en este artículo para importar y exportar datos a una base de datos de SQL Server.
	
		> [AZURE.NOTE]HDInsight solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.
	
		* Para crear y configurar una red virtual, consulte [Tareas de configuración de red virtual](../services/virtual-machines/).
	
			* Cuando use SQL Server en el centro de datos, debe configurar la red virtual como de *sitio a sitio* o de *punto a sitio*.
	
				> [AZURE.NOTE]En el caso d las redes virtuales de **punto a sitio**, SQL Server debe ejecutarse en la aplicación de configuración de clientes VPN, que se encuentra disponible en el **Panel** de la configuración de red virtual de Azure.
	
			* Si usa SQL Server en una máquina virtual de Azure, se puede usar cualquier configuración de red virtual si la máquina virtual que hospeda SQL Server es miembro de la misma red virtual que HDInsight.
	
		* Para aprovisionar un clúster de HDInsight en una red virtual, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight usando opciones personalizadas](hdinsight-provision-clusters.md)
	
		> [AZURE.NOTE]SQL Server también debe permitir la autenticación. Debe usar un inicio de sesión de SQL Server para completar los pasos de este artículo.
	
		<table border="1">
<tr><th>Propiedad de la base de datos de SQL Server</th><th>Nombre de variable de Azure PowerShell</th><th>Valor</th><th>Descripción</th></tr>
<tr><td>Nombre de SQL Server</td><td>$sqlDatabaseServer</td><td></td><td>SQL Server al que Sqoop exportará datos o desde el que los importará. </td></tr>
<tr><td>Nombre de inicio de sesión de SQL Server</td><td>$sqlDatabaseLogin</td><td></td><td>El nombre de inicio de sesión para SQL Server.</td></tr>
<tr><td>Contraseña de inicio de sesión de SQL Server</td><td>$sqlDatabasePassword</td><td></td><td>La contraseña de inicio de sesión para SQL Server.</td></tr>
<tr><td>Nombre de la base de datos de SQL</td><td>$sqlDatabaseName</td><td></td><td>Base de datos de SQL Server a la que Sqoop exportará datos o desde la que los importará. </td></tr>
</table>


> [AZURE.NOTE]Rellene los valores de las tablas anteriores. Le resultará útil para completar el tutorial.

##Descripción del escenario
Un clúster de HDInsight incluye algunos datos de ejemplo. Usará los dos ejemplos siguientes:

- Un archivo registro log4j, ubicado en */example/data/sample.log*. Los registros siguientes se extraen del archivo:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Una tabla de Hive denominada *hivesampletable* que hace referencia al archivo de datos ubicado en */hive/warehouse/hivesampletable*. La tabla contiene algunos datos del dispositivo móvil. El esquema de dicha tabla es el siguiente:

	<table border="1">
<tr><th>Campo</th><th>Tipo de datos</th></tr>
<tr><td>clientid</td><td>cadena</td></tr>
<tr><td>querytime</td><td>cadena</td></tr>
<tr><td>market</td><td>cadena</td></tr>
<tr><td>deviceplatform</td><td>cadena</td></tr>
<tr><td>devicemake</td><td>cadena</td></tr>
<tr><td>devicemodel</td><td>cadena</td></tr>
<tr><td>state</td><td>cadena</td></tr>
<tr><td>country</td><td>cadena</td></tr>
<tr><td>querydwelltime</td><td>double</td></tr>
<tr><td>sessionid</td><td>bigint</td></tr>
<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
</table>

En primer lugar, exportará tanto *sample.log* como *hivesampletable* a la base de datos de SQL de Azure o a SQL Server y, a continuación, importará la tabla que contiene los datos del dispositivo móvil de nuevo en HDInsight con la ruta de acceso siguiente:

	/tutorials/usesqoop/importeddata

###Descripción del almacenamiento de HDInsight

HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Para obtener más información, consulte [Uso de Almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

Cuando se aprovisiona un clúster de HDInsight, se designan una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs específico de dicha cuenta como sistema de archivos predeterminado, de la misma forma que en HDFS. Además de esta cuenta de almacenamiento, puede agregar más cuentas de almacenamiento desde la misma suscripción de Azure o desde otras diferentes durante el proceso de aprovisionamiento.

Para obtener instrucciones sobre cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. Para simplificar el script de Windows PowerShell que se usa en este tutorial, todos los archivos se almacenan en el contenedor del sistema de archivos predeterminado, ubicado en */tutorials/usesqoop*. De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster de HDInsight. La sintaxis es:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]La sintaxis **wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La antigua sintaxis **asv://* es compatible con los clústeres de HDInsight 2.1 y 1.6, pero no es compatible con los clústeres de HDInsight 3.0.

> [AZURE.NOTE]La ruta de acceso **wasb://* es una ruta de acceso virtual. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

Para acceder a un archivo almacenado en el blog del sistema de archivos predeterminado desde HDInsight se puede usar cualquiera de los URI siguientes (en los siguientes ejemplos se usa sample.log):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

	example/data/sample.log


##Preparación del tutorial

Creará dos tablas en la base de datos SQL de Azure o en SQL Server. Sqoop las usa para exportar más adelante en el tutorial. Asimismo, tiene que procesar los archivos sample.log para que Sqoop pueda procesarlos.

###Creación de tablas SQL

**Para una base de datos SQL de Azure**

1. Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell\_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Inicio de Windows PowerShell en Windows 8 y Windows][powershell-start]).

2. Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras cuatro variables:

		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

3. Anexe el script siguiente en el panel de scripts. Estas son las instrucciones SQL que definen las dos tablas y sus índices agrupados. La base de datos SQL de Azure requiere un índice agrupado.

		# SQL query strings for creating tables and clustered indexes
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

4. Anexe el script siguiente en el panel de scripts para ejecutar los comandos SQL:

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

5. Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6. Use el [Portal de vista previa][azure-management-portal] para examinar las tablas y los índices agrupados.

**Para SQL Server**

1. Abra **SQL Server Management Studio** y conéctese a SQL Server.

2. Cree una nueva base de datos denominada **sqoopdb**.

3. Seleccione la base de datos **sqoopdb** y luego elija **Nueva consulta** en la cinta de opciones situada en la parte superior de SQL Server Management Studio.

4. Introduzca la siguiente información en la ventana de consulta:

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

5. Haga clic en **F5** o seleccione **. Ejecute** en la cinta para ejecutar la consulta. Debe aparecer el siguiente mensaje debajo de la consulta:

		Command(s) completed successfully.

6. Cierre SQL Server Management Studio.

###Generación de datos

En este tutorial, exportará un archivo de registro log4j (un archivo delimitado) y una tabla de Hive a una base de datos SQL de Azure. El archivo delimitado se llama */example/data/sample.log*. Anteriormente en este tutorial, se han mostrado algunos ejemplos de registros log4j. El archivo de registro tiene algunas líneas vacías y otras similares a las siguientes:

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Esto es válido para otros ejemplos que usan estos datos, pero debemos quitar estas excepciones para poder importarlos a la base de datos SQL de Azure o a SQL Server. La exportación de Sqoop producirá un error si hay una cadena vacía o una línea con un número de elementos inferior al número de campos definido en la tabla de base de datos SQL de Azure. La tabla log4jlogs tiene 7 campos de tipo cadena.

**Para procesar el archivo sample.log**

1. Abra Windows PowerShell ISE.
2. En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

		Add-AzureAccount

	Se le pedirá que escriba las credenciales de la cuenta de Azure. Este método de agregar una conexión de suscripción expira y, transcurridas 12 horas, tendrá que volver a iniciar sesión.

	> [AZURE.NOTE]Si tiene varias suscripciones de Azure y no desea usar la predeterminada, use el cmdlet <strong>Select-AzureSubscription</strong> para seleccionar la suscripción actual.

3. Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras dos variables:

		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4. Anexe el script siguiente en el panel de scripts:

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)

		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream

		# process the source blob
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

5. Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6. Para examinar el archivo de datos modificado, puede usar el portal de vista previa, una herramienta del explorador de almacenamiento de Azure o Azure PowerShell. [Introducción a HDInsight][hdinsight-get-started] incluye código de ejemplo para usar Azure PowerShell para descargar un archivo y mostrar el contenido del mismo.


##Uso de PowerShell para ejecutar la exportación en Sqoop

En esta sección, usará Azure PowerShell para ejecutar el comando de exportación de Sqoop a fin de exportar una tabla de Hive y un archivo de datos a una base de datos SQL de Azure o a SQL Server. En la siguiente sección se ofrece un ejemplo de .NET de HDInsight.

> [AZURE.NOTE]Aparte de la información de la cadena de conexión, los pasos indicados en esta sección deben funcionar para una base de datos SQL de Azure o para SQL Server. Estos pasos se probaron con la siguiente configuración:
>
> * **Configuración de punto a sitio de la red virtual de Azure**: red virtual que conecta el clúster de HDInsight a SQL Server en un centro privado de datos. Para obtener más información, consulte[ Configuración de una VPN de punto a sitio en el Portal de administración](../vpn-gateway/vpn-gateway-point-to-site-create.md).
> * **Azure HDInsight 3.1**: consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight usando opciones personalizadas](hdinsight-provision-clusters.md) para obtener información sobre la creación de un clúster en una red virtual.
> * **SQL Server 2014**: configurado para permitir la autenticación y la ejecución del paquete de configuración de clientes VPN para conectarse de forma segura a la red virtual.

**Para exportar el archivo de registro log4j**

1. Abra Windows PowerShell ISE.
2. En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

		Add-AzureAccount

	Se le pedirá que escriba las credenciales de la cuenta de Azure.

3. Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras siete variables:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_log4j = "/tutorials/usesqoop/data"

	Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

	Observe que $exportDir\_log4j no tiene especificado el nombre del archivo sample.log. Sqoop exportará los datos de todos los archivos ubicados en esa carpeta.

4. Anexe el script siguiente en el panel de scripts:

		# Submit a Sqoop job
		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureRmHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureRmHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureRmHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureRmHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Tenga en cuenta el delimitador de campo es**\\0x20**, que equivale a un espacio. El delimitador se define en el script de Azure PowerShell de archivo sample.log. Para obtener información sobre el valor**-m 1**, consulte el [manual del usuario de Sqoop (en inglés)][sqoop-user-guide-1.4.4].

5. Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6. Use el [Portal de vista previa][azure-management-portal] para examinar los datos exportados.

**Para exportar la tabla de Hive hivesampletable**

1. Abra Windows PowerShell ISE.
2. En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

		Add-AzureAccount

	Se le pedirá que escriba las credenciales de la cuenta de Azure.

3. Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras siete variables:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_mobile = "/hive/warehouse/hivesampletable"

	Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4. Anexe el script siguiente en el panel de scripts:

		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


		$sqoopJob = Start-AzureRmHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureRmHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureRmHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureRmHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6. Use el [Portal de vista previa][azure-management-portal] para examinar los datos exportados.

##Uso del SDK .NET de HDInsight para ejecutar la exportación en Sqoop

En esta sección, creará una aplicación de consola de C# para exportar hivesampletable a la tabla de Base de datos SQL que creó anteriormente en este tutorial.

**Para enviar un trabajo de Sqoop**

1. En la Consola del administrador de paquetes de Visual Studio, ejecute el siguiente comando de Nuget para importar el paquete.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Use las siguientes instrucciones using en el archivo Program.cs:

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
3. En la función Main(), agregue el código siguiente. Si desea obtener información general acerca de cómo usar el SDK .NET de HDInsight, consulte [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs].

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		var sqlDatabaseServerName = "<AzureSQLDatabaseServerName>";
		var sqlDatabaseLogin = "<AzureSQLDatabaseLogin>";
		var sqlDatabaseLoginPassword = "<AzureSQLDatabaseLoginPassword>";
		var sqlDatabaseDatabaseName = "<AzureSQLDatabaseDatabaseName>";
		
		var sqlDatabaseTableName = "log4jlogs";
		var exportDir = "/hive/warehouse/hivesampletable";

		var cmdExport = @"export";
		// Connection string for using Azure SQL Database.
		// Comment if using SQL Server
		cmdExport = cmdExport + @" --connect 'jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName +"'"; 
		// Connection string for using SQL Server.
		// Uncomment if using SQL Server
		//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
		cmdExport = cmdExport + @" --export-dir " + exportDir;
		cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
		
		HDInsightJobManagementClient _hdiJobManagementClient;
		var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

		var parameters = new SqoopJobSubmissionParameters
		{
		    UserName = ExistingClusterUsername,
		    Command = cmdExport
		};
		
		System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
		System.Console.WriteLine("Validating that the response is as expected...");
		System.Console.WriteLine("Response status code is " + response.StatusCode);
		System.Console.WriteLine("Validating the response object...");
		System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		Console.WriteLine("Press ENTER to continue ...");
		Console.ReadLine();
4. Presione **F5** para ejecutar el programa. 

##Uso de Azure PowerShell para ejecutar la importación en Sqoop

En esta sección, volverá a importar los registros log4j (que ha exportado a la base de datos SQL de Azure) en HDInsight.

1. Abra Windows PowerShell ISE.
2. En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

		Add-AzureAccount

	Se le pedirá que escriba las credenciales de la cuenta de Azure.

3. Copie el script siguiente en el panel de scripts y, a continuación, establezca las primeras siete variables:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

	Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4. Anexe el script siguiente en el panel de scripts:

		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

		$sqoopJob = Start-AzureRmHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureRmHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureRmHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDRmInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script.
6. Para examinar el archivo de datos modificado, puede usar el portal de vista previa, una herramienta del explorador de almacenamiento de Azure o Azure PowerShell. [Introducción a HDInsight][hdinsight-get-started] incluye código de ejemplo sobre el uso de Azure PowerShell para descargar un archivo y mostrar el contenido del mismo.

##Pasos siguientes

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

- [Uso de Oozie con HDInsight][hdinsight-use-oozie]\: use la acción Sqoop en un flujo de trabajo de Oozie.
- [Análisis de la información de retraso de vuelos con HDInsight][hdinsight-analyze-flight-data]\: use Hive para analizar la información de retraso de los vuelos y luego use Sqoop para exportar los datos a una base de datos SQL de Azure.
- [Carga de datos en HDInsight][hdinsight-upload-data]\: busque otros métodos para cargar datos en HDInsight o el almacenamiento de blobs de Azure.




[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=Nov15_HO3-->