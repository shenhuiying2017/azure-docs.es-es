---
title: Uso de Apache Spark Structured Streaming con Event Hubs en Azure HDInsight | Microsoft Docs
description: "Compile un ejemplo de streaming de Apache Spark sobre cómo enviar una transmisión de datos a Azure Event Hubs y, luego, recibir esos eventos en un clúster de HDInsight Spark mediante una aplicación de Scala."
keywords: apache spark streaming de, streaming de spark, ejemplo de spark, ejemplo de streaming de apache spark, ejemplo de azure event hub, ejemplo de spark
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: e0486d2c5f78da1d1e4a12703f120eccef43c305
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Apache Spark Structured Streaming en HDInsight para procesar eventos desde Event Hubs

En este artículo, aprenderá a procesar telemetría en tiempo real mediante Spark Structured Streaming. Para lograr esto, debe realizar los siguientes pasos de alto nivel:

1. Compile y ejecute en la estación de trabajo local una aplicación de ejemplo de producción de eventos que genera eventos para enviarlos a Event Hubs.
2. Use el [shell de Spark](apache-spark-shell.md) para definir y ejecutar una aplicación sencilla de Spark Structured Streaming.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).

* Un espacio de nombres de Azure Event Hubs. Consulte [Creación de un espacio de nombres de Azure Event Hubs](apache-spark-eventhub-streaming.md#create-an-azure-event-hub) para más información.

* Kit de desarrollo de Oracle Java. Se puede instalar desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. Puede descargarla [aquí](https://maven.apache.org/download.cgi). Las instrucciones para instalar Maven están disponibles [aquí](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Compilación, configuración y ejecución del productor de eventos
En esta tarea, puede clonar una aplicación de ejemplo que crea eventos aleatorios y los envía a un centro de eventos configurado. Esta aplicación de ejemplo está disponible en GitHub en [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Asegúrese de que tiene instaladas las herramientas de Git. Puede descargarlas desde [Descargas de GIT](http://www.git-scm.com/downloads). 
2. Abra un símbolo del sistema o shell de terminal y ejecute el siguiente comando desde el directorio de su elección para clonar el proyecto.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. Esto crea una nueva carpeta denominada eventhubs-client-sample. Dentro del shell o del símbolo del sistema, vaya a esta carpeta.
4. Ejecute Maven para compilar la aplicación mediante el comando siguiente:

          mvn package

5. Dentro del shell o del símbolo del sistema, vaya al directorio de destino que se ha creado y que contiene el archivo ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. A continuación, debe compilar la línea de comandos para ejecutar el productor de eventos en el centro de eventos. Para ello, reemplace los valores del comando de la siguiente manera:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. Por ejemplo, un comando completo tendría un aspecto similar al siguiente:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. El comando se inicia y si la configuración es correcta en unos momentos verá salidas relacionadas con los eventos que se están enviando al centro de eventos, que son parecidos a los siguientes:

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Deje el productor de eventos en ejecución mientras continúa con los pasos.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Ejecución del shell de Spark en el clúster de HDInsight
En esta tarea, usará SSH en el nodo principal del clúster de HDInsight, iniciará el shell de Spark y ejecutará una aplicación de Spark Structured Streaming, que recupera y procesa los eventos de Event Hubs. 

Llegados a este punto, el clúster de HDInsight debería estar listo. Si no es así, debe esperar hasta que termine el aprovisionamiento. Cuando esté listo, continúe con los pasos siguientes:

1. Conéctate al clúster de HDInsight con SSH: Para obtener instrucciones, consulte [Conexión a HDInsight mediante SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. La aplicación que va a compilar requiere el paquete Spark Streaming Event Hubs. Para ejecutar el shell de Spark para que recupere automáticamente esta dependencia de [Maven Central](https://search.maven.org), asegúrese de proporcionar el modificador packages con las coordenadas de Maven como se indica a continuación:

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.5"

6. Una vez que el shell de Spark finalice la carga, debe ver lo siguiente:

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.1.2.6.2.3-1
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_151)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. Copie el siguiente fragmento de código en un editor de texto y modifíquelo para que "policyKey" y "namespace" estén correctamente configurados para el centro de eventos.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )
            
8. Si observa que el punto de conexión compatible con EventHub tiene el formato siguiente, la parte que pone `iothub-xxxxxxxxxx` es el nombre del espacio de nombres compatible con EventHub, y puede usarse para `eventhubs.namespace`. El campo `SharedAccessKeyName` puede usarse para `eventhubs.policyname`, y `SharedAccessKey` para `eventhubs.policykey`: 

        Endpoint=sb://iothub-xxxxxxxxxx.servicebus.windows.net/;SharedAccessKeyName=xxxxx;SharedAccessKey=xxxxxxxxxx 

9. Pegue el fragmento de código modificado en el símbolo del sistema de scala> y pulse Entrar. Debería mostrarse una salida similar a esta:

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

10. A continuación, empiece a crear una consulta de Spark Structured Streaming y especifique el origen de la consulta. Pegue lo siguiente en el shell de Spark y pulse Entrar.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

11. Debería mostrarse una salida similar a esta:

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

12. A continuación, cree la consulta de forma que escriba su salida en la consola. Para ello, pegue lo siguiente en el shell de Spark y pulse Entrar.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

13. Debería observar que algunos lotes comienzan con un resultado similar al siguiente

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

14. Esto va seguido de los resultados de salida del procesamiento de cada microlote de eventos. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

15. Cuando lleguen nuevos eventos del productor de eventos, se procesarán mediante esta consulta de Structured Streaming.
16. Asegúrese de eliminar el clúster de HDInsight cuando haya terminado de ejecutar este ejemplo.



## <a name="see-also"></a>Otras referencias

* [Introducción al streaming en Spark](apache-spark-streaming-overview.md)













