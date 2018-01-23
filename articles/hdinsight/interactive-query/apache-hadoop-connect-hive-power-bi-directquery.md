---
title: "Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight | Microsoft Docs"
description: Aprenda a usar Microsoft Power BI para visualizar datos de Interactive Query Hive procesados por Azure HDInsight.
keywords: hdinsight,hadoop,hive,consulta interactiva,hive interactivo,LLAP,directquery
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
ms.date: 12/19/2017
ms.author: jgao
ms.openlocfilehash: 290e600b7be4a6f9fb57afa50bb771e42e6a0624
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualización de datos de Interactive Query Hive con Power BI mediante DirectQuery en Azure HDInsight

Aprenda a conectar Microsoft Power BI a clústeres de Interactive Query de Azure HDInsight y a visualizar los datos de Hive mediante DirectQuery. En este tutorial se cargan los datos de una tabla de Hive (hivesampletable) en Power BI. La tabla de Hive contiene algunos datos de uso de teléfonos móviles. Luego se trazan los datos de uso en un mapamundi:

![Informe de mapa de Power BI en HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Para obtener información sobre la conexión a Hive mediante ODBC, consulte [Visualización de datos de Hive con Microsoft Power BI mediante ODBC en Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 

## <a name="prerequisites"></a>requisitos previos
Antes de seguir los pasos de este artículo, debe disponer de los siguientes elementos:

* **Clúster de HDInsight**. El clúster puede ser un clúster de HDInsight con Hive o un clúster de Interactive Query recién lanzado. Para crear clústeres, vea [Crear clúster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Puede descargar una copia desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Carga de datos desde HDInsight

La tabla de Hive hivesampletable incluye todos los clústeres de HDInsight.

1. Inicie sesión en Power BI Desktop.
2. Haga clic en la pestaña **Inicio**, en **Obtener datos** en la cinta **Datos externos** y luego seleccione **Más**.

    ![Datos abiertos de Power BI en HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Desde el panel **Obtener datos**, escriba **hdinsight** en el cuadro de búsqueda. Si no ve **HDInsight Interactive Query (Beta)**, debe actualizar Power BI Desktop a la versión más reciente.
4. Haga clic en **HDInsight Interactive Query (Beta)** y, a continuación, clic en **Conectar**.
5. Haga clic en **Continuar** para cerrar el cuadro de diálogo de advertencia **Conector de vista previa**.
6. En **HDInsight Interactive Query**, seleccione o escriba la información siguiente:

    - **Servidor**: escriba el nombre de clúster de Interactive Query (por ejemplo, *myiqcluster.azurehdinsight.net*).
    - **Base de datos**: para este tutorial, escriba **default**.
    - **Modo de conectividad de datos**: para este tutorial, seleccione **DirectQuery**.

    ![HDInsight interactive query power bi directquery conectar](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Haga clic en **OK**.
8. Escriba las credenciales de usuario HTTP y, a continuación, haga clic en **Aceptar**.  El nombre de usuario predeterminado es **admin**.
9. En el panel izquierdo, seleccione **hivesampletale** y, a continuación, haga clic en **Cargar**.

    ![HDInsight interactive query power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Visualización de datos

Continúe a partir del último procedimiento.

1. En el panel Visualizaciones, seleccione **Mapa**.  Es un icono de globo terráqueo.

    ![HDInsight Power BI personaliza el informe](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. En el panel Campos, seleccione **country** y **devicemake**. Puede ver los datos trazados en el mapa.
3. Expanda el mapa.

## <a name="next-steps"></a>pasos siguientes
En este artículo ha aprendido a visualizar datos de HDInsight mediante Power BI.  Para obtener más información, consulte los artículos siguientes:

* [Visualización de datos de Hive con Microsoft Power BI mediante ODBC en Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use Zeppelin para ejecutar consultas de Hive en Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Conexión de Excel a Hadoop en Azure HDInsight con Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conexión de Excel a Hadoop con Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carga de datos en HDInsight](./../hdinsight-upload-data.md).
