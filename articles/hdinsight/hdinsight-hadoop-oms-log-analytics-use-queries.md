---
title: "Consulta de Azure Log Analytics para supervisar clústeres de Azure HDInsight | Microsoft Docs"
description: "Aprenda a ejecutar consultas en Azure Log Analytics para supervisar los trabajos que se ejecutan en un clúster de HDInsight."
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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: da7b83846418bfe5f95b126d4f5f7b34d3a7b35d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Consulta de Azure Log Analytics para supervisar clústeres de HDInsight

Conozca algunos escenarios básicos de uso de Azure Log Analytics para supervisar clústeres de Azure HDInsight:

* [Análisis de métricas de clúster de HDInsight](#analyze-hdinsight-cluster-metrics)
* [Búsqueda de mensajes de registro específicos](#search-for-specific-log-messages)
* [Creación de alertas de eventos](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Requisitos previos

* Debe haber configurado un clúster de HDInsight para usar Azure Log Analytics. Puede encontrar instrucciones en [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Uso de Azure Log Analytics con clústeres de HDInsight).

* Debe haber agregado las soluciones de administración específicas del clúster de HDInsight al área de trabajo de [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), como se describe en [Add HDInsight cluster management solutions to Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Adición de soluciones de administración de clústeres de HDInsight a Log Analytics).

## <a name="analyze-hdinsight-cluster-metrics"></a>Análisis de métricas de clúster de HDInsight

Aprenda a buscar métricas específicas de su clúster de HDInsight.

1. Abra un clúster de HDInsight que haya asociado con Azure Log Analytics en Azure Portal.
2. Haga clic en **Supervisión** y, a continuación, en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. Haga clic en **Búsqueda de registros** en el menú izquierdo.

    ![Abrir Búsqueda de registros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. Escriba la siguiente consulta en el cuadro de texto para buscar todas las métricas de todas las disponibles para todos los clústeres de HDInsight que están configurados para usar Azure Log Analytics y, luego, presione **ENTRAR**.

        `search *` 

    ![Buscar todas las métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Search all metrics")

    La salida debe parecerse a esta:

    ![Salida de búsqueda de todas las métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Search all metrics output")

5. En el panel izquierdo, en **Tipo**, seleccione una métrica en la que quiera profundizar y, a continuación, haga clic en **Aplicar**. La siguiente captura de pantalla muestra que se ha seleccionado el tipo `metrics_resourcemanager_queue_root_default_CL`. 

    > [!NOTE]
    > Puede que deba hacer clic en el botón **[+]Más** para encontrar la métrica que busca. Además, el botón **Aplicar** se encuentra en la parte inferior de la lista por lo que debe desplazarse hacia abajo para verlo.
    > 
    >    

    Tenga en cuenta que la consulta del cuadro de texto cambia a la que se muestra en el cuadro resaltado en la captura de pantalla siguiente:

    ![Buscar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Search for specific metrics")

6. Para profundizar en esta métrica específica, puede, por ejemplo, restringir la salida existente en función de los recursos medios usados en un intervalo de 10 minutos, clasificados por nombre del clúster mediante la siguiente consulta:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. En lugar de restringir en función de los recursos medios usados, puede usar la siguiente consulta para restringir los resultados según el número máximo de recursos que se usaron (así como los percentiles 90 y 95) en una ventana de 10 minutos:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Búsqueda de mensajes de registro específicos

Aprenda a buscar mensajes de error durante una ventana de tiempo específica. Estos pasos son un ejemplo de cómo puede llegar al mensaje de error en el que está interesado. Para encontrar los errores que busca, puede usar cualquier propiedad que esté disponible.

1. Abra un clúster de HDInsight que haya asociado con Azure Log Analytics en Azure Portal.
2. Haga clic en **Supervisión** y, a continuación, en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. En la pantalla principal del panel de OMS, haga clic en **Búsqueda de registros**.

    ![Abrir Búsqueda de registros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. Escriba la siguiente consulta para buscar todos los mensajes de error de todos los clústeres de HDInsight configurados para usar Azure Log Analytics y, a continuación, presione **ENTRAR**. 

         search "Error"

    Debería ver una salida similar a la siguiente:

    ![Salida de búsqueda de todos los errores](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Search all errors output")

5. En el panel izquierdo, en la categoría **Tipo**, seleccione un tipo de error en el que quiera profundizar y, a continuación, haga clic en **Aplicar**.  Observe que los resultados se restringen para mostrar únicamente el error del tipo seleccionado.
7. Puede profundizar en esta lista específica de errores mediante las opciones disponibles en el panel izquierdo. Por ejemplo, 

    - Para ver los mensajes de error de un nodo de trabajo específico:

        ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Search for specific errors output")

    - Para ver que un error producido en un momento determinado:

        ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Search for specific errors output")

9. Para ver el error específico: Puede hacer clic en **[+] Mostrar más** para examinar el mensaje de error real.

    ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Search for specific errors output")

## <a name="create-alerts-for-tracking-events"></a>Creación de alertas para el seguimiento de eventos

El primer paso para crear una alerta es llegar a una consulta en función de la cual se desencadena la alerta. Por simplicidad, se usará la siguiente consulta que proporciona una lista de aplicaciones con error que se ejecutan en clústeres de HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Puede usar cualquier consulta que desee para crear una alerta.

1. Abra un clúster de HDInsight que haya asociado con Azure Log Analytics en Azure Portal.
2. Haga clic en **Supervisión** y, a continuación, en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. En la pantalla principal del panel de OMS, haga clic en **Búsqueda de registros**.

    ![Abrir Búsqueda de registros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. Ejecute la siguiente consulta en la que quiere crear una alerta y, a continuación, presione **ENTRAR**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Haga clic en **Alerta** en la parte superior de la página.

    ![Escribir una consulta para crear una alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter query to create an alert")

4. En la ventana **Agregar regla de alerta**, escriba la consulta y otros detalles para cfrear una alerta; a continuación, haga clic en **Guardar**.

    ![Escribir una consulta para crear una alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter query to create an alert")

    La captura de pantalla muestra la configuración para enviar una notificación por correo electrónico cuando la consulta de alerta devuelve un resultado.

5. También puede editar o eliminar una alerta existente. Para ello, desde cualquier página del portal de OMS, haga clic en el icono **Configuración**.

    ![Escribir una consulta para crear una alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter query to create an alert")

6. En la página **Configuración**, haga clic en **Alertas** para ver las alertas que ha creado. También puede habilitar o deshabilitar una alerta, editarla o eliminarla. Para más información, consulte [Uso de reglas de alertas en Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Otras referencias

* [Trabajo con Log Analytics de Operations Management Suite](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creación de reglas de alerta en Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
