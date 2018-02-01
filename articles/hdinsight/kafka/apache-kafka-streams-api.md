---
title: 'Uso de Streams API de Apache Kafka: Azure HDInsight | Microsoft Docs'
description: Aprenda a usar Streams API de Apache Kafka con Kafka en HDInsight. Esta API permite realizar el procesamiento de flujos entre temas de Kafka.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 1ea20eceb28fead003c7279632b1e75ae1fd3553
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-streams-api"></a>Streams API de Apache Kafka

Aprenda a crear una aplicación que use Streams API de Kafka y ejecútela con Kafka en HDInsight.

Cuando se trabaja con Apache Kafka, el procesamiento de flujos se suele hacer con Apache Spark o Storm. Streams API se incluyó por primera vez en la versión 0.10.0 de Kafka (en HDInsight 3.5 y 3.6). Esta API le permite transformar flujos de datos entre los temas de entrada y de salida, con una aplicación que se ejecuta en Kafka. En algunos casos, esta puede ser una alternativa a la creación de una solución de streaming de Spark o Storm. Para más información sobre Kafka Streams, consulte la documentación de [introducción a Kafka Streams](https://kafka.apache.org/10/documentation/streams/) en Apache.org.

## <a name="set-up-your-development-environment"></a>Configuración de su entorno de desarrollo

Debe tener los siguientes componentes instalados en el entorno de desarrollo:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Un cliente de SSH y el comando `scp`. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configuración del entorno de implementación

Este ejemplo requiere Kafka en HDInsight 3.6. Para aprender a crear un clúster de Kafka en HDInsight, consulte el documento [Inicio de Apache Kafka en HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Compilación e implementación del ejemplo

Use los siguientes pasos para compilar e implementar el proyecto en el clúster de Kafka en HDInsight.

1. Descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Cambie los directorios al directorio `Streaming` y use el siguiente comando para crear un paquete jar:

    ```bash
    mvn clean package
    ```

    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Use el siguiente comando para copiar el archivo `kafka-streaming-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH. Para obtener más información sobre cómo usar `scp` con HDInsight, consulte [Conexión a través de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Ejecutar el ejemplo

1. Para abrir una conexión de SSH con el clúster, use el siguiente comando:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH. Para obtener más información sobre cómo usar `scp` con HDInsight, consulte [Conexión a través de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Para crear los temas de Kafka que se usan en este ejemplo, use los siguientes comandos:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Reemplace __el nombre de su clúster__ por el nombre del clúster de HDInsight. Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster de HDInsight.

    > [!NOTE]
    > Si el inicio de sesión del clúster es diferente del valor predeterminado de `admin`, reemplace el valor `admin` de los comandos anteriores por el nombre de inicio de sesión del clúster.

5. Para ejecutar este ejemplo, debe hacer tres cosas:

    * Iniciar la solución de flujos incluida en `kafka-streaming.jar`.
    * Iniciar un productor que escriba en el tema `test`.
    * Iniciar un consumidor para que pueda ver la salida escrita en el tema `wordcounts`

    Puede realizar estas operaciones abriendo tres sesiones SSH. Pero, a continuación, tendrá que establecer `$KAFKABROKERS` y `$KAFKAZKHOSTS` para cada una ejecutando el paso 4 de esta sección en cada sesión SSH. Una solución más fácil consiste en usar la utilidad `tmux`, que puede dividir la pantalla actual de SSH en varias secciones. Para iniciar el flujo, el productor y el consumidor mediante `tmux`, use el siguiente comando:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Este comando divide la pantalla de SSH en tres secciones:

    * La sección izquierda ejecuta un consumidor de consola, que lee los mensajes procedentes del tema `wordcounts`:`/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > Los parámetros `--property` indican al consumidor de consola que imprima la clave (palabra) junto con el número (valor). Este parámetro también configura el deserializador que se utilizará al leer estos valores de Kafka.

    * La sección superior derecha ejecuta la solución Streams API:`java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * La sección inferior derecha ejecuta el productor de consola y esperará a que escriba mensajes para enviarlos al tema `test`:`/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Una que el comando `tmux` haya dividido la pantalla, el cursor estará en la sección inferior derecha. Empiece a escribir frases. Después de cada frase, se actualiza el panel izquierdo para mostrar un recuento de palabras únicas. La salida será similar al siguiente texto:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > El recuento aumenta cada vez que se encuentra una palabra.

7. Use __Ctrl + C__ para salir del productor. Siga usando __Ctrl + C__ para salir de la aplicación y del consumidor.

## <a name="next-steps"></a>pasos siguientes

En este documento, aprendió a usar Streams API con Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Análisis de los registros de Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Kafka](apache-kafka-mirroring.md)
* [Uso de Producer y Consumer API de Kafka con HDInsight](apache-kafka-producer-consumer-api.md)
* [Ejemplo de streaming de Apache Spark (DStream) con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Uso del flujo estructurado de Spark con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Uso de los flujos estructurados de Apache Spark para mover datos de Kafka para HDInsight a Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)