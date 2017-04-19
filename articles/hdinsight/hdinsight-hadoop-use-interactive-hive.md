---
title: Uso de Interactive Hive en HDInsight | Microsoft Docs
description: Aprenda a usar Interactive Hive (Hive en LLAP) en HDInsight.
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
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 72206e299c245187731e4397610bbcfb01f5311c
ms.lasthandoff: 04/18/2017


---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>Uso de Interactive Hive en HDInsight (versión preliminar)
Interactive Hive (también conocido como "[Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP))" es un nuevo [tipo de clúster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) de HDInsight.  Interactive Hive permite el almacenamiento en caché en memoria, con lo que las consultas de Hive serán mucho más interactivas y rápidas. Esta nueva característica convierte HDInsight en una de las soluciones de macrodatos en la nube más abiertas, flexibles y eficaces del mercado con almacenamiento caché en memoria (con Hive y Spark) y análisis avanzados gracias a la integración con R Services. 

El clúster de Interactive Hive es diferente al de Hadoop. Solo contiene el servicio de Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie y otros servicios se quitarán pronto de este tipo de clúster.
> Al servicio de Hive en el clúster de Interactive Hive solo puede accederse a través de la vista de Ambari Hive, Beeline y Hive ODBC. No se puede acceder mediante la consola de Hive, Templeton, la CLI de Azure y Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Creación de un clúster de Interactive Hive
El clúster de Interactive Hive solo es compatible con los clústeres basados en Linux. Para obtener más información sobre cómo crear clústeres de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-from-interactive-hive"></a>Ejecución de Hive desde Interactive Hive
Hay diferentes opciones en lo que respecta a cómo ejecutar consultas de Hive:

* Ejecución de Hive mediante la vista de Ambari Hive
  
    Para ver la información sobre cómo usar la vista de Hive, consulte [Use the Hive View with Hadoop in HDInsight](hdinsight-hadoop-use-hive-ambari-view.md) (Uso de la vista de Hive con Hadoop en HDInsight).
* Ejecución de Hive mediante Beeline
  
    Para obtener más información sobre cómo utilizar Beeline en HDInsight, consulte [Uso de Hive con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    Utilice Beeline en el nodo principal o en uno perimetral vacío.  Se recomienda usar Beeline en un nodo perimetral vacío.  Para obtener información sobre cómo crear un clúster de HDInsight con un nodo perimetral vacío, consulte [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Uso de nodos perimetrales vacíos en HDInsight).
* Ejecución de Hive mediante Hive ODBC
  
    Para obtener información sobre cómo usar Hive ODBC, consulte [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md).

**Para encontrar la cadena de conexión de JBDC, siga estos pasos:**

1. Inicie sesión en Ambari con la siguiente dirección URL: https://<ClusterName>. AzureHDInsight.net.
2. Haga clic en **Hive** en el menú izquierdo.
3. Haga clic en el icono resaltado para copiar la dirección URL:
   
   ![Cadena de conexión de JDBC para Interactive Hive con LLAP (Hadoop de HDInsight)](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Consulte también
* [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md): aprenda a crear clústeres de Interactive Hive en HDInsight.
* [Uso de Hive con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md): obtenga información sobre cómo usar Beeline para enviar consultas de Hive.
* [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md): aprenda a conectar Excel con Hive.


