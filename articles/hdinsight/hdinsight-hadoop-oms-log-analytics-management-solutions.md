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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Adición de soluciones de administración de clústeres de HDInsight a Log Analytics (versión preliminar)

HDInsight proporciona soluciones de administración específicas de clúster que se pueden agregar a Azure Log Analytics. Las [soluciones de administración](../log-analytics/log-analytics-add-solutions.md) agregan funcionalidad a OMS, que proporcionan datos y herramientas de análisis adicionales a Log Analytics. Estas soluciones recopilan importantes métricas de rendimiento de los clústeres de HDInsight y proporcionan las herramientas para buscar las métricas. También proporcionan visualizaciones y paneles para la mayoría de los tipos de clúster admitidos en HDInsight. Mediante el uso de las métricas que se recopilan con la solución, puede crear alertas y reglas de supervisión personalizadas. 

En este artículo, aprenderá a agregar soluciones de administración específicas de clúster a un área de trabajo de OMS.

## <a name="prerequisites"></a>Requisitos previos

* Debe haber configurado un clúster de HDInsight para usar Azure Log Analytics. Puede encontrar instrucciones en [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Uso de Azure Log Analytics con clústeres de HDInsight).

## <a name="add-cluster-specific-management-solutions"></a>Adición de soluciones de administración específicas de clúster

En esta sección, agregará una solución de administración de clúster de HBase a un área de trabajo de OMS existente. Próximamente estarán disponibles soluciones similares para otros tipos de clúster de HDInsight.

1. Abra el panel de OMS. En Azure Portal, abra la hoja del clúster de HDInsight que ha asociado con Azure Log Analytics, haga clic en la pestaña Supervisión y haga clic en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

1. En el panel de OMS, haga clic en **Galería de soluciones** o en el icono del Diseñador de vistas en el panel izquierdo.

    ![Agregar la solución de administración en OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Add management solution in OMS")

2. En la Galería de soluciones, busque **HDInsight HBase Monitoring** y, luego, haga clic en el icono.

    ![Buscar la solución de administración de HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "find HBase management solution")

3. En la siguiente pantalla, haga clic en **Agregar**.

     ![Agregar la solución de administración de HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Add HBase management solution")

4. Ahora debería ver un icono en el panel de OMS correspondiente a la solución de administración de HBase. Si el clúster asociado OMS (como parte de los requisitos previos de este artículo) es un clúster de HBase, el icono muestra el nombre del clúster y el número de nodos del clúster.

    ![Solución de administración de HBase agregada](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase management solution added")

## <a name="next-steps"></a>Pasos siguientes

* [Consulta de Azure Log Analytics para supervisar clústeres de Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Otras referencias

* [Working with OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/) (Uso de Log Analytics de OMS)
* [Creación de reglas de alerta en Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
