---
title: Temas de Apache Kafka en Azure HDInsight | Microsoft Docs
description: "Aprenda a usar la característica de creación de reflejo de Apache Kafka para mantener una réplica de un Kafka en un clúster de HDInsight mediante el reflejo de temas en un clúster secundario."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0b8de346d8209dcfd665baf18ce054e5556a883b
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight-preview"></a>Uso de MirrorMaker para replicar temas de Apache Kafka con Kafka en HDInsight (versión preliminar)

Obtenga información sobre cómo usar la característica de creación de reflejo de Apache Kafka para replicar temas a un clúster secundario. La creación de reflejo se puede ejecutar como proceso continuo, o utilizar de forma intermitente como método de migración de datos de un clúster a otro.

En este ejemplo, la creación de reflejo se usa para replicar temas entre dos clústeres de HDInsight. Ambos clústeres están en una red virtual de Azure en la misma región.

> [!WARNING]
> La creación de reflejo no debe considerarse como un medio para conseguir tolerancia a errores. El desplazamiento a los elementos de un tema es diferentes entre los clústeres de origen y de destino, por lo que los clientes no puede usar los dos indistintamente.
>
> Si le preocupa la tolerancia a errores, establezca la replicación para los temas en el clúster. Para más información, consulte [Get started with Kafka on HDInsight](hdinsight-apache-kafka-get-started.md) (Introducción a Kafka en HDInsight).

## <a name="how-kafka-mirroring-works"></a>Funcionamiento de la creación de reflejo de Kafka

La creación de reflejo se realiza mediante la herramienta MirrorMaker (parte de Apache Kafka), que consume registros de temas del clúster de origen y crea una copia local en el clúster de destino. MirrorMaker usa uno o más *consumidores* que leen desde el clúster de origen y un *productor* que escribe en el clúster local (de destino).

En el siguiente diagrama se ilustra el proceso de reflejo:

