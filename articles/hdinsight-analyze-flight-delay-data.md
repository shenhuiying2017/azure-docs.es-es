<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with HDInsight" pageTitle="Analyze flight delay data using HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Análisis de la información de retraso de vuelos usando HDInsight
================================================================

Hive ofrece el modo de ejecutar un trabajo de MapReduce mediante un lenguaje de scripting de tipo SQL, llamado *[HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL)*, que se puede aplicar al resumen, la consulta y el análisis de grandes volúmenes de datos. En este tutorial se muestra cómo utilizar Hive para calcular retrasos promedio entre aeropuertos y cómo utilizar Sqoop para exportar los resultados a la Base de datos SQL.

**Requisitos previos:**

Antes de empezar este tutorial, debe tener lo siguiente:

-   Un clúster de HDInsight de Azure. Para obtener información acerca de cómo aprovisionar un clúster de HDInsight, consulte [Introducción a HDInsight](/es-es/documentation/articles/hdinsight-get-started/) o [Aprovisionamiento de clústeres de HDInsight](/es-es/documentation/articles/hdinsight-provision-clusters/).
-   Una estación de trabajo con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).

**Duración aproximada:** 30 minutos

Apartados de este tutorial
--------------------------

-   [Preparación del tutorial](#prepare)
-   [Creación y carga del script de HiveQL](#createscript)
-   [Ejecución del script de HiveQL](#executehqlscript)
-   [Exportación del resultado a la Base de datos SQL de Azure](#exportdata)
-   [Pasos siguientes](#nextsteps)

Preparación del tutorial
------------------------

En este tutorial se utiliza el rendimiento puntual de los datos de vuelos de aerolíneas de [Research and Innovative Technology Administration, Bureau of Transportation Statistics](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA) en su estación de trabajo. Realizará lo siguiente:

1.  descargar datos de rendimiento puntual desde RITA a su estación de trabajo con un explorador web
2.  cargar los datos a HDInsight usando Azure PowerShell
3.  preparar la Base de datos SQL para la exportación de datos usando Azure PowerShell

**Descripción del almacenamiento de HDInsight**

HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/documentation/articles/hdinsight-use-blob-storage/).

Cuando aprovisiona un clúster de HDInsight, se designa un contenedor de almacenamiento de blobs como sistema de archivos predeterminado, al igual que en HDFS. Además de este contenedor, puede agregar más contenedores desde la misma cuenta de almacenamiento de Azure o desde otras diferentes durante el proceso de aprovisionamiento. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight](/es-es/documentation/articles/hdinsight-provision-clusters/).

Para simplificar el script de PowerShell que se utiliza en este tutorial, todos los archivos se almacenan en el contenedor del sistema de archivos predeterminado, ubicado en */tutorials/flightdelays*. De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster de HDInsight.

La sintaxis de WASB es la siguiente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] La sintaxis *wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La sintaxis *asv://* anterior es compatible con los clústeres de las versiones 2.1 y 1.6 de HDInsight, pero no con los de la versión 3.0, y no será compatible con versiones posteriores.

> La ruta de acceso WASB es la ruta de acceso virtual. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/documentation/articles/hdinsight-use-blob-storage/).

Para tener acceso a un archivo almacenado en el contenedor del sistema de archivos predeterminado desde HDInsight se puede usar cualquiera de los URI siguientes (use flightdelays.hql como ejemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

    tutorials/flightdelays/flightdelays.hql

La siguiente tabla enumera los archivos utilizados en este tutorial:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Archivos</strong></td><td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\tutorials\flightdelays\data</td><td data-morhtml="true">datos de vuelos de entrada para Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\tutorials\flightdelays\output</td><td data-morhtml="true">salida para Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\tutorials\flightdelays\flightdelays.hql</td><td data-morhtml="true">archivo de script de HiveQL</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\tutorials\flightdelays\jobstatus</td><td data-morhtml="true">estado del trabajo de Hadoop</td></tr>
</table>


**Descripción de la tabla interna y externa de Hive**

Existen determinados aspectos que debe conocer en relación con la tabla interna y externa de Hive:

-   El comando CREATE TABLE crea una tabla interna. El archivo de datos debe ubicarse en el contenedor predeterminado.
-   El comando CREATE TABLE mueve el archivo de datos a la carpeta /hive/warehouse/.
-   El comando CREATE EXTERNAL TABLE crea una tabla externa. El archivo de datos puede estar ubicado fuera del contenedor predeterminado.
-   El comando CREATE EXTERNAL TABLE no mueve el archivo de datos.
-   El comando CREATE EXTERNAL TABLE no permite ninguna carpeta en LOCATION. Este es el motivo por el que el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: Hive Internal and External Tables Intro][cindygross-hive-tables].

> [WACOM.NOTE] Una de las instrucciones de HiveQL crea una tabla externa de Hive. La tabla externa de Hive conserva el archivo de datos en la ubicación original. La tabla interna de Hive mueve el archivo de datos a hive\\warehouse. La tabla externa de Hive requiere que el archivo de datos se encuentre en el contenedor WASB del sistema de archivos predeterminado. Si selecciona almacenar los archivos de datos de vuelo en un contenedor diferente al contenedor de blobs predeterminado, debe utilizar las tablas internas de Hive.

**Para descargar los datos de vuelo**

1.  Diríjase a [Research and Innovative Technology Administration, Bureau of Transportation Statistics](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA)
2.  En la página, seleccione los siguientes valores:

 <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Nombre</th><th data-morhtml="true">Valor</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">Filter Year</td><td data-morhtml="true">2012</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">Filter Period</td><td data-morhtml="true">January</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">Fields:</td><td data-morhtml="true">*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (borrar todos los dem&aacute;s campos)</td></tr>
 </table>


3.  Haga clic en **Download**. Cada archivo puede tardar hasta 15 minutos en descargarse.
4.  Descomprima el archivo en la carpeta **C:\\Tutorials\\FlightDelays\\Data**. Cada archivo es un archivo CSV y tiene un tamaño aproximado de 60 GB.
5.  Cambie el nombre del archivo al nombre del mes que contiene los datos. Por ejemplo, el archivo que contiene los datos de enero se llamaría *Enero.csv*.
6.  Repita el paso 2 y 5 para descargar un archivo para cada uno de los 12 meses de 2012. Necesitará como mínimo un archivo para ejecutar el tutorial.

**Para cargar los datos de retraso de vuelos en el almacenamiento de blobs de Azure**

1.  Abra Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos.

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $ContainerName = "<BlobStorageContainerName>"
            
         $localFolder = "C:\Tutorials\FlightDelays\Data"
         $destFolder = "tutorials/flightdelays/data"
            
         $month = "January", "Feburary", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    Estas son las variables y su descripción:

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Nombre de la variable</strong></td><td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$subscriptionName</td><td data-morhtml="true">Su nombre de suscripci&oacute;n a Azure.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true">La cuenta de almacenamiento de Azure que se usa para almacenar los archivos de datos de vuelo. Se recomienda utilizar la cuenta de almacenamiento predeterminada.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$containerName</td><td data-morhtml="true">El contenedor de almacenamiento de blobs de Azure que se utiliza para almacenar los archivos de datos de vuelo. Se recomienda utilizar el contenedor de blobs del sistema de archivos de cl&uacute;ster de HDInsight predeterminado.  De manera predeterminada, tiene el mismo nombre que el del cl&uacute;ster de HDInsight.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$localFolder</td><td data-morhtml="true">Esta es la carpeta de la estaci&oacute;n de trabajo donde se almacenan los archivos de retrasos de vuelos.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$destFolder</td><td data-morhtml="true">Esta es la ruta de acceso de WASB donde se cargar&aacute;n los datos de retraso de los vuelos. La ruta de acceso de Hadoop (HDInsight) distingue may&uacute;sculas de min&uacute;sculas.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$month</td><td data-morhtml="true">Si no descarg&oacute; los 12 archivos, necesita actualizar esta variable.</td></tr>
 </table>


4.  Ejecute los siguientes comandos para cargar y enumerar los archivos:

         # Crear un objeto de contexto de almacenamiento
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         # Copiar el archivo desde la estación de trabajo local a WASB        
         foreach ($item in $month){
             $fileName = "$localFolder\$item.csv"
             $blobName = "$destFolder/$item.csv"
            
             Write-Host "Copying $fileName to $blobName" -BackgroundColor Green
            
             Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
         }
            
         # Enumerar los archivos cargados en HDinsight
         Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

Si decide utilizar un método diferente para cargar los archivos, asegúrese de que la ruta de acceso del archivo sea *tutorials/flightdelays/data*. La sintaxis para tener acceso a los archivos es:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* es la carpeta virtual creada cuando se cargan los archivos. Compruebe que haya 12 archivos, uno para cada mes.

**Para preparar la Base de datos SQL**

1.  Abra Azure PowerShell.
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Establezca las seis primeras variables en el siguiente script y, a continuación, ejecute los comandos:

         # Nombre de suscripción de Azure
         $subscriptionName = "<WindowsAzureSubscriptionName>"
                    
         # Variables del servidor de Base de datos SQL
         $sqlDatabaseServer = ""  # Especificar el nombre del servidor de Base de datos SQL de Azure si ha creado uno. De lo contrario, use "".
         $sqlDatabaseUsername = "<SQLDatabaseUserName>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #Por ejemplo, Oeste de EE. UU.
            
         # Variables de Base de datos SQL
         $sqlDatabaseName = ""  # Especificar el nombre de la base de datos si ha creado una.  De lo contrario, use "" para que el script cree uno automáticamente.
         $sqlDatabaseMaxSizeGB = 10

         #Cadena de consulta de SQL para crear una tabla AvgDelays
         $sqlDatabaseTableName = "AvgDelays"
         $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                 [origin_city_name] [nvarchar](50) NOT NULL,
                 [weather_delay] float,
             CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
             (
                 [origin_city_name] ASC
             )
             )"

    Estas son las variables y sus descripciones:

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Nombre de la variable</strong></td><td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$subscriptionName</td><td data-morhtml="true">Su nombre de suscripci&oacute;n a Azure.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true">El nombre del servidor de Base de datos SQL utilizado por Sqoop para exportar los datos. Si lo deja como est&aacute;, el script crear&aacute; uno autom&aacute;ticamente. De lo contrario, especifique una Base de datos SQL o SQL Server existente.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseUsername</td><td data-morhtml="true">Nombre de usuario de Base de datos SQL/SQL Server.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabasePassword</td><td data-morhtml="true">Contrase&ntilde;a de usuario de Base de datos SQL/SQL Server.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseLocation</td><td data-morhtml="true">Solo se utiliza si desea que el script cree un servidor de Base de datos SQL autom&aacute;ticamente.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true">El nombre de la Base de datos SQL utilizado por Sqoop para exportar los datos. Si lo deja como est&aacute;, el script crear&aacute; uno autom&aacute;ticamente. De lo contrario, especifique una Base de datos SQL o SQL Server existente.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseMaxSizeGB</td><td data-morhtml="true">Solo se utiliza si desea que el script cree una Base de datos SQL autom&aacute;ticamente.</td></tr>
 </table>


