---
title: 'Tutorial: Realización de las operaciones de extracción, transformación y carga (ETL) mediante Hive en HDInsight - Azure | Documentos de Microsoft'
description: Aprenda a extraer datos de un conjunto de datos CSV sin procesar, a transformarlos mediante Hive en HDInsight y, después, a cargar los datos transformados en una base de datos SQL de Azure mediante Sqoop.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 46c80f326c8210ac3282cf128058cee91ff3836c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33770844"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Realización de las operaciones de extracción, transformación y carga mediante Apache Hive en Azure HDInsight

En este tutorial, se toma un archivo de datos CSV sin procesar, se importa en un almacenamiento de clúster de HDInsight y, después, los datos se transforman mediante Apache Hive en Azure HDInsight. Una vez que los datos se han transformado, se cargan en una base de datos SQL de Azure mediante Apache Sqoop. En este artículo, se usan datos de vuelos disponibles públicamente.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de Azure HDInsight o en versiones posteriores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

En este tutorial se describen las tareas siguientes: 

> [!div class="checklist"]
> * Descarga de datos de vuelos de ejemplo
> * Carga de datos en un clúster de HDInsight
> * Transformación de los datos mediante Hive
> * Creación de una tabla en la base de datos SQL de Azure
> * Uso de Sqoop para exportar los datos a la base de datos SQL de Azure


La siguiente ilustración muestra el flujo de la aplicación de ETL típico.

![Operación de ETL mediante Apache Hive en HDInsight de Azure](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "ETL operation using Apache Hive on Azure HDInsight")

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

* **Un clúster de Hadoop basado en Linux en HDInsight** . Consulte en [Introducción al uso de Hadoop con Hive en HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) los pasos para crear un nuevo clúster de HDInsight basado en Linux.

* **Azure SQL Database**. Use una instancia de Azure SQL Database como almacén de datos de destino. Si aún no tiene ninguna, consulte [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started.md).

* **CLI de Azure 2.0**. Si no tiene instalada la CLI de Azure, consulte en [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) el procedimiento que debe seguir.

