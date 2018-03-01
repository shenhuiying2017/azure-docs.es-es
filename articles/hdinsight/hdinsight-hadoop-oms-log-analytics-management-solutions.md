---
title: "Adición de soluciones de clúster de HDInsight a Azure Log Analytics | Microsoft Docs"
description: "Aprenda a usar soluciones de administración de clústeres de Azure Log Analytics para crear vistas personalizadas para clústeres de HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: f8a073fbf40feaef97053bb1165d63bbca3e3feb
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Incorporación de soluciones de administración de clústeres de HDInsight en Log Analytics

HDInsight proporciona soluciones de administración específicas de clúster que se pueden agregar a Azure Log Analytics. Las [soluciones de administración](../log-analytics/log-analytics-add-solutions.md) agregan funcionalidad a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) y proporcionan datos y herramientas de análisis adicionales para Log Analytics. Estas soluciones recopilan importantes métricas de rendimiento de los clústeres de HDInsight y proporcionan las herramientas para buscar las métricas. También proporcionan visualizaciones y paneles para la mayoría de los tipos de clúster admitidos en HDInsight. Mediante el uso de las métricas que se recopilan con la solución, puede crear alertas y reglas de supervisión personalizadas. 

En este artículo obtendrá información sobre cómo agregar soluciones de administración específicas de clúster a un área de trabajo de Operations Management Suite.

## <a name="prerequisites"></a>requisitos previos

* Debe haber configurado un clúster de HDInsight para usar Azure Log Analytics. Puede encontrar instrucciones en [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Uso de Azure Log Analytics con clústeres de HDInsight).

## <a name="add-cluster-specific-management-solutions"></a>Adición de soluciones de administración específicas de clúster

En esta sección agregará una solución de administración de clúster de HBase a un área de trabajo existente de Operations Management Suite.

1. Haga clic en un clúster de HDInsigt en Azure Portal, haga clic en **Supervisión** y en **Abrir el panel de OMS**.

    ![Abrir el panel de Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Abrir el panel de OMS")

1. En el panel de OMS, haga clic en **Galería de soluciones** o en el icono del **Diseñador de vistas** del panel izquierdo.

    ![Agregar una solución de administración en Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Agregar una solución de administración en Operations Management Suite")

2. En la Galería de soluciones, haga clic en uno de los iconos siguientes:

    - Supervisión de HDInsight Hadoop
    - HDInsight HBase Monitoring (versión preliminar)
    - Supervisión de HDInsight Kafka
    - Supervisión de HDInsight Storm
    - Supervisión de HDInsight Spark

3. En la siguiente pantalla, haga clic en **Agregar**.  La captura de pantalla siguiente muestra el botón Agregar para HBase Monitoring.

     ![Agregar la solución de administración de HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Add HBase management solution")

4. Verá un icono en el panel de OMS correspondiente a la solución de administración de HBase. Si el clúster que asoció a Operations Management Suite (como parte de los requisitos previos de este artículo) es un clúster de HBase, se mostrarán el nombre del clúster y el número de nodos del clúster en el icono.

    ![Solución de administración de HBase agregada](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase management solution added")

## <a name="next-steps"></a>pasos siguientes

* [Consulta de Azure Log Analytics para supervisar clústeres de Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Otras referencias

* [Trabajo con Log Analytics de Operations Management Suite](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creación de reglas de alerta en Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
