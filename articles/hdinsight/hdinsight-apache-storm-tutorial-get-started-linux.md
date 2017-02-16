---
title: "Tutorial de Apache Storm: Introducción a Storm basado en Linux en HDInsight | Microsoft Docs"
description: "Introducción al análisis de macrodatos con Apache Storm y los ejemplos de storm-starter en HDInsight basado en Linux. Aprenda a usar Storm para procesar datos en tiempo real."
keywords: "Storm de Apache, tutorial de Storm de Apache, análisis de macrodatos, inicio de Storm"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 856043148b3fc28594850ae27bedd57d48292582


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Tutorial de Apache Storm: introducción a las muestras de Inicio de Storm para análisis de grandes cantidades de datos en HDInsight

Apache Storm es un sistema de cálculo distribuido, escalable, con tolerancia a errores y en tiempo real para el procesamiento de secuencias de datos. Con Storm en HDInsight de Azure, puede crear un clúster de Storm basado en la nube que realice análisis en tiempo real de grandes cantidades de datos en tiempo real.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Debe cumplir los siguientes requisitos previos para poder completar correctamente este tutorial sobre Apache Storm.

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Familiaridad con SSH y SCP**. Para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:
  
    * **Clientes Linux, Unix u OS X**: consulte [Utilización de SSH con Hadoop basado en Linux en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Clientes Windows**: consulte [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Creación de un clúster de Storm

En esta sección, se crea un clúster de HDInsight, versión 3.5 (Storm, versión 1.0.1) mediante una plantilla de Azure Resource Manager. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md). Para conocer otros métodos de creación de clústeres, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    La plantilla de ARM que se usa en este tutorial se encuentra en un contenedor de blobs público, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 

2. En la hoja __Implementación personalizada__, escriba lo siguiente:
   
    * __Grupo de recursos__: el grupo de recursos en que se crea el cluster.

    * **Nombre de clúster**: el nombre del clúster de Hadoop.

    * __Nombre__ y __contraseña de inicio de sesión de clúster__: el nombre de inicio de sesión predeterminado es admin.
    
    * __Nombre de usuario__ y __contraseña de SSH__: el usuario y la contraseña para conectarse al clúster mediante SSH.

    * __Ubicación__: la ubicación geográfica del clúster.
     
     Escriba estos valores.  Los necesitará más adelante en el tutorial.
     
     > [!NOTE]
     > SSH sirve para tener acceso remoto a un clúster de HDInsight mediante una línea de comandos. El nombre de usuario y la contraseña que usa aquí se emplean al conectarse al clúster a través de SSH. Además, el nombre de usuario SSH tiene que ser único, ya que crea una cuenta de usuario en todos los nodos del clúster de HDInsight. A continuación se indican algunos de los nombres de cuenta reservados para su uso en los servicios que se ejecutan en el clúster y no pueden usarse como nombre de usuario SSH:
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:
     > 
     > * [Utilización de SSH con Hadoop basado en Linux en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Seleccione __Acepto los términos y condiciones indicados anteriormente__, haga clic en **OK** (Aceptar) y, después, seleccione __Anclar al panel__.

6. Haga clic en **Comprar**. Verá un icono nuevo llamado Envío de implementación para la implementación de plantilla. Tarda aproximadamente 20 minutos en crear el clúster.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Ejecución de una muestra de inicio de Storm en HDInsight

Los ejemplos de [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) se incluyen en el clúster de HDInsight. En los pasos siguientes, ejecutará el ejemplo de WordCount.

1. Conéctese al clúster de HDInsight con SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá la contraseña. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Para obtener más información sobre el uso de SSH con HDInsight basado en Linux, vea los siguientes artículos:
   
    * [Utilización de SSH con Hadoop basado en Linux en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Use el comando siguiente para iniciar una topología de ejemplo:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > En versiones anteriores de HDInsight, el nombre de clase de la topología es `storm.starter.WordCountTopology`, en lugar de `org.apache.storm.starter.WordCountTopology`.
   
    Esto iniciará la topología de WordCount de ejemplo en el clúster, con un nombre descriptivo de "wordcount". Generará frases aleatoriamente y contará la aparición de cada palabra en las oraciones.
   
    > [!NOTE]
    > Al enviar sus propias topologías al clúster, primero debe copiar el archivo jar que contiene el clúster antes de usar el comando `storm`. Esto se puede lograr mediante el comando `scp` desde el cliente donde se encuentra el archivo. Por ejemplo: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > El ejemplo de WordCount, y otros ejemplos de inicio de Storm, ya están incluidos en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Si está interesado en ver el origen de los ejemplos de storm starter, puede encontrar el código en [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Este vínculo es para Storm 1.0. x, que se proporciona con HDInsight 3.5. Para otras versiones de Storm, use el botón __Branch__ de la parte superior de la página para seleccionar otra versión de Storm.

## <a name="monitor-the-topology"></a>Supervisión de la topología

La interfaz de usuario de Storm ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster de HDInsight.

Siga estos pasos para supervisar la topología mediante la interfaz de usuario de Storm:

1. Abra un explorador web en https://CLUSTERNAME.azurehdinsight.net/stormui, donde **CLUSTERNAME** es el nombre del clúster. Se abrirá la interfaz de usuario de Storm.
    
    > [!NOTE]
    > Si se le pide que ofrezca un nombre de usuario y una contraseña, escriba el administrador de clústeres (admin) y la contraseña que usó al crear el clúster.

2. En el **resumen de la topología**, seleccione la entrada **wordcount** de la columna **Nombre**. Se mostrará más información sobre la topología.
    
    ![Panel de Storm con la información de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    Esta página ofrece la siguiente información:
    
    * **Estadísticas de topología** : información básica sobre el rendimiento de la topología, organizada en ventanas de tiempo.
     
        > [!NOTE]
        > Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

    * **Spouts** : información básica sobre spouts, entre la que se incluye el último error que ha devuelto cada spout.
    
    * **Bolts** : información básica sobre bolts.
    
    * **Configuración de la topología** : información detallada sobre la configuración de la topología.
     
    Esta página también ofrece acciones que se pueden realizar en la topología:
   
    * **Activar** : reanuda el procesamiento de una topología desactivada.
    
    * **Desactivar** : pausa una topología en ejecución.
    
    * **Reequilibrar** : ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster. Para más información, consulte la entrada de blog [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
    
    * **Eliminar** : finaliza una topología de Storm tras el tiempo de espera especificado.

3. En esta página, seleccione una entrada en la sección **Spouts** o **Bolts**. Se mostrará información sobre el componente seleccionado.
   
    ![Panel de Storm con información acerca de los componentes seleccionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    En esta página se muestra la siguiente información:
   
    * **Estadísticas de spouts y bolts** : información básica sobre el rendimiento de los componentes, organizada en ventanas de tiempo.
     
        > [!NOTE]
        > Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.
     
    * **Estadísticas de entrada** (solo bolt): información sobre los componentes que generan los datos que consume el bolt.
    
    * **Estadísticas de salida** : información sobre los datos que emite este bolt.
    
    * **Ejecutores** : información sobre las instancias de este componente.
    
    * **Errores** : errores que ha generado este componente.

4. Al ver los detalles de un spout o bolt, seleccione una entrada de la columna **Puerto** en la sección **Ejecutores** para ver los detalles de una instancia específica del componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    A partir de estos datos, puede ver que la palabra **seven** se ha repetido 1493957 veces. Ese es el número de veces que se ha encontrado desde que se inició esta topología.

## <a name="stop-the-topology"></a>Detención de la topología

Vuelva a la página **Resumen de la topología** de la topología de recuento de palabras y seleccione el botón **Eliminar** de la sección **Acciones de topología**. Cuando se le solicite, escriba 10 como los segundos de espera antes de detener la topología. Tras el período de tiempo de espera, ya no aparecerá la topología cuando visite la sección **IU de Storm** del panel.

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>Pasos siguientes

En este tutorial de Apache Storm, usó el inicio de Storm para aprender a crear un clúster de Storm en HDInsight y usar el panel de Storm para implementar, supervisar y administrar topologías de Storm. A continuación, aprenda a [desarrollar topologías basadas en Java con Maven](hdinsight-storm-develop-java-topology.md).

Si ya está familiarizado con el desarrollo de topologías basadas en Java y desea implementar una topología ya existente en HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md).

Si es desarrollador de. NET, puede crear topologías de C# o C#/Java híbridas con Visual Studio. Para más información, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Por ejemplo, consulte los siguientes ejemplos de las topologías que pueden utilizarse con Storm en HDInsight:

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