* **Un cliente SSH**. Para más información, consulte [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Descarga de los datos de vuelo

1. Diríjase a[Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. En la página, seleccione los siguientes valores:

   | NOMBRE | Valor |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Fields |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Borre el resto de los campos. 

3. Seleccione **Descargar**. Obtenga un archivo .zip con los campos de datos que ha seleccionado.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Carga de datos en un clúster de HDInsight

Hay muchas maneras de cargar datos en el almacenamiento asociado a un clúster de HDInsight. En esta sección, se usa `scp` para cargar datos. Para obtener información acerca de otras formas de cargar los datos, consulte [Carga de datos en HDInsight](hdinsight-upload-data.md).

1. Abra una ventana del símbolo del sistema y use el siguiente comando para cargar el archivo .zip en el nodo principal del clúster de HDInsight:

    ```bash
    scp <FILENAME>.zip <SSH-USERNAME>@<CLUSTERNAME>-ssh.azurehdinsight.net:<FILENAME.zip>
    ```

    Reemplace *FILENAME* por el nombre del archivo .zip. Reemplace *USERNAME* por el inicio de sesión SSH para el clúster de HDInsight. Reemplace *CLUSTERNAME* por el nombre del clúster de HDInsight.

   > [!NOTE]
   > Si usa una contraseña para autenticar el inicio de sesión SSH, se le pedirá la contraseña. Si utiliza una clave pública, es posible que tenga que usar el parámetro `-i` y especificar la ruta de acceso a la clave privada correspondiente. Por ejemplo, `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Cuando la carga haya finalizado, conéctese al clúster mediante SSH. En el símbolo del sistema escriba el siguiente comando:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

3. Utilice el comando siguiente para descomprimir el archivo .zip:

    ```bash
    unzip FILENAME.zip
    ```

    Este comando extrae un archivo .csv que tiene un tamaño aproximado de 60 MB.

4. Use los siguientes comandos para crear un directorio en el almacenamiento de HDInsight y copie el archivo .csv en dicho directorio:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put <FILENAME>.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformación de datos mediante una consulta de Hive

Hay muchas formas de ejecutar un trabajo de Hive en un clúster de HDInsight. En esta sección se usa Beeline para ejecutar un trabajo de Hive. Para obtener información acerca de otros métodos de ejecución de trabajos de Hive, consulte [Uso de Hive en HDInsight](./hadoop/hdinsight-use-hive.md).

Como parte del trabajo de Hive importe los datos del archivo .csv en una tabla de Hive denominada **Delays**.

1. En la ventana del símbolo del sistema de SSH que ya tiene para el clúster de HDInsight, use el siguiente comando para crear y editar un nuevo archivo denominado **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Use el texto siguiente como contenido de este archivo:

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

2. Para guardar el archivo, presione **Esc** y escriba `:x`.

3. Para iniciar Hive y ejecutar el archivo **flightdelays.hql**, use el siguiente comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Cuando finalice el script __flightdelays.hql__, use el siguiente comando para abrir una sesión interactiva de Beeline:

    ```bash
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

## <a name="create-a-sql-database-table"></a>Creación de una tabla de SQL Database

En esta sección, se da por supuesto que ya ha creado una base de datos SQL de Azure. Si no dispone todavía de una base de datos SQL, consulte [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started.md) para crear una.

Si ya tiene una base de datos SQL, debe obtener el nombre del servidor. Para buscar el nombre del servidor en [Azure Portal](https://portal.azure.com), seleccione **SQL Database** y filtre por el nombre de la base de datos que desee usar. El nombre del servidor aparece en la columna **Nombre de servidor**.

![Obtener detalles del servidor de Azure SQL Server](./media/hdinsight-analyze-flight-delay-data-linux/get-azure-sql-server-details.png "Get Azure SQL server details")

> [!NOTE]
> Hay muchas maneras de conectarse a SQL Database y crear una tabla. En los siguientes pasos se utiliza [FreeTDS](http://www.freetds.org/) desde el clúster de HDInsight.


1. Para instalar FreeTDS, use el siguiente comando desde una conexión SSH al clúster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Cuando finalice la instalación, use el comando siguiente para conectarse al servidor de SQL Database. Reemplace **serverName** por el nombre del servidor de SQL Database. Reemplace **adminLogin** y **adminPassword** por el inicio de sesión de SQL Database. Reemplace **databaseName** por el nombre de la base de datos.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Cuando se le solicite, escriba la contraseña para el inicio de sesión del administrador de SQL Database.

    Recibirá una salida similar al texto siguiente:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. En el símbolo del sistema `1>` , introduzca las líneas siguientes:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Cuando se haya especificado la instrucción `GO`, se evaluarán las instrucciones anteriores. Se crea una tabla denominada **delays** con un índice agrupado.

    Use la siguiente consulta para comprobar que se ha creado la tabla:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    La salida será similar al siguiente texto:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Entrar `exit` at the `1>` .

## <a name="export-data-to-sql-database-using-sqoop"></a>Exportación de datos a una base de datos SQL mediante Sqoop

En las secciones anteriores, copió los datos transformados en `/tutorials/flightdelays/output`. En esta, utilizará Sqoop para exportar los datos de "/ tutoriales/flightdelays/salida" a la tabla que creó en la base de datos SQL de Azure. 

1. Utilice el comando siguiente para comprobar si Sqoop puede ver la instancia de SQL Database:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Este comando devuelve una lista de bases de datos, incluida la base de datos en la que anteriormente creó la tabla delays.

2. Use el comando siguiente para exportar datos de hivesampletable a la tabla de retrasos:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop se conecta a la base de datos que contiene la tabla delays y exporta los datos del directorio `/tutorials/flightdelays/output` a dicha tabla.

3. Cuando finalice el comando sqoop, use la utilidad tsql para conectarse a la base de datos:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Use las instrucciones siguientes para comprobar que los datos se exportaron a la tabla de retrasos:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Debería ver una lista de los datos de la tabla. La tabla incluye el nombre de la ciudad y el tiempo medio de retraso de los vuelos promedio a la ciudad. 

    Escriba `exit` para salir de la utilidad de tsql.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a realizar la extracción, transformación y carga de operaciones de datos mediante un clúster de Apache Hadoop en HDInsight. Pase al siguiente tutorial para aprender a crear clústeres de HDInsight Hadoop a petición mediante Azure Data Factory.

> [!div class="nextstepaction"]
>[Creación de clústeres de Hadoop a petición en HDInsight mediante Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)

Para conocer otras formas de trabajar con datos en HDInsight, consulte los siguientes documentos:

* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight][hdinsight-develop-mapreduce]
* [Desarrollo de programas de MapReduce de streaming de Python para HDInsight][hdinsight-develop-streaming]
* [Uso de Oozie con HDInsight][hdinsight-use-oozie]
* [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
