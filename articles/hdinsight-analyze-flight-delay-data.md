<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="An&aacute;lisis de datos de retraso de vuelos con Hadoop en HDInsight | Azure" metaKeywords="" description="Vea c&oacute;mo cargar datos en HDInsight, procesarlos con Hive y exportar los resultados a Base de datos SQL usando Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="An&aacute;lisis de datos de retraso de vuelos con Hadoop en HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Análisis de datos de retraso de vuelos con Hadoop en HDInsight

Hive ofrece un modo de ejecutar trabajos de MapReduce de Hadoop mediante un lenguaje de scripting de tipo SQL, denominado *[HiveQL][HiveQL]*, que se puede utilizar para resumir, consultar y analizar grandes volúmenes de datos. En este tutorial se muestra cómo utilizar Hive para calcular la media de retrasos en aeropuertos y cómo utilizar Sqoop para exportar los resultados a Base de datos SQL de Azure.

Una de las principales ventajas de HDInsight es la separación del almacenamiento de datos y el proceso. HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Un proceso normal de HDInsight se puede dividir en 3 partes:

1.  Almacenar los datos en el almacenamiento de blobs de Azure. Este puede ser un proceso continuo. Por ejemplo, los datos meteorológicos, datos de sensores, registros web y, en este caso, datos de retraso de vuelos se guardan en almacenamiento de blobs.
2.  Ejecutar trabajos. Cuando llega el momento de procesar los datos, ejecuta un script de PowerShell (o una aplicación cliente) para aprovisionar un clúster de HDInsight, ejecutar los trabajos y eliminar el clúster. Los trabajos guardan los datos de salida en el almacenamiento de blobs de Azure. Los datos de salida permanecen incluso después de haber eliminado el clúster. De este modo, solo paga por lo que ha consumido.
3.  Recuperar la salida del almacenamiento de blobs o, en este caso, exportar los datos a una base de datos SQL de Azure.

El diagrama siguiente muestra el escenario y la estructura de este artículo:

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

Nota: Los números del diagrama corresponden a los títulos de sección.

**Duración aproximada:** 30 minutos

## Apartados de este tutorial

-   [Requisitos previos][Requisitos previos]
-   [Ejecución de un trabajo de Hive en un clúster de HDInsight nuevo o existente (M1)][Ejecución de un trabajo de Hive en un clúster de HDInsight nuevo o existente (M1)]
-   [Uso de Sqoop para exportar la salida a Base de datos SQL de Azure (M2)][Uso de Sqoop para exportar la salida a Base de datos SQL de Azure (M2)]
-   [Pasos siguientes][Pasos siguientes]
-   [Apéndice A: Carga de datos de retraso de vuelos en el almacenamiento de blobs de Azure (A1)][Apéndice A: Carga de datos de retraso de vuelos en el almacenamiento de blobs de Azure (A1)]
-   [Apéndice B: Creación y carga de un script de HiveQL (A2)][Apéndice B: Creación y carga de un script de HiveQL (A2)]

## <span id="prerequisite"></span></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una estación de trabajo con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].
-   Una suscripción de Azure. \*\*\*

**Descripción del almacenamiento de HDInsight**

Los clústeres de Hadoop en HDInsight usan el almacenamiento de blobs de Azure para el almacenamiento de datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación de *HDFS* de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

Cuando aprovisiona un clúster de HDInsight, se designa un contenedor de almacenamiento de blobs de una cuenta de almacenamiento de Azure como sistema de archivos predeterminado, al igual que en HDFS. Esta cuenta de almacenamiento se conoce como *cuenta de almacenamiento predeterminada* y el contenedor de blobs se conoce como *contenedor de blobs predeterminado* o *contenedor predeterminado*. La cuenta de almacenamiento predeterminada debe colocarse en el mismo centro de datos que el clúster de HDInsight. Al eliminar un clúster de HDInsight no se elimina el contenedor predeterminado ni la cuenta de almacenamiento predeterminada.

