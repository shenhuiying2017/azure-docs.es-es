---
title: 'Carga masiva en Phoenix Apache con psql: Azure HDInsight | Microsoft Docs'
description: Utilice la herramienta psql para cargar datos de forma masiva en tablas de Phoenix.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 0a623113ee9f3fe2c0f5f616ecd79b8311a8ffc1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Carga masiva de datos en Phoenix mediante psql

[Apache Phoenix](http://phoenix.apache.org/) es una base de datos relacional de código abierto y masivamente paralela que se basa en [HBase](../hbase/apache-hbase-overview.md). Phoenix proporciona consultas de tipo SQL a través de HBase. Phoenix usa controladores JDBC para permitir a los usuarios crear, eliminar y modificar índices, vistas, secuencias y tablas SQL, así como realizar operaciones de upsert en filas de forma individual o masiva. Phoenix utiliza la compilación nativa de NoSQL (en lugar de usar MapReduce para compilar consultas) para crear aplicaciones de baja latencia a partir de HBase. Phoenix agrega coprocesadores para admitir la ejecución de código proporcionado por clientes en el espacio de direcciones del servidor, ejecutando el código colocado con los datos. Esto minimiza la transferencia de datos de cliente/servidor.  Para trabajar con datos mediante Phoenix en HDInsight, primero debe crear tablas y, después, cargar datos en ellas.

## <a name="bulk-loading-with-phoenix"></a>Carga masiva con Phoenix

Hay varias maneras de obtener datos en HBase, incluido el uso de API de cliente, un trabajo MapReduce con TableOutputFormat, o la introducción de datos manualmente con el shell de HBase. Phoenix proporciona dos métodos para cargar datos CSV en tablas de Phoenix: una herramienta de carga de cliente denominada `psql` y una herramienta de carga masiva basada en MapReduce.

La herramienta `psql` tiene un subproceso único y es perfecta para cargar megabytes o gigabytes de datos. Todos los archivos CSV que se carguen deben tener la extensión de archivo "csv".  También puede especificar archivos de script SQL en la línea de comandos `psql` con la extensión de archivo "SQL".

La carga masiva con MapReduce se usa para volúmenes de datos mucho más grandes, normalmente en escenarios de producción, ya que usa varios subprocesos.

Antes de iniciar la carga de datos, compruebe que Phoenix está habilitado y que la configuración de tiempo de espera de consulta es la prevista.  Acceda al panel de Ambari del clúster de HDInsight, seleccione HBase y, después, la pestaña Configuración.  Desplácese hacia abajo para comprobar que Apache Phoenix está establecido en `enabled` tal como se muestra:

![Configuración del clúster de Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Usar `psql` para cargar tablas de forma masiva

1. Cree una nueva tabla y, después, guarde la consulta con el nombre de archivo `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Copie el archivo CSV (se muestra el contenido de ejemplo) como `customers.csv` en un directorio `/tmp/` para cargarlo en una tabla recién creada.  Use el comando `hdfs` para copiar el archivo CSV en la ubicación de origen deseada.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Cree una consulta SQL SELECT para comprobar que los datos de entrada se cargaron correctamente y, después, guarde la consulta con el nombre de archivo `listCustomers.sql`. Puede usar cualquier consulta SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Para cargar de forma masiva los datos, abra una *nueva* ventana de comandos de Hadoop. En primer lugar, vaya a la ubicación del directorio de ejecución con el comando `cd` y, después, use la herramienta `psql` (comando `psql.py` de Python). 

    En el ejemplo siguiente, se espera que copie el archivo `customers.csv` desde una cuenta de almacenamiento al directorio temporal local con `hdfs`, tal como se muestra en el paso 2 anterior.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Para determinar el nombre `ZookeeperQuorum`, busque la cadena de cuórum de ZooKeeper en el archivo `/etc/hbase/conf/hbase-site.xml` con el nombre de la propiedad `hbase.zookeeper.quorum`.

5. Una vez finalizada la operación `psql`, debería ver un mensaje en la ventana de comandos:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Usar MapReduce para cargar tablas de forma masiva

Para las cargas de mayor rendimiento distribuidas en el clúster, use la herramienta de carga de MapReduce. En primer lugar, este cargador convierte todos los datos en HFiles y, después, proporciona los archivos HFiles creados a HBase.

1. Inicie el cargador de CSV MapReduce con el comando `hadoop` con el archivo JAR del cliente de Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Cree una tabla nueva con una instrucción SQL, al igual que con `CreateCustomersTable.sql` en el paso 1 anterior.

3. Para comprobar el esquema de la tabla, ejecute `!describe inputTable`.

4. Determine la ruta de acceso a los datos de entrada, como en el archivo `customers.csv` de ejemplo. Los archivos de entrada pueden estar en la cuenta de almacenamiento WASB/ADLS. En este escenario de ejemplo, los archivos de entrada se encuentran en el directorio `<storage account parent>/inputFolderBulkLoad`.

5. Cambie al directorio de ejecución para el comando de carga masiva de MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Busque el valor `ZookeeperQuorum` en `/etc/hbase/conf/hbase-site.xml`, con el nombre de la propiedad `hbase.zookeeper.quorum`.

7. Configure la ruta de acceso de clase y ejecute el comando de la herramienta `CsvBulkLoadTool`:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Para usar MapReduce con ADLS, localice el directorio raíz de ADLS, que es el valor `hbase.rootdir` en `hbase-site.xml`. En el siguiente comando, el directorio raíz de ADLS es `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. En este comando, especifique las carpetas de entrada y salida de ADLS como parámetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Recomendaciones

* Use el mismo medio de almacenamiento, WASB o ADLS, para las carpetas de entrada y salida. Para transferir datos de WASB a ADLS, puede usar el comando `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nodos de trabajo de tamaño grande. Los procesos de asignación de la copia masiva de MapReduce generan grandes cantidades de resultados temporales que llenan el espacio disponible que no es DFS. Para una gran cantidad de carga masiva, use más nodos de trabajo y más grandes. El número de nodos de trabajo que asigne al clúster afecta directamente a la velocidad de procesamiento.

* Divida los archivos de entrada en fragmentos de aproximadamente 10 GB. La carga masiva es una operación de almacenamiento intensivo, por lo que dividir los archivos de entrada en varios fragmentos mejora el rendimiento.

* Evite puntos de acceso del servidor de región. Si la clave de fila aumenta de forma uniforme, las escrituras secuenciales de HBase pueden inducir puntos de acceso del servidor de región. *Cifrar* con sal la clave de fila reduce las escrituras secuenciales. Phoenix proporciona una manera de cifrar de forma transparente la clave de fila con un byte cifrado con sal para una determinada tabla, tal como se indica a continuación.

## <a name="next-steps"></a>pasos siguientes

* [Bulk Data Loading with Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html) (Carga de datos masiva con Apache Phoenix)
* [Uso de Apache Phoenix con clústeres de HBase basados en Linux en HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Salted Tables](https://phoenix.apache.org/salted.html) (Tablas cifradas)
* [Phoenix Grammar](http://phoenix.apache.org/language/index.html) (Gramática de Phoenix)
