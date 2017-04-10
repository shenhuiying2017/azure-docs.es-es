---
title: "Introducción a Apache Storm en HDInsight | Microsoft Docs"
description: "Introducción a Apache Storm en HDInsight"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introducción a Apache Storm en HDInsight: análisis en tiempo real de Hadoop

Apache Storm en HDInsight le permite crear soluciones de análisis en tiempo real distribuidas en Azure.

Apache Storm es un sistema de cálculo de código abierto, distribuido y con tolerancia a errores que le permite procesar datos en tiempo real con Hadoop. Las soluciones de Storm pueden proporcionar también procesamiento de datos garantizado, con la posibilidad de reproducir los datos que no se han procesado correctamente la primera vez.

## <a name="how-does-storm-work"></a>¿Cómo funciona Storm?

Apache Storm ejecuta **topologías** en lugar de los trabajos de MapReduce con los que podría estar familiarizado en HDInsight o Hadoop. Las topologías están formadas por varios componentes que se organizan en un gráfico acíclico dirigido (DAG). El siguiente diagrama ilustra cómo fluyen los datos entre componentes de una topología de recuento de palabras básica

![Ejemplo de cómo se organizan los componentes en una topología de Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* Los componentes de __Spout__ introducen los datos en una topología. Emiten uno o varios flujos en la topología

* Los componentes de __Bolt__ consumen los flujos emitidos desde los componentes de Spout u otros componentes de Bolt. Los componentes de Bolt pueden emitir nuevos flujos en la topología. Los componentes de Bolt son también responsables de escribir datos en un almacenamiento persistente como HDFS o HBase.

## <a name="why-use-storm-on-hdinsight"></a>¿Por qué usar Storm en HDInsight?

Apache Storm en HDInsight proporciona las siguientes ventajas principales:

* Actúa como un servicio administrado con un SLA de un 99,9 % de tiempo de actividad.

* Fácil personalización mediante la ejecución de scripts en el clúster durante su creación, o después de ella. Para más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

* Use el lenguaje de su elección: los componentes de Storm se pueden escribir en diversos lenguajes, como **Java**, **C#** y **Python**.

    * Integración de Visual Studio con HDInsight para el desarrollo, la administración y la administración de topologías de C#. Para más información, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Admite la interfaz de Java **Trident**. Esta interfaz permite crear topologías de Storm que admiten el procesamiento de mensajes "exactamente una vez", la persistencia del almacén de datos transaccional y un conjunto de operaciones comunes de Stream Analytics.

* Fácil escalado vertical y horizontal del clúster: agregue o quite nodos de trabajo sin que ello afecte a la ejecución de topologías de Storm.

* Integración con los siguientes servicios de Azure:

    * Event Hubs

    * Red virtual

    * Base de datos SQL

    * Almacenamiento de Azure

    * Azure DocumentDB.

    * Combine de forma segura las funcionalidades de varios clústeres de HDInsight mediante Azure Virtual Network: cree canalizaciones analíticas que usen clústeres de Hadoop, HDInsight o HBase.

Para obtener una lista de empresas que usan Apache Storm con sus soluciones de análisis en tiempo real, consulte [Compañías que usan Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para ver una introducción al uso de Storm, consulte [Introducción a Storm en HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Creación fácil

Puede aprovisionar un nuevo clúster de Storm en HDInsight en minutos. Para información sobre la creación de un clúster de Storm, consulte [Introducción a Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Facilidad de uso

* __Conectividad de Secure Shell__: puede acceder a los nodos principales de un clúster de HDInsight a través de Internet mediante SSH. SSH le permite ejecutar comandos directamente en el clúster.

  Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividad web__: los clústeres de HDInsight proporcionan la interfaz de usuario web de Ambari. La interfaz de usuario web de Ambari le permite supervisar, configurar y administrar servicios fácilmente en el clúster. Storm en HDInsight también proporciona la interfaz de usuario de Storm, lo que permite supervisar y administrar desde el explorador las topologías de Storm en ejecución.

  Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md) y [Supervisar y administrar mediante la interfaz de usuario de Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell y la CLI__: tanto Azure PowerShell como la CLI de Azure proporcionan utilidades de línea de comandos que puede utilizar desde su sistema cliente para trabajar con HDInsight y otros servicios de Azure.

* __Integración de Visual Studio__: las Herramientas de Data Lake para Visual Studio incluyen plantillas de proyecto para la creación de topologías de Storm en C# mediante la plataforma SCP.Net. Las herramientas de Data Lake también ofrecen herramientas para implementar, supervisar y administrar soluciones con Storm en HDInsight.

  Para más información, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integración con otros servicios de Azure

* __Azure Data Lake Store__: para ver un ejemplo del uso de Data Lake Store con Storm, consulte [Uso de Azure Data Lake Store con Apache Storm con HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Azure Event Hubs__: para obtener un ejemplo del uso de Azure Event Hubs con Storm, consulte los siguientes documentos:

    * [Desarrollo de una topología basada en Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)

    * [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* Algunos ejemplos para trabajar con __SQL DB__, __DocumentDB__, __EventHub__ y __HBase__ se incluyen como plantillas en Azure Data Lake Tools para Visual Studio. Para más información, consulte [Desarrollo de topologías de C# para Storm en HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Confiabilidad

Apache Storm siempre garantiza que cada mensaje entrante se procese por completo, incluso cuando el análisis de datos esté repartido en cientos de nodos.

El **nodo de nimbus** ofrece una funcionalidad similar a la de JobTracker de Hadoop y asigna tareas a otros nodos del clúster mediante **Zookeeper**. Los nodos de Zookeeper ofrecen coordinación para el clúster y facilitan la comunicación entre Nimbus y el proceso **Supervisor** en los nodos de trabajo. Si un nodo de procesamiento deja de funcionar, se informa al nodo Nimbus y se asigna la tarea y los datos asociados a otro nodo.

La configuración predeterminada de Apache Storm es que solo tenga un nodo Nimbus. Storm en HDInsight ejecuta dos nodos Nimbus. Si se produce un error en el nodo principal, el clúster de HDInsight cambia al nodo secundario mientras se recupera el nodo principal.

![Diagrama de nimbus, zookeeper y supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Escala

Aunque puede especificar el número de nodos del clúster durante la creación, puede que desee aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres de HDInsight le permiten cambiar el número de nodos del clúster, incluso durante el procesamiento de datos.

> [!NOTE]
> Para aprovechar los nodos nuevos que se agregan mediante el escalado, debe reequilibrar las topologías iniciadas antes de que aumentara el tamaño del clúster.

## <a name="support"></a>Soporte técnico

Storm en HDInsight incluye soporte técnico completo ininterrumpido de nivel de empresa. Storm en HDInsight también tiene un SLA del 99,9 %. Eso significa que se garantiza que el clúster dispondrá de conectividad externa como mínimo el 99,9 % del tiempo.

Para más información, consulte [Soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Casos de uso comunes

A continuación se indican algunos de los escenarios comunes en los que se podría usar Apache Storm. 

* Internet de las cosas (IoT)
* Detección de fraudes
* Análisis social
* Extraer, transformar y cargar (ETL)
* Supervisión de redes
* Search
* Mobile Engagement

Para más información sobre escenarios del mundo real, consulte el documento sobre [cómo usan Storm las empresas](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Desarrollo

__Herramientas de Azure Data Lake para Visual Studio__ permite a los desarrolladores de .NET diseñar e implementar una topología en __C#__. También puede crear topologías híbridas que usen los componentes de Java y C#.

Para más información, consulte [Desarrollo de topologías de C# para Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

También puede desarrollar soluciones de __Java__ mediante el IDE de su elección. Para más información, consulte [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md).

Python también puede usarse para desarrollar componentes de Storm. Para más información, consulte [Desarrollo de topologías Apache Storm con Python en HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Patrones de desarrollo comunes

### <a name="guaranteed-message-processing"></a>Procesamiento de mensajes garantizado

Storm puede proporcionar diferentes niveles de procesamiento de mensajes garantizado. Por ejemplo, una aplicación básica de Storm puede garantizar un procesamiento una vez al menos y Trident puede garantizar exactamente el procesamiento una vez.

Para obtener más información, consulte las [Garantías en el procesamiento de los datos](https://storm.apache.org/about/guarantees-data-processing.html) en apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Un patrón habitual es leer una tupla de entrada, emitir cero o más tuplas y confirmar la tupla de entrada inmediatamente al final del método de ejecución. Storm proporciona la interfaz [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar este patrón.

### <a name="joins"></a>Combinaciones

El modo en que se unen las secuencias de datos varía entre aplicaciones. Por ejemplo, puede unir cada tupla de varios flujos en una nueva o unir únicamente solo los lotes de tuplas de una ventana específica. De cualquier modo, la unión se puede efectuar mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que es una forma de definir la forma en que las tuplas se enrutan a los bolts.

En el siguiente ejemplo de Java, se usa fieldsGrouping para enrutar las tuplas que se originan desde los componentes "1", "2" y "3" al bolt **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Lotes

Storm proporciona un mecanismo de temporización interno conocido como "tupla tick", que se puede usar para emitir un lote cada X segundos.

Para ver un ejemplo del uso de una tupla de señalización de un componente de C#, consulte [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Si está usando Trident, se basa en el procesamiento por lotes de tuplas.

### <a name="caching"></a>Almacenamiento en caché

El almacenamiento en caché de memoria interna se usa a menudo como mecanismo para acelerar el procesamiento, porque mantiene en memoria los activos usados con mayor frecuencia. Como una topología se distribuye entre varios nodos, y varios proceso dentro de cada nodo, debe considerar el uso de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). `fieldsGrouping` garantiza que las tuplas que contienen los campos que se usan para la búsqueda en caché se enruten siempre al mismo proceso. Esta funcionalidad de agrupación evita la duplicación de entradas de caché entre procesos.

### <a name="streaming-top-n"></a>Transmisión por secuencias del N principal

Cuando la topología depende de calcular un valor del "N principal", deberá calcular el valor de este en paralelo. A continuación, combine la salida de esos cálculos en un valor global. Esta operación se puede realizar mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para enrutar por campo para el procesamiento en paralelo, y luego enrutar a un bolt que determina globalmente el valor de N principales.

Para ver un ejemplo de cálculo de un valor de "N principales", consulte el ejemplo de [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registro

Storm utiliza Apache Log4j para registrar información. De forma predeterminada, se registra una gran cantidad de datos y puede que sea difícil ordenar la información. Puede incluir un archivo de configuración de registro como parte de la topología de Storm para controlar el comportamiento de registro.

Para ver una topología de ejemplo que muestre cómo configurar el registro, consulte el ejemplo para Storm en HDInsight mostrado en el artículo [Desarrolle topologías basadas en Java para una aplicación básica de recuento de palabras con Apache Storm y Maven en HDInsight](hdinsight-storm-develop-java-topology.md) .

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las soluciones de análisis en tiempo real con Apache Storm en HDInsight:

* [Introducción a Storm en HDInsight][gettingstarted]
* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

