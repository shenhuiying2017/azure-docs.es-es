---
title: 'Uso de Producer y Consumer API de Apache Kafka: Azure HDInsight | Microsoft Docs'
description: Aprenda a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight. Estas API le permiten desarrollar aplicaciones que escriben en Apache Kafka y que leen desde este.
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
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: b57745d6bd993a993e923c964327d9071e745413
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Producer y Consumer API de Apache Kafka

Aprenda a crear una aplicación que use Producer y Consumer API de Kafka con Kafka en HDInsight.

Para obtener documentación sobre las API, consulte [Producer API](https://kafka.apache.org/documentation/#producerapi) y [Consumer API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configuración de su entorno de desarrollo

Debe tener los siguientes componentes instalados en el entorno de desarrollo:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Un cliente de SSH y el comando `scp`. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configuración del entorno de implementación

Este ejemplo requiere Kafka en HDInsight 3.6. Para aprender a crear un clúster de Kafka en HDInsight, consulte el documento [Inicio de Apache Kafka en HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Compilación e implementación del ejemplo

1. Descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Cambie los directorios a la ubicación del directorio `Producer-Consumer` y use el siguiente comando:

    ```
    mvn clean package
    ```

    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Use los siguientes comandos para copiar el archivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Cuando se le solicite, escriba la contraseña del usuario SSH.

## <a id="run"></a> Ejecutar el ejemplo

1. Para abrir una conexión de SSH con el clúster, use el siguiente comando:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH. Para obtener más información sobre cómo usar `scp` con HDInsight, consulte [Conexión a través de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para crear los temas de Kafka que se usan en este ejemplo, use los siguientes comandos:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Reemplace __el nombre de su clúster__ por el nombre del clúster de HDInsight. Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster de HDInsight.

    > [!NOTE]
    > Si el inicio de sesión del clúster es diferente del valor predeterminado de `admin`, reemplace el valor `admin` de los comandos anteriores por el nombre de inicio de sesión del clúster.

3. Para ejecutar el productor y escribir datos en el tema, utilice el siguiente comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Cuando finalice el productor, use el comando siguiente para leer desde el tema:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Se muestran los registros leídos, junto con un número de registros.

5. Use __Ctrl + C__ para salir del consumidor.

### <a name="multiple-consumers"></a>Varios consumidores

Los consumidores de Kafka usan un grupo de consumidores al leer los registros. Usar el mismo grupo con varios consumidores deriva en lecturas de un tema con equilibrio de carga. Cada consumidor del grupo recibe una parte de los registros.

La aplicación del consumidor acepta un parámetro que se usa como identificador del grupo. Por ejemplo, el comando siguiente inicia un consumidor mediante un identificador de grupo `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Para ver este proceso en acción, siga estos pasos:

1. Repita los pasos 1 y 2 de la sección [Ejecutar el ejemplo](#run) para abrir una nueva sesión SSH.

2. En las sesiones SSH, escriba el siguiente comando:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Escriba los comandos simultáneamente, para que ambos se ejecuten al mismo tiempo.

    Tenga en cuenta que el número de mensajes leídos no es igual al de la sección anterior, en la que tenía un solo consumidor. En vez de eso, la lectura de mensajes está divida entre las instancias.

El consumo por clientes del mismo grupo se controla mediante las particiones del tema. El tema `test` creado anteriormente tiene ocho particiones. Si abre ocho sesiones de SSH e inicia un consumidor en todas ellas, cada consumidor leerá los registros de una única partición del tema.

> [!IMPORTANT]
> No puede haber más instancias de consumidor en un grupo de consumidores que particiones. En este ejemplo, un grupo de consumidores puede contener hasta ocho, ya que es el número de particiones del tema. O bien, puede tener varios grupos de consumidores, los cuales no tengan más de ocho consumidores cada uno.

Los registros almacenados en Kafka se almacenan en el orden en que se reciben dentro de una partición. Para lograr la entrega ordenada de registros *dentro de una partición*, cree un grupo de consumidores donde el número de instancias de consumidor coincida con el número de particiones. Para lograr la entrega ordenada de registros *dentro del tema*, cree un grupo de consumidores con solo una instancia de consumidor.

## <a name="next-steps"></a>pasos siguientes

En este documento, aprendió a usar Producer y Consumer API de Kafka con Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Análisis de los registros de Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Kafka](apache-kafka-mirroring.md)
* [Streams API de Kafka con HDInsight](apache-kafka-streams-api.md)
* [Ejemplo de streaming de Apache Spark (DStream) con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Uso del flujo estructurado de Spark con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Uso de los flujos estructurados de Apache Spark para mover datos de Kafka para HDInsight a Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)