4.  Ejecute los siguientes comandos para crear un servidor de Base de datos SQL/base de datos/tabla.

         # Seleccionar la suscripción de Azure actual en caso de que haya varias suscripciones
         Select-AzureSubscription $subscriptionName

         # Crear una nueva Base de datos SQL de Azure si se solicita
         if ([string]::IsNullOrEmpty($sqlDatabaseServer))
         {
             $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
             Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

         }
         else
         {
             Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
         }

         # Crear una nueva Base de datos SQL si se solicita
         if ([string]::IsNullOrEmpty($sqlDatabaseName))
         {
             $sqlDatabaseName = "HDISqoop"
             $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
             $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

             $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
            
             Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

         }
         else
         {
             Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
         }

         #Crear una tabla AvgDelays
         $conn = New-Object System.Data.SqlClient.SqlConnection
         $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
         $conn.open()
         $cmd = New-Object System.Data.SqlClient.SqlCommand
         $cmd.connection = $conn
         $cmd.commandtext = $sqlCreateAvgDelaysTable
         $cmd.executenonquery()

         $conn.close()

Creación y carga del script de HiveQL
-------------------------------------

Con Azure PowerShell, puede ejecutar varias instrucciones HiveQL a la vez, o empaquetar la instrucción de HiveQL en un archivo script. En este tutorial, creará un script de HiveQL. El archivo de script debe cargarse en WASB. En la siguiente sección, ejecutará el archivo de script con Azure PowerShell.