Además de la cuenta de almacenamiento predeterminada, se pueden enlazar otras cuentas de almacenamiento de Azure a un clúster de HDInsight durante el proceso de aprovisionamiento. El enlace consiste en agregar la cuenta de almacenamiento y su clave al archivo de configuración. De este modo, el clúster puede obtener acceso a esas cuentas de almacenamiento en tiempo de ejecución. Para obtener instrucciones sobre cómo agregar otras cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight][Aprovisionamiento de clústeres de Hadoop en HDInsight].

La sintaxis de WASB es la siguiente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] La ruta WASB es una ruta de acceso virtual. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

Para archivos almacenados en el contenedor predeterminado, se puede obtener acceso desde HDInsight con cualquiera de los URI siguientes (se usa flightdelays.hql como ejemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Para obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

    tutorials/flightdelays/flightdelays.hql

Observe que no se pone "/" delante del nombre de blob.

**Archivos usados en este tutorial**

La siguiente tabla enumera los archivos utilizados en este tutorial:

| Archivos                                                                  | Descripción                                                                                                               |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | Archivo de scripts de HiveQL que necesita el trabajo de Hive que va a ejecutar.                                           |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Datos de entrada para los trabajos de Hive.                                                                               |
| \\tutorials\\flightdelays\\output                                         | Ruta de acceso de salida para el trabajo de Hive. El contenedor predeterminado se usa para almacenar los datos de salida. |
| \\tutorials\\flightdelays\\jobstatus                                      | Carpeta de estado del trabajo de Hive                                                                                     |

En este tutorial se utiliza el rendimiento puntual de los datos de vuelos de aerolíneas de [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA). Los datos se han cargado en un contenedor de almacenamiento de blobs de Azure con el permiso de acceso Blob público. Puesto que es un contenedor de blobs público, no es necesario que enlace esta cuenta de almacenamiento al clúster de HDInsight. El script de HiveQL se carga también en el mismo contenedor de blobs. Si desea más información sobre cómo obtener o cargar los datos en su propia cuenta de almacenamiento y cómo crear o cargar el archivo de scripts de HiveQL, consulte el [apéndice][apéndice].

**Descripción de la tabla interna y externa de Hive**

Existen determinados aspectos que debe conocer en relación con la tabla interna y externa de Hive:

-   El comando CREATE TABLE crea una tabla interna. El archivo de datos debe ubicarse en el contenedor predeterminado.
-   El comando CREATE TABLE mueve el archivo de datos a la carpeta /hive/warehouse/<tablename>.
-   El comando CREATE EXTERNAL TABLE crea una tabla externa. El archivo de datos puede estar ubicado fuera del contenedor predeterminado.
-   El comando CREATE EXTERNAL TABLE no mueve el archivo de datos.
-   El comando CREATE EXTERNAL TABLE no permite ninguna carpeta en LOCATION. Este es el motivo por el que el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: Hive Internal and External Tables Intro][HDInsight: Hive Internal and External Tables Intro].

> [WACOM.NOTE] Una de las instrucciones de HiveQL crea una tabla externa de Hive. La tabla externa de Hive conserva el archivo de datos en la ubicación original. La tabla interna de Hive mueve el archivo de datos a hive\\warehouse. La tabla interna de Hive requiere que el archivo de datos se encuentre en el contenedor predeterminado. Para datos almacenados fuera del contenedor de blobs predeterminado, debe usar tablas externas de Hive.

## <span id="runjob"></span></a>Ejecución de un trabajo de Hive en un clúster de HDInsight nuevo o existente

Hadoop es procesamiento por lotes. La forma más rentable de ejecutar un trabajo de Hive es aprovisionar un clúster para el trabajo y eliminar el trabajo una vez completado. El siguiente script cubre todo el proceso. Para obtener más información sobre el aprovisionamiento del clúster de HDInsight y la ejecución de trabajos de Hive, consulte[Aprovisionamiento de clústeres de Hadoop en HDInsight][Aprovisionamiento de clústeres de Hadoop en HDInsight] y [Uso de Hive con HDInsight][Uso de Hive con HDInsight].

**Para ejecutar las consultas de Hive usando PowerShell**

1.  Abra PowerShell ISE.
2.  Copie y pegue el siguiente script en el panel de scripts:

        *** add script here

