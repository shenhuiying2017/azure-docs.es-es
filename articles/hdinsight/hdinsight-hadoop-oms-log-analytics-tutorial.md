---
title: "Uso de Log Analytics para supervisar clústeres de Azure HDInsight | Microsoft Docs"
description: "Aprenda a usar Azure Log Analytics para supervisar trabajos que se ejecutan en un clúster de HDInsight."
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
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: af5b05a366c1abbe7c91d186358dba2b4a957f92
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Uso de Azure Log Analytics para supervisar clústeres de HDInsight (versión preliminar)

En este artículo, aprenderá a usar Azure Log Analytics para supervisar operaciones de clústeres HDInsight Hadoop.

Log Analytics es un servicio de [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que supervisa los entornos local y en la nube para mantener su disponibilidad y rendimiento. Recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión, para proporcionar análisis entre varios orígenes. 

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte la página [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

* **Un clúster de HDInsight de Azure**. Actualmente, puede usar Azure OMS con los siguientes tipos de clúster de HDInsight:
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Hive interactivo

    Para obtener instrucciones sobre cómo crear un clúster de HDInsight, consulte [Introducción a Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


* **Un área de trabajo de Log Analytics**. Considere el área de trabajo como un entorno de Log Analytics único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Debe tener ya creada una de estas áreas de trabajo que puede asociar a clústeres de Azure HDInsight. Para obtener instrucciones, consulte [Creación de un área de trabajo de Log Analytics](../log-analytics/log-analytics-get-started.md#2-create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Configuración de un clúster de HDInsight para usar Azure Log Analytics

En esta sección, configurará un clúster de HDInsight Hadoop existente para usar un área de trabajo de Azure Log Analytics para supervisar trabajos, depurar registros, etc.

1. En el panel izquierdo de Azure Portal, haga clic en **Clústeres de HDInsight** y luego haga clic en el nombre del clúster que quiere configurar con Azure Log Analytics.

2. En el panel izquierdo de la hoja de clúster, haga clic en **Supervisión**.

3. En el panel derecho, haga clic en **Habilitar** y, luego, seleccione un área de trabajo de Log Analytics existente. Haga clic en **Guardar**.

    ![Habilitar la supervisión para clústeres de HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Enable monitoring for HDInsight clusters")

4. Cuando el clúster esté configurado para usar Log Analytics para la supervisión, verá una opción **Abrir el panel de OMS** en la parte superior de la pestaña. Haga clic en el botón.

    ![Abrir el panel de OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Open OMS dashboard")

5. Escriba sus credenciales de Azure cuando se le solicite. Se le dirigirá al panel de Microsoft OMS.

    ![Portal de Operations Management](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite portal")

## <a name="next-steps"></a>Pasos siguientes
* [Add HDInsight cluster management solutions to Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Agregar soluciones de administración de clústeres de HDInsight a Log Analytics)

