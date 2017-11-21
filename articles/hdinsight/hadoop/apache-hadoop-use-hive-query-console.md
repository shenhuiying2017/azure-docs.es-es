---
title: Uso de Hive de Hadoop en la consola de consulta en HDInsight - Azure | Microsoft Docs
description: "Aprenderá a utilizar la consola de consulta basada en web para ejecutar consultas de Hive en un clúster de Hadoop de HDInsight desde el explorador."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: d6032b8a1e3d338b046c958804102aeb9efcf4ab
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-using-the-query-console"></a>Ejecución de consultas de Hive mediante la consola de consulta
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a utilizar la consola de la consulta de HDInsight para ejecutar consultas de Hive en un clúster de Hadoop de HDInsight desde el explorador.

> [!IMPORTANT]
> La consola de consulta de HDInsight solo está disponible en los clústeres de HDInsight basados en Windows. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para HDInsight 3.4 o superior, consulte [Ejecución de consultas de Hive en la vista Hive de Ambari](apache-hadoop-use-hive-ambari-view.md) para más información sobre cómo ejecutar consultas de Hive desde un explorador web.

## <a id="prereq"></a>Requisitos previos
Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Hadoop para HDInsight basado en Windows
* Un explorador web moderno

## <a id="run"></a> Ejecución de consultas de Hive mediante la consola de consulta
1. Abra un explorador web y vaya a **https://CLUSTERNAME.azurehdinsight.net**, donde **CLUSTERNAME** es el nombre de su clúster de HDInsight. Si se le solicita, escriba el nombre de usuario y la contraseña que escribió cuando creó el clúster.
2. En los vínculos de la parte superior de la página, seleccione **Editor Hive**. Se muestra un formulario que puede utilizarse para introducir instrucciones de HiveQL que desea ejecutar en el clúster de HDInsight.

    ![el editor de Hive](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Reemplace el texto `Select * from hivesampletable` por las siguientes instrucciones de HiveQL.

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las acciones siguientes:

   * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
   * **CREATE EXTERNAL TABLE**: crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original.

     > [!NOTE]
     > Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
     >
     > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.
     >
     >
   * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
   * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio de ejemplos/datos) y que se almacenen como texto.
   * **SELECT**: permite seleccionar un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
   * **INPUT__FILE__NAME LIKE '%.log'**: indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y le impide que devuelva datos de otros archivos de datos de ejemplo que no coinciden con el esquema que hemos definido.
3. Haga clic en **Enviar**. La **sesión de trabajo** en la parte inferior de la página debe mostrar detalles del trabajo.
4. Cuando el campo **Estado** cambie a **Completado**, seleccione **Ver detalles** para el trabajo. En la página de detalles, **Job Output** (Salida de trabajo) contendrá `[ERROR]    3`. Puede utilizar el botón **Descargar** situado debajo de este campo para descargar un archivo que contiene la salida del trabajo.

## <a id="summary"></a>Resumen
Como puede ver, la consola de consulta proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, supervisar el estado del trabajo y recuperar la salida.

Para obtener más información acerca del uso de la consola de consulta de Hive para ejecutar trabajos de Hive, seleccione **Introducción** en la parte superior de la consola de consulta y, a continuación, use los ejemplos proporcionados. Cada ejemplo se recorre el proceso de uso de Hive para analizar datos, incluidas explicaciones de las instrucciones de HiveQL utilizadas en el ejemplo.

## <a id="nextsteps"></a>Pasos siguientes
Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si usa Tez con Hive, consulte los siguientes documentos para la información de depuración:

* [Use the Tez UI on Windows-based HDInsight](../hdinsight-debug-tez-ui.md)
* [Use the Ambari Tez view on Linux-based HDInsight](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

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

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