3.  Presione **F5** para ejecutar el script. La salida debe ser similar a:

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

    Entre cada ciudad y tiempo de retraso, hay un delimitador que no es visible en la ventana de salida de PowerShell. Es "\\001". Utilizará este delimitador cuando ejecute la exportación de Sqoop.

## <span id="exportdata"></span></a>Exportación del resultado del trabajo de Hive a la Base de datos SQL de Azure

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

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Estas son las variables y su descripción:

    <table border="1">

    <tr>
    <td>
    **Nombre de la variable**

    </td>
    <td>
    **Nota:**

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Nombre del clúster de HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    El servidor de Base de datos SQL hacia donde Sqoop exportará los datos.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nombre de usuario de Base de datos SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Contraseña de usuario de Base de datos SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    La Base de datos SQL hacia donde Sqoop exportará los datos. El nombre predeterminado es \*HDISqoop".

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    La Base de datos SQL hacia donde Sqoop exportará los datos. El nombre predeterminado es AvgDelays. Esta es la tabla que creó anteriormente en el tutorial.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Esta es la ubicación del archivo de salida de Hive. Sqoop exportará los archivos de esta ubicación a la Base de datos SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Ejecute el siguiente comando para exportar los datos:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Conéctese a la Base de datos SQL y consulte los retrasos promedio de los vuelos por ciudad en la tabla *AvgDelays*:

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>Pasos siguientes

Ahora sabe cómo cargar archivos al almacenamiento de blobs, cómo rellenar una tabla de Hive utilizando los datos del almacenamiento de blobs, cómo ejecutar consultas de Hive y cómo utilizar Sqoop para exportar datos de HDFS a la Base de datos SQL de Azure. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight][Introducción a HDInsight]
-   [Uso de Hive con HDInsight][Uso de Hive con HDInsight]
-   [Uso de Oozie con HDInsight][Uso de Oozie con HDInsight]
-   [Uso de Sqoop con HDInsight][Uso de Sqoop con HDInsight]
-   [Uso de Pig con HDInsight][Uso de Pig con HDInsight]
-   [Desarrollo de programas MapReduce de Java para HDInsight][Desarrollo de programas MapReduce de Java para HDInsight]
-   [Desarrollo de programas de streaming de Hadoop C# para HDInsight][Desarrollo de programas de streaming de Hadoop C# para HDInsight]

## <span id="appendix-a"></span></a>Apéndice A: Carga de datos de retraso de vuelos en el almacenamiento de blobs de Azure

**Para descargar los datos de vuelo**

1.  Diríjase a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA)
2.  En la página, seleccione los siguientes valores:

    | Nombre        | Valor                                                                                                                                                                                                                                                                                                                                                                                           |
    |---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2013                                                                                                                                                                                                                                                                                                                                                                                            |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                         |
    | Fields:       | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (borrar todos los demás campos) |

3.  Haga clic en **Download**.
4.  Descomprima el archivo en la carpeta **C:\\Tutorials\\FlightDelays\\Data**. Cada archivo es un archivo CSV y tiene un tamaño aproximado de 60 GB.
5.  Cambie el nombre del archivo al nombre del mes que contiene los datos. Por ejemplo, el archivo que contiene los datos de enero se llamaría *Enero.csv*.
6.  Repita el paso 2 y 5 para descargar un archivo para cada uno de los 12 meses de 2013. Necesitará como mínimo un archivo para ejecutar el tutorial.

**Para cargar los datos de retraso de vuelos en el almacenamiento de blobs de Azure**

1.  Abra PowerShell ISE.
2.  Pegue el siguiente script en el panel de scripts:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  Presione **F5** para ejecutar el script.

## <span id="appendix-b"></span></a>Apéndice B: Creación y carga de un script de HiveQL

Con Azure PowerShell, puede ejecutar varias instrucciones HiveQL a la vez, o empaquetar la instrucción de HiveQL en un archivo script. Esta sección muestra cómo crear un script de HiveQL y cargarlo en el almacenamiento de blobs de Azure usando PowerShell. Hive requiere que los scripts de HiveQL se almacenen en WASB.

El script de HiveQL realizará lo siguiente:

