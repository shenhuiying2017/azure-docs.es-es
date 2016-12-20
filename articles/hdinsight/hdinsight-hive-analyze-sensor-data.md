---
title: "Análisis de datos de sensor usando Hive y Hadoop | Microsoft Docs"
description: Aprenda a analizar datos de sensor usando la Consola de consultas de Hive con HDInsight (Hadoop) y a visualizar los datos en Microsoft Excel con PowerView.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ef2e4b9272389f4e027821e84df4499bf8987c39


---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Análisis de datos de sensor mediante la consola de consultas de Hive en Hadoop con HDInsight 
Aprenda a analizar datos de sensor usando la consola de consultas de Hive con HDInsight (Hadoop) y a visualizar los datos en Microsoft Excel con Power View.

> [!NOTE]
> Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows.
> 
> 

En este ejemplo, usará Hive para procesar datos de historial producidos por sistemas de calefacción, ventilación y aire acondicionado (HVAC) para identificar sistemas que no pueden mantener de manera fiable una temperatura establecida . Aprenderá a:

* Crear tablas de HIVE para consultar datos almacenados en archivos de valores separados por comas (CSV).
* Crear consultas de HIVE para analizar los datos.
* Usar Microsoft Excel para conectarse a HDInsight (usando una conexión de base de datos abierta, ODBC) para recuperar los datos analizados.
* Usar Power View para visualizar los datos.

![Diagrama de la arquitectura de la solución](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Requisitos previos
* Un clúster de HDInsight (Hadoop): consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md) para obtener información sobre la creación de un clúster.
* Microsoft Excel 2013
  
  > [!NOTE]
  > Microsoft Excel se usa para la visualización de datos con [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).
  > 
  > 
* [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Para ejecutar el ejemplo
1. Desde el explorador web, navegue a la siguiente dirección URL. Reemplace `<clustername>` por el nombre del clúster de HDInsight.
   
         https://<clustername>.azurehdinsight.net
   
    Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó este clúster.
2. En la página web que se abre, haga clic en la pestaña **Getting Started Gallery** (Galería de introducción) y, a continuación, en la categoría **Solutions with Sample Data** (Soluciones con datos de ejemplo), haga clic en el ejemplo **Análisis de datos del sensor**.
   
    ![Introducción a la galería de imágenes](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)
3. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.




<!--HONumber=Nov16_HO3-->


