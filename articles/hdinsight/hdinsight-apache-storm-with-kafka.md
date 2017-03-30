---
title: Uso de Apache Kafka con Storm en HDInsight | Microsoft Docs
description: "Apache Kafka se instala con Apache Storm en HDInsight. Vea cómo escribir en Kafka y después leer de él con los componentes KafkaBolt y KafkaSpout proporcionados Storm. Aprenda también a usar el entorno de Flux para definir y enviar topologías de Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3f0d284e122704ba01676c4b0028e196fe47bca8
ms.lasthandoff: 03/21/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Uso de Apache Kafka (versión preliminar) con Storm en HDInsight

Apache Kafka es una solución de mensajería de publicación-suscripción que está disponible con HDInsight. Apache Storm es un sistema distribuido que puede utilizarse para analizar datos en tiempo real. En este documento se muestra cómo usar Storm en HDInsight para leer y procesar los datos de Kafka en HDInsight. En el ejemplo de este documento se usa una topología de Storm que usa los componentes spout y bolt de Kafka disponibles con Apache Storm.

> [!NOTE]
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Storm en HDInsight y un clúster Kafka en HDInsight. Estos dos clústeres se encuentran en una red virtual de Azure, lo que permite al clúster Storm comunicarse directamente con el clúster Kafka.
> 
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="prerequisites"></a>Requisitos previos

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 o versiones posteriores. O equivalente, como [OpenJDK](http://openjdk.java.net/).
  
    > [!NOTE]
    > En los pasos de este documento se usa un clúster de HDInsight 3.5, que usa Java 8.

* [Maven 3.x](http://maven.apache.org/): paquete de administración de compilación para aplicaciones Java.

* Un editor de texto o IDE de Java

* Un cliente SSH (se necesitan los comandos `ssh` y `scp`). Para más información sobre el uso de SSH con HDInsight, consulte los siguientes documentos:
  
  * [Uso de SSH con HDInsight basado en Linux desde Linux, Unix, macOS y Bash en Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md)

  * [Uso de SSH (PuTTY) con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Cualquier comunicación con Kafka debe realizarse en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka y Storm se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Storm y Kafka en una red virtual de Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Aunque Kafka tiene limitadas la comunicación en la red virtual, se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).


Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka y Storm, resulta más sencillo utilizar una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y los clústeres Kafka y Storm en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    La plantilla de Azure Resource Manager se encuentra en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**.

2. Utilice la guía siguiente para rellenar las entradas de la hoja **Implementación personalizada**:
   
    ![Implementación personalizada de HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Grupo de recursos**: cree un nuevo grupo de recursos o seleccione uno existente. Este grupo contiene el clúster de HDInsight.
   
    * **Ubicación**: seleccione una ubicación geográfica próxima a usted. Esta ubicación debe coincidir con la especificada en la sección __CONFIGURACIÓN__.

    * **Nombre base del clúster**: este valor se utiliza como nombre base para los clústeres Storm y Kafka. Por ejemplo, si especifica **hdi**, creará un clúster Storm llamado **storm-hdi** y un clúster Kafka llamado **kafka-hdi**.
   
    * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Storm y Kafka.
   
    * **Contraseña de inicio de sesión de clúster**: contraseña del usuario administrador para los clústeres Storm y Kafka.
    
    * **Nombre de usuario SSH**: usuario SSH que se va a crear para los clústeres Storm y Kafka.
    
    * **Contraseña SSH**: contraseña del usuario SSH para los clústeres Storm y Kafka.
    
    * **Ubicación**: región en la que se crearán los clústeres.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Una vez creados los recursos, se le redirigirá a una hoja del grupo de recursos que contiene los clústeres y el panel web.

![Hoja de grupo de recursos de la red virtual y clústeres](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **storm-BASENAME** y **kafka-BASENAME**, donde BASENAME es el nombre que indicó en la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres.

## <a name="get-the-code"></a>Obtención del código

El código del ejemplo que se describe en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

## <a name="understanding-the-code"></a>Descripción del código

Este proyecto contiene dos topologías:

* **KafkaWriter**: definida por el archivo **writer.yaml**, esta topología escribe frases aleatorias en Kafka con el KafkaBolt proporcionado con Apache Storm.
  
    Esta topología usa un componente **SentenceSpout** personalizado para generar frases aleatorias.

* **KafkaReader**: definida por el archivo **reader.yaml**, esta topología lee los datos de Kafka con el KafkaSpout proporcionado con Apache Storm y, después, registra los datos en stdout.
  
    Esta topología usa un componente **PrinterBolt** personalizado componentes para registrar los datos leídos de Kafka.

### <a name="flux"></a>Flux

Las topologías se definen mediante [Flux](https://storm.apache.org/releases/1.0.1/flux.html). Flux se incorporó por primera vez a Storm 0.10.x y permite separar la configuración de la topología del código. Para las topologías que usan el entorno Flux, la topología se define en un archivo YAML. El archivo YAML puede incluirse como parte de la topología o se puede especificar cuando la topología se envía al servidor de Storm. Flux también admite la sustitución de variables en tiempo de ejecución, que se usa en este ejemplo.

Ambas topologías esperan las siguientes variables de entorno:

* **KAFKATOPIC**: nombre del tema de Kafka donde las topologías leen y escriben.

* **KAFKABROKERS**: hosts donde se ejecutan los agentes de Kafka. KafkaBolt usa la información de los agentes al escribir en Kafka.

* **KAFKAZKHOSTS**: hosts donde se ejecuta Zookeeper.

En los pasos de este documento se muestra cómo usar estas variables de entorno.

## <a name="create-a-kafka-topic"></a>Creación de un tema de Kafka

1. Conéctese al clúster de Kafka con SSH. Reemplace `USERNAME` por el nombre de usuario SSH que usó al crear el clúster. Reemplace `BASENAME` por el nombre base que se utilizó al crear el clúster.
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    Cuando se le solicite, escriba la contraseña que utilizó al crear los clústeres.
   
    Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes:
   
    * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix, macOS y Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Uso de SSH (PuTTY) con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Desde la conexión SSH al clúster de Kafka, use los comandos siguientes para establecer las variables del nombre del clúster y el inicio de sesión HTTP. Estos valores se usan en otros pasos de esta sección.

  ```bash
  ADMIN='admin' #replace with the name of the admin account for the cluster
  PASSWORD='password' #replace with the password for the admin account
  ```

3. Use los siguientes comandos para instalar la utilidad `jq`, recuperar el nombre del clúster y establecer la variable `KAFKAZKHOSTS`:

  ```bash
  sudo apt -y install jq
  CLUSTERNAME=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name'`
  KAFKAZKHOSTS=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
  ```

    Use el siguiente comando para recuperar el nombre del clúster:

  ```bash
  echo $CLUSTERNAME
  ```

    La salida de este comando es similar al ejemplo siguiente:

  ```bash
  kafka-myhdi
  ```

    Use el siguiente comando para comprobar que se está ejecutando `KAFKAZKHOSTS`:

  ```bash
  echo $KAFKAZKHOSTS
  ```

    La salida de este comando es similar al ejemplo siguiente:

  ```bash
  zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
  ```

    Guarde el nombre del clúster de Kafka y la información del host de Zookeeper, ya que estos valores se usan al iniciar la topología en el clúster de Storm.

    > [!NOTE]
    > Los comandos anteriores usan __http://headnodehost:8080/__, que conecta a Ambari directamente. Si tiene que recuperar esta información desde fuera del clúster, a través de Internet, debe usar __https://kafka-NOMBREBASE.azurehdinsight.net/__ en su lugar.

4. Use el comando siguiente para crear un tema en Kafka:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
  ```

    Este comando se conecta a Zookeeper mediante la información de host almacenada en `$KAFKAZKHOSTS`, y luego crea un tema de Kafka llamado **stormtest**. Para comprobar que el tema se creó, use el siguiente comando para enumerar los temas:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
  ```

    La salida de este comando muestra los temas de Kafka, que contienen el nuevo tema **stormtest**.

Deje la conexión SSH al clúster Kafka activa, porque puede usarla para comprobar que la topología de Storm está escribiendo mensajes en el tema.

## <a name="download-and-compile-the-project"></a>Descarga y compilación del proyecto

1. En el entorno de desarrollo, descargue el proyecto de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra una línea de comandos y cambie los directorios a la ubicación en la que descargó el proyecto.

    Dedique unos momentos a examinar el código y entender cómo funciona el proyecto.

2. Desde el directorio **hdinsight-storm-java-kafka**, use el siguiente comando para compilar el proyecto y crear un paquete de implementación:

  ```bash
  mvn clean package
  ```

    El proceso de empaquetado crea un archivo llamado `KafkaTopology-1.0-SNAPSHOT.jar` en el directorio `target`.

3. Use los siguientes comandos para copiar el paquete en el clúster de Storm en HDInsight. Reemplace **USERNAME** por el nombre de usuario SSH para el clúster. Reemplace **BASENAME** por el nombre base que utilizó al crear el clúster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Cuando se le solicite, escriba la contraseña que utilizó al crear los clústeres.

4. Use el comando siguiente para copiar el archivo `set-env-variables.sh` desde el directorio `scripts` del proyecto al clúster de Storm:

  ```bash
  scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
  ```

    Este script se usa para establecer las variables de entorno que las topologías de Storm usan para comunicarse con el clúster de Kafka.

## <a name="start-the-writer"></a>Inicio del escritor

1. Para conectarse al clúster de Storm mediante SSH, utilice lo siguiente. Reemplace **USERNAME** por el nombre de usuario SSH que usó al crear el clúster. Reemplace **BASENAME** por el nombre base que se utilizó al crear el clúster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Cuando se le solicite, escriba la contraseña que utilizó al crear los clústeres.

    Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes:

    * [Uso de SSH con HDInsight basado en Linux desde Linux, Unix, macOS y Bash en Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Uso de SSH (PuTTY) con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Desde la conexión de SSH al clúster Storm, use los siguientes comandos para ejecutar el script de `set-env-variables.sh`:

  ```bash
  chmod +x set-env-variables.sh
  . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD
  ```

    Reemplace __KAFKACLUSTERNAME__ por el nombre del clúster Kafka. Reemplace __PASSWORD__ por la contraseña de inicio de sesión de administrador del clúster Kafka.

    El script se conecta al clúster Kafka y recupera una lista de agentes de Kafka y hosts de Zookeeper. La información se almacena después en variables de entorno que usan las topologías de Storm.

    La salida del script es similar al ejemplo siguiente:

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Desde la conexión de SSH al clúster Storm, use el siguiente comando para iniciar la topología del escritor:

        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e

    Los parámetros que se usan en este comando son los siguientes:

    * `org.apache.storm.flux.Flux`: use Flux para configurar y ejecutar esta topología.

    * `--remote`: envíe la topología a Nimbus. La topología se distribuye entre los nodos de trabajo del clúster.

    * `-R /writer.yaml`: use el archivo `writer.yaml` para configurar la topología. `-R` indica que este recurso está incluido en el archivo jar. Se encuentra en la raíz de jar, por lo que `/writer.yaml` es la ruta de acceso a él.

    * `-e`: use la sustitución de variables de entorno. Flux recoge los valores de $KAFKABROKERS y $KAFKATOPIC establecidos previamente, y los usa en el archivo reader.yaml archivo en lugar de las entradas `${ENV-KAFKABROKER}` y `${ENV-KAFKATOPIC}`.

5. Una vez que ha iniciado la topología, cambie a la conexión SSH al clúster Kafka y use el siguiente comando para ver los mensajes escritos al tema **stormtest**:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
  ```

    Este comando usa un script incluido con Kafka para supervisar el tema. Tras unos instantes, empezará a devolver frases aleatorias que se han escrito en el tema. La salida es similar a la del ejemplo siguiente:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Use Ctrl+C para detener el script.

## <a name="start-the-reader"></a>Inicio del lector

1. Desde la sesión de SSH al clúster Storm, use el siguiente comando para iniciar la topología del lector:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. Cuando se inicie la topología, abra la interfaz de usuario de Storm. Esta interfaz de usuario web se encuentra en https://storm-BASENAME.azurehdinsight.net/stormui. Reemplace __BASENAME__ por el nombre base que utilizó al crear el clúster. 

    Cuando se le pida, use el nombre de inicio de sesión de administrador (de forma predeterminada, `admin`) y la contraseña que utilizó al crear el clúster. Verá una página web similar a la siguiente:

    ![UI de Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. En la interfaz de usuario de Storm, seleccione el vínculo __kafka-reader__ en la sección __Resumen de la topología__ para mostrar información sobre la topología __kafka-reader__.

    ![Sección Resumen de la topología en la interfaz de usuario web de Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Seleccione el vínculo __logger-bolt__ en la sección __Bolts (All time)__ para mostrar información acerca de las instancias del componente logger-bolt.

    ![Vínculo Logger-bolt en la sección Bolt](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. En la sección __Executors__ (Ejecutores), seleccione un vínculo en la columna __Port__ (Puerto) para mostrar la información de registro de esta instancia del componente.

    ![Vínculo Executors](./media/hdinsight-apache-storm-with-kafka/executors.png)

    El registro contiene un registro de los datos leídos desde el tema de Kafka. La información del registro es similar a la siguiente:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Detención de las topologías

Desde una sesión de SSH al clúster Storm, use los siguientes comandos para detener las topologías de Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como el procedimiento descrito en este documento crea los dos clústeres en el mismo grupo de recursos de Azure, puede eliminar el grupo de recursos de Azure Portal. Se eliminan también todos los recursos creados con el procedimiento descrito en este documento, la red virtual de Azure Virtual Network y la cuenta de almacenamiento que utilizan los clústeres.

## <a name="next-steps"></a>Pasos siguientes

Para ver más ejemplos de topologías que pueden utilizarse con Storm en HDInsight, consulte [Topologías y componentes de ejemplo de Storm para Apache Storm en HDInsight](hdinsight-storm-example-topology.md).

Para más información sobre la implementación y supervisión de topologías en HDInsight basado en Linux, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md).