1.  **Anular la tabla delays\_raw**, en caso de que la tabla ya exista.
2.  **Crear la tabla externa de Hive delays\_raw**, que apunta a la ubicación de WASB con los archivos de retraso de los vuelos. Esta consulta especifica que los campos están delimitados por "," y que las líneas terminan en "\\n". Esto plantea un problema cuando los valores de campo *contienen* comas porque Hive no puede distinguir entre una coma que es un delimitador de campo y una que es parte de un valor de campo (que es el caso de los valores de campo de ORIGIN\_CITY\_NAME y DEST\_CITY\_NAME). Para solucionar este problema, la consulta crea columnas TEMP para contener datos que se dividen incorrectamente en columnas.
3.  **Anular la tabla de retrasos**, en caso de que la tabla ya exista.
4.  **Crear la tabla de retrasos**. Es útil realizar una limpieza de los datos antes de realizar un procesamiento adicional. Esta consulta crea una tabla nueva, *delays*, a partir de la tabla *delays\_raw*. Tenga en cuenta que las columnas TEMP no se copian (como se mencionó anteriormente) y que la función *substring* se utiliza para quitar las comillas de los datos.
5.  **Procesa el retraso promedio a causa del tiempo y agrupa los resultados por nombre de la ciudad.** También producirá los resultados en WASB. Tenga en cuenta que la consulta eliminará los apóstrofes de los datos y excluirá las filas donde el valor de *weather\_deal*y es *null*, lo que es necesario porque Sqoop, que se utiliza más adelante en este tutorial, no controla correctamente esos valores de manera predeterminada.

Para obtener una lista completa de los comandos de HiveQL, consulte [Hive Data Definition Language][HiveQL]. Cada comando de HiveQL debe terminar con un punto y coma.

**Para crear un archivo de script de HiveQL**

1.  Abra Azure PowerShell ISE.
2.  Copie y pegue el siguiente script en el panel de scripts:

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Estas son las constantes que se usan en el script:

    -   **$hqlLocalFileName**: el script guarda el archivo de scripts de HiveQL en modo local antes de cargarlo en WASB. Este es el nombre del archivo. El valor predeterminado es <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
    -   **$hqlBlobName**: este es el nombre de blob del archivo de scripts de HiveQL que se ha usado en el almacenamiento de blobs de Azure. El valor predeterminado es <u>tutorials/flightdelays/flightdelays.hql</u>. Puesto que el archivo se escribirá directamente en el almacenamiento de blobs de Azure, NO se pone "/" delante del nombre de blob. Si quiere acceder al archivo desde WASB, debe agregar "/" delante del nombre de archivo.
    -   **$srcDataFolder** y **$dstDataFolder**: = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

        Add-AzureAccount

4.  Configure las dos primeras variables y, a continuación, ejecute los siguientes comandos.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Estas son las variables y su descripción:

    <table border="1">

    <tr>
    <td>
    **Nombre de la variable**

    </td>
    <td>
    **Descripción**

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    La cuenta de almacenamiento de Azure que se usa para almacenar el archivo de script de HiveQL. Los scripts de PowerShell que se proporcionan en este tutorial requieren los archivos de datos de vuelo y el archivo de script ubicado en la misma cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    El contenedor de almacenamiento de blobs de Azure que se usa para almacenar el archivo de script de HiveQL. Los scripts de PowerShell que se proporcionan en este tutorial requieren los archivos de datos de vuelo y el archivo de script ubicado en la misma cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    El nombre de los archivos locales para el script de HiveQL antes de cargarlo a WASB. Para simplificar el script de PowerShell, debe escribir el archivo localmente y luego usar el cmdlet Set-AzureStorageBlobContent para cargar el archivo de script en HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    Este es el nombre del archivo de script con la ruta de acceso en WASB

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    Esta es la carpeta de WASB de donde el script de HiveQL extrae los datos.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    Esta es la carpeta de WASB hacia donde el script de HiveQL envía los resultados. Más adelante en el tutorial, utilizará Sqoop para exportar los datos de esta carpeta a la Base de datos SQL de Azure.

    </td>
    </tr>

    </table>
    </p>
5.  Ejecute los siguientes comandos para definir las instrucciones de HiveQL:

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

