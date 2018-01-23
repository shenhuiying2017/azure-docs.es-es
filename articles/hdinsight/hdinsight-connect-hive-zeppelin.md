---
title: Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight | Microsoft Docs
description: Aprenda a usar Zeppelin para ejecutar consultas de Hive.
keywords: hdinsight,hadoop,hive,interactive query,LLAP
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 39f99bef252e93db55e0493ee284ef78b7d087a1
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight 

Los clústeres de HDInsight Interactive Query incluyen blocs de notas de Zeppelin con los que puede ejecutar consultas de Hive interactivas. Con este artículo aprenderá a usar Zeppelin para ejecutar consultas de Hive en Azure HDInsight. 

## <a name="prerequisites"></a>requisitos previos
Antes de seguir los pasos de este artículo, debe disponer de los siguientes elementos:

* **Clúster de HDInsight Interactive Query**. Vea [Crear clúster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para crear un clúster de HDInsight.  Asegúrese de elegir el tipo Interactive Query. 

## <a name="create-a-zeppelin-note"></a>Crear una nota de Zeppelin

1. Vaya a esta dirección URL:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Reemplace **CLUSTERNAME** por el nombre del clúster.

2. Escriba el nombre de usuario y la contraseña de Hadoop. En la página de Zeppelin, puede crear una nota o abrir notas existentes. HiveSample contiene algunos ejemplos de consultas de Hive.  

    ![Zeppelin de HDInsight Interactive Query](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Haga clic en **Create new Note** (Crear nueva nota).
4. Escriba o seleccione los valores siguientes:

    - Nombre de la nota: escriba un nombre para la nota.
    - Default interpreter (Intérprete predeterminado): seleccione **JDBC**.

5. Haga clic en **Crear nota**.
6. Ejecute esta consulta de Hive:

        %jdbc(hive)
        show tables

    ![Zeppelin de HDInsight Interactive Query ejecuta una consulta](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    La instrucción **%jdbc(hive)** en la primera línea indica al bloc de notas que debe usar el intérprete JDBC de Hive.

    La consulta devolverá una tabla de Hive denominada *hivesampletable*.

    Aquí se muestran dos consultas más de Hive que se pueden ejecutar en hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Comparado con Hive tradicional, los resultados de la consulta se devuelven mucho más rápido.


## <a name="next-steps"></a>pasos siguientes
En este artículo ha aprendido a visualizar datos de HDInsight mediante Power BI.  Para obtener más información, consulte los artículos siguientes:

* [Visualize Hive data with Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md) (Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight)
* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conexión de Excel a Hadoop en Azure HDInsight con Microsoft Hive ODBC Driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conexión de Excel a Hadoop con Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carga de datos en HDInsight](./hdinsight-upload-data.md).
