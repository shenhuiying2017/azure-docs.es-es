---
title: "Qué son Apache Hive y HiveQL: Azure HDInsight | Microsoft Docs"
description: Apache Hive es un sistema de almacenamiento de datos para Hadoop. Puede consultar datos almacenados en Hive mediante HiveQL, que se parece a Transact-SQL. En este documento, aprenda a usar Hive y HiveQL con Azure HDInsight.
keywords: "hiveql,qué es hive,hadoop hiveql,cómo usar hive,aprender hive,qué es hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: ecf08b765ba17ac410f45bc3604a2aa0f3b4823e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>¿Qué son Apache Hive y HiveQL en Azure HDInsight?

[Apache Hive](http://hive.apache.org/) es un sistema de almacenamiento de datos para Hadoop. Hive hace posibles el resumen de los datos, las consultas y el análisis de datos. Las consultas de Hive se escriben en HiveQL, que es un lenguaje de consulta similar a SQL.

Hive le permite proyectar la estructura del proyecto en datos que en gran medida no están estructurados. Después de definir la estructura, puede usar HiveQL para consultar los datos sin conocimientos de Java ni MapReduce.

HDInsight proporciona varios tipos de clúster, que están optimizados para cargas de trabajo específicas. Se suelen usar los siguientes tipos de clúster para consultas de Hive:

* __Interactive Query__: un clúster de Hadoop que proporciona la característica de [procesamiento analítico de baja latencia (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) para mejorar los tiempos de respuesta de las consultas interactivas. Para más información, vea el documento [Uso de Hive interactivo con HDInsight (versión preliminar)](../interactive-query/apache-interactive-query-get-started.md).

* __Hadoop__: un clúster de Hadoop que está optimizado para cargas de trabajo de procesamiento por lotes. Para más información, consulte el documento sobre el [inicio con Hadoop en HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* __Spark__: Apache Spark tiene funcionalidad integrada para trabajar con Hive. Para más información, consulte el documento sobre el [inicio con Spark en HDInsight](../spark/apache-spark-jupyter-spark-sql.md).

* __HBase__: se puede usar HiveQL para consultar datos almacenados en HBase. Para más información, consulte el documento sobre el [inicio con HBase en HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md).

## <a name="how-to-use-hive"></a>Uso de Hive

Utilice la siguiente tabla para averiguar cómo usar Hive con HDInsight:

| **Use este método** si desea… | ...un shell **interactivo** | ...procesamiento por**lotes** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Cualquiera (en función del explorador) |
| [Cliente Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [API de REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux o Windows* |Linux, Unix, Mac OS X o Windows |
| [Herramientas de HDInsight para Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux o Windows* |Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux o Windows* |Windows |

> [!IMPORTANT]
> \* Linux es el único sistema operativo que se usa en HDInsight versión 3.4 o posteriores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Si está usando un clúster de HDInsight basado en Windows, puede usar la [consola de consulta](../hadoop/apache-hadoop-use-hive-query-console.md) desde el explorador o el [Escritorio remoto](../hadoop/apache-hadoop-use-hive-remote-desktop.md) para ejecutar consultas de Hive.

## <a name="hiveql-language-reference"></a>Referencia del lenguaje HiveQL

La referencia sobre el lenguaje HiveQL está disponible en el [manual del lenguaje (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive y estructura de datos

Hive entiende cómo trabajar con los datos estructurados y semiestructurados. Por ejemplo, archivos de texto donde los campos están delimitados por caracteres específicos. La siguiente instrucción HiveQL crea una tabla de datos delimitados por espacios:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive también admite **serializador/deserializadores (SerDe)** personalizados para datos estructurados irregularmente o complejos. Para obtener más información, consulte el documento [Uso de un SerDe de JSON personalizado con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

Para más información sobre los formatos de archivo compatibles con Hive, consulte el [manual del lenguaje (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-internal-tables-vs-external-tables"></a>Tablas internas frente a tablas externas de Hive.

Hay dos tipos de tablas que puede crear con Hive:

* __Internas__: los datos se almacenan en el almacenamiento de datos de Hive. El almacenamiento de datos se encuentra en `/hive/warehouse/` en el almacenamiento predeterminado para el clúster.

    Use tablas internas cuando:

    * Los datos sean temporales.
    * Desee que Hive administre el ciclo de vida de la tabla y los datos.

* __Externas__: los datos se almacenan fuera del almacenamiento de datos. Los datos se pueden almacenar en cualquier almacenamiento accesible desde el clúster.

    Use tablas externas cuando:

    * Los datos también se utilicen fuera de Hive. Por ejemplo, los archivos de datos se actualizan en otro proceso (que no bloquea los archivos).
    * Los datos deban permanecer en la ubicación subyacente, incluso después de eliminarse la tabla.
    * Necesite una ubicación personalizada, como una cuenta de almacenamiento no predeterminada.
    * Un programa distinto de Hive administre el formato de datos, la ubicación, etc.

Para más información, consulte la entrada de blog [Hive Internal and External Tables Intro][cindygross-hive-tables] (Introducción a las tablas internas y externas de Hive).

## <a name="user-defined-functions-udf"></a>Funciones definidas por el usuario (UDF)

Hive también puede extenderse a través de **funciones definidas por el usuario (UDF)**. Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL. Para obtener un ejemplo del uso de UDF con Hive, vea los siguientes documentos:

* [Utilización de una función definida por el usuario de Java con Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Uso de funciones definidas por el usuario (UDF) de Python con Hive y Pig](../hadoop/python-udf-hdinsight.md)

* [Uso de funciones definidas por el usuario de C# con Hive y Pig](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Cómo agregar una función definida por el usuario de Hive personalizada a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Un ejemplo de función definida por el usuario de Hive para convertir formatos de fecha y hora en una marca de tiempo de Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Datos de ejemplo

Hive en HDInsight tiene ya cargada una tabla interna denominada `hivesampletable`. Además, HDInsight proporciona conjuntos de datos de ejemplo que se pueden usar con Hive. Estos conjuntos de datos se almacenan en los directorios `/example/data` y `/HdiSamples`. Estos directorios existen en el almacenamiento predeterminado del clúster.

## <a id="job"></a>Ejemplo de consulta de Hive

Las siguientes instrucciones de HiveQL proyectan columnas sobre el archivo `/example/data/sample.log`:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

En el ejemplo anterior, las instrucciones de HiveQL realizan las acciones siguientes:

* `set hive.execution.engine=tez;`: establece el motor de ejecución para usar Tez. Si se utiliza Tez en lugar de MapReduce, se puede mejorar el rendimiento de las consultas. Para más información sobre Tez, consulte la sección [Use Apache Tez para un mejor rendimiento](#usetez) .

    > [!NOTE]
    > Esta instrucción solo se requiere cuando se usa un clúster de HDInsight basado en Windows. Tez es el motor de ejecución predeterminado para HDInsight basado en Linux.

* `DROP TABLE`: si la tabla ya existe, la elimina.

* `CREATE EXTERNAL TABLE`: crea una tabla **externa** en Hive. Las tablas externas solo almacenan la definición de Tabla en Hive. Los datos permanecen en la ubicación y formato originales.

* `ROW FORMAT`: Indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.

* `STORED AS TEXTFILE LOCATION`: indica a Hive dónde se almacenan los datos (el directorio `example/data`) y que se almacenen como texto. Los datos pueden estar en un archivo o distribuidos en varios archivos dentro del directorio.

* `SELECT`: selecciona el número total de filas donde la columna **t4** contiene el valor **[ERROR]**. Esta instrucción devuelve un valor de **3** porque hay tres filas que contienen este valor.

* `INPUT__FILE__NAME LIKE '%.log'`: Hive intenta aplicar el esquema a todos los archivos en el directorio. En este caso, el directorio contiene archivos que no coinciden con el esquema. Para evitar que haya datos inservibles en los resultados, esta instrucción indica a Hive que solo se deben devolver datos de archivos que terminen en .log.

> [!NOTE]
> Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un proceso de carga de datos automatizado o una operación de MapReduce.
>
> La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

Para crear una tabla **interna** en lugar de una externa, use el siguiente HiveQL:

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Estas instrucciones realizan las acciones siguientes:

* `CREATE TABLE IF NOT EXISTS`: si la tabla no existe, créela. Como no se usa la palabra clave **EXTERNAL**, esta instrucción crea una tabla interna. La tabla se almacena en el almacenamiento de datos de Hive y Hive la administra por completo.

* `STORED AS ORC`: almacena los datos en el formato de columnas de filas optimizadas (ORC, Optimized Row Columnar). ORC es un formato altamente optimizado y eficiente para almacenar datos de Hive.

* `INSERT OVERWRITE ... SELECT`: selecciona filas de la tabla **log4jLogs** que contiene **[ERROR]** y luego inserta los datos en la tabla **errorLogs**.

> [!NOTE]
> A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.

## <a name="improve-hive-query-performance"></a>Mejora del rendimiento de las consultas de Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) es un marco que permite que aplicaciones con uso intensivo de datos, como Hive, se ejecuten con mucha más eficacia a escala. Tez está habilitado de manera predeterminada para los clústeres de HDInsight basados en Linux.

> [!NOTE]
> Tez actualmente está desactivado de forma predeterminada para clústeres de HDInsight basados en Windows y debe estar habilitado. Para aprovechar Tez, debe establecerse el siguiente valor en una consulta de Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez es el motor predeterminado para clústeres de HDInsight basados en Linux.

Los [documentos de diseño de Hive en Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) incluyen detalles sobre las opciones de implementación y las configuraciones de ajuste.

Para ayudar a depurar los trabajos que se ejecutaron mediante Tez, HDInsight proporciona las siguientes interfaces de usuario web que le permiten ver los detalles de los trabajos de Tez:

* [Use the Ambari Tez view on Linux-based HDInsight (Uso de la vista Tez de Ambari en HDInsight basado en Linux)](../hdinsight-debug-ambari-tez-view.md)

* [Use the Tez UI on Windows-based HDInsight (Uso de la IU de Tez en HDInsight basado en Windows)](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Procesamiento analítico de baja latencia (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (conocido a veces como Larga vida y procesamiento) es una característica nueva de Hive 2.0 que permite el almacenamiento en memoria caché de las consultas. LLAP hace que las consultas de Hive sean mucho más rápidas, hasta [26 más que Hive 1.x en algunos casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight proporciona LLAP en el tipo de clúster Interactive Query. Para más información, vea el documento [Uso de Hive interactivo con HDInsight (versión preliminar)](../interactive-query/apache-interactive-query-get-started.md).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Trabajos de Hive y servicios de integración de SQL Server

Puede usar SQL Server Integration Services (SSIS) para ejecutar un trabajo de Hive. El paquete de características de Azure para SSIS proporciona los siguientes componentes que funcionan con trabajos de Hive en HDInsight.

* [Tarea de Hive de Azure HDInsight][hivetask]

* [Administrador de conexiones de suscripción de Azure][connectionmanager]

Obtenga más información sobre el paquete de características de Azure para SSIS [aquí][ssispack].

## <a id="nextsteps"></a>Pasos siguientes

Ahora que aprendió qué es Hive y cómo usarlo con Hadoop en HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de trabajos de MapReduce con HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
