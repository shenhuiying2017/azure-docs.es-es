---
title: Uso de Hive en Hadoop y Escritorio remoto en HDInsight (Azure) | Microsoft Docs
description: "Aprenda a conectarse a un clúster de Hadoop en HDInsight mediante Escritorio remoto y, a continuación, ejecute consultas de Hive usando la interfaz de línea de comandos (CLI) de Hive."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: ded90b0f94e545b4066a0220afffae26f1cd15ee
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Uso de Hive con Hadoop en HDInsight con el escritorio remoto
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

En este artículo, obtendrá información sobre cómo conectarse a un clúster de HDInsight mediante Escritorio remoto y, a continuación, ejecutar consultas de Hive usando la interfaz de línea de comandos (CLI) de Hive.

> [!IMPORTANT]
> Remote Desktop solo está disponible para clústeres de HDInsight que usan Windows como sistema operativo. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para HDInsight 3.4 o superior, consulte [Uso de Hive con HDInsight y Beeline](apache-hadoop-use-hive-beeline.md) para más información sobre cómo ejecutar consultas de Hive directamente en el clúster desde una línea de comandos.

## <a id="prereq"></a>Requisitos previos
Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de HDInsight basado en Windows (Hadoop en HDInsight)
* Un equipo cliente con Windows 10, Window 8 o Windows 7

## <a id="connect"></a>Conexión con el Escritorio remoto
Habilite el Escritorio remoto para el clúster de HDInsight y conéctese a él siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Uso del comando de Hive
Cuando se haya conectado al escritorio para el clúster de HDInsight, utilice los pasos siguientes para trabajar con Hive.

1. Desde el escritorio de HDInsight, inicie la **línea de comandos de Hadoop**.
2. Introduzca el siguiente comando para iniciar la CLI de Hive:

        %hive_home%\bin\hive

    Una vez iniciada la CLI, verá el símbolo del sistema de la CLI de Hive: `hive>`.
3. Con la CLI, introduzca las siguientes instrucciones para crear una nueva tabla denominada **log4jLogs** con los datos de ejemplo:

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
   * **SELECT**: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
   * **INPUT__FILE__NAME LIKE '%.log'**: indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y le impide que devuelva datos de otros archivos de datos de ejemplo que no coinciden con el esquema que hemos definido.
4. Use las siguientes instrucciones para crear una nueva tabla "interna" llamada **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las acciones siguientes:

   * **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no se usa, se trata de una tabla interna, que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive.

     > [!NOTE]
     > A diferencia de las tablas **EXTERNAL** , la eliminación de una tabla interna también eliminará los datos subyacentes.
     >
     >
   * **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
   * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.

     Para comprobar que solamente las filas que contienen **[ERROR]** en la columna t4 se almacenaron en la tabla **errorLogs**, use la siguiente instrucción para devolver todas las filas de **errorLogs**:

       SELECT * de errorLogs;

     Se deben devolver tres filas de datos y todas ellas deben contener **[ERROR]** en la columna t4.

## <a id="summary"></a>Resumen
Como puede ver, el comando de Hive proporciona una manera fácil de ejecutar consultas de Hive interactivamente en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
