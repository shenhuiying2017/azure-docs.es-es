---
title: "Análisis de datos de sensor usando Hive y Hadoop (Azure HDInsight) | Microsoft Docs"
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
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 3abb71c12b4769bebd808276f8bdd832aad22d7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Análisis de datos de sensor mediante la consola de consultas de Hive en Hadoop con HDInsight 

Aprenda a analizar datos de sensor usando la consola de consultas de Hive con HDInsight (Hadoop) y a visualizar los datos en Microsoft Excel con Power View.

> [!IMPORTANT]
> Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. HDInsight solo está disponible en Windows en versiones inferiores a la 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


En esta muestra, usa Hive para procesar datos históricos e identificar problemas con sistemas de calefacción y aire acondicionado. En concreto, se puedan identificar sistemas que no son capaces de mantener de forma confiable una temperatura fijada mediante la realización de las tareas siguientes:

* Crear tablas de HIVE para consultar datos almacenados en archivos de valores separados por comas (CSV).
* Crear consultas de HIVE para analizar los datos.
* Para recuperar los datos analizados, use Microsoft Excel para conectarse a HDInsight.
* Para visualizar los datos, use Power View.

![Diagrama de la arquitectura de la solución](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight (Hadoop): consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md) para obtener información sobre la creación de un clúster.
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel se usa para la visualización de datos con [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Para ejecutar el ejemplo

1. Desde el explorador web, navegue a la siguiente dirección URL: 

         https://<clustername>.azurehdinsight.net

    Reemplace `<clustername>` por el nombre del clúster de HDInsight.

    Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó este clúster.

2. En la página web que se abre, haga clic en la pestaña **Getting Started Gallery** (Galería de introducción) y, a continuación, en la categoría **Solutions with Sample Data** (Soluciones con datos de ejemplo), haga clic en el ejemplo **Análisis de datos del sensor**.

    ![Introducción a la galería de imágenes](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.
