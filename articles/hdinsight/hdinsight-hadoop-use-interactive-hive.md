---
title: Uso de Hive interactivo con Azure HDInsight | Microsoft Docs
description: Aprenda a usar Hive interactivo (Hive LLAP) con HDInsight.
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
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: es-es
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>Uso de Hive interactivo con HDInsight (versión preliminar)
Hive interactivo (también llamado Hive LLAP, o [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) es un nuevo [tipo de clúster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) de Azure HDInsight. Hive interactivo es compatible con el almacenamiento en caché en memoria, lo que hace consultas de Hive sean más rápidas y mucho más interactivas. 

El clúster de Hive interactivo es diferente al de Hadoop. Solo contiene el servicio de Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie y otros servicios se quitarán pronto de este tipo de clúster.
> Solo se puede acceder al servicio Hive en el clúster de Hive interactivo mediante la vista de Hive de Ambari, Beeline y el controlador de conectividad abierta de bases de datos de Microsoft Hive (Hive ODBC). No se puede acceder a él mediante la consola de Hive, Templeton, la herramienta de línea de comando (CLI de Azure) o Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Creación de un clúster de Interactive Hive
Solo se pueden usar clústeres de Hive interactivo en clústeres basados en Linux. Para más información sobre cómo crear clústeres de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-queries-from-interactive-hive"></a>Ejecución de consultas de Hive desde Hive interactivo
Para ejecutar consultas de Hive, tiene las siguientes opciones:

* Ejecutar Hive mediante la vista de Hive de Ambari.
  
    Para más información sobre cómo usar la vista de Hive, consulte [Uso de Ambari Hive View con Hadoop en HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Ejecutar Hive mediante Beeline.
  
    Para más información sobre cómo usar Beeline en HDInsight, consulte [Uso de Hive con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    Puede usar Beeline desde el nodo principal o desde un nodo perimetral vacío. Se recomienda usar Beeline en un nodo perimetral vacío. Para más información sobre la creación de un clúster de HDInsight con un nodo perimetral vacío, consulte [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md).
* Ejecute Hive mediante Hive ODBC
  
    Para más información sobre el uso de Hive ODBC, consulte [Conexión de Excel a Hadoop con el controlador ODBC de Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

Para buscar la cadena de conexión de Java Database Connectivity (JDBC):

1. Inicie sesión Ambari mediante la siguiente dirección URL: https://\<nombre del clúster\>.AzureHDInsight.net.
2. En el menú de la izquierda, seleccione **Hive**.
3. Para copiar la dirección URL, seleccione el icono del Portapapeles:
   
   ![Cadena de conexión de JDBC para Interactive Hive con LLAP (Hadoop de HDInsight)](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [crear clústeres de Hive interactivo en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Aprenda a [usar Beeline para enviar consultas de Hive en HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Aprenda a [conectar Excel a Hadoop con el controlador ODBC de Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).


