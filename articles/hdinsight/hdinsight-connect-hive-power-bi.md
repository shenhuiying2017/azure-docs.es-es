---
title: Visualizar macrodatos con Power BI en Azure HDInsight | Microsoft Docs
description: Aprenda a usar Microsoft Power BI para visualizar datos de Hive procesados por Azure HDInsight.
keywords: hdinsight,hadoop,hive,consulta interactiva,hive interactivo,LLAP,odbc
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
ms.openlocfilehash: 9a17c984f5ca801dfa017f1d6fc8fa5ccdeaf39b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>Visualizar datos de Hive con Microsoft Power BI en Azure HDInsight

Aprenda a conectar Microsoft Power BI a Azure HDInsight y a visualizar los datos de Hive. Actualmente, Power BI solo admite la conexión ODBC a HDInsight. En este tutorial se cargan los datos de una tabla de Hive (hivesampletable) en Power BI. La tabla de Hive contiene algunos datos de uso de teléfonos móviles. Luego se trazan los datos de uso en un mapamundi:

![Informe de mapa de Power BI en HDInsight](./media/hdinsight-connect-power-bi/hdinsight-power-bi-visualization.png)

La información también se aplica al nuevo tipo de clúster de [consulta interactiva](./hdinsight-hadoop-use-interactive-hive.md).

## <a name="prerequisites"></a>Requisitos previos
Antes de seguir los pasos de este artículo, debe disponer de los siguientes elementos:

* **Clúster de HDInsight**. El clúster puede ser un clúster de HDInsight con Hive o un clúster de consulta interactiva recién lanzado. Para crear clústeres, vea [Crear clúster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Puede descargar una copia desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Creación de un origen de datos de Hive ODBC

Vea [Creación de un origen de datos de Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carga de datos desde HDInsight

La tabla de Hive hivesampletable incluye todos los clústeres de HDInsight.

1. Inicie sesión en Power BI Desktop.
2. Haga clic en la pestaña **Inicio**, en **Obtener datos** en la cinta **Datos externos** y luego seleccione **Más**.

    ![Datos abiertos de Power BI en HDInsight](./media/hdinsight-connect-power-bi/hdinsight-power-bi-open-odbc.png)
3. En el panel **Obtener datos**, haga clic en **Otro** a la izquierda, en **ODBC** a la derecha y luego en **Conectar** en la parte inferior.
4. En el panel **De ODBC**, seleccione el nombre del origen de datos creado en la última sección y luego haga clic en **Aceptar**.
5. En el panel **Navegador**, expanda **ODBC->HIVE->Predeterminado**, seleccione **hivesampletable** y luego haga clic en **Cargar**.

## <a name="visualize-date"></a>Visualización de fecha

Continúe a partir del último procedimiento.

1. En el panel Visualizaciones, seleccione **Mapa**.  Es un icono de globo terráqueo.

    ![HDInsight Power BI personaliza el informe](./media/hdinsight-connect-power-bi/hdinsight-power-bi-customize.png)
2. En el panel Campos, seleccione **country** y **devicemake**. Puede ver los datos trazados en el mapa.
3. Expanda el mapa.

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha aprendido a visualizar datos de HDInsight mediante Power BI.  Para obtener más información, consulte los artículos siguientes:

* [Use Zeppelin to run Hive queries in Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md) (Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight)
* [Conexión de Excel a Hadoop en Azure HDInsight con Microsoft Hive ODBC Driver](./hdinsight-connect-excel-hive-odbc-driver.md).
* [Conexión de Excel a Hadoop con Power Query](./hdinsight-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carga de datos en HDInsight](./hdinsight-upload-data.md).
