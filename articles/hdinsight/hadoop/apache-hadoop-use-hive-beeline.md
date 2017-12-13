---
title: Uso de Beeline con Apache Hive - Azure HDInsight | Microsoft Docs
description: Aprenda a usar el cliente de Beeline para ejecutar consultas de Hive con Hadoop en HDInsight. Beeline es una utilidad para trabajar con HiveServer2 sobre JDBC.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline hive,hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: 19c5f165b47f7de4a014226460f82f3ca12b3eec
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="use-the-beeline-client-with-apache-hive"></a>Usar el cliente de Beeline con Apache Hive

Aprenda a usar [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para ejecutar consultas de Hive en HDInsight.

Beeline es un cliente de Hive que se incluye en los nodos principales del clúster de HDInsight. Beeline usa JDBC para conectarse a HiveServer2, un servicio hospedado en el clúster de HDInsight. Beeline también se puede usar para tener acceso a Hive en HDInsight de forma remota a través de Internet. En los ejemplos siguientes se proporcionan las cadenas de conexión más comunes usadas para conectarse a HDInsight desde Beeline:

* __Uso de Beeline de una conexión SSH a un nodo principal o un nodo perimetral__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Uso de Beeline en un cliente mediante la conexión a HDInsight a través de una red virtual de Azure__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Uso de Beeline en un cliente, mediante la conexión a HDInsight a través de la red pública de Internet__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Reemplace `admin` por la cuenta de inicio de sesión del clúster.
>
> Reemplace `password` por la contraseña de la cuenta de inicio de sesión del clúster.
>
> Reemplace `clustername` por el nombre del clúster de HDInsight.
>
> Al conectarse al clúster mediante una red virtual, sustituya `<headnode-FQDN>` por el nombre de dominio completo de un nodo principal del clúster.

## <a id="prereq"></a>Requisitos previos

* Un clúster de Hadoop basado en Linux en HDInsight.

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un cliente de SSH o un cliente de Beeline local. En la mayoría de los pasos descritos en este documento se da por hecho que está usando Beeline desde una sesión de SSH al clúster. Para más información sobre cómo ejecutar Beeline desde fuera del clúster, vea la sección sobre el [uso remoto de Beeline](#remote).

    Para obtener más información sobre cómo usar SSH, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Uso de BeeLine

1. Cuando Beeline se inicia, hay que proporcionar una cadena de conexión de HiveServer2 en el clúster de HDInsight:

    * Si se conecta a través de la red pública de Internet, debe proporcionar el nombre de la cuenta de inicio de sesión del clúster (de forma predeterminada `admin`) y la contraseña. Por ejemplo, al usar Beeline desde un sistema cliente para conectarse a la dirección `<clustername>.azurehdinsight.net`. Esta conexión se realiza a través del puerto `443` y se cifra mediante SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Si se conecta desde una sesión de SSH a un nodo principal del clúster, puede conectarse a la dirección `headnodehost` en el puerto `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Si se conecta a través de una red virtual de Azure, debe proporcionar el nombre de dominio completo (FQDN) de un nodo principal del clúster. Puesto que esta conexión se realiza directamente a los nodos del clúster, se usa el puerto `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Los comandos de Beeline empiezan con un carácter `!`. Por ejemplo `!help` muestra la ayuda. Sin embargo, el `!` se puede omitir en algunos comandos. Por ejemplo, `help` también funciona.

    Hay un `!sql`, que se usa para ejecutar instrucciones de HiveQL. Sin embargo, HiveQL es de uso tan frecuente que puede omitir el elemento `!sql`que le precede. Las dos instrucciones siguientes son equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    En un nuevo clúster, solo debe aparecer una tabla: **hivesampletable**.

3. Use el siguiente comando para mostrar el esquema de hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Este comando devuelve la siguiente información:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Esta información describe las columnas de la tabla. Aunque podríamos realizar algunas consultas en estos datos, vamos a crear esta vez una nueva tabla para demostrar cómo cargar datos en Hive y aplicar un esquema.

4. Escriba las siguientes instrucciones para crear una tabla denominada **log4jLogs** con los datos de ejemplo proporcionados con el clúster de HDInsight:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Estas instrucciones realizan las acciones siguientes:

    * `DROP TABLE`: si la tabla existe, se elimina.

    * `CREATE EXTERNAL TABLE`: crea una tabla **externa** en Hive. Las tablas externas solo almacenan la definición de Tabla en Hive. Los datos permanecen en la ubicación original.

    * `ROW FORMAT`: indica el formato de los datos. En este caso, los campos de cada registro se separan mediante un espacio.

    * `STORED AS TEXTFILE LOCATION`: indica dónde se almacenan los datos y en qué formato de archivo.

    * `SELECT`: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esta consulta devuelve un valor de **3** ya que hay tres filas que contienen este valor.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive intenta aplicar el esquema en todos los archivos del directorio. En este caso, el directorio contiene archivos que no coinciden con el esquema. Para evitar que haya datos inservibles en los resultados, esta instrucción indica a Hive que solo se deben devolver datos de archivos que terminen en .log.

  > [!NOTE]
  > Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un proceso de carga de datos automatizado o una operación de MapReduce.
  >
  > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

    La salida de este comando es similar al texto siguiente:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Para salir de Beeline, use `!exit`.

## <a id="file"></a>Usar Beeline para ejecutar un archivo HiveQL

Use los pasos siguientes para crear un archivo y, luego, ejecútelo mediante Beeline.

1. Use el comando siguiente para crear un archivo denominado **query.hql**:

    ```bash
    nano query.hql
    ```

2. Use el texto siguiente como contenido del archivo. Esta consulta crea una nueva tabla "interna" denominada **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Estas instrucciones realizan las acciones siguientes:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que no se utiliza la palabra clave **EXTERNAL**, esta instrucción crea una tabla interna. Las tablas internas se guardan en el almacenamiento de datos de Hive y Hive las administra por completo.
    * **STORED AS ORC** : almacena los datos en el formato Optimized Row Columnar (ORC). ORC es un formato altamente optimizado y eficiente para almacenar datos de Hive.
    * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.

    > [!NOTE]
    > A diferencia de las tablas externas , la eliminación de una tabla interna también elimina los datos subyacentes.

3. Para guardar el archivo, use **Ctrl**+**_X**, escriba **Y** y, finalmente, presione **Entrar**.

4. Use el siguiente código para ejecutar el archivo mediante Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > El parámetro `-i` inicia Beeline y ejecuta las instrucciones del archivo `query.hql`. Una vez que se completa la consulta, llegará al aviso `jdbc:hive2://headnodehost:10001/>`. También puede ejecutar un archivo mediante el parámetro `-f` que sale de Beeline después de que la consulta se completa.

5. Para comprobar que la tabla **errorLogs** se creó, use la siguiente instrucción para devolver todas las filas de **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Se deben devolver tres filas de datos y todas ellas deben contener **[ERROR]** en la columna t4.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Usar Beeline de forma remota

Si tiene Beeline instalado localmente y se conecta a través de la red pública de Internet, use los siguientes parámetros:

* __Cadena de conexión__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Nombre de inicio de sesión del clúster__: `-n admin`

* __Contraseña de inicio de sesión del clúster__`-p 'password'`

Reemplace el `clustername` en la cadena de conexión por el nombre del clúster de HDInsight.

Reemplace `admin` por el nombre de inicio de sesión del clúster y `password` por la contraseña.

Si tiene Beeline instalado localmente y se conecta a través de una red virtual de Azure, use los siguientes parámetros:

* __Cadena de conexión__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Para buscar el nombre de dominio completo de un nodo principal, use la información del documento [Administración de clústeres de HDInsight con la API de REST de Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

## <a id="sparksql"></a>Uso de Beeline con Spark

Spark proporciona su propia implementación de HiveServer2 que, en algunas ocasiones, se denomina servidor Thrift de Spark. Este servicio utiliza Spark SQL para resolver las consultas en lugar de Hive y proporciona un mejor rendimiento en función de la consulta.

Para conectarse al servidor Thrift de Spark en el clúster de HDInsight, use el puerto `10002` en lugar del `10001`. Por ejemplo: `beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'`.

> [!IMPORTANT]
> No se puede acceder al servidor Thrift de Spark directamente a través de internet. Solo puede conectarse a él desde una sesión de SSH o desde dentro de la misma instancia de Azure Virtual Network del clúster de HDInsight.

## <a id="summary"></a><a id="nextsteps"></a>Pasos siguientes

Para más información general sobre el uso de Hive en HDInsight, consulte los documentos siguientes:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para más información sobre otras maneras de trabajar con Hadoop en HDInsight, consulte los documentos siguientes:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si usa Tez con Hive, consulte los siguientes documentos:

* [Use the Tez UI on Windows-based HDInsight](../hdinsight-debug-tez-ui.md)
* [Use the Ambari Tez view on Linux-based HDInsight](../hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
