---
title: "Introducción a Apache Kafka en HDInsight | Microsoft Docs"
description: Aprenda los fundamentos para crear y trabajar con Kafka en HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/07/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 9d20050dada974c0c2a54399e2db7b9a289f7e89
ms.openlocfilehash: 618a564dea11ed605d66dad1a36819653dcf912f

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Introducción a Apache Kafka (versión preliminar) en HDInsight

[Apache Kafka](https://kafka.apache.org) es una plataforma de streaming distribuida de código abierto que está disponible con HDInsight. A menudo se usa como agente de mensajes, ya que proporciona una funcionalidad similar a una cola de mensajes de publicación o suscripción. En este documento, aprenderá a crear un clúster de Kafka en HDInsight y luego a enviar y recibir datos desde una aplicación de Java.

> [!NOTE]
> Actualmente hay dos versiones de Kafka disponibles con HDInsight; 0.9.0 (HDInsight 3.4) y 0.10.0 (HDInsight 3.5). En los pasos de este documento se supone que usa Kafka en HDInsight 3.5.

## <a name="prerequisite"></a>Requisito previo

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Debe cumplir los siguientes requisitos previos para poder completar correctamente este tutorial sobre Apache Storm.

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Familiaridad con SSH y SCP**. Para más información sobre el uso de SSH y SCP con HDInsight, consulte los siguientes documentos:
  
   * **Clientes Linux, Unix u OS X**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md)
   
   * **Clientes Windows**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows (vista previa)](hdinsight-hadoop-linux-use-ssh-windows.md)

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Creación de un clúster de Kafka

Siga estos pasos para crear un clúster de Kafka en HDInsight:

1. En [Azure Portal](https://portal.azure.com), seleccione **+ NUEVOE**, **Inteligencia y análisis** y luego seleccione **HDInsight**.
   
    ![Creación de un clúster de HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. En la hoja **Nuevo clúster de HDInsight**, escriba un **nombre de clúster** y seleccione la **suscripción** que se usará con dicho clúster.
   
    ![Seleccione la suscripción.](./media/hdinsight-apache-kafka-get-started/new-hdinsight-cluster-blade.png)

3. Haga clic en **Seleccionar tipo de clúster** y seleccione los siguientes valores en la hoja **Cluster Type configuration** (Configuración del tipo de clúster):
   
    * **Tipo de clúster**: Kafka

    * **Versión**: Kafka 0.10.0 (HDI 3.5)

    * **Nivel de clúster**: estándar
     
    Por último, use el botón **Seleccionar** para guardar la configuración.
     
    ![Seleccionar el tipo de clúster](./media/hdinsight-apache-kafka-get-started/cluster-type.png)

    > [!NOTE]
    > Si su suscripción de Azure no tiene acceso a la versión preliminar de Kafka, se muestran instrucciones sobre cómo obtener acceso a la versión preliminar. Las instrucciones mostradas son similares a la siguiente imagen:
    >
    > ![mensaje de versión preliminar: if you would like to deploy a managed Apache Kafka cluster on HDInsight, email us to request preview access (Si quiere implementar un clúster de Apache Kafka administrado en HDInsight, envíenos un correo electrónico para solicitar el acceso a la versión preliminar)](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. En **Credenciales**, configure el inicio de sesión de clúster y las credenciales de usuario de SSH.  Use el botón **Seleccionar** para guardar la configuración.
   
    > [!NOTE]
    > El inicio de sesión del clúster se usa al acceder al clúster a través de Internet mediante HTTPS. El usuario SSH se usa para conectarse al clúster y ejecutar los comandos de forma interactiva.
   
    ![Configurar el inicio de sesión del clúster](./media/hdinsight-apache-kafka-get-started/cluster-credentials.png)
   
    Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes:
   
    * [Utilización de SSH con HDInsight desde un cliente Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [Utilización de SSH con HDInsight desde un cliente Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

5. Use **Origen de datos** para configurar el almacén de datos principal del clúster. En la hoja **Origen de datos**, use la siguiente información para crear un almacén de datos para el clúster:
   
    * Seleccione **Crear nuevo** y luego escriba un nombre para la cuenta de almacenamiento.
    
    * Seleccione **Ubicación** y luego seleccione la ubicación que esté más próxima geográficamente. Esta ubicación se usat para crear la cuenta de almacenamiento y el clúster de HDInsight.
     
   Por último, use el botón **Seleccionar** para guardar la configuración.
     
    ![Configurar el almacenamiento](./media/hdinsight-apache-kafka-get-started/configure-storage.png)

6. Use **Precios** y luego establezca el **número de nodos de trabajado** en 2. El uso de dos nodos de trabajo le ayuda a reducir el costo del clúster y es suficiente para este ejemplo. Use el botón **Seleccionar** para guardar la configuración.
   
    ![Precios](./media/hdinsight-apache-kafka-get-started/pricing.png)
   
    > [!NOTE]
    > Los precios que se muestran en el portal pueden ser diferentes de los precios de la captura de pantalla.

7. Use **Grupo de recursos** para crear un grupo y escriba el nombre en el campo. Seleccione **Anclar al panel**. Cuando haya terminado, seleccione **Crear** para crear el clúster.
   
    ![Campo Grupo de recursos](./media/hdinsight-apache-kafka-get-started/resource-group.png)
   
    > [!NOTE]
    > Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="connect-to-the-cluster"></a>Conexión al clúster

Desde el cliente, uset SSH para conectarse al clúster. Si va a usar Linux, Unix, MacOS o Bash en Windows 10, utilice el siguiente comando:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Reemplace **SSHUSER** por el nombre de usuario SSH que proporcionó durante la creación del clúster. Reemplace **CLUSTERNAME** por el nombre del clúster.

Cuando se le solicite, escriba la contraseña usada para la cuenta SSH.

Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes:

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Obtención de la información del host de Zookeeper y del agente

Cuando se trabaja con Kafka, debe conocer dos valores de host; los hosts de *Zookeeper* y los hosts de *Broker*. Estos hosts se usan con la API de Kafka y muchas de las utilidades que se incluyen con Kafka.

Use los pasos siguientes para crear variables de entorno que contengan la información de host. Estas variables de entorno se usan en los pasos de este documento.

1. Desde una conexión SSH al clúster, use el siguiente comando para instalar la utilidad `jq`. Esta utilidad se emplea para analizar documentos JSON y es útil para recuperar la información de host del agente:
   
    ```sudo apt -y install jq```

2. Use los siguientes comandos para establecer las variables de entorno con la información recuperada de Ambari. Sustituya __KAFKANAME__ por el nombre del clúster Kafka. Sustituya __PASSWORD__ por la contraseña de inicio de sesión (administrador) que utilizó al crear el clúster.

    ```
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    A continuación se muestra un ejemplo del contenido de `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    A continuación se muestra un ejemplo del contenido de `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > No confíe en que la información que se devuelve en esta sesión sea siempre precisa. Si escala el clúster, se agregan o se quitan nuevos agentes. Si se produce un error y se reemplaza un nodo, el nombre de host del nodo puede cambiar. 
    > 
    > Siempre debe recuperar la información de los hosts de Zookeeper y del agente antes de usarla para garantizar que tiene información válida.

## <a name="create-a-topic"></a>de un tema

Kafka almacena los flujos de datos en categorías denominadas *temas*. Desde una conexión SSH a un nodo principal de un clúster, use un script proporcionado con Kafka para crear un tema:

```/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS```

Este comando se conecta a Zookeeper mediante la información de host almacenada en `$KAFKAZKHOSTS` y luego crea un tema de Kafka llamado **test**. También puede comprobar que se creó el tema mediante el siguiente script para mostrar temas:

```/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS```

La salida de este comando muestra los temas de Kafka, que contienen el tema **test**.

## <a name="produce-and-consume-records"></a>Generación y consumo de registros

Kafka almacena *registros* en temas. Los registros se generan mediante *productores* y se consumen mediante *consumidores*. Los productores recuperan registros de *agentes* de Kafka. Cada nodo de trabajo del clúster de HDInsight es un agente de Kafka.

Use los pasos siguientes para almacenar registros en el tema de prueba que creó anteriormente y luego leerlos mediante un consumidor:

1. Desde la sesión SSH, use un script que se proporciona con Kafka para escribir registros en el tema:
   
    ```/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test```
   
    Tras este comando no volverá al símbolo del sistema. En su lugar, escriba algunos mensajes de texto y luego use **Ctrl + C** para detener el envío del tema. Cada línea se envía como un registro independiente.

2. Use un script proporcionado con Kafka para leer registros del tema:
   
    ```/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning```
   
    Se recuperarán los registros del tema y se mostrarán. Con `--from-beginning` se indica al consumidor que comience desde el principio del flujo, de modo que se recuperan todos los registros.

3. Use __Ctrl + C__ para detener el consumidor.

## <a name="producer-and-consumer-api"></a>API de productor y consumidor

También puede producir y consumir registros mediante programación con las [API de Kafka](http://kafka.apache.org/documentation#api). Use los siguientes pasos para descargar y compilar un productor y un consumidor basados en Java:

1. Descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). En el ejemplo de productor o consumidor, use el proyecto del directorio `Producer-Consumer`. Asegúrese de revisar el código para entender cómo funciona este ejemplo. Contiene las siguientes clases:
   
    * **Ejecución**: inicia el consumidor o el productor según los argumentos de la línea de comandos.

    * **Productor**: almacena 1 000 000 registros en el tema.

    * **Consumidor**: lee registros del tema.

2. Desde la línea de comandos del entorno de desarrollo, cambie los directorios a la ubicación del directorio `Producer-Consumer` del ejemplo y luego use el siguiente comando para crear un paquete jar:
   
    ```mvn clean package```
   
    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Use los siguientes comandos para copiar el archivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar```
   
    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Cuando se le solicite, escriba la contraseña del usuario SSH.

4. Cuando el comando `scp` termine de copiar el archivo, conéctese al clúster mediante SSH y luego use lo siguiente para escribir registros en el tema de prueba que creó anteriormente.
   
    ```./kafka-producer-consumer.jar producer $KAFKABROKERS```
   
    Se iniciará el productor y se escribirán los registros. Se muestra un contador para que pueda ver el número de registros que se han escrito.

    > [!NOTE]
    > Si recibe un error de permiso denegado, use el siguiente comando para hacer que el archivo sea ejecutable: ```chmod +x kafka-producer-consumer.jar```.

5. Cuando finalice el proceso, use el comando siguiente para leer desde el tema:
   
    ```./kafka-producer-consumer.jar consumer $KAFKABROKERS```
   
    Se muestran los registros leídos, junto con un número de registros. Puede ver algo más de 1 000 000 registrados ya que enviamos varios registros al tema mediante un script en un paso anterior.

6. Use __Ctrl + C__ para salir del consumidor.

### <a name="multiple-consumers"></a>Varios consumidores

Un concepto importante con Kafka es que los consumidores usan un grupo de consumidores (definido por un identificador de grupo) al leer registros. Varios consumidores que usan el mismo grupo se reparten la carga de lecturas de un tema; cada consumidor recibe una parte de los registros. Para ver esto en acción, siga estos pasos:

1. Abra una nueva sesión SSH en el clúster, para que tenga dos de ellas. En cada sesión, use lo siguiente para iniciar un consumidor con el mismo identificador de grupo de consumidor:
   
    ```./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup```

    > [!NOTE]
    > Como se trata de una nueva sesión de SSH, deberá usar los comandos de la sección [Obtención de la información del host de Zookeeper y del agente](#getkafkainfo) para establecer `$KAFKABROKERS`.

2. Vea como cada sesión cuenta los registros que recibe del tema. El total de ambas sesiones debe ser el mismo que recibió anteriormente de un consumidor.

El consumo por clientes del mismo grupo se controla mediante las particiones del tema. El tema `test` creado anteriormente tiene 8 particiones. Si abre 8 las sesiones de SSH e inicia un consumidor en todas las sesiones, cada consumidor leerá registros de una única partición del tema.

> [!IMPORTANT]
> No puede haber más instancias de consumidor en un grupo de consumidores que particiones. En este ejemplo, un grupo de consumidores puede contener hasta 8 consumidores ya que es el número de particiones en el tema. O bien, puede tener varios grupos de consumidores, cada uno con no más de 8 consumidores.

Los registros almacenados en Kafka se almacenan en el orden en que se reciben dentro de una partición. Para lograr la entrega ordenada de registros *dentro de una partición*, cree un grupo de consumidores donde el número de instancias de consumidor coincida con el número de particiones. Para lograr la entrega ordenada de registros *dentro del tema*, cree un grupo de consumidores con solo una instancia de consumidor.

## <a name="streaming-api"></a>API de streaming

La API de streaming se agregó a Kafka en la versión 0.10.0; las versiones anteriores se basan en Apache Spark o Storm para el procesamiento de secuencias.

1. Si aún no lo ha hecho, descargue los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) en su entorno de desarrollo. En el ejemplo de streaming, use el proyecto del directorio `streaming`. Asegúrese de revisar el código para entender cómo funciona este ejemplo. 
   
    Este proyecto contiene solo una clase `Stream`, que lee los registros del tema `test` creado anteriormente. Cuenta las palabras leídas y emite cada palabra y recuento a un tema llamado `wordcounts`. El tema `wordcounts` se crea en un paso más adelante de esta sección.

2. Desde la línea de comandos del entorno de desarrollo, cambie los directorios a la ubicación del directorio `Streaming` y luego use el siguiente comando para crear un paquete jar:
   
    ```mvn clean package```
   
    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Use los siguientes comandos para copiar el archivo `kafka-streaming-1.0-SNAPSHOT.jar` en el clúster de HDInsight:
   
    ```scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar```
   
    Reemplace **SSHUSER** por el usuario SSH de su clúster y **CLUSTERNAME** por el nombre de su clúster. Cuando se le solicite, escriba la contraseña del usuario SSH.

4. Cuando el comando `scp` termine de copiar el archivo, conéctese al clúster mediante SSH y luego use el siguiente comando para crear el tema `wordcounts`:

    ```/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS```

5. A continuación, inicie el proceso de streaming mediante el comando siguiente:
   
    ```./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &```
   
    Este comando inicia el proceso de streaming en segundo plano.

6. Use el siguiente comando para enviar mensajes al tema `test`. Estos se procesan mediante el ejemplo de streaming:
   
    ```./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &```

7. Use el siguiente comando para ver la salida que el proceso de streaming escribe en el tema `wordcounts`:
   
    ```/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer```
   
    > [!NOTE]
    > Debemos decir al consumidor que imprima la clave (que contiene el valor de palabra) y al deserializador que use la clave y el valor para ver los datos.
   
    La salida será similar a la siguiente:
   
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
   
    Observe que el recuento aumenta cada vez que se encuentra una palabra.

7. Use __Ctrl + C__ para salir del consumidor y luego use el comando `fg` para llevar la tarea en segundo plano de streaming de vuelta al primer plano. Use __Ctrl + C__ también para salir de esto.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido los conceptos básicos de trabajar con Apache Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Documentación de Apache Kafka](http://kafka.apache.org/documentation.html) en kafka.apache.org.
* [Uso de MirrorMaker para crear una réplica de Kafka en HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Uso de Apache Kafka con Storm en HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Uso de Apache Spark con Kafka en HDInsight](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Feb17_HO2-->


