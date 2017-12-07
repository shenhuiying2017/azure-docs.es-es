---
title: Uso de la vista Tez de Ambari con HDInsight - Azure | Microsoft Docs
description: "Obtenga información sobre cómo usar la vista de Tez de Ambari para depurar trabajos de Tez en HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: b565ef0f7672d1288e922e28551ad3f6ec5b6cb7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usar vistas de Ambari para depurar trabajos de Tez en HDInsight

La interfaz de usuario de Ambari Web para HDInsight contiene una vista de Tez que puede usarse para comprender y depurar los trabajos que utilizan Tez. La vista de Tez permite visualizar el trabajo como un gráfico de elementos conectados, profundizar en cada elemento y recuperar las estadísticas y la información de registro.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el artículo relativo al [control de versiones de componentes de HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basado en Linux Para conocer la forma de crear un clúster, consulte el [artículo de introducción al uso de HDInsight basado en Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Un explorador web moderno que sea compatible con HTML5.

## <a name="understanding-tez"></a>Descripción de Tez

Tez es un marco de trabajo extensible para el procesamiento de datos en Hadoop que proporciona una mayor velocidad que el procesamiento tradicional de MapReduce. Para los clústeres de HDInsight basados en Linux, es el motor predeterminado de Hive.

Tez crea un grafo acíclico dirigido (DAG) que describe el orden de las acciones que requieren los trabajos. Las acciones individuales se denominan vértices y ejecutan una parte del trabajo total. La ejecución real del trabajo descrito por un vértice se denomina tarea, y puede distribuirse por varios nodos del clúster.

### <a name="understanding-the-tez-view"></a>Descripción de la vista de Tez

La vista de Tez proporciona información histórica y datos sobre los procesos que se están ejecutando. Esta información muestra la distribución de un trabajo a través de los clústeres, así como los contadores que emplean las tareas y vértices, e información sobre errores en relación con el trabajo. Puede ofrecer información útil en los escenarios siguientes:

* Supervisión de procesos de ejecución prolongada, visualización del progreso de asignación y reducción de las tareas.
* Análisis de datos históricos de procesos correctos o con errores para obtener información sobre cómo se puede mejorar el procesamiento o sobre la causa del error.

## <a name="generate-a-dag"></a>Generar un DAG

La vista de Tez solo contiene datos si se está ejecutando, o se ejecutó previamente, un trabajo que utiliza el motor de Tez. Las consultas simples de Hive pueden resolverse sin necesidad de usar Tez. Para aquellas consultas más complejas que realizan filtrados, agrupaciones, ordenaciones, combinaciones, etc., sí se emplea el motor de Tez.

Siga estos pasos para ejecutar una consulta de Hive que utilice Tez:

1. En un explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net, donde **CLUSTERNAME** es el nombre de su clúster de HDInsight.

2. En el menú que aparece en la parte superior de la página, seleccione el icono **Vistas**. Este icono tiene el aspecto de un conjunto de cuadrados. En la lista desplegable que aparece, seleccione **Vista de Hive**.

    ![Seleccionar la vista de Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Cuando se cargue la vista de Hive, pegue la siguiente consulta en el Editor de consultas y, a continuación, haga clic en **Ejecutar**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Cuando se haya completado el trabajo, debería ver el resultado en la sección **Resultados del proceso de consulta**. Los resultados deberían asemejarse a este texto:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Seleccione la pestaña **Registro**. Verá información similar al texto siguiente:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Guarde el valor de **App id**, ya que se usará en la próxima sección.

## <a name="use-the-tez-view"></a>Usar la vista de Tez

1. En el menú que aparece en la parte superior de la página, seleccione el icono **Vistas**. En la lista desplegable que aparece, seleccione **Vista de Tez**.

    ![Seleccionar la vista de Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Cuando se cargue la vista de Tez, verá una lista de consultas de Hive que se están ejecutando o que se ejecutaron anteriormente en el clúster.

    ![Todos los DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Si solo tiene una entrada, será la de la consulta que ejecutó en la sección anterior. Si tiene varias entradas, puede buscar mediante los campos de la parte superior de la página.

4. Seleccione el **Id. de consulta** para una consulta de Hive. Se muestra información sobre la consulta.

    ![Detalles del DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Las pestañas de esta página le permiten ver la información siguiente:

    * **Detalles de la consulta**: detalles sobre la consulta de Hive.
    * **Escala de tiempo**: información sobre cuánto tiempo ha tardado cada fase de procesamiento.
    * **Configuraciones**: la configuración usada para esta consulta.

    Desde __Detalles de la consulta__ puede usar los vínculos para buscar información sobre la __Aplicación__ o el __DAG__ para esta consulta.
    
    * El vínculo __Aplicación__ muestra información sobre la aplicación de YARN para esta consulta. Desde aquí puede tener acceso a los registros de aplicaciones en YARN.
    * El vínculo __DAG__ muestra información sobre el grafo acíclico dirigido para esta consulta. Desde aquí puede ver una representación gráfica del DAG. También puede encontrar información sobre los vértices en el DAG.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar la vista de Tez, obtenga más información sobre el [Uso de Hive en HDInsight](hadoop/hdinsight-use-hive.md).

Para obtener información técnica más detallada sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).

Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)
