---
title: Usar la vista Tez de Ambari con HDInsight | Microsoft Docs
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
ms.date: 04/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 4dcccb313c517982680266656c36b9f23dee44c4
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usar vistas de Ambari para depurar trabajos de Tez en HDInsight

La interfaz de usuario de Ambari Web para HDInsight contiene una vista de Tez que puede usarse para comprender y depurar los trabajos que utilizan Tez. La vista de Tez permite visualizar el trabajo como un gráfico de elementos conectados, profundizar en cada elemento y recuperar las estadísticas y la información de registro.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el artículo relativo al [control de versiones de componentes de HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basado en Linux Para conocer la forma de crear un clúster, consulte el [artículo de introducción al uso de HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
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

2. Cuando se cargue la vista de Tez, verá una lista de los DAG que se están ejecutando o que se ejecutaron anteriormente en el clúster.

    ![Todos los DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Si solo tiene una entrada, será la de la consulta que ejecutó en la sección anterior. Si tiene varias entradas, puede realizar una búsqueda escribiendo el identificador de la aplicación en el campo **Id. de la aplicación** y presionando Entrar.

4. Seleccione el **Nombre del DAG**. Aparecerá información sobre el DAG. También puede descargar un ZIP de archivos JSON que contenga esta información.

    ![Detalles del DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Encima de **Detalles del DAG** aparecen varios vínculos que pueden usarse para mostrar información sobre el DAG.

   * **Contadores del DAG**: muestra la información de los contadores para este DAG.
   * **Vista gráfica**: muestra una representación gráfica de este DAG.
   * **Todos los vértices**: muestra una lista con los vértices de este DAG.
   * **Todas las tareas**: muestra una lista con las tareas de todos los vértices de este DAG.
   * **Todos los intentos de tarea**: muestra información sobre los intentos de ejecutar las tareas de este DAG.

     > [!NOTE]
     > Si se desplaza por la presentación en columnas de Vértices, Tareas e Intentos de tarea, verá que hay vínculos para ver los **contadores** y para **ver o descargar los registros** de cada fila.

     Si se produjo un error en el trabajo, en Detalles del DAG aparecerá el estado ERROR, junto con vínculos a información relativa a la tarea con el error. Debajo de los detalles del DAG, se mostrará información de diagnóstico.

     ![Pantalla de detalles del DAG que muestra un error con detalles](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

6. Seleccione **Vista gráfica**. Esta vista muestra una representación gráfica del DAG. Puede colocar el mouse sobre cada vértice de la vista para mostrar su información.

    ![Vista gráfica](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

7. Seleccione un vértice para cargar los **Detalles del vértice** de ese elemento. Seleccione el vértice **Asignación 1** para mostrar los detalles de este elemento.

    ![Detalles del vértice](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

8. Ahora aparecen vínculos en la parte superior de la página que están relacionados con las tareas y los vértices.

   > [!NOTE]
   > Para llegar a esta página, puede regresar a **Detalles del DAG**, seleccionar **Detalles del vértice** y seleccionar el vértice **Asignación 1**.

   * **Contadores del vértice**: muestra información de los contadores de este vértice.
   * **Tareas**: muestra las tareas de este vértice.
   * **Intentos de tarea**: muestra información sobre los intentos de ejecutar las tareas de este vértice.
   * **Orígenes y receptores**: muestra los orígenes de datos y los receptores de este vértice.

     > [!NOTE]
     > Al igual que en el menú anterior, puede desplazarse por la presentación de columna de Tareas, Intentos de tarea y Orígenes y receptores para que aparezcan vínculos a información adicional sobre cada elemento.

9. Seleccione **Tareas** y luego seleccione el elemento llamado **00_000000**. Aparecerá el cuadro **Detalles de la tarea** de esta tarea. En esta pantalla, puede ver los **Contadores de tarea** y los **Intentos de tarea**.

   ![Detalles de la tarea](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar la vista de Tez, obtenga más información sobre el [Uso de Hive en HDInsight](hdinsight-use-hive.md).

Para obtener información técnica más detallada sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).

Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)

