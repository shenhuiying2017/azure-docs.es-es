---
title: Adición de soluciones de clúster de HDInsight a Azure Log Analytics | Microsoft Docs
description: Aprenda a usar soluciones de administración de clústeres de Azure Log Analytics para crear vistas personalizadas para clústeres de HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: caa10682f8419cfbc0837e5069357e02ee1e5f64
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Incorporación de soluciones de administración de clústeres de HDInsight en Log Analytics

HDInsight proporciona soluciones de administración específicas de clúster que se pueden agregar a Azure Log Analytics. Las [soluciones de administración](../log-analytics/log-analytics-add-solutions.md) agregan funcionalidad a Log Analytics, que proporcionan datos y herramientas de análisis adicionales. Estas soluciones recopilan importantes métricas de rendimiento de los clústeres de HDInsight y proporcionan las herramientas para buscar las métricas. También proporcionan visualizaciones y paneles para la mayoría de los tipos de clúster admitidos en HDInsight. Mediante el uso de las métricas que se recopilan con la solución, puede crear alertas y reglas de supervisión personalizadas. 

En este artículo, aprenderá a agregar soluciones de administración específicas de clúster a un área de trabajo de Log Analytics.

## <a name="prerequisites"></a>requisitos previos

* Debe haber configurado un clúster de HDInsight para usar Azure Log Analytics. Puede encontrar instrucciones en [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Uso de Azure Log Analytics con clústeres de HDInsight).

## <a name="add-cluster-specific-management-solutions"></a>Adición de soluciones de administración específicas de clúster

En esta sección, agregará una solución de administración de clúster de HBase a un área de trabajo de Log Analytics existente.

1. Abra un clúster de HDInsigt en Azure Portal, haga clic en **Supervisión** y en **Abrir el panel de OMS**.

    ![Abrir el panel de Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Abrir el panel de OMS")

1. En el panel, haga clic en **Galería de soluciones** o en el icono del **Diseñador de vistas** del panel izquierdo.

    ![Agregar una solución de administración en Log Analytics](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Agregar una solución de administración en Operations Management Suite")

2. En la Galería de soluciones, haga clic en uno de los iconos siguientes:

    - Supervisión de HDInsight Hadoop
    - HDInsight HBase Monitoring (versión preliminar)
    - Supervisión de HDInsight Kafka
    - Supervisión de HDInsight Storm
    - Supervisión de HDInsight Spark

3. En la siguiente pantalla, haga clic en **Agregar**.  La captura de pantalla siguiente muestra el botón Agregar para HBase Monitoring.

     ![Agregar la solución de administración de HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Add HBase management solution")

4. Verá un icono en el panel correspondiente a la solución de administración de HBase. Si el clúster que asoció a Operations Management Suite (como parte de los requisitos previos de este artículo) es un clúster de HBase, se mostrarán el nombre del clúster y el número de nodos del clúster en el icono.

    ![Solución de administración de HBase agregada](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase management solution added")

## <a name="next-steps"></a>Pasos siguientes

* [Consulta de Azure Log Analytics para supervisar clústeres de Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Otras referencias

* [Working with Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/) (Uso de Log Analytics)
* [Creación de reglas de alerta en Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