El script de HiveQL realizará lo siguiente:

1.  **Anular la tabla delays\_raw**, en caso de que la tabla ya exista.
2.  **Crear la tabla externa de Hive delays\_raw** que apunta a la ubicación de WASB con los archivos de retraso de los vuelos. Esta consulta especifica que los campos están delimitados por "," y que las líneas terminan en "\\n". Esto plantea un problema cuando los valores de campo *contienen* comas porque Hive no puede distinguir entre una coma que es un delimitador de campo y una que es parte de un valor de campo (que es el caso de los valores de campo de ORIGIN\_CITY\_NAME y DEST\_CITY\_NAME). Para solucionar este problema, la consulta crea columnas TEMP para contener datos que se dividen incorrectamente en columnas.
3.  **Anular la tabla de retrasos**, en caso de que la tabla ya exista.
4.  **Crear la tabla de retrasos**. Es útil realizar una limpieza de los datos antes de realizar un procesamiento adicional. Esta consulta crea una tabla nueva, *delays*, a partir de la tabla *delays\_raw*. Tenga en cuenta que las columnas TEMP no se copian (como se mencionó anteriormente) y que la función *substring* se utiliza para quitar las comillas de los datos.
5.  **Procesa el retraso promedio a causa del tiempo y agrupa los resultados por nombre de la ciudad.** También producirá los resultados en WASB. Tenga en cuenta que la consulta eliminará los apóstrofes de los datos y excluirá las filas donde el valor de *weather\_deal*y es *null*, lo que es necesario porque Sqoop, que se utiliza más adelante en este tutorial, no controla correctamente esos valores de manera predeterminada.

