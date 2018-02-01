---
title: Streaming en escala en Azure HDInsight | Microsoft Docs
description: "Cómo usar streaming de datos con clústeres de HDInsight escalables."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ramoha
ms.openlocfilehash: 46b5723805ab5d8bc1cf5b5183d9501cd3e4e3a2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streaming a escala en HDInsight

Las soluciones de macrodatos en tiempo real actúan sobre los datos que se mueven. Normalmente, estos datos son especialmente útiles en el momento de su llegada. Si el flujo de datos entrante pasa a ser mayor de lo que pueda controlarse en ese momento, es posible que tenga que limitar los recursos. Como alternativa, un clúster de HDInsight puede escalarse para hacer frente a la solución de streaming agregando nodos a petición.

En una aplicación de streaming, uno o varios orígenes de datos generan eventos (a veces, millones por segundo) que deben ser ingeridos rápidamente sin quitar ninguna información útil. Los eventos de entrada se tratan con el *almacenamiento en búfer de los flujos de datos*, también denominado *puesta en cola de eventos*, por un servicio como [Kafka](kafka/apache-kafka-introduction.md) o [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Después de recopilar los eventos, puede analizar los datos mediante un sistema de análisis en tiempo real en el nivel de *procesamiento de flujos de datos*, como [Storm](storm/apache-storm-overview.md) o [Spark Streaming](spark/apache-spark-streaming-overview.md). Los datos procesados se pueden almacenar en sistemas de almacenamiento a largo plazo, como [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) y se muestran en tiempo real en un panel de inteligencia empresarial, como [Power BI](https://powerbi.microsoft.com), Tableau o una página web personalizada.

![Patrones de streaming de HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka proporciona un servicio de puesta en cola de mensajes de baja latencia y alto rendimiento, y ahora forma parte del conjunto de aplicaciones Apache de Software de código abierto (OSS). Kafka usa un modelo de mensajes de publicación y suscripción, y almacena flujos de datos con particiones de forma segura en un clúster distribuido y replicado. Kafka se escala linealmente a medida que el rendimiento aumenta.

Para más información, consulte [Introducing Apache Kafka on HDInsight](kafka/apache-kafka-introduction.md) (Introducción a Apache Kafka en HDInsight).

## <a name="apache-storm"></a>Apache Storm

Apache Storm es un sistema de cálculo de código abierto, distribuido y con tolerancia a errores que está optimizado para procesar flujos de datos en tiempo real con Hadoop. La unidad básica de datos para un evento de entrada es una tupla, que es un conjunto inmutable de pares clave-valor. Una secuencia ilimitada de estas tuplas forma lo que se denomina stream, que procede de un spout. Un spout encapsula un origen de datos de streaming (como Kafka) y emite tuplas. Una topología de Storm es una secuencia de transformaciones en estos flujos.

Para más información, vea [¿Qué es Apache Storm en Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming es una extensión para Spark que permite reutilizar el mismo código que se usa para el procesamiento por lotes. Puede combinar tanto consultas interactivas como por lotes en la misma aplicación. A diferencia de Storm, Spark Streaming proporciona una semántica de procesamiento de una sola vez con información de estado. Cuando se utiliza en combinación con la [API Direct de Kafka](http://spark.apache.org/docs/latest/streaming-kafka-integration.html), que garantiza que todos los datos de Kafka son recibidos por Spark Streaming exactamente una vez, es posible tener la garantía de que llegan solo una vez de un extremo a otro. Uno de los puntos fuertes de Spark Streaming es su funcionalidad de tolerancia a errores, que permite recuperar los nodos rápidamente cuando se utilizan varios dentro del clúster.

Para más información, consulte [¿Qué es Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Escala de un clúster

Aunque puede especificar el número de nodos del clúster durante su creación, puede ser conveniente aumentar o reducir el clúster para que se ajuste a la carga de trabajo. Todos los clústeres de HDInsight le permiten [cambiar el número de nodos del clúster](hdinsight-administer-use-management-portal.md#scale-clusters). Los clústeres de Spark se pueden quitar sin que se pierdan datos, ya que todos los datos están almacenados en Azure Storage o en Data Lake Store.

Hay ventajas para las tecnologías de desacoplamiento. Por ejemplo, Kafka es una tecnología de almacenamiento en búfer de eventos, por lo que se realizan muchas operaciones de E/S y no se necesita mucha capacidad de procesamiento. En comparación, los procesadores de flujos de datos como Spark Streaming utilizan una gran capacidad de procesamiento, lo que requiere máquinas virtuales más eficaces. Debido a que estas tecnologías se desacoplan en diferentes clústeres, puede escalarlas independientemente al tiempo que las máquinas virtuales se aprovechan de la mejor manera.

### <a name="scale-the-stream-buffering-layer"></a>Escala del nivel de almacenamiento en búfer de flujos de datos

Ambas tecnologías de almacenamiento en búfer de flujos de datos, Event Hubs y Kafka, usan particiones y los consumidores leen dichas particiones. Escalar el rendimiento de las entradas requiere incrementar el número de particiones y agregar particiones proporciona un mayor paralelismo. En Event Hubs, el número de particiones no se puede cambiar después de la implementación, de modo que es importante empezar teniendo la escala de destino en cuenta. Con Kafka, es posible [agregar particiones](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), incluso mientras está procesando datos. Kafka proporciona una herramienta para volver a asignar las particiones, `kafka-reassign-partitions.sh`. HDInsight proporciona una [herramienta de reajuste de las réplicas de particiones](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Esta herramienta de reajuste llama a la herramienta `kafka-reassign-partitions.sh` de manera que cada réplica se encuentra en un dominio de error independiente y en un dominio de actualización, con lo que permite que Kafka tenga en cuenta el soporte y disfrute de una mayor tolerancia a errores.

### <a name="scale-the-stream-processing-layer"></a>Escala del nivel de procesamiento de flujos de datos

Tanto Apache Storm como Spark Streaming permiten agregar nodos de trabajo a sus clústeres, incluso durante el procesamiento de los datos.

Para aprovechar los nodos nuevos que se agregan al escalar Storm, debe reajustar las topologías de Storm iniciadas antes de que aumentara el tamaño del clúster. Este reajuste puede realizarse utilizando la interfaz de usuario web de Storm o su CLI. Para más información, consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark utiliza tres parámetros clave para configurar su entorno, según los requisitos de la aplicación: `spark.executor.instances`, `spark.executor.cores` y `spark.executor.memory`. Un *ejecutor* es un proceso que se inicia para una aplicación Spark. Se ejecuta en el nodo de trabajo y es responsable de realizar las tareas de la aplicación. El número predeterminado de ejecutores y su tamaño para cada clúster se calcula en función del número de nodos de trabajo y del tamaño de estos. Estos números se almacenan en el archivo `spark-defaults.conf` en cada nodo principal del clúster.

Estos tres parámetros se pueden configurar en el nivel de clúster para todas las aplicaciones que se ejecutan en el clúster y se pueden especificar también para cada aplicación individual. Para más información, consulte [Administración de recursos para los clústeres de Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>pasos siguientes

* [Introducción a Apache Storm en HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Topologías de ejemplo para Apache Storm en HDInsight](storm/apache-storm-example-topology.md)
* [Introducción a Spark en HDInsight](spark/apache-spark-overview.md)
* [Introducción a Apache Kafka en HDInsight](kafka/apache-kafka-get-started.md)