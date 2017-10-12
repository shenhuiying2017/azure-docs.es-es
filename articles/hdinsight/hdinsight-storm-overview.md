---
title: "Qué es Apache Storm en Azure HDInsight | Microsoft Docs"
description: "Apache Storm permite procesar los flujos de datos en tiempo real. Azure HDInsight le permite crear fácilmente clústeres de Storm en la nube de Azure. Con Visual Studio, puede crear soluciones de Storm con C# y, después, implementar en los clústeres de Storm de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: "apache storm casos de uso, el clúster de storm, qué es apache storm"
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/03/2017
ms.author: larryfr
ms.openlocfilehash: 073672f1223313938baedee027072cb96062294b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>¿Qué es Apache Storm en Azure HDInsight?

[Apache Storm](http://storm.apache.org/) es un sistema de cálculo de código abierto distribuido, con tolerancia a errores. Puede usar Storm para procesar transmisiones de datos en tiempo real con Hadoop. Las soluciones de Storm pueden proporcionar también procesamiento de datos garantizado, con la posibilidad de reproducir los datos que no se han procesado correctamente la primera vez.

Storm en HDInsight proporciona las siguientes ventajas principales:

* Actúa como un servicio administrado con un Acuerdo de Nivel de Servicio de un 99,9 % de tiempo de actividad.

* Admite la personalización sencilla mediante la ejecución de scripts en el clúster de Storm durante su creación o después de ella. Para más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

* Usa diversos lenguajes. Puede escribir componentes de Storm en el lenguaje de su elección, como Java, C# y Python.

    * Integra Visual Studio con HDInsight para el desarrollo, la administración y la supervisión de topologías de C#. Para más información, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Admite la interfaz Java de Trident. Puede crear topologías de Storm que admitan el procesamiento de mensajes "exactamente una vez", la persistencia del almacén de datos transaccional y un conjunto de operaciones comunes de Stream Analytics.

*  Escale o reduzca verticalmente clústeres de Storm con facilidad. Puede agregar o quitar nodos de trabajo sin afectar a las topologías de Storm en ejecución.

* Se integra con los siguientes servicios de Azure:

    * Azure Event Hubs

    * Red virtual

    * Azure SQL Database

    * Azure Storage

    * Azure Cosmos DB

* Combina de forma segura las funcionalidades de varios clústeres de HDInsight mediante Virtual Network. Puede crear canalizaciones analíticas que usen clústeres de Storm, Kafka, Spark, HBase o Hadoop.

Para obtener una lista de empresas que usan Apache Storm con sus soluciones de análisis en tiempo real, consulte las [compañías que usan Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para ver una introducción al uso de Storm, consulte [Introducción a Storm en HDInsight][gettingstarted].

## <a name="how-does-storm-work"></a>¿Cómo funciona Storm?

Storm ejecuta topologías en lugar de los trabajos de MapReduce que puede que ya conozca. Las topologías de Storm están formadas por varios componentes que se organizan en un grafo acíclico dirigido (DAG). Los datos fluyen entre los componentes del grafo. Cada componente consume uno o varios flujos de datos y, opcionalmente, puede emitir uno o varios flujos. En el siguiente diagrama, se ilustra cómo fluyen los datos entre componentes de una topología básica de recuento de palabras:

![Ejemplo de cómo se organizan los componentes en una topología de Storm](./media/hdinsight-storm-overview/example-apache-storm-topology-diagram.png)

* Los componentes spout introducen los datos en una topología y emiten uno o varios flujos en la topología.

* Los componentes bolt consumen los flujos emitidos desde spouts u otros bolts. Además, podrían emitir flujos en la topología. También se encargan de escribir datos en un almacenamiento o servicios externos, como HDFS, Kafka o HBase.

## <a name="ease-of-creation"></a>Creación fácil

Puede aprovisionar un nuevo clúster de Storm en HDInsight en minutos. Para información sobre la creación de un clúster de Storm, consulte [Introducción a Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Facilidad de uso

* __Conectividad de Secure Shell (SSH)__: puede acceder a los nodos principales de un clúster de Storm a través de Internet mediante SSH. Puede ejecutar comandos directamente en el clúster mediante SSH.

  Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividad web__: todos los clústeres de HDInsight proporcionan la interfaz de usuario web de Ambari. Puede supervisar, configurar y administrar servicios fácilmente en el clúster mediante la interfaz de usuario web de Ambari. Clústeres de Storm también proporcionan la interfaz de usuario de Storm. Puede supervisar y administrar topologías de Storm en ejecución desde el explorador mediante la interfaz de usuario de Storm.

  Para más información, consulte el artículo [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md) y la sección [Supervisión y administración: IU de Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell y la CLI de Azure__: tanto PowerShell como la CLI de Azure proporcionan utilidades de línea de comandos que puede utilizar desde su sistema cliente para trabajar con HDInsight y otros servicios de Azure.

* __Integración de Visual Studio__: las Herramientas de Azure Data Lake para Visual Studio incluyen plantillas de proyecto para la creación de topologías de Storm en C# mediante la plataforma SCP.Net. Las herramientas de Data Lake también ofrecen herramientas para implementar, supervisar y administrar soluciones con Storm en HDInsight.

  Para más información, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integración con otros servicios de Azure

* __Azure Data Lake Store__: para ver un ejemplo del uso de Data Lake Store con un clúster de Storm, consulte [Uso de Azure Data Lake Store con Apache Storm con HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Event Hubs__: para obtener un ejemplo del uso de Event Hubs con un clúster de Storm, consulte los siguientes documentos:

    * [Desarrollo de una topología basada en Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)

    * [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ y __HBase__: se incluyen ejemplos de plantillas en Data Lake Tools para Visual Studio. Para más información, consulte [Desarrollo de topologías de C# para Storm en HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Confiabilidad

Apache Storm garantiza que cada mensaje entrante se procese siempre por completo, incluso cuando el análisis de datos esté repartido entre cientos de nodos.

El nodo de Nimbus ofrece una funcionalidad similar a la de JobTracker de Hadoop y asigna tareas a otros nodos de un clúster mediante Zookeeper. Los nodos de Zookeeper ofrecen coordinación para un clúster y facilitan la comunicación entre Nimbus y el proceso Supervisor en los nodos de trabajo. Si un nodo de procesamiento deja de funcionar, se informa al nodo Nimbus y se asigna la tarea y los datos asociados a otro nodo.

La configuración predeterminada de los clústeres de Apache Storm es que solo tenga un nodo Nimbus. Storm en HDInsight proporciona dos nodos Nimbus. Si se produce un error en el nodo principal, el clúster de Storm cambia al nodo secundario mientras se recupera el nodo principal. En el diagrama siguiente se muestra la configuración de flujo de tareas para Storm en HDInsight:

![Diagrama de nimbus, zookeeper y supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Escala

Los clústeres de HDInsight se pueden escalar dinámicamente mediante la adición o supresión de nodos de trabajo. Esta operación puede realizarse durante el procesamiento de datos.

> [!IMPORTANT]
> Para aprovechar los nodos nuevos que se agregan mediante el escalado, debe reequilibrar las topologías de Storm iniciadas antes de que aumentara el tamaño del clúster.

## <a name="support"></a>Soporte técnico

Storm en HDInsight incluye soporte técnico completo y continuo de nivel de empresa. Storm en HDInsight también tiene un Acuerdo de Nivel de Servicio del 99,9 %. Eso significa que se garantiza que un clúster de Storm dispondrá de conectividad externa como mínimo el 99,9 % del tiempo.

Para más información, consulte [Soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Casos de uso de Apache Storm

A continuación, se indican algunos de los escenarios habituales en los que se podría usar Storm en HDInsight:

* Internet de las cosas (IoT)
* Detección de fraudes
* Análisis social
* Extracción, transformación y carga de datos (ETL)
* Supervisión de redes
* Search
* Mobile Engagement

Para más información sobre escenarios del mundo real, consulte el documento sobre [cómo usan Storm las empresas](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Desarrollo

Los desarrolladores de .NET pueden diseñar e implementar topologías en C# mediante las Herramientas de Data Lake para Visual Studio. También puede crear topologías híbridas que usen los componentes de Java y C#.

Para más información, consulte [Desarrollo de topologías de C# para Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

También puede desarrollar soluciones de Java mediante el IDE de su elección. Para más información, consulte [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md).

Python también puede usarse para desarrollar componentes de Storm. Para más información, consulte [Desarrollo de topologías Apache Storm con Python en HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Patrones de desarrollo comunes

### <a name="guaranteed-message-processing"></a>Procesamiento de mensajes garantizado

Apache Storm puede proporcionar diferentes niveles de procesamiento de mensajes garantizado. Por ejemplo, una aplicación básica de Storm puede garantizar un procesamiento una vez al menos y Trident puede garantizar exactamente el procesamiento una vez.

Para obtener más información, consulte las [Garantías en el procesamiento de los datos](https://storm.apache.org/about/guarantees-data-processing.html) en apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Un patrón habitual es leer una tupla de entrada, emitir cero o más tuplas y confirmar la tupla de entrada inmediatamente al final del método de ejecución. Storm proporciona la interfaz [IBasicBolt](https://storm.apache.org/releases/1.0.3/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar este patrón.

### <a name="joins"></a>Combinaciones

El modo en que se unen las secuencias de datos varía entre aplicaciones. Por ejemplo, puede unir las tuplas procedentes de varios flujos en uno nuevo o unir únicamente los lotes de tuplas de una ventana específica. En cualquier caso, se puede realizar la unión mediante el uso de [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). La agrupación de los campos es una forma de definir cómo se enrutan las tuplas a los bolts.

En el siguiente ejemplo de Java, se usa fieldsGrouping para enrutar las tuplas que se originan en los componentes "1", "2" y "3" al bolt MyJoiner:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Lotes

Apache Storm proporciona un mecanismo de temporización interno conocido como "tupla tick". Puede establecer con qué frecuencia se genera una tupla tick en la topología.

Para ver un ejemplo del uso de una tupla tick desde un componente de C#, consulte [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Memorias caché

El almacenamiento en caché de memoria interna se usa a menudo como mecanismo para acelerar el procesamiento, porque mantiene en memoria los activos usados con mayor frecuencia. Como una topología se distribuye entre varios nodos, y varios proceso dentro de cada nodo, debe considerar el uso de [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Use `fieldsGrouping` para garantizar que las tuplas que contienen los campos que se usan para la búsqueda en caché se enruten siempre al mismo proceso. Esta funcionalidad de agrupación evita la duplicación de entradas de caché entre procesos.

### <a name="stream-top-n"></a>Secuencia "N principal"

Cuando la topología depende de calcular un valor de "N principal", calcule este valor en paralelo. A continuación, combine la salida de esos cálculos en un valor global. Esta operación puede realizarse mediante [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) para enrutar por campo para el procesamiento paralelo. A continuación, puede enrutar a un bolt que determina de manera global el valor de N principal.

Para ver un ejemplo de cálculo de un valor de "N principal", consulte el ejemplo de [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registro

Storm utiliza Apache Log4j para registrar información. De forma predeterminada, se registra una gran cantidad de datos y puede que sea difícil ordenar la información. Puede incluir un archivo de configuración de registro como parte de la topología de Storm para controlar el comportamiento de registro.

Para ver una topología de ejemplo que muestre cómo configurar el registro, consulte el ejemplo para Storm en HDInsight mostrado en el artículo [Desarrolle topologías basadas en Java para una aplicación básica de recuento de palabras con Apache Storm y Maven en HDInsight](hdinsight-storm-develop-java-topology.md) .

## <a name="next-steps"></a>Pasos siguientes

Aprenda más sobre las soluciones de análisis en tiempo real con Storm en HDInsight:

* [Introducción a Apache Storm en HDInsight][gettingstarted]
* [Topologías de ejemplo para Apache Storm en HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md
