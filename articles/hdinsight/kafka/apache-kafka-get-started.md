---
title: "Introducción a Apache Kafka en Azure HDInsight | Microsoft Docs"
description: "Obtenga información acerca de cómo crear un clúster de Apache Kafka en Azure HDInsight. Obtenga información acerca de cómo crear temas, suscriptores y consumidores."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 20b95f16e16c4b42289e1e25def4910fbca70db5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Inicio de Apache Kafka en HDInsight

Obtenga información acerca de cómo crear y usar un clúster de [Apache Kafka](https://kafka.apache.org) en Azure HDInsight. Kafka es una plataforma de streaming distribuida de código abierto que está disponible con HDInsight. A menudo se usa como agente de mensajes, ya que proporciona una funcionalidad similar a una cola de mensajes de publicación o suscripción.

> [!NOTE]
> Actualmente hay dos versiones de Kafka disponibles con HDInsight; 0.9.0 (HDInsight 3.4) y 0.10.0 (HDInsight 3.5 y 3.6). En los pasos de este documento se supone que usa Kafka en HDInsight 3.6.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Creación de un clúster de Kafka

Siga estos pasos para crear un clúster de Kafka en HDInsight:

1. En [Azure Portal](https://portal.azure.com), seleccione **+ NUEVOE**, **Inteligencia y análisis** y luego seleccione **HDInsight**.
   
    ![Creación de un clúster de HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. En **Básico**, escriba la siguiente información:

    * **Nombre del clúster**: nombre del clúster de HDInsight.
    * **Suscripción**: seleccione la suscripción que vaya a usar.
    * **Nombre de usuario de inicio de sesión del clúster** y **contraseña de inicio de sesión de clúster**: inicio de sesión de acceso al clúster a través de HTTPS. Use estas credenciales para acceder a servicios como la interfaz de usuario de Ambari Web o la API de REST.
    * **Nombre de usuario de Secure Shell (SSH)**: inicio de sesión para acceder al clúster a través de SSH. De forma predeterminada, la contraseña es la misma que la de inicio de sesión en el clúster.
    * **Grupo de recursos**: en el que se va a crear el clúster.
    * **Ubicación**: región de Azure donde se va crear el clúster.

        > [!IMPORTANT]
        > Para lograr alta disponibilidad de los datos, se recomienda seleccionar una ubicación (región) que contenga __tres dominios de error__. Para más información, consulte la sección [Alta disponibilidad de los datos](#data-high-availability).
   
 ![Seleccione la suscripción.](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Seleccione **Tipo de clúster** y establezca los siguientes valores de **Configuración de clúster**:
   
    * **Tipo de clúster**: Kafka

    * **Versión**: Kafka 0.10.0 (HDI 3.6)

    * **Nivel de clúster**: estándar
     
 Por último, use el botón **Seleccionar** para guardar la configuración.
     
 ![Seleccionar el tipo de clúster](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. Después de seleccionar el tipo de clúster, use el botón __Seleccionar__ para establecer el tipo de clúster. A continuación, use el botón __Siguiente__ para finalizar la configuración básica.

5. Desde **Storage**, seleccione o cree una cuenta de Storage. Para seguir los pasos de este documento, deje los demás campos con los valores predeterminados. Use el botón __Siguiente__ para guardar la configuración de almacenamiento.

    ![Configuración de la cuenta de almacenamiento de HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. En __Aplicaciones (opcional)__, seleccione __Siguiente__ para continuar. Para este ejemplo no se requieren aplicaciones.

7. En __Tamaño del clúster__, seleccione __Siguiente__ para continuar.

    > [!WARNING]
    > Para garantizar la disponibilidad de Kafka en HDInsight, el clúster debe contener al menos tres nodos de trabajo. Para más información, consulte la sección [Alta disponibilidad de los datos](#data-high-availability).

    ![Establecer al tamaño de clúster de Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > Los **discos por entrada de nodo de trabajo** controlan la escalabilidad de Kafka en HDInsight. Kafka en HDInsight utiliza el disco local de las máquinas virtuales del clúster. Como Kafka tiene muchas E/S, [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) se utiliza para proporcionar alto rendimiento y mayor espacio de almacenamiento por nodo. El tipo de disco administrado puede ser __Estándar__ (HDD) o __Premium__ (SSD). Los discos Premium se utilizan con máquinas virtuales de las series DS y GS. Todos los otros tipos de máquina virtual usan discos estándar.

8. En __Configuración avanzada__, seleccione __Siguiente__ para continuar.

9. En **Resumen**, revise la configuración para el clúster. Use los vínculos __Edit__ (Editar) para cambiar cualquier configuración incorrecta. Por último, use el botón __Crear__ para crear el clúster.
   
    ![Resumen de configuración del clúster](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="connect-to-the-cluster"></a>Conexión al clúster

> [!IMPORTANT]
> Al realizar los pasos siguientes, debe utilizar un cliente de SSH. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Desde el cliente, use SSH para conectarse al clúster:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Reemplace **SSHUSER** por el nombre de usuario SSH que proporcionó durante la creación del clúster. Reemplace **CLUSTERNAME** por el nombre del clúster.

Cuando se le solicite, escriba la contraseña usada para la cuenta SSH.

Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Obtención de la información del host de Zookeeper y del agente

Cuando se trabaja con Kafka, debe conocer dos valores de host; los hosts de *Zookeeper* y los hosts de *Broker*. Estos hosts se usan con la API de Kafka y muchas de las utilidades que se incluyen con Kafka.

Use los pasos siguientes para crear variables de entorno que contengan la información de host. Estas variables de entorno se usan en los pasos de este documento.

1. Desde una conexión SSH al clúster, use el siguiente comando para instalar la utilidad `jq`. Esta utilidad se emplea para analizar documentos JSON y es útil para recuperar la información de host del agente:
   
    ```bash
    sudo apt -y install jq
    ```

2. Para establecer las variables de entorno con la información recuperada de Ambari, use los siguientes comandos:

    ```bash
    CLUSTERNAME='your cluster name'
    PASSWORD='your cluster password'
    export KAFKAZKHOSTS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > Establezca `CLUSTERNAME=` en el nombre del clúster de Kafka. Establezca `PASSWORD=` en la contraseña de inicio de sesión (administrador) que utilizó al crear el clúster.

    A continuación se muestra un ejemplo del contenido de `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    A continuación se muestra un ejemplo del contenido de `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > El comando `cut` se usa para recortar la lista de hosts a dos entradas. Al crear un productor o cliente de Kafka no es necesario proporcionar la lista completa de hosts.
   
    > [!WARNING]
    > No confíe en que la información que se devuelve en esta sesión sea siempre precisa. Si escala el clúster, se agregan o se quitan nuevos agentes. Si se produce un error y se reemplaza un nodo, el nombre de host del nodo puede cambiar.
    >
    > Debe recuperar la información de los hosts de Zookeeper y del agente antes de usarla para garantizar que tiene información válida.

## <a name="create-a-topic"></a>de un tema

Kafka almacena los flujos de datos en categorías denominadas *temas*. Desde una conexión SSH a un nodo principal de un clúster, use un script proporcionado con Kafka para crear un tema:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Este comando se conecta a Zookeeper mediante la información de host almacenada en `$KAFKAZKHOSTS` y luego crea un tema de Kafka llamado **test**. También puede comprobar que se creó el tema mediante el siguiente script para mostrar temas:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

La salida de este comando muestra los temas de Kafka, que contienen el tema **test**.

## <a name="produce-and-consume-records"></a>Generación y consumo de registros

Kafka almacena *registros* en temas. Los registros se generan mediante *productores* y se consumen mediante *consumidores*. Los productores generan registros para los *agentes* de Kafka. Cada nodo de trabajo del clúster de HDInsight es un agente de Kafka.

Use los pasos siguientes para almacenar registros en el tema de prueba que creó anteriormente y luego leerlos mediante un consumidor:

1. Desde la sesión SSH, use un script que se proporciona con Kafka para escribir registros en el tema:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Tras este comando no volverá al símbolo del sistema. En su lugar, escriba algunos mensajes de texto y luego use **Ctrl + C** para detener el envío del tema. Cada línea se envía como un registro independiente.

2. Use un script proporcionado con Kafka para leer registros del tema:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Este comando recupera los registros del tema y los muestra. Con `--from-beginning` se indica al consumidor que comience desde el principio del flujo, de modo que se recuperan todos los registros.

3. Use __Ctrl + C__ para detener el consumidor.

## <a name="producer-and-consumer-api"></a>API de productor y consumidor

También puede producir y consumir registros mediante programación con las [API de Kafka](http://kafka.apache.org/documentation#api). Para compilar un productor y un consumidor de Java, siga los pasos a continuación desde el entorno de desarrollo.

> [!IMPORTANT]
> Debe tener los siguientes componentes instalados en el entorno de desarrollo:
>
> * [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, como OpenJDK.
>
> * [Apache Maven](http://maven.apache.org/)
>
> * Un cliente de SSH y el comando `scp`. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). En el ejemplo de productor o consumidor, use el proyecto del directorio `Producer-Consumer`. Este ejemplo contiene las siguientes clases:
   
    * **Ejecución**: inicia el consumidor o el productor.

    * **Productor**: almacena 1 000 000 registros en el tema.

    * **Consumidor**: lee registros del tema.

2. Para crear un paquete jar, cambie los directorios a la ubicación del directorio `Producer-Consumer` y use el siguiente comando:

    ```
    mvn clean package
    ```

    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Use los siguientes comandos para copiar el archivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Cuando se le solicite, escriba la contraseña del usuario SSH.

4. Cuando el comando `scp` termine de copiar el archivo, conéctese al clúster mediante SSH. Use el comando siguiente para escribir registros en el tema de prueba:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

5. Cuando finalice el proceso, use el comando siguiente para leer desde el tema:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Se muestran los registros leídos, junto con un número de registros. Puede ver algo más de 1 000 000 de registros, ya que enviamos varios al tema mediante un script en un paso anterior.

6. Use __Ctrl + C__ para salir del consumidor.

### <a name="multiple-consumers"></a>Varios consumidores

Los consumidores de Kafka usan un grupo de consumidores al leer los registros. Usar el mismo grupo con varios consumidores deriva en lecturas de un tema con equilibrio de carga. Cada consumidor del grupo recibe una parte de los registros. Para ver este proceso en acción, siga estos pasos:

1. Abra una nueva sesión SSH en el clúster, para que tenga dos de ellas. En cada sesión, use lo siguiente para iniciar un consumidor con el mismo identificador de grupo de consumidores:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    Este comando inicia un consumidor mediante el identificador de grupo `mygroup`.

    > [!NOTE]
    > Use los comandos de la sección [Obtención de la información del host de Zookeeper y del agente](#getkafkainfo) para establecer `$KAFKABROKERS` para esta sesión SSH.

2. Vea como cada sesión cuenta los registros que recibe del tema. El total de ambas sesiones debe ser el mismo que recibió anteriormente de un consumidor.

El consumo por clientes del mismo grupo se controla mediante las particiones del tema. El tema `test` creado anteriormente tiene ocho particiones. Si abre ocho sesiones de SSH e inicia un consumidor en todas ellas, cada consumidor leerá los registros de una única partición del tema.

> [!IMPORTANT]
> No puede haber más instancias de consumidor en un grupo de consumidores que particiones. En este ejemplo, un grupo de consumidores puede contener hasta ocho, ya que es el número de particiones del tema. O bien, puede tener varios grupos de consumidores, los cuales no tengan más de ocho consumidores cada uno.

Los registros almacenados en Kafka se almacenan en el orden en que se reciben dentro de una partición. Para lograr la entrega ordenada de registros *dentro de una partición*, cree un grupo de consumidores donde el número de instancias de consumidor coincida con el número de particiones. Para lograr la entrega ordenada de registros *dentro del tema*, cree un grupo de consumidores con solo una instancia de consumidor.

## <a name="streaming-api"></a>API de streaming

La API de streaming se agregó a Kafka en la versión 0.10.0; las versiones anteriores se basan en Apache Spark o Storm para el procesamiento de secuencias.

1. Si aún no lo ha hecho, descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) en su entorno de desarrollo. En el ejemplo de streaming, use el proyecto del directorio `streaming`.
   
    Este proyecto contiene solo una clase `Stream`, que lee los registros del tema `test` creado anteriormente. Cuenta las palabras leídas y emite cada palabra y recuento a un tema llamado `wordcounts`. El tema `wordcounts` se crea en un paso más adelante de esta sección.

2. Desde la línea de comandos del entorno de desarrollo, cambie los directorios a la ubicación del directorio `Streaming` y luego use el siguiente comando para crear un paquete jar:

    ```bash
    mvn clean package
    ```

    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Use los siguientes comandos para copiar el archivo `kafka-streaming-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Cuando se le solicite, escriba la contraseña del usuario SSH.

4. Cuando el comando `scp` termine de copiar el archivo, conéctese al clúster mediante SSH y luego use el siguiente comando para crear el tema `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. A continuación, inicie el proceso de streaming mediante el comando siguiente:
   
    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Este comando inicia el proceso de streaming en segundo plano.

6. Use el siguiente comando para enviar mensajes al tema `test`. Estos mensajes se procesan mediante el ejemplo de transmisión:
   
    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Use el siguiente comando para ver la salida que el proceso de streaming escribe en el tema `wordcounts`:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Para ver los datos debe decir al consumidor que imprima la clave y al deserializador que use la clave y el valor. El nombre de la clave es la palabra y su valor contiene el recuento.
   
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
        a       13805
        snow    13637
   
    > [!NOTE]
    > El recuento aumenta cada vez que se encuentra una palabra.

7. Use __Ctrl + C__ para salir del consumidor y luego use el comando `fg` para llevar la tarea en segundo plano de streaming de vuelta al primer plano. Use __Ctrl + C__ también para salir de esto.

## <a name="data-high-availability"></a>Alta disponibilidad de los datos

Cada región de Azure (ubicación) proporciona _dominios de error_. Un dominio de error es una agrupación lógica del hardware subyacente en un centro de datos de Azure. Todos los dominios de error comparten la fuente de energía y el conmutador de red. Las máquinas virtuales y los discos administrados que implementan los nodos en un clúster de HDInsight se distribuyen por estos dominios de error. Esta arquitectura limita el impacto potencial de errores del hardware físico.

Para información sobre el número de dominios de error de una región, consulte el documento sobre la [disponibilidad de las máquinas virtuales Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> Se recomienda utilizar una región de Azure que contenga tres dominios de error y un factor de replicación de 3.

Si debe usar una región que contenga solo dos dominios de error, use un factor de replicación de 4 para distribuir las réplicas uniformemente entre los dominios de error de dos.

### <a name="kafka-and-fault-domains"></a>Kafka y los dominios de error

Kafka no es compatible con dominios de error. Al crear réplicas de la partición de temas, puede que estas no se distribuyan correctamente con alta disponibilidad. Para garantizar la alta disponibilidad, use la [herramienta de reequilibrado de particiones de Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta herramienta se debe ejecutar desde una sesión de SSH en el nodo principal del clúster de Kafka.

Para garantizar la máxima disponibilidad de los datos de Kafka, es preciso reequilibrar las réplicas de las particiones del tema en las siguientes ocasiones:

* Cuando se crean un nuevo tema o una partición

* Cuando un clúster se escala verticalmente

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido los conceptos básicos de trabajar con Apache Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Análisis de los registros de Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Kafka](apache-kafka-mirroring.md)
* [Ejemplo de streaming de Apache Spark (DStream) con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Uso del flujo estructurado de Spark con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