Para obtener una lista completa de los comandos de HiveQL, consulte [Hive Data Definition Language](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL). Cada comando de HiveQL debe terminar con un punto y coma.

**Para crear un archivo de script de HiveQL**

1.  Abra Azure PowerShell.
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

3.  Configure las dos primeras variables y, a continuación, ejecute los siguientes comandos.

         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName ="<BlobStorageContainerName>"

         $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
         $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
            
         $srcDataFolder = "tutorials/flightdelays/data" 
         $dstDataFolder = "tutorials/flightdelays/output"

    Estas son las variables y su descripción:

   <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Nombre de la variable</strong></td><td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true">La cuenta de almacenamiento de Azure que se usa para almacenar el archivo de script de HiveQL. Los scripts de PowerShell que se proporcionan en este tutorial requieren los archivos de datos de vuelo y el archivo de script ubicado en la misma cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$containerName</td><td data-morhtml="true">El contenedor de almacenamiento de blobs de Azure que se usa para almacenar el archivo de script de HiveQL. Los scripts de PowerShell que se proporcionan en este tutorial requieren los archivos de datos de vuelo y el archivo de script ubicado en la misma cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$hqlLocalFileName</td><td data-morhtml="true">El nombre de los archivos locales para el script de HiveQL antes de cargarlo a WASB. Para simplificar el script de PowerShell, debe escribir el archivo localmente y luego usar el cmdlet Set-AzureStorageBlobContent para cargar el archivo de script en HDInsight.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$hqlBlobName</td><td data-morhtml="true">Este es el nombre del archivo de script con la ruta de acceso en WASB</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$srcDataFolder</td><td data-morhtml="true">Esta es la carpeta de WASB de donde el script de HiveQL extrae los datos.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$dstDataFolder </td><td data-morhtml="true">Esta es la carpeta de WASB hacia donde el script de HiveQL env&iacute;a los resultados. M&aacute;s adelante en el tutorial, utilizar&aacute; Sqoop para exportar los datos de esta carpeta a la Base de datos SQL de Azure.</td></tr>
 </table>