6.  Ejecute los siguientes comandos para escribir el archivo de script de Hive en la estación de trabajo y cargarlo a WASB:

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    La salida debe ser similar a:

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## Preparación de la base de datos SQL

**Para preparar la base de datos SQL (combine esto con el script de Sqoop)**

1.  Abra PowerShell ISE.
2.  Copie y pegue el siguiente script en el panel de scripts:

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > WACOM.NOTE El script utiliza un servicio REST, http://bot.whatismyipaddress.com, para recuperar la dirección IP externa. Esta dirección IP se usa para crear una regla de firewall para el servidor de Base de datos SQL.

    Estas son algunas de las constantes que se usan en el script:

    -   **$ipAddressRestService**: el valor predeterminado es <u>http://bot.whatismyipaddress.com</u>. Es un servicio REST de direcciones IP públicas para obtener la dirección IP externa. Puede usar los servicios que desee. La dirección IP externa recuperada con el servicio se usará para crear una regla de firewall para el servidor de Base de datos SQL de Azure, de forma que pueda acceder a la base de datos desde su estación de trabajo (usando el script de PowerShell).
    -   **$fireWallRuleName**: este es el nombre de la regla de firewall del servidor de Base de datos SQL de Azure. El nombre predeterminado es <u>FlightDelay</u>. Puede cambiarlo si lo desea.
    -   **$sqlDatabaseMaxSizeGB**: este valor se usa solo cuando se crea un nuevo servidor de Base de datos SQL de Azure. El valor predeterminado es <u>10 GB</u>. 10 GB es suficiente para este tutorial.
    -   **$sqlDatabaseName**: este valor se usa solo cuando se crea una nueva base de datos SQL de Azure. El valor predeterminado es <u>HDISqoop</u>. Si cambia el nombre, debe actualizar el script de PowerShell de Sqoop en consecuencia.

3.  Presione **F5** para ejecutar el script. Debe proporcionar las credenciales de su suscripción de Azure y los valores siguientes:

    -   **sqlDatabaseServer**: escriba el nombre del servidor de Base de datos SQL de Azure adonde desea exportar la salida de Hive. No escriba nada para crear uno.
    -   **sqlDatabaseUsername**: escriba el nombre de usuario de inicio de sesión de la base de datos. Debe especificarla tanto si desea crear un servidor de bases de datos nuevo como si desea usar uno existente.
    -   **sqlDatabasePassword**: escriba la contraseña de inicio de sesión de la base de datos. Debe especificarla tanto si desea crear un servidor de bases de datos nuevo como si desea usar uno existente.
    -   **sqlDatabaseLocation**: escriba la región. Se usa solo cuando se crea un servidor de bases de datos nuevo.
    -   **sqlDatabaseName**: escriba el nombre de la base de datos SQL de Azure. No escriba nada para crear una nueva. El nombre predeterminado de la base de datos es **HDISqoop**.

4.  Valide la salida del script. Asegúrese de que el script se ejecutó correctamente.

Si decide utilizar un método diferente para cargar los archivos, asegúrese de que la ruta de acceso del archivo sea *tutorials/flightdelays/data*. La sintaxis para tener acceso a los archivos es:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* es la carpeta virtual creada cuando se cargan los archivos. Compruebe que haya 12 archivos, uno para cada mes.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [Requisitos previos]: #prerequisite
  [Ejecución de un trabajo de Hive en un clúster de HDInsight nuevo o existente (M1)]: #runjob
  [Uso de Sqoop para exportar la salida a Base de datos SQL de Azure (M2)]: #exportdata
  [Pasos siguientes]: #nextsteps
  [Apéndice A: Carga de datos de retraso de vuelos en el almacenamiento de blobs de Azure (A1)]: #appdendix-a
  [Apéndice B: Creación y carga de un script de HiveQL (A2)]: #appendix-b
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Aprovisionamiento de clústeres de Hadoop en HDInsight]: ../hdinsight-provision-clusters/
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [apéndice]: #appendix
  [HDInsight: Hive Internal and External Tables Intro]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Uso de Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Uso de Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
  [Desarrollo de programas MapReduce de Java para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Desarrollo de programas de streaming de Hadoop C# para HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
