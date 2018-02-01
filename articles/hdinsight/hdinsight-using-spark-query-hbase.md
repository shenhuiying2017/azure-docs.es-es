---
title: Uso de Spark para leer y escribir datos de HBase en Azure HDInsight | Microsoft Docs
description: "Use el conector Spark HBase para leer y escribir datos de un clúster Spark en un clúster HBase."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: ccbcd1d9cb45da7076d73f71a2ed692e71816650
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Uso de Spark para leer y escribir datos de HBase

Apache HBase se consulta, normalmente, con su API de bajo nivel (instrucciones scan, get y put) o con una sintaxis SQL que utiliza Phoenix. Apache también proporciona el conector Spark HBase, que es una alternativa práctica y eficaz para consultar y modificar los datos almacenados por HBase.

## <a name="prerequisites"></a>requisitos previos

* Dos clústeres HDInsight independientes, uno HBase y otro Spark con Spark 2.1 (HDInsight 3.6) instalado.
* El clúster Spark debe comunicarse directamente con el clúster HBase con una latencia mínima, por lo que la configuración recomendada es implementar ambos clústeres en la misma red virtual. Para obtener más información, consulte [Crear clústeres basados en Linux en HDInsight con Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* Acceso SSH a cada clúster.
* Acceso al almacenamiento predeterminado de cada clúster.

## <a name="overall-process"></a>Proceso general

El proceso de alto nivel para habilitar el clúster Spark para consultar el clúster HDInsight es el siguiente:

1. Prepare algunos datos de ejemplo en HBase.
2. Recupere el archivo hbase-site.xml de la carpeta de configuración del clúster HBase (/etc/hbase/conf).
3. Coloque una copia de hbase-site.xml en la carpeta de configuración de Spark 2 (/etc/spark2/conf).
4. Ejecute `spark-shell` haciendo referencia al conector Spark HBase por sus coordenadas Maven en la opción `packages`.
5. Defina un catálogo que asigne el esquema de Spark a HBase.
6. Interactúe con los datos de HBase mediante las API de DataFrame o RDD.

## <a name="prepare-sample-data-in-hbase"></a>Preparar datos de ejemplo en HBase

En este paso, creará y rellenará una tabla sencilla en HBase que, a continuación, podrá consultar con Spark.

1. Conéctese al nodo principal del clúster HBase mediante SSH. Para más información, vea [Conexión a través de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Ejecute el shell HBase:

        hbase shell

3. Cree una tabla `Contacts` con las familias de columna `Personal` y `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Cargue algunas filas de datos de ejemplo:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Recuperar hbase-site.xml del clúster HBase

1. Conéctese al nodo principal del clúster HBase mediante SSH.
2. Copie hbase-site.xml del almacenamiento local en la raíz del almacenamiento predeterminado del clúster HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Navegue hasta el clúster HBase a través de [Azure Portal](https://portal.azure.com).
4. Seleccione Cuentas de almacenamiento. 

    ![Cuentas de almacenamiento](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Seleccione la cuenta de almacenamiento en la lista que tenga una marca de verificación en la columna de valor predeterminado.

    ![Cuenta de almacenamiento predeterminada](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. En el panel de la cuenta de almacenamiento, seleccione el icono de blobs.

    ![Icono de blobs](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. En la lista de contenedores, seleccione el contenedor que usa el clúster HBase.
8. En la lista de archivos, seleccione `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. En el panel Propiedades del blob, seleccione las acciones de descargar y guardar `hbase-site.xml` en una ubicación del equipo local.

    ![Descargar](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar hbase-site.xml en el clúster Spark

1. Navegue hasta el clúster Spark con [Azure Portal](https://portal.azure.com).
2. Seleccione Cuentas de almacenamiento.

    ![Cuentas de almacenamiento](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Seleccione la cuenta de almacenamiento en la lista que tenga una marca de verificación en la columna de valor predeterminado.

    ![Cuenta de almacenamiento predeterminada](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. En el panel de la cuenta de almacenamiento, seleccione el icono de blobs.

    ![Icono de blobs](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. En la lista de contenedores, seleccione el contenedor que usa el clúster Spark.
6. Seleccione Cargar.

    ![Cargar](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Seleccione el archivo `hbase-site.xml` que ha descargado previamente en el equipo local.

    ![Cargar hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Seleccione Cargar.
9. Conéctese al nodo principal del clúster Spark mediante SSH.
10. Copie `hbase-site.xml` del almacenamiento predeterminado del clúster Spark en la carpeta de configuración de Spark 2 en el almacenamiento local del clúster:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Ejecutar el shell Spark haciendo referencia al conector Spark HBase

1. Conéctese al nodo principal del clúster Spark mediante SSH.
2. Inicie el shell Spark, especificando el paquete del conector Spark HBase:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11

3. Mantenga abierta esta instancia de shell Spark y continúe con el paso siguiente.

## <a name="define-a-catalog-and-query"></a>Definir un catálogo y una consulta

En este paso, definirá un objeto de catálogo que asigne el esquema de Spark a HBase. 

1. En el shell Spark abierto, ejecute las siguientes instrucciones `import`:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Defina un catálogo para la tabla de contactos creada en HBase:
    1. Defina un esquema de catálogo para la tabla HBase denominada `Contacts`.
    2. Identifique el objeto rowkey como `key` y asigne los nombres de columna usados en Spark a la familia de columna, el nombre de columna y el tipo de columna tal como se usa en HBase.
    3. El objeto rowkey también tiene que definirse con detalle como una columna con nombre (`rowkey`), que tiene una familia de columna específica `cf` de `rowkey`.

            def catalog = s"""{
                |"table":{"namespace":"default", "name":"Contacts"},
                |"rowkey":"key",
                |"columns":{
                |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
                |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
                |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
                |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
                |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
                |}
            |}""".stripMargin

3. Defina un método que proporcione un elemento DataFrame en la tabla `Contacts` en HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Cree una instancia de DataFrame:

        val df = withCatalog(catalog)

5. Consulte el elemento DataFrame:

        df.show()

6. Verá dos filas de datos:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registre una tabla temporal para poder consultar la tabla HBase mediante Spark SQL:

        df.registerTempTable("contacts")

8. Emita una consulta SQL en la tabla `contacts`:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Verá resultados parecidos a los siguientes:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Insertar nuevos datos

1. Para insertar un nuevo registro de contacto, defina una clase `ContactRecord`:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Cree una instancia de `ContactRecord` y colóquela en una matriz:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Guarde la matriz de nuevos datos en HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Examine los resultados:
    
        df.show()

5. La salida debería ser parecida a la que se muestra a continuación:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>pasos siguientes

* [Conector Spark HBase](https://github.com/hortonworks-spark/shc)