4.  Ejecute los siguientes comandos para definir las instrucciones de HiveQL:

         $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
            
         $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                 "YEAR string, " +
                 "FL_DATE string, " +
                 "UNIQUE_CARRIER string, " +
                 "CARRIER string, " +
                 "FL_NUM string, " +
                 "ORIGIN_AIRPORT_ID string, " +
                 "ORIGIN string, " +
                 "ORIGIN_CITY_NAME string, " +
                 "ORIGIN_CITY_NAME_TEMP string, " +
                 "ORIGIN_STATE_ABR string, " +
                 "DEST_AIRPORT_ID string, " +
                 "DEST string, " +
                 "DEST_CITY_NAME string, " +
                 "DEST_CITY_NAME_TEMP string, " +
                 "DEST_STATE_ABR string, " +
                 "DEP_DELAY_NEW float, " +
                 "ARR_DELAY_NEW float, " +
                 "CARRIER_DELAY float, " +
                 "WEATHER_DELAY float, " +
                 "NAS_DELAY float, " +
                 "SECURITY_DELAY float, " +
                 "LATE_AIRCRAFT_DELAY float) " +
             "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
             "LINES TERMINATED BY '\n' " +
             "STORED AS TEXTFILE " +
             "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 
            
         $hqlDropDelays = "DROP TABLE delays;"
            
         $hqlCreateDelays = "CREATE TABLE delays AS " +
             "SELECT YEAR AS year, " +
                 "FL_DATE AS flight_date, " +
                 "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                 "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                 "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                 "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                 "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                 "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                 "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                 "DEST_AIRPORT_ID AS dest_airport_id, " +
                 "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                 "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                 "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                 "DEP_DELAY_NEW AS dep_delay_new, " +
                 "ARR_DELAY_NEW AS arr_delay_new, " +
                 "CARRIER_DELAY AS carrier_delay, " +
                 "WEATHER_DELAY AS weather_delay, " +
                 "NAS_DELAY AS nas_delay, " +
                 "SECURITY_DELAY AS security_delay, " +
                 "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
             "FROM delays_raw;" 
            
         $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
             "SELECT regexp_replace(origin_city_name, '''', ''), " +
                 "avg(weather_delay) " +
             "FROM delays " +
             "WHERE weather_delay IS NOT NULL " +
             "GROUP BY origin_city_name;"
            
         $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  Ejecute los siguientes comandos para escribir el archivo de script de Hive en la estación de trabajo y cargarlo a WASB:

         # Escribir el script de Hive en un archivo local
         $hqlScript | Out-File $hqlLocalFileName -Encoding ascii
            
         # Cree un objeto de contexto de almacenamiento.
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         # Copiar el archivo desde la estación de trabajo local a WASB
         Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext
            
         # Enumerar el archivo de script desde WASB
         Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

     La salida debe ser similar a:

         Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3
            
         Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
         ----                              --------   ------                            -----------                       ------------                      ------------
         tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

Ejecución del script de HiveQL
------------------------------

Hay varios cmdlets de Azure PowerShell que puede utilizar para ejecutar Hive. En este tutorial se utiliza Invoke-Hive. Para otros métodos, consulte [Uso de Hive con HDInsight](/es-es/documentation/articles/hdinsight-use-hive/). Con Invoke-Hive, puede ejecutar una instrucción de HiveQL o un script de HiveQL. Usará el script de HiveQL que ha creado y cargado al almacenamiento de blobs de Azure.

Hay un problema conocido de la ruta de acceso de Hive. Las instrucciones para solucionarlo se pueden encontrar en [TechNet Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx).

**Para ejecutar las consultas de Hive usando PowerShell**

1.  Abra Azure PowerShell.
2.  Ejecute el siguiente comando para cambiar el directorio actual:

    cd \\Tutorials\\FlightDelays\\

    Este paso es necesario porque descargará una copia del resultado de Hive en su estación de trabajo. De forma predeterminada, no tiene permiso de escritura en la carpeta de PowerShell.

3.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

4.  Configure las tres primeras variables y, a continuación, ejecútelas:

        $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<WindowsAzureBlobStorageContainerName>"
         $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
         $outputBlobName = "tutorials/flightdelays/output/000000_0"

    Estas son las variables y su descripción:

 <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Nombre de la variable</strong></td><td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$clusterName</td><td data-morhtml="true">El cl&uacute;ster de HDInsight que ejecutar&aacute; el script de Hive y la exportaci&oacute;n de Sqoop.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true">La cuenta de almacenamiento de Azure se utiliza para almacenar el script de HiveQL. Consulte <a data-morhtml="true" href="#createScript">Creaci&oacute;n y carga del script de HiveQL</a>.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$containerName</td><td data-morhtml="true">El contenedor de almacenamiento de blobs de Azure que se usa para almacenar el script de HiveQL. Consulte <a data-morhtml="true" href="#createScript">Creaci&oacute;n y carga del script de HiveQL</a>.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$hqlScriptFile</td><td data-morhtml="true">Esta es el URI para el archivo de script de HiveQL.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$outputBlobName</td><td data-morhtml="true">Este es el archivo de salida del script de HiveQL. El nombre predeterminado es *000000_0*.</td></tr>
 </table>



