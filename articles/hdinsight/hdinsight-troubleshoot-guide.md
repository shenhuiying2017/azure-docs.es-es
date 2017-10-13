---
title: "Guías de solución de problemas de Azure HDInsight | Microsoft Docs"
description: "Solucione problemas de cargas de trabajo de Hadoop con Azure HDInsight. La documentación paso a paso muestra cómo usar HDInsight para resolver problemas habituales con Hive, Spark, YARN, HBase, HDFS y Storm."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.openlocfilehash: f246c6d1355220314a2f56aa91634625ec352c69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Solución de problemas mediante Azure HDInsight

| Carga de trabajo de Apache | Preguntas más frecuentes |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Solución de problemas de HBase](hdinsight-troubleshoot-hbase.md)|<br>[Cómo se ejecutan informes de comando hbck con varias regiones sin asignar](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Cómo solucionar problemas de tiempo de espera con comandos hbck para las asignaciones de regiones](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Cómo forzar la deshabilitación del modo seguro de HDFS en un clúster](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Cómo se solucionan problemas de conectividad de JDBC o SQLLine con Apache Phoenix](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Qué hace que un servidor maestro no se pueda iniciar](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Qué provoca un error de reinicio en un servidor de regiones](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Solución de problemas de HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Cómo se accede a la instancia de HDFS local desde un clúster](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Cómo forzar la deshabilitación del modo seguro de HDFS en un clúster](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Solución de problemas de HBase](hdinsight-troubleshoot-hive.md)|<br>[How do I export a Hive metastore and import it on another cluster](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster) (Cómo se explota Hive Metastore y se importa en otro clúster)<br><br>[Búsqueda de registros de Hive en un clúster](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Inicio del shell de Hive con configuraciones específicas en un clúster](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[¿Cómo se analizan datos de DAG de Tez en una ruta crítica de clúster?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[How do I download Tez DAG data from a cluster](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster) (Cómo se descargan datos de Tez DAG desde un clúster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Solución de problemas de Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Cómo se configura una aplicación de Spark a través de Ambari en clústeres](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Cómo se configura una aplicación de Spark a través de un cuaderno de Jupyter Notebook en clústeres](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Cómo se configura una aplicación de Spark a través de LIVY en clústeres](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Cómo se configura una aplicación de Spark a través de spark-submit en clústeres](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Qué produce una excepción OutOfMemoryError en una aplicación Spark](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Solución de problemas de Storm](hdinsight-troubleshoot-STORM.md)|<br>[Acceso a la interfaz de usuario de Storm en un clúster](hdinsight-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Transferencia de información del punto de control del spout del centro de eventos de Storm de una topología a otra](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Cómo se buscan los archivos binarios de Storm en un clúster](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Cómo se determina la topología de implementación de un clúster de Storm](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Localización de los archivos binarios del spout del centro de eventos de Storm para el desarrollo](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Cómo se encuentran los archivos de configuración Log4J de Storm en clústeres](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Solución de problemas de YARN](hdinsight-troubleshoot-YARN.md)|<br>[Creación de una nueva cola Yarn en un clúster](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Descarga de los registros de Yarn desde un clúster](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Recursos de solución de problemas de HDInsight

| Para información acerca de | Consulte estos artículos |
| --- | --- |
| HDInsight en Linux y optimización | - [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md)<br>- [Solución de problemas de memoria y rendimiento de Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Rendimiento de consultas de Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Registros y volcados de memoria | - [Acceso a registros de aplicación de YARN en HDInsight basado en Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Habilitación de los volcados de montón de los servicios de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Análisis de registros de HDInsight](hdinsight-debug-jobs.md)|
| Errors | - [Entender y resolver errores recibidos de WebHCat en HDInsight](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Corrección de un error de memoria insuficiente de Hive en Azure HDInsight](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Herramientas | - [Uso de vistas de Ambari para depurar trabajos de Tez](hdinsight-debug-ambari-tez-view.md)<br>- [Optimización de consultas de Hive](hdinsight-hadoop-optimize-hive-query.md) |
