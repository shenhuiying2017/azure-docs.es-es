---
title: Uso de Apache Kafka en HDInsight con Azure IoT Hub | Microsoft Docs
description: Aprenda a usar Apache Kafka en HDInsight con Azure IoT Hub. El proyecto Kafka Connect Azure IoT Hub proporciona un conector de origen y de receptor para Kafka. El conector de origen puede leer datos de IoT Hub y el conector de receptor los escribe en IoT Hub.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2018
ms.author: larryfr
ms.openlocfilehash: 33fdb5b099efc40fec94a860b21cda75ced44fe9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267306"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Uso de Apache Kafka en HDInsight con Azure IoT Hub

Aprenda a usar el conector [Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) para mover datos entre Apache Kafka en HDInsight y Azure IoT Hub. En este documento, aprenderá a ejecutar el conector de IoT Hub desde un nodo perimetral del clúster.

La API de Kafka Connect le permite implementar conectores que continuamente insertan datos en Kafka, o extraen datos de Kafka en otro sistema. [Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) es un conector que extrae datos de Azure IoT Hub y los inserta en Kafka. También se pueden insertar datos de Kafka en IoT Hub. 

Al extraer datos de IoT Hub, usará un conector de __origen__. Al insertar en Azure IoT, usará un conector de __receptor__. El conector de IoT Hub proporciona los conectores de origen y de receptor.

En el siguiente diagrama se muestra el flujo de datos entre Azure IoT Hub y Kafka en HDInsight al usar el conector.

