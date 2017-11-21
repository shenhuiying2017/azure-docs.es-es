---
title: Uso de Interactive Query con Azure HDInsight | Microsoft Docs
description: Aprenda a usar Interactive Query (Hive LLAP) con HDInsight.
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: jgao
ms.openlocfilehash: 6da89b7b63231fe5886c82e84e06abc2ee7b3e38
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="use-interactive-query-with-hdinsight"></a>Uso de Interactive Query con HDInsight
Interactive Query (también llamado Hive LLAP o [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) es un nuevo [tipo de clúster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types) de Azure HDInsight. Interactive Query admite el almacenamiento en caché en memoria, lo que hace que las consultas de Hive sean más rápidas y mucho más interactivas. 

Un clúster de Interactive Query no es igual que un clúster de Hadoop. Solo contiene el servicio de Hive. 

> [!NOTE]
> Solo se puede acceder al servicio Hive en el clúster de Interactive Query mediante la vista Ambari Hive, Beeline y el controlador de conectividad abierta de bases de datos de Microsoft Hive (Hive ODBC). No se puede acceder a él mediante la consola de Hive, Templeton, la herramienta de línea de comando (CLI de Azure) o Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Creación de un clúster de Interactive Query
Vea [Creación de clústeres de Hadoop en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) para más información sobre la creación de un clúster de HDInsight. Elija el tipo de clúster de Interactive Query.

## <a name="execute-hive-queries-from-interactive-query"></a>Ejecutar consultas de Hive desde Interactive Query
Para ejecutar consultas de Hive, tiene las siguientes opciones:

* Usar Power BI

    Vea [Visualize Hive data with Microsoft Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md) (Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight).

* Uso de Zeppelin

    Vea [Use Zeppelin to run Hive queries in Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md) (Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight).

* Usar Visual Studio

    Vea [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).

* Usar Visual Studio Code

    Vea [Use Visual Studio Code for Hive, LLAP or pySpark](../hdinsight-for-vscode.md) (Uso de Visual Studio Code para Hive, LLAP o pySpark).
* Ejecutar Hive mediante la vista de Hive de Ambari.
  
    Vea [Use Hive View with Hadoop in Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md) (Uso de Hive View con Hadoop en Azure HDInsight).
* Ejecutar Hive mediante Beeline.
  
    Vea [Uso de Hive con Hadoop en HDInsight con Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Puede usar Beeline desde el nodo principal o desde un nodo perimetral vacío. Se recomienda usar Beeline en un nodo perimetral vacío. Para más información sobre la creación de un clúster de HDInsight con un nodo perimetral vacío, consulte [Uso de nodos perimetrales vacíos en HDInsight](../hdinsight-apps-use-edge-node.md).
* Ejecute Hive mediante Hive ODBC
  
    Vea [Conexión de Excel a Hadoop en Azure HDInsight con el controlador ODBC de Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para buscar la cadena de conexión de Java Database Connectivity (JDBC):

1. Inicie sesión Ambari mediante la siguiente dirección URL: https://\<nombre del clúster\>.AzureHDInsight.net.
2. En el menú de la izquierda, seleccione **Hive**.
3. Para copiar la dirección URL, seleccione el icono del Portapapeles:
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [crear clústeres de Interactive Query en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Aprenda a [visualizar datos de Hive con Microsoft Power BI en Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Aprenda a [usar Zeppelin para ejecutar consultas de Hive en Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Aprenda a [ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).
* Obtenga información sobre cómo [usar Herramientas de HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* Aprenda a [usar Hive View con Hadoop en HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Aprenda a [usar Beeline para enviar consultas de Hive en HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Aprenda a [conectar Excel a Hadoop con el controlador ODBC de Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

