---
title: "¿Qué es Spark Streaming en Azure HDInsight? | Microsoft Docs"
description: "En este artículo se describe cómo usar las aplicaciones de Spark Streaming en los clústeres de HDInsight Spark."
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
ms.date: 09/08/2017
ms.author: ramoha
ms.openlocfilehash: e80b69fa3e60618dbaccd612114200a941019854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-spark-streaming"></a>Introducción a Spark Streaming

Spark Streaming proporciona una funcionalidad de procesamiento de flujo de datos en los clústeres de HDInsight Spark, con una garantía de que cualquier evento de entrada se procesa exactamente una vez, aunque se produzca un error de nodo. Un flujo de Spark es un trabajo de ejecución prolongada que recibe datos de entrada de una gran variedad de orígenes —como Azure Event Hubs, Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ y sockets TCP sin procesar— o de sistemas de archivos HDFS de supervisión. A diferencia de los procesos controlados exclusivamente por eventos, un flujo de Spark procesa datos de entrada por lotes en ventanas de tiempo, por ejemplo, un intervalo de 2 segundos y, luego, transforma cada lote de datos mediante operaciones de asignación, reducción, combinación y extracción. Después, escribe los datos transformados en sistemas de archivos, bases de datos, paneles y la consola.

![Procesamiento de flujos con Spark Streaming y HDInsight](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming.png)

Las aplicaciones de Spark Streaming deben esperar unas fracciones de segundo para recopilar cada *microlote* de eventos antes de enviar ese lote para procesarlo. En cambio, una aplicación controlada por eventos procesa cada evento inmediatamente. La latencia de Spark Streaming suele ser de unos segundos. Las ventajas del enfoque de los microlotes son una mayor eficacia del procesamiento de datos y cálculos agregados más sencillos.

## <a name="introducing-the-dstream"></a>Introducción a los flujos DStream

Spark Streaming representa un flujo continuo de datos entrantes que utilizan un *flujo discretizado* llamado "DStream". Los flujos DStream pueden crearse a partir de orígenes de entrada, como Event Hubs o Kafka, o bien aplicando transformaciones a otro flujo DStream.

Un flujo DStream proporciona una capa de abstracción basada en los datos del evento sin procesar. 

Comience con un solo evento, por ejemplo, una lectura de temperatura en un termostato conectado. Cuando este evento llega a la aplicación de Spark Streaming, se almacena de forma confiable, es decir, se replica en varios nodos. Esta tolerancia a errores garantiza que un error de un solo nodo no provocará la pérdida del evento. El núcleo de Spark usa una estructura de datos que distribuye los datos en varios nodos del clúster, donde cada nodo suele mantener sus propios datos en memoria para garantizar un rendimiento óptimo. Esta estructura de datos se denomina *conjunto de datos distribuido resistente* (RDD).

Cada RDD representa eventos recopilados a través de un período definido por el usuario llamado "intervalo entre lotes". Cuando transcurre cada intervalo entre lotes, se genera un nuevo diseño que contiene todos los datos de ese intervalo. El conjunto continuo de RDD se recopila en un flujo DStream. Por ejemplo, si el intervalo entre lotes es de un segundo, el flujo DStream emite un lote cada segundo con un RDD que contiene todos los datos ingeridos durante ese segundo. Al procesar el flujo DStream, el evento de temperatura se muestra en uno de estos lotes. Una aplicación de Spark Streaming procesa los lotes que contienen los eventos y, en última instancia, actúa en los datos almacenados en cada RDD.

