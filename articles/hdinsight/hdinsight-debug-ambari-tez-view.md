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
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 7654875c7b6384943d733de2d0097082270d64b4
ms.lasthandoff: 04/11/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usar vistas de Ambari para depurar trabajos de Tez en HDInsight
La interfaz de usuario web de Ambari para HDInsight contiene una vista de Tez que puede usarse para comprender y depurar los trabajos que usan Tez como motor de ejecución. La vista de Tez permite visualizar el trabajo como un gráfico de elementos conectados, profundizar en cada elemento y recuperar las estadísticas y la información de registro.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos
* Un clúster de HDInsight basado en Linux Para obtener información sobre cómo crear un clúster, consulte [Introducción al uso de HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
* Un explorador web moderno que sea compatible con HTML5.

## <a name="understanding-tez"></a>Descripción de Tez
Tez es un marco de trabajo extensible para el procesamiento de datos en Hadoop que proporciona una mayor velocidad que el procesamiento tradicional de MapReduce. Para los clústeres de HDInsight basados en Linux, es el motor predeterminado de Hive.

Cuando se envía un trabajo a Tez, este crea un grafo acíclico dirigido (DAG) que describe el orden de ejecución de las acciones requeridas por el trabajo. Las acciones individuales se denominan vértices y ejecutan una parte del trabajo total. La ejecución real del trabajo descrito por un vértice se denomina tarea, y puede distribuirse por varios nodos del clúster.

### <a name="understanding-the-tez-view"></a>Descripción de la vista de Tez
La vista de Tez proporciona información sobre los procesos que se están ejecutando o que se ejecutaron previamente mediante Tez. Permite ver el DAG generado por Tez, la manera en que se distribuye por los clústeres, contadores como la memoria usada por tareas y vértices, e información de error. Puede ofrecer información útil en los escenarios siguientes:

* Supervisión de procesos de ejecución prolongada, visualización del progreso de asignación y reducción de las tareas.
* Análisis de datos históricos de procesos correctos o con errores para obtener información sobre cómo se puede mejorar el procesamiento o sobre la causa del error.

## <a name="generate-a-dag"></a>Generar un DAG
La vista de Tez solo contendrá datos si se está ejecutando actualmente un trabajo que usa el motor de Tez, o bien si se ejecutó anteriormente. Las consultas de Hive sencillas normalmente se pueden resolver sin usar Tez, pero las consultas más complejas con filtrado, agrupación, ordenación, uniones, etc. suelen requerir Tez.

Siga los pasos que se indican a continuación para realizar una consulta de Hive que se ejecutará mediante Tez.

1. En un explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net, donde **CLUSTERNAME** es el nombre de su clúster de HDInsight.
2. En el menú que aparece en la parte superior de la página, seleccione el icono **Vistas**. Tiene el aspecto de un conjunto de cuadrados. En la lista desplegable que aparece, seleccione **Vista de Hive**.

    ![Seleccionar la vista de Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)
3. Cuando se cargue la vista de Hive, pegue lo siguiente en el Editor de consultas y luego haga clic en **Ejecutar**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Cuando se haya completado el trabajo, debería ver el resultado en la sección **Resultados del proceso de consulta**. Los resultados deberían parecerse a lo siguiente.

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
4. Seleccione la pestaña **Registro**. Verá información similar a la siguiente:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Guarde el valor del **Id. de la aplicación**, ya que se usará en la sección siguiente.

## <a name="use-the-tez-view"></a>Usar la vista de Tez
1. En el menú que aparece en la parte superior de la página, seleccione el icono **Vistas**. En la lista desplegable que aparece, seleccione **Vista de Tez**.

    ![Seleccionar la vista de Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)
2. Cuando se cargue la vista de Tez, verá una lista de los DAG que se están ejecutando actualmente o que se ejecutaron anteriormente en el clúster. La vista predeterminada incluye el nombre del DAG, el identificador, el remitente, el estado, la hora de inicio, la hora de finalización, la duración, el identificador de la aplicación y la cola. Puede agregar más columnas mediante el icono de engranaje que se encuentra a la derecha de la página.

    ![Todos los DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)
3. Si solo tiene una entrada, será la de la consulta que ejecutó en la sección anterior. Si tiene varias entradas, puede realizar una búsqueda escribiendo el identificador de la aplicación en el campo **Id. de la aplicación** y presionando Entrar.
4. Seleccione el **Nombre del DAG**. Se mostrará información sobre el DAG, así como la opción de descargar un archivo ZIP con archivos JSON que contienen información sobre el DAG.

    ![Detalles del DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)
5. Encima de **Detalles del DAG** aparecen varios vínculos que pueden usarse para mostrar información sobre el DAG.

   * **Contadores del DAG** muestra información de los contadores de este DAG.
   * **Vista gráfica** muestra una representación gráfica de este DAG.
   * **Todos los vértices** muestra una lista de los vértices de este DAG.
   * **Todas las tareas** muestra una lista de las tareas de todos los vértices de este DAG.
   * **Todos los intentos de tarea** muestra información sobre los intentos de ejecutar las tareas de este DAG.

     > [!NOTE]
     > Si se desplaza por la presentación en columnas de Vértices, Tareas e Intentos de tarea, verá que hay vínculos para ver los **contadores** y para **ver o descargar los registros** de cada fila.
     >
     >

     Si se produjo un error en el trabajo, los detalles del DAG mostrarán un estado de error, junto con vínculos a información sobre la tarea con error. Debajo de los detalles del DAG, se mostrará información de diagnóstico.

     ![Pantalla de detalles del DAG que muestra un error con detalles](./media/hdinsight-debug-ambari-tez-view/faileddag.png)
6. Seleccione **Vista gráfica**. Muestra una representación gráfica del DAG. Puede colocar el mouse sobre cada vértice de la vista para mostrar su información.

    ![Vista gráfica](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)
7. Al hacer clic en un vértice, se cargarán los **Detalles del vértice** de ese elemento. Haga clic en el vértice **Asignación 1** para mostrar los detalles de ese elemento.

    ![Detalles del vértice](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)
8. Observe que ahora aparecen vínculos en la parte superior de la página que están relacionados con las tareas y los vértices.

   > [!NOTE]
   > Para llegar a esta página, puede regresar a **Detalles del DAG**, seleccionar **Detalles del vértice** y seleccionar el vértice **Asignación 1**.
   >
   >

   * **Contadores del vértice** muestra información de los contadores de este vértice.
   * **Tareas** muestra las tareas de este vértice.
   * **Intentos de tarea** muestra información sobre los intentos de ejecutar las tareas de este vértice.
   * **Orígenes y receptores** muestra los orígenes de datos y los receptores de este vértice.

     > [!NOTE]
     > Al igual que en el menú anterior, puede desplazarse por la presentación de columna de Tareas, Intentos de tarea y Orígenes y receptores para que aparezcan vínculos a información adicional sobre cada elemento.
     >
     >
9. Seleccione **Tareas** y luego seleccione el elemento llamado **00_000000**. De este modo se mostrarán los **Detalles de la tarea** de esta tarea. En esta pantalla, puede ver los **Contadores de tarea** y los **Intentos de tarea**.

   ![Detalles de la tarea](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a usar la vista de Tez, obtenga más información sobre el [Uso de Hive en HDInsight](hdinsight-use-hive.md).

Para obtener información técnica más detallada sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).

Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)