![Diagrama del proceso de creación de reflejo](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka en HDInsight no proporciona acceso a los servicios de Kafka a través de Internet. Los productores o consumidores de Kafka deben estar en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka de origen y destino se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Kafka de origen y destino en una red virtual de Azure](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

Los clústeres de origen y destino pueden ser diferentes en el número de nodos y las particiones; los desplazamientos dentro de los temas también son diferentes. La creación de reflejos conserva el valor de la clave que se utiliza para crear particiones, por lo que se mantiene el orden de los registros en una base por claves.

### <a name="mirroring-across-network-boundaries"></a>Creación de reflejo en los límites de red

Si tiene que crear un reflejo entre los clústeres Kafka en distintas redes, existen las siguientes consideraciones adicionales:

* **Puertas de enlace**: las redes deben ser capaces de comunicarse en el nivel de TCP/IP.

* **Resolución de nombres**: los clústeres Kafka en cada red deben ser capaces de conectarse entre sí mediante nombres de host. Esto puede requerir un servidor de sistema de nombres de dominio (DNS) en cada red que se configure para reenviar solicitudes a las demás redes.

    Al crear una red virtual de Azure, en lugar de usar el DNS automático proporcionado con la red, debe especificar un servidor DNS personalizado y la dirección IP para el servidor. Una vez creada la red virtual, debe crear una máquina virtual de Azure que use esa dirección IP, y en ella instalar y configurar el software DNS.

    > [!WARNING]
    > Cree y configure el servidor DNS personalizado antes de instalar HDInsight en la red virtual. No es necesaria ninguna configuración adicional para que HDInsight use el servidor DNS configurado para la red virtual.

Para más información sobre cómo conectar dos redes virtuales de Azure, consulte [Configuración de una conexión de red virtual a red virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Creación de clústeres Kafka

Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka, resulta más sencillo con una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y dos clústeres Kafka en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    La plantilla de Azure Resource Manager se encuentra en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.json**.

2. Utilice los datos siguientes para rellenar las entradas de la hoja **Implementación personalizada**:
    
    ![Implementación personalizada de HDInsight](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **Grupo de recursos**: cree un nuevo grupo de recursos o seleccione uno existente. Este grupo contiene el clúster de HDInsight.

    * **Ubicación**: seleccione una ubicación geográfica próxima a usted.
     
    * **Nombre base del clúster**: este valor se utiliza como nombre base en los clústeres Kafka. Por ejemplo, si se especifica **hdi**, se crean clústeres denominados **source-hdi** y **dest-hdi**.

    * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Kafka de origen y destino.

    * **Contraseña de inicio de sesión del clúster**: contraseña del administrador de los clústeres Kafka de origen y destino.

    * **Nombre de usuario de SSH**: usuario de SSH para crear los clústeres Kafka de origen y destino.

    * **Contraseña SSH**: contraseña del usuario de SSH para los clústeres Kafka de origen y destino.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Una vez creados los recursos, se le redirigirá a una hoja del grupo de recursos que contiene los clústeres y el panel web.

![Hoja de grupo de recursos de la red virtual y clústeres](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **source-BASENAME** y **dest-BASENAME**, donde BASENAME es el nombre proporcionado a la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres.

## <a name="create-topics"></a>Creación de temas

1. Conéctese al clúster **de origen**con SSH:
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    Reemplace **sshuser** por el nombre de usuario de SSH que usó al crear el clúster. Reemplace **BASENAME** por el nombre base que se utilizó al crear el clúster.
   
    Para obtener más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use el siguiente comando para encontrar los hosts Zookeeper, establezca la variable `SOURCE_ZKHOSTS` y cree varios temas nuevos denominados `testtopic`:
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Use el siguiente comando para comprobar que se creó el tema:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```
   
    La respuesta contiene `testtopic`.

4. Use lo siguiente para ver la información de host de Zookeeper para este clúster (de **origen**):
   
    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Esto devuelve información similar al texto siguiente:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Guarde esta información. Se usa en la siguiente sección.

## <a name="configure-mirroring"></a>Configuración del reflejo

1. Conéctese al clúster de **destino** mediante una sesión SSH diferente:
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    Reemplace **sshuser** por el nombre de usuario de SSH que usó al crear el clúster. Reemplace **BASENAME** por el nombre base que se utilizó al crear el clúster.
   
    Para obtener más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use el siguiente comando para crear un archivo `consumer.properties` que describa cómo comunicarse con el clúster de **origen**:
   
    ```bash
    nano consumer.properties
    ```
   
    Use el texto siguiente como contenido del archivo `consumer.properties`:
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    Reemplace **SOURCE_ZKHOSTS** con la información de hosts de Zookeeper del clúster de **origen**.
   
    Este archivo describe la información de consumidor que se utilizará al leer desde clúster Kafka de origen. Para más información sobre la configuración de los consumidores, consulte [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configuraciones de consumidor) en kafka.apache.org.
   
    Para guardar el archivo, presione **Ctr+X**, luego, **Y** y **Entrar**.

3. Antes de configurar el productor que se comunica con el clúster de destino, debe encontrar el agente de hosts para el clúster de **destino**. Use los siguientes comandos para recuperar esta información:
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    Estos comandos devuelven información similar a la siguiente:
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Use lo siguiente para crear un archivo `producer.properties` que describa cómo comunicarse con el clúster de **destino**:

    ```bash
    nano producer.properties
    ```

    Use el texto siguiente como contenido del archivo `producer.properties`:
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    Reemplace **DEST_BROKERS** con la información de agente del paso anterior. 
   
    Para más información sobre la configuración del productor, consulte [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configuraciones de productor) en kafka.apache.org.

## <a name="start-mirrormaker"></a>Inicio de MirrorMaker

1. En la conexión SSH al clúster de **destino**, use el siguiente comando para iniciar el proceso de MirrorMaker:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    Parámetros que se utilizan en este ejemplo:
   
    * **--consumer.config**: especifica el archivo que contiene las propiedades de consumidor. Estas propiedades se utilizan para crear un consumidor que se lea desde el clúster Kafka de *origen*.

    * **--producer.config**: especifica el archivo que contiene las propiedades de productor. Estas propiedades se utilizan para crear un productor que escriba en el clúster Kafka de *destino*.

    * **--whitelist**: lista de temas que MirrorMaker replica desde el clúster de origen al de destino.
    
    * **--num.streams**: número de subprocesos de consumidor para crear.
     
    Al inicio, MirrorMaker devuelve información similar al texto siguiente:
        
    ```
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. En la conexión SSH al clúster de **origen**, use el siguiente comando para iniciar un productor y enviar mensajes al tema:
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 Cuando llegue a una línea en blanco con un cursor, escriba algunos mensajes de texto. Estos se envían al tema en el clúster de **origen**. Cuando haya terminado, use **Ctrl + C** para finalizar el proceso de productor.

3. En la conexión SSH al clúster de **destino**, use **Ctrl + C** para iniciar el proceso de MirrorMaker. A continuación, use los siguientes comandos para comprobar que el tema `testtopic` se creó y que los datos de dicho tema se han replicado a este reflejo:
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  La lista de temas ahora incluye `testtopic`, que se crea cuando MirrorMaster refleja el tema desde el clúster de origen al destino. Los mensajes que se recuperan desde el tema son los mismos que se introdujeron en el clúster de origen.

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como el procedimiento descrito en este documento crea los dos clústeres en el mismo grupo de recursos de Azure, puede eliminar el grupo de recursos de Azure Portal. Al eliminar el grupo de recursos se eliminan también todos los recursos creados con el procedimiento descrito en este documento, Azure Virtual Network y la cuenta de almacenamiento que utilizan los clústeres.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a utilizar MirrorMaker para crear una réplica de un clúster de Kafka. Utilice los vínculos siguientes para conocer otras formas de trabajar con Kafka:

* [Documentación de Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) en cwiki.apache.org.
* [Introducción a Apache Kafka en HDInsight](hdinsight-apache-kafka-get-started.md)
* [Uso de Apache Spark con Kafka en HDInsight](hdinsight-apache-spark-with-kafka.md)
* [Uso de Apache Kafka con Storm en HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Conexión a Kafka a través de una red virtual de Azure](hdinsight-apache-kafka-connect-vpn-gateway.md)