![Flujo DStream de ejemplo con eventos de temperatura ](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estructura de una aplicación de Spark Streaming
Una aplicación de Spark Streaming es una aplicación de ejecución prolongada que recibe datos de orígenes de ingesta, aplica transformaciones para procesar los datos y, luego, los envía a uno o varios destinos. La estructura de una aplicación de Spark Streaming tiene dos partes principales. En primer lugar, definirá el origen de los datos, qué procesamiento hay que realizar en ellos y el destino de los resultados. En segundo lugar, ejecutará la aplicación de forma indefinida y esperará una señal de detención.

Por ejemplo, aquí se muestra una aplicación sencilla que recibe una línea de texto a través de un socket TCP y cuenta el número de veces que aparece cada palabra.

### <a name="define-the-application"></a>Definición de la aplicación

La definición de la lógica de la aplicación consta de cuatro pasos:

1. Crear un objeto StreamingContext
2. Crear un flujo DStream a partir del objeto StreamingContext
3. Aplicar transformaciones al flujo DStream
4. Generar los resultados

Esta definición es estática y no se procesa ningún dato hasta que se ejecuta la aplicación.

#### <a name="create-a-streamingcontext"></a>Creación de un objeto StreamingContext
Cree un objeto StreamingContext desde el objeto SparkContext que apunta al clúster. Al crear un objeto StreamingContext, especifique el tamaño del lote en segundos, por ejemplo:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Creación de un flujo DStream
Con la instancia del objeto StreamingContext que creó, cree un flujo DStream de entrada para el origen de entrada. En este caso, nos fijaremos en la apariencia de los archivos nuevos en el almacenamiento predeterminado asociado al clúster de HDInsight.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Aplicación de transformaciones
Implemente el procesamiento aplicando transformaciones en el flujo DStream. Nuestra aplicación recibe una línea de texto a la vez desde el archivo, divide cada línea en palabras y, luego, utiliza un patrón de asignación-reducción para contar el número de veces que aparece cada palabra.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Generación de los resultados
Envíe los resultados de transformación a los sistemas de destino aplicando operaciones de salida. En este caso, mostramos el resultado de cada ejecución durante el cálculo en la salida de consola.

    wordCounts.print()

### <a name="run-the-application"></a>Ejecución de la aplicación
Inicie la aplicación de streaming y ejecútela hasta que se reciba una señal de finalización.

    ssc.start()            
    ssc.awaitTermination()

Para obtener más información sobre la API de Spark Streaming, además de los orígenes de eventos, las transformaciones y las operaciones de salida que se admiten, consulte [Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html) (Guía de programación de Spark Streaming).

La siguiente aplicación de ejemplo es independiente, así que puede ejecutarla en un [cuaderno de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md). En el ejemplo, creamos un origen de datos ficticio en la clase DummySource que genera el valor de un contador y la hora actual en milisegundos cada cinco segundos. Creamos un nuevo objeto StreamingContext que tiene un intervalo entre lotes de 30 segundos. Cada vez que se crea un lote, examina el RDD generado, lo convierte en un objeto DataFrame de Spark y crea en él una tabla temporal.

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Después, podemos realizar periódicamente una consulta en la trama de datos para ver el conjunto actual de los valores del lote. En este caso, utilizamos la siguiente consulta SQL:

    %%sql
    SELECT * FROM demo_numbers

El resultado tendrá un aspecto similar al siguiente:

| value | Twitter en tiempo |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Esperamos ver seis valores, ya que el objeto DummySource crea un valor cada 5 segundos y emitimos un lote cada 30 segundos.

## <a name="sliding-windows"></a>Ventanas deslizantes
Si quiere realizar cálculos agregados en el flujo DStream durante un período concreto, por ejemplo, para obtener una temperatura media durante los últimos 2 segundos, puede usar las operaciones de ventana deslizante incluidas con Spark Streaming. Una ventana deslizante es aquella que tiene una duración (la longitud de la ventana) y un intervalo durante el cual se evalúa su contenido (el intervalo de deslizamiento).

Estas ventanas deslizantes se pueden superponer; por ejemplo, puede definir una ventana con una longitud de 2 segundos, que se desliza cada segundo. Es decir, cada vez que realice un cálculo de agregación, la ventana incluirá los datos del último segundo de la ventana anterior, además de cualquier dato nuevo del siguiente segundo.

![Ejemplo de ventana inicial con eventos de temperatura](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Ventana de ejemplo con eventos de temperatura después del deslizamiento](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

En el ejemplo siguiente, actualizamos el código que utiliza el objeto DummySource para recopilar los lotes en una ventana con una duración de un minuto y un deslizamiento de un minuto.

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Después del primer minuto, se generarán 12 entradas: 6 entradas de cada uno de los dos lotes recopilados en la ventana.

| value | Twitter en tiempo |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Las funciones de ventana deslizante disponibles en la API de Spark Streaming incluyen las funciones window, countByWindow, reduceByWindow y countByValueAndWindow. Para obtener más información sobre estas funciones, consulte [Transformations on DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams) (Transformaciones en flujos DStream).

## <a name="checkpointing"></a>Puntos de control
Para proporcionar resistencia y tolerancia a errores, Spark Streaming se basa en los puntos de control para garantizar que el procesamiento de flujos puede continuar sin interrupciones, aunque se produzcan errores de nodo. En HDInsight, Spark crea puntos de control en un almacenamiento duradero (Azure Storage o Data Lake Store). Estos puntos de control almacenan los metadatos de la aplicación de streaming, como la configuración, las operaciones definidas por la aplicación y todos los lotes que se ponen en cola pero no se procesan. En algunos casos, los puntos de control también guardarán los datos en los RDD para reducir el tiempo que se tarda en volver a generar el estado de los datos a partir de lo que haya presente en los RDD que administra Spark.

## <a name="deploying-spark-streaming-applications"></a>Implementación de aplicaciones de Spark Streaming
Normalmente, compilará su aplicación de Spark Streaming localmente y, luego, la implementará en Spark en HDInsight copiando el archivo JAR que contiene la aplicación en el almacenamiento predeterminado asociado al clúster de HDInsight. Después, puede iniciar la aplicación mediante las API de REST de LIVY disponibles en el clúster. Se trata de una operación POST cuyo cuerpo incluye un documento JSON que proporciona la ruta de acceso al archivo JAR, el nombre de la clase cuyo método principal define y ejecuta la aplicación de streaming y, opcionalmente, los requisitos de recursos del trabajo (por ejemplo, el número de ejecutores, memoria y núcleos) y los parámetros de configuración que requiere el código de la aplicación.

![Ventana de ejemplo con eventos de temperatura después del deslizamiento](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

También se puede comprobar el estado de todas las aplicaciones con una solicitud GET en un punto de conexión de LIVY. Finalmente, puede finalizar una aplicación en ejecución emitiendo una solicitud DELETE en el punto de conexión de LIVY. Para obtener más información sobre la API de LIVY, consulte [Trabajos remotos con LIVY](hdinsight-apache-spark-livy-rest-interface.md).

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un clúster de Apache Spark en HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guía de programación de streaming de Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Inicio de trabajos de Spark de forma remota con LIVY](hdinsight-apache-spark-livy-rest-interface.md)