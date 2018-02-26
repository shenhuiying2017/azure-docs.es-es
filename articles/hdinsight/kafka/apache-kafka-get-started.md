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
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: e00ab06a26d60dd5beca11362df58f35812491d9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Inicio de Apache Kafka en HDInsight

Obtenga información acerca de cómo crear y usar un clúster de [Apache Kafka](https://kafka.apache.org) en Azure HDInsight. Kafka es una plataforma de streaming distribuida de código abierto que está disponible con HDInsight. A menudo se usa como agente de mensajes, ya que proporciona una funcionalidad similar a una cola de mensajes de publicación o suscripción. Kafka se utiliza a menudo junto con Apache Spark y Apache Storm para mensajería, seguimiento de actividades, agregación de flujos o transformación de datos.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Creación de un clúster de Kafka

Siga estos pasos para crear un clúster de Kafka en HDInsight:

1. En [Azure Portal](https://portal.azure.com), seleccione **+ Crear un recurso**, **Datos y análisis** y seleccione **HDInsight**.
   
    ![Creación de un clúster de HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. En **Básico**, escriba la siguiente información:

    * **Nombre del clúster**: nombre del clúster de HDInsight. Este nombre debe ser único.
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
    > Los **discos por entrada de nodo de trabajo** configuran la escalabilidad de Kafka en HDInsight. Kafka en HDInsight utiliza el disco local de las máquinas virtuales del clúster. Como Kafka tiene muchas E/S, [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) se utiliza para proporcionar alto rendimiento y mayor espacio de almacenamiento por nodo. El tipo de disco administrado puede ser __Estándar__ (HDD) o __Premium__ (SSD). Los discos Premium se utilizan con máquinas virtuales de las series DS y GS. Todos los otros tipos de máquina virtual usan discos estándar.

8. En __Configuración avanzada__, seleccione __Siguiente__ para continuar.

9. En **Resumen**, revise la configuración para el clúster. Use los vínculos __Edit__ (Editar) para cambiar cualquier configuración incorrecta. Por último, use el botón __Crear__ para crear el clúster.
   
    ![Resumen de configuración del clúster](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="connect-to-the-cluster"></a>Conexión al clúster

> [!IMPORTANT]
> Al realizar los pasos siguientes, debe utilizar un cliente de SSH. Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Para conectarse al clúster mediante SSH, debe proporcionar el nombre de la cuenta de usuario SSH y el nombre del clúster. Sustituya `sshuser` y `clustername` por el nombre de la cuenta y del clúster en el siguiente ejemplo:

```ssh sshuser@clustername-ssh.azurehdinsight.net```

Cuando se le solicite, escriba la contraseña usada para la cuenta SSH.

Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Obtención de la información del host de Zookeeper y del agente

Cuando se trabaja con Kafka, debe conocer los hosts de *Zookeeper* y los hosts de *Broker*. Estos hosts se usan con la API de Kafka y muchas de las utilidades que se incluyen con Kafka.

Use los pasos siguientes para crear variables de entorno que contengan la información de host:

1. Desde una conexión SSH al clúster, use el siguiente comando para instalar la utilidad `jq`. Esta utilidad se emplea para analizar documentos JSON y es útil para recuperar la información de host del agente:
   
    ```bash
    sudo apt -y install jq
    ```

2. Para establecer una variable de entorno en el nombre del clúster, use el comando siguiente:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

3. Para establecer una variable de entorno con la información de host de Zookeeper, use el comando siguiente:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster (administrador).

4. Para comprobar que la variable de entorno se ha establecido correctamente, use el comando siguiente:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Este comando devuelve información similar al texto siguiente:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Para establecer una variable de entorno con la información de host del agente de Kafka, use el comando siguiente:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster (administrador).

6. Para comprobar que la variable de entorno se ha establecido correctamente, use el comando siguiente:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Este comando devuelve información similar al texto siguiente:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
> [!WARNING]
> No confíe en que la información que se devuelve en esta sesión sea siempre precisa. Cuando escala el clúster, se agregan o se quitan nuevos agentes. Si se produce un error y se reemplaza un nodo, el nombre de host del nodo puede cambiar.
>
> Debe recuperar la información de los hosts de Zookeeper y del agente antes de usarla para garantizar que tiene información válida.

## <a name="create-a-topic"></a>de un tema

Kafka almacena los flujos de datos en categorías denominadas *temas*. Desde una conexión SSH a un nodo principal de un clúster, use un script proporcionado con Kafka para crear un tema:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Este comando se conecta a Zookeeper mediante la información de host almacenada en `$KAFKAZKHOSTS`. Y, luego, crea un tema de Kafka llamado **test**. También puede comprobar que se creó el tema mediante el siguiente script para mostrar temas:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

La salida de este comando muestra los temas de Kafka en el clúster.

## <a name="produce-and-consume-records"></a>Generación y consumo de registros

Kafka almacena *registros* en temas. Los registros se generan mediante *productores* y se consumen mediante *consumidores*. Los productores generan registros para los *agentes* de Kafka. Cada nodo de trabajo del clúster de HDInsight es un agente de Kafka.

Use los pasos siguientes para almacenar registros en el tema de prueba que creó anteriormente y luego leerlos mediante un consumidor:

1. Desde la sesión SSH, use un script que se proporciona con Kafka para escribir registros en el tema:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Después de este comando, llega a una línea vacía.

2. Escriba un mensaje de texto en la línea vacía y presione ENTRAR. Escriba algunos mensajes de esta forma y, a continuación, use **Ctrl + C** para volver al símbolo del sistema normal. Cada línea se envía como un registro independiente al tema de Kafka.

3. Use un script proporcionado con Kafka para leer registros del tema:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Este comando recupera los registros del tema y los muestra. Con `--from-beginning` se indica al consumidor que comience desde el principio del flujo, de modo que se recuperan todos los registros.

    > [!NOTE]
    > Si está utilizando una versión anterior de Kafka, reemplace `--bootstrap-server $KAFKABROKERS` por `--zookeeper $KAFKAZKHOSTS`.

4. Use __Ctrl + C__ para detener el consumidor.

También puede crear mediante programación los productores y consumidores. Para obtener un ejemplo del uso de esta API, consulte el documento [Producer y Consumer API de Kafka](apache-kafka-producer-consumer-api.md).

## <a name="data-high-availability"></a>Alta disponibilidad de los datos

Cada región de Azure (ubicación) proporciona _dominios de error_. Un dominio de error es una agrupación lógica del hardware subyacente en un centro de datos de Azure. Todos los dominios de error comparten la fuente de energía y el conmutador de red. Las máquinas virtuales y los discos administrados que implementan los nodos en un clúster de HDInsight se distribuyen por estos dominios de error. Esta arquitectura limita el impacto potencial de errores del hardware físico.

Para información sobre el número de dominios de error de una región, consulte el documento sobre la [disponibilidad de las máquinas virtuales Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> Si es posible, use una región de Azure que contenga tres dominios de error y cree temas con un factor de replicación de 3.

Si utiliza una región que contiene solo dos dominios de error, use un factor de replicación de 4 para distribuir las réplicas uniformemente entre los dos dominios de error.

### <a name="kafka-and-fault-domains"></a>Kafka y los dominios de error

Kafka no es compatible con dominios de error. Al crear réplicas de la partición de temas, puede que estas no se distribuyan correctamente con alta disponibilidad. Para garantizar la alta disponibilidad, use la [herramienta de reequilibrado de particiones de Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta herramienta se debe ejecutar desde una sesión de SSH en el nodo principal del clúster de Kafka.

Para garantizar la máxima disponibilidad de los datos de Kafka, es preciso reequilibrar las réplicas de las particiones del tema cuando:

* Cree un nuevo tema o una partición

* Escale verticalmente un clúster

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>pasos siguientes

En este documento, ha aprendido los conceptos básicos de trabajar con Apache Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Análisis de los registros de Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Kafka](apache-kafka-mirroring.md)
* [Uso de Producer y Consumer API de Kafka con HDInsight](apache-kafka-producer-consumer-api.md)
* [Streams API de Kafka con HDInsight](apache-kafka-streams-api.md)
* [Ejemplo de streaming de Apache Spark (DStream) con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Uso del flujo estructurado de Spark con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Uso de los flujos estructurados de Apache Spark para mover datos de Kafka para HDInsight a Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
* [Uso de Kafka con Azure Container Service](apache-kafka-azure-container-services.md)
* [Uso de Kafka con Azure Function Apps](apache-kafka-azure-functions.md)
