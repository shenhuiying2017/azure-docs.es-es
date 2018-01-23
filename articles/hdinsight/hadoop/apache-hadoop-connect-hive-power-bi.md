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
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 8b4347fde274d0d7520ef3acbd081fdb83d7dc7d
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualización de datos de Hive con Microsoft Power BI mediante ODBC en Azure HDInsight

Aprenda a conectar Microsoft Power BI a Azure HDInsight mediante ODBC y a visualizar los datos de Hive. En este tutorial se cargan los datos de una tabla de Hive (hivesampletable) en Power BI. La tabla de Hive contiene algunos datos de uso de teléfonos móviles. Luego se trazan los datos de uso en un mapamundi:

![Informe de mapa de Power BI en HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

La información también se aplica al nuevo tipo de clúster de [consulta interactiva](../interactive-query/apache-interactive-query-get-started.md). Para saber cómo conectarse a HDInsight Interactive Query mediante DirectQuery, consulte [Visualización de datos de Interactive Query Hive con Power BI mediante DirectQuery en Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>requisitos previos
Antes de seguir los pasos de este artículo, debe disponer de los siguientes elementos:

* **Clúster de HDInsight**. El clúster puede ser un clúster de HDInsight con Hive o un clúster de consulta interactiva recién lanzado. Para crear clústeres, vea [Crear clúster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Puede descargar una copia desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Creación de un origen de datos de Hive ODBC

Vea [Creación de un origen de datos de Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carga de datos desde HDInsight

La tabla de Hive hivesampletable incluye todos los clústeres de HDInsight.

1. Inicie sesión en Power BI Desktop.
2. Haga clic en la pestaña **Inicio**, en **Obtener datos** en la cinta **Datos externos** y luego seleccione **Más**.

    ![Datos abiertos de Power BI en HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. En el panel **Obtener datos**, haga clic en **Otro** a la izquierda, en **ODBC** a la derecha y luego en **Conectar** en la parte inferior.
4. En el panel **De ODBC**, seleccione el nombre del origen de datos creado en la última sección y luego haga clic en **Aceptar**.
5. En el panel **Navegador**, expanda **ODBC->HIVE->Predeterminado**, seleccione **hivesampletable** y luego haga clic en **Cargar**.

## <a name="visualize-data"></a>Visualización de datos

Continúe a partir del último procedimiento.

1. En el panel Visualizaciones, seleccione **Mapa**.  Es un icono de globo terráqueo.

    ![HDInsight Power BI personaliza el informe](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. En el panel Campos, seleccione **country** y **devicemake**. Puede ver los datos trazados en el mapa.
3. Expanda el mapa.

## <a name="next-steps"></a>pasos siguientes
En este artículo ha aprendido a visualizar datos de HDInsight mediante Power BI.  Para obtener más información, consulte los artículos siguientes:

* [Use Zeppelin para ejecutar consultas de Hive en Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Conexión de Excel a Hadoop en Azure HDInsight con Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conexión de Excel a Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carga de datos en HDInsight](./../hdinsight-upload-data.md).
