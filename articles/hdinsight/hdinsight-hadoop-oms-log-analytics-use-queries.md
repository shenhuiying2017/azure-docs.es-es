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
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Consulta de Azure Log Analytics para supervisar clústeres de HDInsight (versión preliminar)

En este artículo, se examinan algunos escenarios de uso de Azure Log Analytics con clústeres de Azure HDInsight. Los tres escenarios más comunes son:

* Análisis de métricas de clúster de HDInsight en OMS
* Búsqueda de mensajes de registro específicos para clústeres de HDInsight
* Creación de alertas basadas en eventos que se producen en los clústeres

## <a name="prerequisites"></a>Requisitos previos

* Debe haber configurado un clúster de HDInsight para usar Azure Log Analytics. Puede encontrar instrucciones en [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Uso de Azure Log Analytics con clústeres de HDInsight).

* Debe haber agregado las soluciones de administración específicas del clúster de HDInsight al área de trabajo de OMS, como se describe en [Add HDInsight cluster management solutions to Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Adición de soluciones de administración de clústeres de HDInsight a Log Analytics).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Análisis de métricas de clúster de HDInsight en OMS

Esta sección le guiará por los pasos para buscar métricas específicas para su clúster de HDInsight.

1. Abra el panel de OMS. En Azure Portal, abra la hoja del clúster de HDInsight que ha asociado con Azure Log Analytics, haga clic en la pestaña Supervisión y haga clic en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. En la pantalla principal del panel de OMS, haga clic en **Búsqueda de registros**.

    ![Abrir Búsqueda de registros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. En el cuadro de texto **Begin search here** (Iniciar búsqueda aquí) de la ventana Búsqueda de registros, escriba `*` para buscar todas las métricas de todas las disponibles para todos los clústeres de HDInsight configurados para usar Azure Log Analytics. Presione Entrar.

    ![Buscar todas las métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Search all metrics")

4. La salida debe ser parecida a la siguiente:

    ![Salida de búsqueda de todas las métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Search all metrics output")

5. En el panel izquierdo, en la categoría **Tipo**, busque una métrica que quiera explorar en profundidad. En este tutorial, se seleccionará `metrics_resourcemanager_queue_root_default_CL`. Active la casilla correspondiente a la métrica y, a continuación, haga clic en **Aplicar**.

    > [!NOTE]
    > Puede que deba hacer clic en el botón **[+]Más** para encontrar la métrica que busca. Además, el botón **Aplicar** se encuentra en la parte inferior de la lista por lo que debe desplazarse hacia abajo para verlo.
    > 
    >    
    Tenga en cuenta que la consulta del cuadro de texto ahora cambia a la que se muestra en el cuadro resaltado en la captura de pantalla siguiente:

    ![Buscar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Search for specific metrics")

6. Ahora puede profundizar más en esta métrica específica. Por ejemplo, ahora puede restringir la salida existente en función de los recursos medios usados en un intervalo de 10 minutos, clasificados por nombre del clúster. Escriba la consulta siguiente en el cuadro de texto de consulta.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Buscar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Search for specific metrics")

7. En lugar de restringir en función de los recursos medios usados, puede utilizar la siguiente consulta para restringir los resultados según el número máximo de recursos que se usaron (así como los percentiles 90 y 95) en una ventana de 10 minutos.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Buscar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Search for specific metrics")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Búsqueda de mensajes de registro específicos en clústeres de HDInsight

Esta sección le guía por los pasos para buscar mensajes de error durante una ventana de tiempo específica. Estos pasos son un ejemplo de cómo puede llegar al mensaje de error en el que está interesado. Para encontrar los errores que busca, puede usar cualquier propiedad que esté disponible.

1. Abra el panel de OMS. En Azure Portal, abra la hoja del clúster de HDInsight que ha asociado con Azure Log Analytics, haga clic en la pestaña Supervisión y haga clic en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. En la pantalla principal del panel de OMS, haga clic en **Búsqueda de registros**.

    ![Abrir Búsqueda de registros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. En el cuadro de texto **Begin search here** (Iniciar búsqueda aquí), de la ventana Búsqueda de registros, escriba `"Error"` (con las comillas) para buscar todos los mensajes de error de todos los clústeres de HDInsight configurados para usar Azure Log Analytics. Presione Entrar.

    ![Buscar todos los errores](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Search all errors")

4. La salida debe ser parecida a la siguiente:

    ![Salida de búsqueda de todos los errores](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Search all errors output")

5. En el panel izquierdo, en la categoría **Tipo**, busque un tipo de error que quiera explorar en profundidad. En este tutorial, se seleccionará `log_sparkappsexecutors_CL`. Active la casilla correspondiente a la métrica y, a continuación, haga clic en **Aplicar**.

    ![Buscar errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Search for specific error")

        
6. Tenga en cuenta que la consulta del cuadro de texto ahora cambia a la que se muestra en el cuadro resaltado a continuación y que los resultados se restringen para mostrar solamente el error del tipo seleccionado.

    ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Search for specific errors output")

7. Ahora puede profundizar en esta lista de errores específicos mediante las opciones disponibles en el panel izquierdo. Por ejemplo, puede restringir la consulta para examinar solamente los mensajes de error de un nodo de trabajo determinado.

    ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Search for specific errors output")

8. Además, puede acotar aún más la hora a la que cree que se produjo el error mediante la selección del valor de tiempo pertinente en el panel izquierdo.

    ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Search for specific errors output")

9. Ahora ya está en el error específico que busca. Puede hacer clic en **[+] Mostrar más** para examinar el mensaje de error real.

    ![Salida de búsqueda de errores específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Search for specific errors output")

## <a name="create-alerts-to-track-events"></a>Creación de alertas para realizar el seguimiento de eventos

El primer paso para crear una alerta es llegar a una consulta en función de la cual se desencadena la alerta. Por simplicidad, se usará la siguiente consulta que proporciona una lista de aplicaciones con error que se ejecutan en clústeres de HDInsight.

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

Puede usar cualquier consulta que desee para crear una alerta.

1. Abra el panel de OMS. En Azure Portal, abra la hoja del clúster de HDInsight que ha asociado con Azure Log Analytics, haga clic en la pestaña Supervisión y haga clic en **Abrir el panel de OMS**.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. En la pantalla principal del panel de OMS, haga clic en **Búsqueda de registros**.

    ![Abrir Búsqueda de registros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. En el cuadro de texto **Begin search here** (Iniciar búsqueda aquí) de la ventana Búsqueda de registros, pegue la consulta en la que quiere crear una alerta, presione Entrar y luego haga clic en el botón **Alerta**.

    ![Escribir una consulta para crear una alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter query to create an alert")

4. En la ventana **Agregar regla de alerta**, escriba la consulta y otros detalles para cfrear una alerta; a continuación, haga clic en **Guardar**.

    ![Escribir una consulta para crear una alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter query to create an alert")

    En esta captura de pantalla, solo se envía una notificación por correo electrónico si la consulta de alerta recupera una salida.

5. También puede editar o eliminar una alerta existente. Para ello, desde cualquier página del portal de OMS, haga clic en el icono **Configuración**.

    ![Escribir una consulta para crear una alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter query to create an alert")

6. En la página **Configuración**, haga clic en **Alertas** para ver las alertas que ha creado. También puede habilitar o deshabilitar una alerta, editarla o eliminarla. Para más información, consulte [Uso de reglas de alertas en Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Otras referencias

* [Working with OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/) (Uso de Log Analytics de OMS)
* [Creación de reglas de alerta en Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
