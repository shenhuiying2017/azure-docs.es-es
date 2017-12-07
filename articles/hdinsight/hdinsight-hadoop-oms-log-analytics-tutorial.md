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
ms.date: 11/27/2017
ms.author: nitinme
ms.openlocfilehash: ca2cf642cfff2961dcb0dd18f0e712f61d6915c2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Uso de Azure Log Analytics para supervisar clústeres de Azure HDInsight

Aprenda a usar Azure Log Analytics para supervisar las operaciones de los clústeres de Hadoop en HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) es un servicio de [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que supervisa los entornos local y de nube para mantener su disponibilidad y rendimiento. Recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión, para proporcionar análisis entre varios orígenes. 

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte la página [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

* **Un clúster de HDInsight de Azure**. Actualmente, puede usar Azure Operations Management Suite con los siguientes tipos de clúster de HDInsight:

    * Hadoop
    * HBase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    Para instrucciones sobre cómo crear un clúster en HDInsight, consulte el artículo de [introducción a Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Un área de trabajo de Log Analytics**. Considere el área de trabajo como un entorno de Log Analytics único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Debe tener ya creada una de estas áreas de trabajo que puede asociar a clústeres de Azure HDInsight. Para obtener instrucciones, consulte [Creación de un área de trabajo de Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Habilitación de Log Analytics mediante el portal

En esta sección, configurará un clúster de HDInsight Hadoop existente para usar un área de trabajo de Azure Log Analytics para supervisar trabajos, depurar registros, etc.

1. Abra un clúster de HDInsight en Azure Portal.
2. En el panel izquierdo, haga clic en **Supervisión**.
3. En el panel derecho, haga clic en **Habilitar**, seleccione un área de trabajo de Log Analytics existente y haga clic en **Guardar**.

    ![Habilitar la supervisión para clústeres de HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Enable monitoring for HDInsight clusters")

    Guardar la configuración tarda unos minutos.  Una vez terminado, verá un botón **Abrir el panel de OMS** en la parte superior. 

    ![Abrir el panel de Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Abrir el panel de OMS")

5. Haga clic en **Abrir el panel de OMS**.
6. Escriba sus credenciales de Azure cuando se le solicite.

    ![Portal de Operations Management](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite portal")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Habilitación de Log Analytics mediante Azure PowerShell

Puede habilitar Log Analytics con Azure PowerShell. El cmdlet es el siguiente:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

Consulte [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Este es el cmdlet para deshabilitarlo: 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

Consulte [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Pasos siguientes
* [Add HDInsight cluster management solutions to Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Agregar soluciones de administración de clústeres de HDInsight a Log Analytics)
