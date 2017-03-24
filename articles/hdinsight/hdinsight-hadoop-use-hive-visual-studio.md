---
title: Consulta de Hive con herramientas de Hadoop para Visual Studio | Microsoft Docs
description: "Obtenga información acerca de cómo usar Hadoop en HDInsight con las herramientas de Hadoop de Visual Studio."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 0627155a86f961531cfb11c0d8dc7a66eafbf0cf
ms.lasthandoff: 03/07/2017


---
# <a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Ejecución de las consultas de Hive mediante las herramientas de HDInsight para Visual Studio

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Aprenda a enviar consultas de Hive a un clúster de HDInsight mediante las herramientas de HDInsight para Visual Studio.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Azure HDInsight (Hadoop en HDInsight).

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Visual Studio (una de las siguientes versiones):
  
    * Visual Studio 2013 Comunidad/Professional/Premium/Ultimate con [actualización 4](https://www.microsoft.com/download/details.aspx?id=44921)
  
    * Visual Studio 2015 (cualquier edición)

    * Visual Studio 2017 (cualquier edición)

* Herramientas de HDInsight para Visual Studio o Azure Data Lake Tools for Visual Studio. Consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) para ejecutar una consulta de Hive para conocer los pasos que le permitirán instalar y configurar las herramientas.

## <a id="run"></a> Ejecución de las consultas de Hive usando Visual Studio

1. Abra **Visual Studio** y seleccione **Nuevo** > **Proyecto** > **Azure Data Lake** > **HIVE** > **Hive Application** (Aplicación de Hive). Indique un nombre para este proyecto.

2. Abra el archivo **Script.hql** creado con este proyecto y péguelo en las siguientes instrucciones de HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```
   
    Estas instrucciones realizan las acciones siguientes:
   
   * `DROP TABLE`: Si la tabla existe, esta instrucción la elimina.

   * `CREATE EXTERNAL TABLE`: Crea una tabla 'externa' en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original.
     
     > [!NOTE]
     > Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
     > 
     > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

   * `ROW FORMAT`: Indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.

   * `STORED AS TEXTFILE LOCATION`: Indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenen como texto.

   * `SELECT`: Seleccione un número de todas las filas donde la columna `t4` contiene el valor `[ERROR]`. Esta instrucción devuelve un valor de `3` porque hay tres filas que contienen este valor.

   * `INPUT__FILE__NAME LIKE '%.log'`: Indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esta cláusula limita la búsqueda al archivo sample.log que contiene los datos.

3. En la barra de herramientas, seleccione el **clúster de HDInsight** que desea usar para esta consulta. Seleccione **Enviar** para ejecutar las instrucciones como un trabajo de Hive.

   ![Barra Enviar](./media/hdinsight-hadoop-use-hive-visual-studio/toolbar.png)

4. El **resumen del trabajo de Hive** aparecerá y mostrará información sobre el trabajo en ejecución. Use el vínculo **Actualizar** para actualizar la información del trabajo, hasta que el **estado del trabajo** cambie a **Completado**.

   ![resumen de trabajos que muestra un trabajo completado](./media/hdinsight-hadoop-use-hive-visual-studio/jobsummary.png)

5. Utilice el vínculo **Salida de trabajo** para ver el resultado de este trabajo. Muestra `[ERROR] 3`, que es el valor que devuelve esta consulta.

6. También puede ejecutar consultas de Hive sin crear un proyecto. Con el **Explorador de servidores**, expanda **Azure** > **HDInsight**, haga clic con el botón derecho en el servidor de HDInsight y luego seleccione **Escribir una consulta de Hive**.

7. En el documento **temp.hql** que aparece, agregue las siguientes instrucciones de HiveQL.
   
   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Estas instrucciones realizan las acciones siguientes:
   
   * `CREATE TABLE IF NOT EXISTS`: Crea una tabla, si todavía no existe. Dado que no se utiliza la palabra clave `EXTERNAL`, esta instrucción crea una tabla interna. Las tablas internas se guardan en el almacenamiento de datos de Hive y Hive las administra.
     
     > [!NOTE]
     > A diferencia de las tablas `EXTERNAL`, la eliminación de una tabla interna también eliminará los datos subyacentes.

   * `STORED AS ORC`: Almacena los datos en el formato de columnas de filas optimizadas (ORC, Optimized Row Columnar). ORC es un formato altamente optimizado y eficiente para almacenar datos de Hive.

   * `INSERT OVERWRITE ... SELECT`: Selecciona filas de la tabla `log4jLogs` que contienen `[ERROR]` y luego inserta los datos en la tabla `errorLogs`.

8. En la barra de herramientas, seleccione **Enviar** para ejecutar el trabajo. Utilice el **estado del trabajo** para determinar que el trabajo se ha completado correctamente.

9. Para comprobar que el trabajo ha creado una nueva tabla, utilice el **Explorador de servidores** y expanda **Azure** > **HDInsight** > el clúster de HDInsight > **Bases de datos de Hive** > **predeterminado**. La tabla **errorLogs** y la tabla **log4jLogs** aparecen en la lista.

## <a id="nextsteps"></a>Pasos siguientes

Como puede ver, las herramientas de HDInsight para Visual Studio proporcionan una manera sencilla de trabajar con consultas de Hive en HDInsight.

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información acerca de las herramientas de HDInsight para Visual Studio:

* [Introducción a las herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

