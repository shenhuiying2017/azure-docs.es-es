<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitor" pageTitle="Monitor HDInsight | Azure" metaKeywords="" description="Learn how to monitor an HDInsight cluster and view Hadoop job history through the Azure management portal." metaCanonical="" services="hdinsight" documentationCenter="" title="How to Monitor HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

Supervisión de HDInsight
========================

En este artículo, aprenderá a supervisar un clúster de HDInsight.

Tabla de contenido
------------------

-   [Supervisión de un clúster de HDInsight](#monitorcluster)
-   [Visualización del historial de trabajos de Hadoop](#jobhistory)

Supervisión de un clúster de HDInsight
--------------------------------------

Para supervisar el estado de un clúster de HDInsight y los trabajos de Hadoop que se están ejecutando en él, puede conectarse al panel de HDInsight y hacer clic en el mosaico de supervisión de clúster.

![MosaicoSupervisiónCluster.HDI](./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG)

La página de supervisión tiene este aspecto:

![PáginaSupervisión.HDI](./media/hdinsight-monitor/HDI.MonitorPage.PNG)

A la derecha, se observa que Namenode y la herramienta de seguimiento de trabajos están en funcionamiento y que los cuatro nodos de datos se ejecutan con normalidad.

A la izquierda, aparecen las métricas de MapReduce correspondientes a los últimos 30 minutos. Puede cambiar las ventanas de supervisión a 30 minutos, 1 hora, 3 horas, 12 horas, 1 día, 2 días, 1 semana y 2 semanas.

Visualización del historial de trabajos de Hadoop
-------------------------------------------------

Para ver el historial de trabajos de Hadoop, conéctese al panel de HDInsight y, a continuación, haga clic en el mosaico del historial de trabajos.

![MosaicoHistorialTrabajos.HDI](./media/hdinsight-monitor/HDI.TileJobHistory.PNG)

En el mosaico aparece el número de trabajos que se ejecutaron; por ejemplo, la imagen anterior indica que el historial de trabajos incluye seis trabajos. La página del historial de trabajos tiene este aspecto:

![PáginaHistorialTrabajos.HDI](./media/hdinsight-monitor/HDI.JobHistoryPage.PNG)

Otras referencias
-----------------

-   [Administración de HDInsight](/en-us/manage/services/hdinsight/howto-administer-hdinsight/)
-   [Implementación de un clúster de HDInsight mediante programación](/en-us/manage/services/hdinsight/howto-deploy-cluster/)
-   [Ejecución de trabajos remotos en el clúster de HDInsight mediante programación](/en-us/manage/services/hdinsight/howto-execute-jobs-programmatically/)
-   [Tutorial: Introducción a HDInsight de Azure](/en-us/manage/services/hdinsight/get-started-hdinsight/)

