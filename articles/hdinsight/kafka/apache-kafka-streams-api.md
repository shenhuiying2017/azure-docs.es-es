---
title: 'Tutorial: Uso de Streams API de Apache Kafka: Azure HDInsight | Microsoft Docs'
description: Aprenda a usar Streams API de Apache Kafka con Kafka en HDInsight. Esta API permite realizar el procesamiento de flujos entre temas de Kafka.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2018
ms.author: larryfr
ms.openlocfilehash: 8aff28079a0aaa7c02d8a187cb379ecdbedcd854
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-apache-kafka-streams-api"></a>Tutorial: Streams API de Apache Kafka

Aprenda a crear una aplicación que use Streams API de Kafka y ejecútela con Kafka en HDInsight. 

La aplicación que se usa en este tutorial es un recuento de palabras de streaming. Lee datos de texto de un tema de Kafka, extrae las palabras individuales y, a continuación, almacena el recuento de palabras en otro tema de Kafka.

> [!NOTE]
> El procesamiento de flujos de Kafka se suele hacer con Apache Spark o Storm. Streams API se incluyó por primera vez en la versión 0.10.0 de Kafka (en HDInsight 3.5 y 3.6). Esta API le permite transformar flujos de datos entre los temas de entrada y de salida. En algunos casos, esta puede ser una alternativa a la creación de una solución de streaming de Spark o Storm. 
>
> Para más información sobre Kafka Streams, consulte la documentación de [introducción a Kafka Streams](https://kafka.apache.org/10/documentation/streams/) en Apache.org.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración de su entorno de desarrollo
> * Comprensión del código
> * Compilar e implementar la aplicación
> * Configurar temas de Kafka
> * Ejecución del código

## <a name="prerequisites"></a>requisitos previos

* Un clúster de Kafka en HDInsight 3.6. Para aprender a crear un clúster de Kafka en HDInsight, consulte el documento [Inicio de Apache Kafka en HDInsight](apache-kafka-get-started.md).

* Complete los pasos que se indican en el documento [Producer y Consumer API de Apache Kafka](apache-kafka-producer-consumer-api.md). Los pasos de este documento utilizan la aplicación y los temas de ejemplo que creó en este tutorial.

## <a name="set-up-your-development-environment"></a>Configuración de su entorno de desarrollo

Debe tener los siguientes componentes instalados en el entorno de desarrollo:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Un cliente de SSH y el comando `scp`. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Comprensión del código

La aplicación de ejemplo se encuentra en [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), en el subdirectorio `Streaming`. La aplicación consta de dos archivos:

* `pom.xml`: este archivo define las dependencias del proyecto, la versión de Java y los métodos de empaquetado.
* `Stream.java`: este archivo implementa la lógica de streaming.

### <a name="pomxml"></a>Pom.xml

Esto es lo más importante que hay que saber del archivo `pom.xml`:

* Dependencias: este proyecto utiliza Streams API de Kafka que la proporciona el paquete `kafka-clients`. El siguiente código XML define esta dependencia:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > La entrada `${kafka.version}` se declara en la sección `<properties>..</properties>` de `pom.xml` y está configurada para la versión Kafka del clúster de HDInsight.

* Complementos: los complementos de Maven proporcionan varias funcionalidades. En este proyecto, se utilizan los siguientes complementos:

    * `maven-compiler-plugin`: se utiliza para establecer que el proyecto utiliza la versión 8 de Java. HDInsight 3.6 requiere Java 8.
    * `maven-shade-plugin`: se utiliza para generar un archivo uber-jar que contiene esta aplicación, así como todas las dependencias. También se usa para establecer el punto de entrada de la aplicación, con el fin de que pueda ejecutar directamente el archivo Jar sin tener que especificar la clase principal.

### <a name="streamjava"></a>Stream.java

El archivo `Stream.java` utiliza Streams API para implementar una aplicación de recuento de palabras. Lee datos de un tema de Kafka llamado `test` y escribe los recuentos de palabras en un tema llamado `wordcounts`.

El código siguiente define la aplicación de recuento de palabras:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>Compilación e implementación del ejemplo

Use los siguientes pasos para compilar e implementar el proyecto en el clúster de Kafka en HDInsight:

1. Descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Cambie los directorios al directorio `Streaming` y use el siguiente comando para crear un paquete jar:

    ```bash
    mvn clean package
    ```

    Este comando crea el paquete en `target/kafka-streaming-1.0-SNAPSHOT.jar`.

3. Use el siguiente comando para copiar el archivo `kafka-streaming-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Reemplace `sshuser` por el usuario de SSH del clúster y `clustername` por el nombre de su clúster. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH. Para obtener más información sobre cómo usar `scp` con HDInsight, consulte [Conexión a través de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-kafka-topics"></a>Creación de temas de Kafka

1. Para abrir una conexión de SSH con el clúster, use el siguiente comando:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Reemplace `sshuser` por el usuario de SSH del clúster y `clustername` por el nombre de su clúster. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH. Para obtener más información sobre cómo usar `scp` con HDInsight, consulte [Conexión a través de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para guardar el nombre del clúster en una variable e instalar una utilidad de análisis de JSON (`jq`), use los siguientes comandos. Cuando se le solicite, escriba el nombre del clúster de Kafka:

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. Para obtener tanto los hosts del agente de Kafka como los hosts de Zookeeper, use los siguientes comandos. Cuando se le solicite, escriba la contraseña de administrador del clúster. Se le pedirá que confirme la contraseña.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. Para crear los temas que emplea la operación de streaming, use los siguientes comandos:

    > [!NOTE]
    > Puede recibir un error que indica que el tema `test` ya existe. Esto es normal, ya que puede que lo haya creado en el tutorial sobre Producer y Consumer API.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Los temas se utilizan para los siguientes fines:

    * `test`: este es el tema en el que se reciben los registros. La aplicación de streaming los lee de aquí.
    * `wordcounts`: este es el tema en el que la aplicación de streaming almacena su salida.
    * `RekeyedIntermediateTopic`: este tema se usa para volver a particionar los datos a medida que el operador `countByKey` actualiza el recuento.
    * `wordcount-example-Counts-changelog`: este tema es un almacén de estados que usa la operación `countByKey`

    > [!IMPORTANT]
    > Kafka en HDInsight se puede también configurar para que cree temas automáticamente. Para más información, consulte el documento [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Configuración de la creación automática de temas).

## <a name="run-the-code"></a>Ejecución del código

1. Para iniciar la aplicación de streaming como un proceso en segundo plano, use el comando siguiente:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > Puede recibir una advertencia sobre log4j. Puede pasarla por alto.

2. Para enviar registros al tema `test`, use el comando siguiente para iniciar la aplicación de producción:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

3. Una vez finalizada la producción, use el siguiente comando para ver la información almacenada en el tema `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > Los parámetros `--property` indican al consumidor de consola que imprima la clave (palabra) junto con el número (valor). Este parámetro también configura el deserializador que se utilizará al leer estos valores de Kafka.

    La salida será similar al siguiente texto:
   
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
    > El parámetro `--from-beggining` configura el consumidor para que empiece por el primero de los registros almacenados en el tema. El recuento se incrementa cada vez que se encuentra una palabra, por lo que el tema contiene varias entradas para cada palabra, con un recuento creciente.

7. Use __Ctrl + C__ para salir del productor. Siga usando __Ctrl + C__ para salir de la aplicación y del consumidor.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a usar Streams API con Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Análisis de los registros de Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Kafka](apache-kafka-mirroring.md)