![Imagen que muestra el flujo de datos de IoT Hub a Kafka mediante el conector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Para más información sobre las API de Connect, consulte [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

* Un clúster de Kafka en HDInsight. Para más información, consulte el documento [Inicio de Apache Kafka en HDInsight](apache-kafka-get-started.md).

* Un nodo perimetral del clúster de Kafka. Para más información, consulte el documento [Uso de nodos perimetrales con HDInsight](../hdinsight-apps-use-edge-node.md).

* Una instancia de Azure IoT Hub. Para este tutorial, se recomienda el documento[Conexión del simulador en línea Raspberry Pi a Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* Un cliente SSH. Los pasos descritos en este documento usan SSH para conectarse al clúster. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Instalación del conector

1. Descargue la versión más reciente de Kafka Connect para Azure IoT Hub desde [https://github.com/Azure/toketi-kafka-connect-iothub/releases/](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Para cargar el archivo .jar en el nodo perimetral del clúster de Kafka en HDInsight, use el comando siguiente:

    > [!NOTE]
    > Reemplace `sshuser` por la cuenta de usuario de SSH del clúster de HDInsight. Reemplace `new-edgenode` por el nombre del nodo perimetral. Reemplace `clustername` por el nombre del clúster. Para más información sobre el punto de conexión de SSH del nodo perimetral, consulte el documento [Uso de nodos perimetrales con HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node).

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Una vez completada la copia del archivo, conéctese al nodo perimetral mediante SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Para instalar el conector en el directorio `libs` de Kafka, use el comando siguiente:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Si experimenta problemas con el resto de los pasos descritos en este documento cuando se usa un archivo .jar precompilado, intente compilar el paquete desde el origen.
>
> Para compilar el conector, debe tener un entorno de desarrollo de Scala con la [herramienta de compilación de Scala](http://www.scala-sbt.org/).
>
> 1. Descargue el código fuente del conector desde [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) hasta el entorno de desarrollo.
>
> 2. En un símbolo del sistema en el directorio del proyecto, use el siguiente comando para compilar y empaquetar el proyecto:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Este comando crea un archivo denominado `kafka-connect-iothub-assembly_2.11-0.6.jar` en el directorio `target/scala-2.11` para el proyecto.

## <a name="configure-kafka"></a>Configuración de Kafka

Desde una conexión SSH en el nodo perimetral, siga estos pasos para configurar Kafka para ejecutar el conector en modo independiente:

1. Guarde el nombre del clúster en una variable. El uso de una variable permite que sea mucho más fácil copiar y pegar los demás comandos de esta sección:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Instale la utilidad `jq`. Esta utilidad facilita el procesamiento de documentos JSON devueltos por consultas de Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Obtenga la dirección de los agentes de Kafka. Puede haber muchos agentes en el clúster, pero solo debe hacer referencia a uno o dos. Para obtener la dirección de los dos hosts de agente, use el comando siguiente:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Cuando se le solicite, escriba la contraseña de administrador del clúster. El valor que se devuelve es similar al texto siguiente:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Obtenga la dirección de los nodos de Zookeeper. Hay varios nodos de Zookeeper en el clúster, pero basta con hacer referencia a uno o dos. Para obtener la dirección de dos nodos Zookeeper, use el comando siguiente:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Cuando se ejecuta el conector en modo independiente, el archivo `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` se usa para comunicarse con los agentes de Kafka. Para editar el archivo `connect-standalone.properties`, use el comando siguiente:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Para realizar la configuración independiente del nodo perimetral para encontrar los agentes de Kafka, busque la línea que comienza con `bootstrap.servers=`. Reemplace el valor `localhost:9092` por los hosts de agente del paso anterior.

    * Cambie las líneas `key.converter=` y `value.converter=` por los siguientes valores:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Este cambio permite probar mediante el productor de consola incluido con Kafka. Puede que necesite convertidores diferentes para otros productores y consumidores. Para información sobre el uso de otros valores de convertidor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Agregue una línea al final del archivo que contenga el texto siguiente:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Este cambio es para evitar tiempos de espera en el conector de receptor al limitarlo a 10 registros a la vez. Para más información, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Para guardar el archivo, presione __Ctr+X__, luego, __Y__ y __Entrar__.

7. Para crear los temas que usa el conector, use los comandos siguientes:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Para comprobar que los temas `iotin` y `iotout` existen, use el comando siguiente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    El tema `iotin` se usa para recibir mensajes de IoT Hub. El tema `iotout` se usa para enviar mensajes a IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Obtención de información de conexión de IoT Hub

Para recuperar información de IoT Hub que usa el conector, siga estos pasos:

1. Obtenga el punto de conexión compatible con el centro de eventos y el nombre del punto de conexión compatible con el centro de eventos de su centro de IoT. Para obtener esta información, use uno de los métodos siguientes:

    * __En [Azure Portal](https://portal.azure.com/)__, use los pasos siguientes:

        1. Vaya a su instancia de IoT Hub y seleccione __Puntos de conexión__.
        2. En __Puntos de conexión integrados__, seleccione __Eventos__.
        3. En __Propiedades__, copie el valor de los campos siguientes:

            * __Nombre compatible con Event Hub__
            * __Punto de conexión compatible con el centro de eventos__
            * __Particiones__

        > [!IMPORTANT]
        > El valor del punto de conexión del portal puede contener texto adicional que no es necesario en este ejemplo. Extraiga el texto que coincida con este patrón `sb://<randomnamespace>.servicebus.windows.net/`.

    * __En la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, use el comando siguiente:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Reemplace `myhubname` por el nombre de su centro de IoT: La respuesta es similar al siguiente texto:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Obtenga la __directiva de acceso compartido__ y la __clave__. En este ejemplo, use la clave __service__. Para obtener esta información, use uno de los métodos siguientes:

    * __En [Azure Portal](https://portal.azure.com/)__, use los pasos siguientes:

        1. Seleccione __Directivas de acceso compartido__ y, luego, __service__.
        2. Copie el valor de __Clave principal__.
        3. Copie el valor __Connection string--primary key__.

    * __En la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, use el comando siguiente:

        1. Para obtener el valor de clave principal, use el comando siguiente:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Reemplace `myhubname` por el nombre de su centro de IoT: La respuesta es la clave principal a la directiva `service` de este centro.

        2. Para obtener la cadena de conexión de la directiva `service`, use el comando siguiente:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Reemplace `myhubname` por el nombre de su centro de IoT: La respuesta es la cadena de conexión de la directiva `service`.

## <a name="configure-the-source-connection"></a>Configuración de la conexión de origen

Para configurar el origen para que funcione con su instancia de IoT Hub, realice las siguientes acciones desde una conexión SSH hasta el nodo perimetral:

1. Cree una copia del archivo `connect-iot-source.properties` en el directorio `/usr/hdp/current/kafka-broker/config/`. Para descargar el archivo del proyecto toketi-kafka-connect-iothub, use el comando siguiente:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Para editar el archivo `connect-iot-source.properties` y agregar la información del centro de IoT, use el comando siguiente:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    En el editor, busque y cambie las siguientes entradas:

    * `Kafka.Topic=PLACEHOLDER`: reemplace `PLACEHOLDER` por `iotin`. Los mensajes recibidos del centro de IoT se colocan en el tema `iotin`.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: reemplace `PLACEHOLDER` por el nombre compatible con el centro de eventos.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: reemplace `PLACEHOLDER` por el punto de conexión compatible con el centro de eventos.
    * `IotHub.Partitions=PLACEHOLDER`: reemplace `PLACEHOLDER` por el número de particiones de los pasos anteriores.
    * `IotHub.AccessKeyName=PLACEHOLDER`: reemplace `PLACEHOLDER` por `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: reemplace `PLACEHOLDER` por la clave principal de la directiva `service`.
    * `IotHub.StartType=PLACEHOLDER`: reemplace `PLACEHOLDER` por una fecha UTC. Esta fecha corresponde a cuando el conector comienza a buscar mensajes. El formato de fecha es `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: reemplace `100` por `5`. Este cambio hace que el conector lea los mensajes en Kafka una vez que hay cinco nuevos mensajes en el centro de IoT.

    Para ver una configuración de ejemplo, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Para guardar el archivo, use __Ctrl+X__, __Y__ y, luego, __Entrar__.

Para más información sobre cómo configurar el origen del conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Configuración de la conexión de receptor

Para configurar la conexión de receptor para que funcione con su instancia de IoT Hub, realice las siguientes acciones desde una conexión SSH hasta el nodo perimetral:

1. Cree una copia del archivo `connect-iothub-sink.properties` en el directorio `/usr/hdp/current/kafka-broker/config/`. Para descargar el archivo del proyecto toketi-kafka-connect-iothub, use el comando siguiente:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Para editar el archivo `connect-iothub-sink.properties` y agregar la información del centro de IoT, use el comando siguiente:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    En el editor, busque y cambie las siguientes entradas:

    * `topics=PLACEHOLDER`: reemplace `PLACEHOLDER` por `iotout`. Los mensajes enviados a `iotout` se reenvían al centro de IoT.
    * `IotHub.ConnectionString=PLACEHOLDER`: reemplace `PLACEHOLDER` por la cadena de conexión de la directiva `service`.

    Para ver una configuración de ejemplo, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Para guardar el archivo, use __Ctrl+X__, __Y__ y, luego, __Entrar__.

Para más información sobre cómo configurar el conector de receptor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Inicio del conector de origen

Para iniciar el conector de origen, use el comando siguiente desde una conexión SSH hasta el nodo perimetral:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Una vez que se inicia el conector, envíe mensajes al centro de IoT desde sus dispositivos. A medida que el conector lee los mensajes del centro de IoT y los almacena en el tema de Kafka, registra información en la consola:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Es posible que vea varias advertencias cuando se inicia el conector. Estas advertencias no ocasionan problemas con la recepción de mensajes desde el centro de IoT.

Para detener el conector, use __Ctrl+C__.

## <a name="start-the-sink-connector"></a>Inicio del conector de receptor

Desde una conexión SSH hasta el nodo perimetral, use el comando siguiente para iniciar el conector de receptor en modo independiente:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Cuando se ejecuta el conector, se muestra información similar al siguiente texto:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Al iniciar el conector, puede que observe varias advertencias. Puede omitir estos errores con seguridad.

Para enviar mensajes mediante el conector, siga estos pasos:

1. Abra otra sesión SSH al clúster de Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Para enviar mensajes al tema `iotout`, use el comando siguiente:

    > [!WARNING]
    > Puesto que se trata de una nueva conexión SSH, la variable `$KAFKABROKERS` no contiene ninguna información. Para establecerla, use uno de los siguientes métodos:
    >
    > * Utilice los tres primeros pasos de la sección [Configuración de Kafka](#configure-kafka).
    > * Use `echo $KAFKABROKERS` desde la conexión SSH anterior para obtener los valores y, a continuación, reemplace `$KAFKABROKERS` en el siguiente comando por los valores reales.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Este comando no le devuelve al símbolo del sistema normal de Bash. En su lugar, envía la entrada del teclado al tema `iotout`.

3. Para enviar un mensaje a su dispositivo, pegue un documento JSON en la sesión SSH para `kafka-console-producer`.

    > [!IMPORTANT]
    > Debe establecer el valor de la entrada `"deviceId"` en el identificador del dispositivo. En el ejemplo siguiente, el dispositivo se denomina `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    El esquema de este documento JSON se describe con más detalle en [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Si está usando el dispositivo simulado Raspberry Pi, y está en ejecución, el dispositivo registra el siguiente mensaje:

    ```text
    Receive message: Turn On
    ```

    Vuelva a enviar el documento JSON, pero cambie el valor de la entrada `"message"`. El dispositivo registra el nuevo valor.

Para más información sobre cómo usar el conector de receptor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a usar la API de Kafka Connect para iniciar Kafka Connector de IoT en HDInsight. Utilice los vínculos siguientes para conocer otras formas de trabajar con Kafka:

* [Uso de Apache Spark con Kafka en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)
