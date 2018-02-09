---
title: "Creación de trabajos de Spark Streaming con el procesamiento de eventos del tipo \"exactly-once\": Azure HDInsight | Microsoft Docs"
description: "Configuración de Spark Streaming para procesar un evento una sola vez"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: ebab9ebc92ae1dff8902d618d0a474ce2b2a0af3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>Creación de trabajos de Spark Streaming con el procesamiento de eventos del tipo "exactly-once"

Las aplicaciones de procesamiento de flujos adoptan enfoques distintos en cuanto al control del reprocesamiento de mensajes tras algún error del sistema:

* Al menos una vez: se garantiza el procesamiento de cada mensaje, pero puede que se procese más de una vez.
* Como máximo una vez: puede que cada mensaje se procese o no. Si se procesa un mensaje, solo se procesa una vez.
* Solo una vez: se garantiza un solo procesamiento de cada mensaje.

En este artículo se explica cómo configurar Spark Streaming para conseguir un procesamiento del tipo "exactly-once".

## <a name="exactly-once-semantics-with-spark-streaming"></a>Semántica del tipo "exactly-once" con Spark Streaming

En primer lugar, tenga en cuenta cómo se reinician todos los puntos de error del sistema tras un problema y cómo evitar la pérdida de datos. Una aplicación de Spark Streaming tiene:

* Un origen de entrada
* Uno o varios procesos de receptor que extraen datos del origen de entrada
* Tareas que procesan los datos
* Un receptor de salida
* Un proceso de controlador que administra el trabajo de ejecución prolongada

La semántica del tipo "exactly-once" requiere que no se pierda ningún dato en ningún punto y que el procesamiento de mensajes se pueda reiniciar, con independencia del lugar donde ocurra el error.

### <a name="replayable-sources"></a>Orígenes reproducibles

El origen desde el que la aplicación de Spark Streaming lee los eventos debe ser *reproducible*. Esto significa que en los casos en que se recuperó el mensaje pero con un error del sistema antes de que el mensaje pudiera guardarse o procesarse, el origen debe volver a proporcionar el mismo mensaje.

En Azure, tanto Azure Event Hubs como Kafka en HDInsight ofrecen orígenes reproducibles. Otro ejemplo de un origen reproducible es un sistema de archivos tolerante a errores como HDFS, Azure Storage Blob o Azure Data Lake Store, donde todos los datos se conservan indefinidamente y, en cualquier momento, puede volver a leer todos los datos.

### <a name="reliable-receivers"></a>Receptores de confianza

En Spark Streaming, los orígenes como Event Hubs y Kafka tienen *receptores de confianza*, donde cada receptor realiza un seguimiento de su progreso mediante la lectura del origen. Un receptor de confianza persiste su estado en un almacenamiento tolerante a errores, ya sea en ZooKeeper o en puntos de control de Spark Streaming escritos en HDFS. Si dicho receptor presenta algún error y se reinicia más tarde, puede continuar desde donde se quedó.

### <a name="use-the-write-ahead-log"></a>Uso del registro de escritura previa

Spark Streaming admite el uso del registro de escritura previa, donde cada evento recibido se escribe primero en el directorio del punto de control de Spark en un almacenamiento tolerante a errores y después se almacena en un conjunto de datos distribuido resistente (RDD). En Azure, el almacenamiento tolerante a errores es HDFS, con el respaldo de Azure Storage o Azure Data Lake Store. En la aplicación de Spark Streaming, el registro de escritura previa está habilitado para todos los receptores si la opción de configuración `spark.streaming.receiver.writeAheadLog.enable` está establecida en `true`. El registro de escritura previa proporciona tolerancia a errores en caso de problemas del controlador y de los ejecutores.

En el caso de los roles de trabajo que ejecutan tareas basadas en los datos de eventos, cada RDD, por definición, se replica y distribuye entre varios roles de trabajo. Si se produce algún error en una tarea porque la ejecución del rol de trabajo está bloqueada, la tarea se reiniciará en otro rol de trabajo que tenga una réplica de los datos del evento, por lo que el evento no se pierde.

### <a name="use-checkpoints-for-drivers"></a>Uso de puntos de control para los controladores

Los controladores de trabajo deben ser reiniciables. Si se bloquea el controlador que ejecuta la aplicación de Spark Streaming, también se bloquean todos los receptores en ejecución, las tareas y RDD donde se almacenan los datos del evento. En este caso, debe tener la posibilidad de guardar el progreso del trabajo para poder reanudarlo más tarde. Esto se consigue mediante la creación de puntos de control del grafo acíclico dirigido (DAG) del flujo DStream periódicamente en el almacenamiento tolerante a errores. Los metadatos de DAG incluyen la configuración utilizada para crear la aplicación de Streaming, las operaciones que definen la aplicación y los lotes que se ponen en cola pero que aún no han finalizado. Estos metadatos permiten reiniciar un controlador con errores a partir de la información del punto de control. Cuando se reinicie el controlador, este lanzará nuevos receptores que se encargan de recuperar por sí mismos los datos del evento en RDD a partir del registro de escritura previa.

Los puntos de control se habilitan en Spark Streaming en dos pasos. 

1. En el objeto StreamingContext, configure la ruta de acceso de almacenamiento para los puntos de control:

    val ssc = new StreamingContext(spark, Seconds(1))  ssc.checkpoint("/path/to/checkpoints")

    En HDInsight, estos puntos de control deben guardarse en el almacenamiento predeterminado conectado al clúster, es decir, en Azure Storage o en Azure Data Lake Store.

2. Después, especifique un intervalo (en segundos) del punto de control en DStream. En cada intervalo, los datos de estado derivados del evento de entrada se guardan en el almacenamiento. Los datos de estado guardados pueden reducir el cálculo necesario para recompilar el estado a partir del evento de origen.

    val lines = ssc.socketTextStream("hostname", 9999)  lines.checkpoint(30)  ssc.start()  ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>Uso de receptores idempotentes

El receptor de destino en el que el trabajo escribe resultados debe ser capaz de controlar la situación donde se proporciona el mismo resultado más de una vez. El receptor debe ser capaz de detectar estos resultados duplicados e ignorarlos. Se puede llamar varias veces a un receptor *idempotente* con los mismos datos sin cambios de estado.

Puede crear receptores idempotentes con la implementación de una lógica que primero comprueba si existen los resultados de entrada en el almacén de datos. Si el resultado ya existe, la operación de escritura debe aparecer como correcta desde la perspectiva del trabajo de Spark, pero en realidad su almacén de datos ignoró los datos duplicados. Si el resultado no existe, el receptor debe insertar este resultado nuevo en su almacenamiento. 

Por ejemplo, podría utilizar un procedimiento almacenado con Azure SQL Database que inserte eventos en una tabla. Este procedimiento almacenado primero busca el evento por los campos clave y, solo cuando no se encuentra ningún evento coincidente, el registro se inserta en la tabla.

Otro ejemplo consiste en usar un sistema de archivos con particiones, como Azure Storage Blob o Azure Data Lake Store. En este caso, la lógica del receptor no necesita comprobar la existencia de un archivo. Si el archivo que representa el evento existe, simplemente se sobrescribe con los mismos datos. De lo contrario, se crea un archivo en la ruta de acceso calculada.

## <a name="next-steps"></a>pasos siguientes

* [Introducción a Spark Streaming](apache-spark-streaming-overview.md)
* [Creación de trabajos de Spark Streaming con alta disponibilidad en YARN](apache-spark-streaming-high-availability.md)
