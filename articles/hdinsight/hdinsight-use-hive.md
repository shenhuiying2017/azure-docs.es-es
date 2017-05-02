---
title: "Aprender qué es Hive y cómo usar HiveQL | Microsoft Docs"
description: "Obtenga información sobre Apache Hive y sobre cómo usarlo con Hadoop en HDInsight. Elija cómo ejecutar el trabajo de Hive y use HiveQL para analizar un archivo log4j de Apache de ejemplo."
keywords: hiveql,what is hive
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: db324cc8269a1f23613e5193f5a1dec1d26a62b1
ms.lasthandoff: 04/17/2017


---
# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight"></a>Uso de Hive y HiveQL con Hadoop en HDInsight

Aprenda a usar Hive con HDInsight. Utilice la tabla siguiente para conocer las distintas formas de usar Hive con HDInsight:

| **Utilícelo** si desea... | ...un shell **interactivo** | ...procesamiento por**lotes** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Vista de Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Cualquiera (en función del explorador) |
| [Comando de Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [API DE REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Consola de consultas](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 y 3.3) |&nbsp; |✔ |Windows |Cualquiera (en función del explorador) |
| [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux o Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux o Windows |Windows |
| [Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 y 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte la sección sobre el [desuso de HDInsight 3.2 y 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a id="why"></a>Qué es Hive

[Apache Hive](http://hive.apache.org/) es un sistema de almacenamiento de datos para Hadoop, que permite realizar resúmenes de datos, consultas y análisis de datos. Hive le permite proyectar la estructura del proyecto en datos que en gran medida no están estructurados. Después de definir la estructura, puede usar Hive para consultar esos datos sin conocimiento de Java o MapReduce.

Las consultas de Hive se escriben en HiveQL, que es un lenguaje de consulta similar a SQL. Hive se puede usar para explorar los datos de forma interactiva o para crear trabajos de procesamiento por lotes reutilizables.

Hive comprende cómo trabajar con datos estructurados y semiestructurados, como archivos de texto donde los campos están delimitados por caracteres específicos. Hive también admite **serializador/deserializadores (SerDe)** personalizados para datos estructurados irregularmente o complejos. Para obtener más información, consulte el documento [Uso de un SerDe de JSON personalizado con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Funciones definidas por el usuario (UDF)

Hive también puede extenderse a través de **funciones definidas por el usuario (UDF)**. Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL. Para obtener un ejemplo del uso de UDF con Hive, vea los siguientes documentos:

* [Use a Java User Defined Function with Hive](hdinsight-hadoop-hive-java-udf.md) (Uso de una función de Java definida por el usuario con Hive)

* [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md)

* [Uso de C# con Hive y Pig en HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Cómo agregar una UDF de Hive personalizada a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Ejemplo de UDF de Hive personalizado para convertir formatos de fecha y hora en la marca de tiempo de Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Tablas internas frente a tablas externas de Hive.

Existen determinados aspectos que debe conocer en relación con la tabla interna y externa de Hive:

* El comando **CREATE TABLE** crea una tabla interna. El archivo de datos debe ubicarse en el contenedor predeterminado.

* El comando **CREATE TABLE** mueve el archivo de datos al directorio `/hive/warehouse/<TableName>` en el almacenamiento predeterminado del clúster.

* El comando **CREATE EXTERNAL TABLE** crea una tabla externa. El archivo de datos puede estar ubicado fuera del contenedor predeterminado.

* El comando **CREATE EXTERNAL TABLE** no mueve el archivo de datos.

Para obtener más información, consulte [HDInsight: introducción de tablas internas y externas de Hive][cindygross-hive-tables].

## <a id="data"></a>Datos de ejemplo

HDInsight proporciona diversos conjuntos de datos de ejemplo que se almacenan en los directorios `/example/data` y `/HdiSamples`. Estos directorios existen en el almacenamiento predeterminado del clúster. Este documento utiliza el archivo `/example/data/sample.log`. Este archivo es un archivo *log4j*. Cada registro del archivo consta de una línea de campos que contiene un campo `[LOG LEVEL]` para mostrar el tipo y la gravedad, por ejemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

En el ejemplo anterior, el nivel de registro es ERROR.

> [!NOTE]
> También puede generar un archivo log4j con la utilidad de registro [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) y luego cargarlo en el contenedor de blobs. Consulte [Carga de datos en HDInsight](hdinsight-upload-data.md) para obtener instrucciones. Para obtener más información sobre el uso del almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Consulta de muestra de Hive

Las siguientes instrucciones de HiveQL proyectan columnas sobre el archivo `/example/data/sample.log`:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

En el ejemplo anterior, las instrucciones de HiveQL realizan las acciones siguientes:

* **set hive.execution.engine=tez;**: establece el motor de ejecución para usar Tez. Si se utiliza Tez en lugar de MapReduce, se puede mejorar el rendimiento de las consultas. Para más información sobre Tez, consulte la sección [Use Apache Tez para un mejor rendimiento](#usetez) .

    > [!NOTE]
    > Esta instrucción solo se requiere cuando se usa un clúster de HDInsight basado en Windows. Tez es el motor de ejecución predeterminado para HDInsight basado en Linux.

* **DROP TABLE**: si la tabla ya existe, la elimina.

* **CREATE EXTERNAL TABLE**: crea una nueva tabla **externa** en Hive. Las tablas externas solo almacenan la definición de Tabla en Hive. Los datos permanecen en la ubicación y formato originales.

* **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.

* **STORED AS TEXTFILE LOCATION** : indica a Hive dónde se almacenan los datos (el directorio `example/data`) y qué se almacenan como texto. Los datos pueden estar en un archivo o distribuidos en varios archivos dentro del directorio.

* **SELECT**: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esta instrucción devuelve un valor de **3** porque hay tres filas que contienen este valor.

* **INPUT__FILE__NAME LIKE '%.log'**: indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esta instrucción limita la búsqueda al archivo `sample.log` que contiene los datos.

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

* **CREATE TABLE IF NOT EXISTS**: si la tabla no existe, la crea. Dado que la palabra clave **EXTERNAL** no se usa, esta instrucción crea una tabla interna, que se almacena en el almacenamiento de datos de Hive y la administra Hive por completo .

* **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.

* **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contiene **[ERROR]** y luego inserta los datos en la tabla **errorLogs**.

> [!NOTE]
> A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.

## <a id="usetez"></a>Use Apache Tez para un mejor rendimiento

[Apache Tez](http://tez.apache.org) es un marco que permite que aplicaciones con uso intensivo de datos, como Hive, se ejecuten con mucha más eficacia a escala. En la última versión de HDInsight, Hive admite la ejecución en Tez. Tez está habilitado de manera predeterminada para los clústeres de HDInsight basados en Linux.

> [!NOTE]
> Tez actualmente está desactivado de forma predeterminada para clústeres de HDInsight basados en Windows y debe estar habilitado. Para aprovechar Tez, debe establecerse el siguiente valor en una consulta de Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez es el motor predeterminado para clústeres de HDInsight basados en Linux.

Los [documentos de diseño de Hive en Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) incluyen detalles sobre las opciones de implementación y las configuraciones de ajuste.

Para ayudar a depurar los trabajos que se ejecutaron mediante Tez, HDInsight proporciona las siguientes interfaces de usuario web que le permiten ver los detalles de los trabajos de Tez:

* [Use the Ambari Tez view on Linux-based HDInsight](hdinsight-debug-ambari-tez-view.md)

* [Use the Tez UI on Windows-based HDInsight](hdinsight-debug-tez-ui.md)

## <a id="run"></a>Ejecución del trabajo de HiveQL

HDInsight puede ejecutar trabajos de HiveQL mediante varios métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Utilícelo** si desea... | ...un shell **interactivo** | ...procesamiento por**lotes** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Vista de Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Cualquiera (en función del explorador) |
| [Comando de Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [API DE REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Consola de consultas](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 y 3.3) |&nbsp; |✔ |Windows |Cualquiera (en función del explorador) |
| [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux o Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux o Windows |Windows |
| [Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 y 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte la sección sobre el [desuso de HDInsight 3.2 y 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Trabajos de Hive y servicios de integración de SQL Server

También puede usar SQL Server Integration Services (SSIS) para ejecutar un trabajo de Hive. El paquete de características de Azure para SSIS proporciona los siguientes componentes que funcionan con trabajos de Hive en HDInsight.

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
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

