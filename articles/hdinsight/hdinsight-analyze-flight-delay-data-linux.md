---
title: "Análisis de datos de retraso de vuelos con Hive en HDInsight: Azure | Microsoft Docs"
description: "Aprenda a usar Hive para analizar datos de vuelos en HDInsight basado en Linux y a exportar después los datos a SQL Database utilizando Sqoop."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f333354311b16c00a0d43a691f139f5f80383d1a
ms.contentlocale: es-es
ms.lasthandoff: 09/13/2017

---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Análisis de datos de retraso de vuelos mediante Hive en HDInsight basado en Linux

Aprenda a analizar datos de retrasos de vuelos con Hive en HDInsight basado en Linux y a exportar después los datos a Azure SQL Database mediante Sqoop.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de Azure HDInsight o en versiones posteriores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Requisitos previos

* **Un clúster de HDInsight**. Consulte en [Introducción al uso de Hadoop con Hive en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) los pasos para crear un nuevo clúster de HDInsight basado en Linux.

* **Base de datos de SQL Azure**. Use una instancia de Azure SQL Database como almacén de datos de destino. Si aún no tiene ninguna, consulte [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started.md).

* **CLI de Azure** Si no tiene instalada la CLI de Azure, consulte en [Instalación de la CLI de Azure 1.0](../cli-install-nodejs.md) el procedimiento que debe seguir.

## <a name="download-the-flight-data"></a>Descarga de los datos de vuelo

1. Diríjase a[Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. En la página, seleccione los siguientes valores:

   | Nombre | Valor |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Fields |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Borre el resto de los campos. 

3. Seleccione **Descargar**.

## <a name="upload-the-data"></a>Carga de los datos

1. Use el siguiente comando para cargar el archivo .zip en el nodo principal del clúster de HDInsight:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Reemplace *FILENAME* por el nombre del archivo .zip. Reemplace *USERNAME* por el inicio de sesión SSH para el clúster de HDInsight. Reemplace *CLUSTERNAME* por el nombre del clúster de HDInsight.

   > [!NOTE]
   > Si usa una contraseña para autenticar el inicio de sesión SSH, se le pedirá la contraseña. Si utiliza una clave pública, es posible que tenga que usar el parámetro `-i` y especificar la ruta de acceso a la clave privada correspondiente. Por ejemplo: `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Cuando la carga haya finalizado, conéctese al clúster mediante SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Para más información, consulte [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Utilice el comando siguiente para descomprimir el archivo .zip:

    ```
    unzip FILENAME.zip
    ```

    Este comando extrae un archivo .csv que tiene un tamaño aproximado de 60 MB.

4. Use el siguiente comando para crear un directorio en el almacenamiento para HDInsight y luego copie el archivo en el directorio:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Creación y ejecución de HiveQL

Siga estos pasos para importar los datos del archivo CSV en una tabla de Hive denominada **Delays**.

1. Use el comando siguiente para crear y editar un nuevo archivo denominado **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Use el texto siguiente como contenido de este archivo:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Para guardar el archivo, presione Ctrl+X y luego Y.

3. Para iniciar Hive y ejecutar el archivo **flightdelays.hql**, use el siguiente comando:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

   > [!NOTE]
   > En este ejemplo, se utiliza `localhost`, ya que tiene conexión al nodo principal del clúster de HDInsight, que es donde se está ejecutando HiveServer2.

4. Cuando finalice el script __flightdelays.hql__, use el siguiente comando para abrir una sesión interactiva de Beeline:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Cuando aparezca `jdbc:hive2://localhost:10001/>` en el símbolo del sistema, utilice la consulta siguiente para recuperar los datos importados sobre los retrasos de vuelos:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Esta consulta recupera una lista de ciudades que experimentaron demoras por inclemencias del tiempo, junto con el tiempo medio de retraso, y la guarda en `/tutorials/flightdelays/output`. Más adelante, Sqoop leerá los datos desde esta ubicación y los exportará a Azure SQL Database.

6. Para salir de Beeline, escriba `!quit` en el símbolo del sistema.

## <a name="create-a-sql-database"></a>Creación de una base de datos SQL

Si ya tiene una base de datos SQL, debe obtener el nombre del servidor. Para buscar el nombre del servidor en [Azure Portal](https://portal.azure.com), seleccione **Bases de datos SQL** y filtre por el nombre de la base de datos que desee usar. El nombre del servidor aparece en la columna **SERVIDOR** .

Si no dispone todavía de una base de datos SQL, consulte [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started.md) para crear una. Guarde el nombre del servidor que se ha utilizado con la base de datos.

## <a name="create-a-sql-database-table"></a>Creación de una tabla de SQL Database

> [!NOTE]
> Hay muchas maneras de conectarse a SQL Database y crear una tabla. En los siguientes pasos se utiliza [FreeTDS](http://www.freetds.org/) desde el clúster de HDInsight.


1. Use SSH para conectarse al clúster de HDInsight basado en Linux y ejecute los siguientes pasos en la sesión SSH.

2. Use el siguiente comando para instalar FreeTDS:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Cuando finalice la instalación, use el comando siguiente para conectarse al servidor de SQL Database. Reemplace **serverName** por el nombre del servidor de la base de datos SQL. Reemplace **adminLogin** y **adminPassword** por el inicio de sesión de SQL Database. Reemplace **databaseName** por el nombre de la base de datos.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Recibirá una salida similar al texto siguiente:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. En el símbolo del sistema `1>` , introduzca las líneas siguientes:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Cuando se haya especificado la instrucción `GO`, se evaluarán las instrucciones anteriores. Se crea una tabla denominada **delays** con un índice agrupado.

    Use la siguiente consulta para comprobar que se ha creado la tabla:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    La salida será similar al siguiente texto:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Entrar `exit` at the `1>` .

## <a name="export-data-with-sqoop"></a>Exportación de datos con Sqoop

1. Utilice el comando siguiente para comprobar si Sqoop puede ver la instancia de SQL Database:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Este comando devuelve una lista de bases de datos, incluida la base de datos en la que anteriormente creó la tabla delays.

2. Use el comando siguiente para exportar datos de hivesampletable a la tabla de retrasos:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop se conecta a la base de datos que contiene la tabla delays y exporta los datos del directorio `/tutorials/flightdelays/output` a dicha tabla.

3. Cuando finalice el comando sqoop, use la utilidad tsql para conectarse a la base de datos:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Use las instrucciones siguientes para comprobar que los datos se exportaron a la tabla de retrasos:

    ```
    SELECT * FROM delays
    GO
    ```

    Debería ver una lista de los datos de la tabla. Escriba `exit` para salir de la utilidad de tsql.

## <a name="next-steps"></a>Pasos siguientes

Para conocer otras formas de trabajar con datos en HDInsight, consulte los siguientes documentos:

* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Oozie con HDInsight][hdinsight-use-oozie]
* [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight][hdinsight-develop-mapreduce]
* [Desarrollo de programas de MapReduce de streaming de Python para HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