5.  Ejecute el siguiente comando para invocar Hive

         Use-AzureHDInsightCluster $clusterName
                    
         Invoke-Hive –File $hqlScriptFile

6.  Ejecute el siguiente comando para comprobar los resultados:

         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
            
         Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 
            
         cat ".\$outputBlobName" | findstr /B "Ch"

    La salida debe ser similar a:

         Champaign/Urbana 19.023255813953487
         Charleston 24.93975903614458
         Charleston/Dunbar 16.954545454545453
         Charlotte 16.88616981831665
         Charlotte Amalie 12.88888888888889
         Charlottesville 25.444444444444443
         Chattanooga 19.883561643835616
         Cheyenne 9.875
         Chicago 16.77321649680922
         Chico 12.340909090909092
         Christiansted 12.318181818181818

    Entre cada ciudad y tiempo de retraso, hay un delimitador que no es visible en la ventana de salida de PowerShell. Es "\\001". Utilizará este delimitador cuando ejecute la exportación de Sqoop.

Exportación del resultado del trabajo de Hive a la Base de datos SQL de Azure
-----------------------------------------------------------------------------

Los últimos pasos sirven para ejecutar la exportación de Sqoop a fin de exportar los datos a la Base de datos SQL. Se crearon la Base de datos SQL y la tabla AvgDelays anteriormente en el tutorial.

**Para exportar datos a la Base de datos SQL**

1.  Abra Azure PowerShell.
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

3.  Configure las cinco primeras variables y, a continuación, ejecute los siguientes comandos:

        $clusterName = "<HDInsightClusterName>"
            
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseUserName = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
            
         $sqlDatabaseName = "<SQLDatabaseName>" # El nombre predeterminado es "HDISqoop"
         $sqlDatabaseTableName = "AvgDelays" 

         $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
            
         $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Estas son las variables y su descripción:

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Nombre de la variable</strong></td><td data-morhtml="true"><strong data-morhtml="true">Nota:</strong></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$clusterName</td><td data-morhtml="true">Nombre del cl&uacute;ster de HDInsight.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true">El servidor de Base de datos SQL hacia donde Sqoop exportar&aacute; los datos.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseUsername</td><td data-morhtml="true">Nombre de usuario de Base de datos SQL.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabasePassword</td><td data-morhtml="true">Contrase&ntilde;a de usuario de Base de datos SQL.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true">La Base de datos SQL hacia donde Sqoop exportar&aacute; los datos. El nombre predeterminado es *HDISqoop&quot;. </td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$sqlDatabaseTableName</td><td data-morhtml="true">La Base de datos SQL hacia donde Sqoop exportar&aacute; los datos. El nombre predeterminado es AvgDelays. Esta es la tabla que cre&oacute; anteriormente en el tutorial.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">$exportDir</td><td data-morhtml="true">Esta es la ubicaci&oacute;n del archivo de salida de Hive. Sqoop exportar&aacute; los archivos de esta ubicaci&oacute;n a la Base de datos SQL.</td></tr>
 </table>

4.  Ejecute el siguiente comando para exportar los datos:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by  $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput
        01 "
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Conéctese a la Base de datos SQL y consulte los retrasos promedio de los vuelos por ciudad en la tabla *AvgDelays*:

    ![HDI.FlightDelays.AvgDelays.Dataset](./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png)

Pasos siguientes
----------------

Ahora sabe cómo cargar archivos al almacenamiento de blobs, cómo rellenar una tabla de Hive utilizando los datos del almacenamiento de blobs, cómo ejecutar consultas de Hive y cómo utilizar Sqoop para exportar datos de HDFS a la Base de datos SQL de Azure. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight](/es-es/documentation/articles/hdinsight-get-started/)
-   [Uso de Hive con HDInsight](/es-es/documentation/articles/hdinsight-use-hive/)
-   [Uso de Oozie con HDInsight](/es-es/documentation/articles/hdinsight-use-oozie/)
-   [Uso de Sqoop con HDInsight](../hdinsight-use-sqoop/)
-   [Uso de Pig con HDInsight](/es-es/documentation/articles/hdinsight-use-pig/)
-   [Desarrollo de programas MapReduce de Java para HDInsight](/es-es/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Desarrollo de programas de streaming de Hadoop C\# para HDInsight](/es-es/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)

