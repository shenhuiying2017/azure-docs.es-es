---
title: Uso de Apache Spark con Kafka en HDInsight | Microsoft Docs
description: "Aprenda a usar Spark en HDInsight para leer y escribir datos en un clúster Kafka de HDInsight. En este ejemplo, se utiliza Scala en un cuaderno de Jupyter Notebook para escribir datos aleatorios en Kafka en HDInsight y leerlos después con el streaming de Spark."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 50a9c3929a4d3194c3786a3d4f6cdd1b73fb5867
ms.openlocfilehash: 0cb9d65e33bf6f6b67e2a74b7e4634aba3f2359b

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>Uso de Apache Spark con Kafka (versión preliminar) en HDInsight

Apache Spark puede utilizarse para trasmitir datos dentro y fuera de Apache Kafka. En este documento, aprenderá a transmitir datos dentro y fuera de Kafka utilizando un cuaderno de Jupyter Notebook desde Spark en HDInsight.

> [!NOTE]
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Spark de HDInsight y un clúster Kafka de HDInsight. Estos dos clústeres se encuentran en una red virtual de Azure, lo que permite al clúster Spark comunicarse directamente con el clúster Kafka.
> 
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure

* Un cliente SSH (se necesitan los comandos `ssh` y `scp`). Para más información sobre el uso de SSH con HDInsight, consulte los siguientes documentos:

    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Uso de SSH con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* [cURL](https://curl.haxx.se/): utilidad multiplataforma para crear solicitudes HTTP.

* [cURL](https://stedolan.github.io/jq/): utilidad multiplataforma para analizar documentos JSON.

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Cualquier comunicación con Kafka debe realizarse en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka y Spark se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Spark y Kafka en una red virtual de Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Aunque Kafka tiene limitadas la comunicación en la red virtual, se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka y Spark, resulta más sencillo utilizar una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y los clústeres Kafka y Spark en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de Azure Resource Manager se encuentra en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json**.

2. Utilice los datos siguientes para rellenar las entradas de la hoja **Implementación personalizada**:
   
    ![Implementación personalizada de HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: cree un nuevo grupo de recursos o seleccione uno existente. Este grupo contiene el clúster de HDInsight.

    * **Ubicación**: seleccione una ubicación geográfica próxima a usted. Esta ubicación debe coincidir con la especificada en la sección __CONFIGURACIÓN__.

    * **Base Cluster Name** (Nombre base del clúster): este valor se utiliza como nombre base en los clústeres Spark y Kafka. Por ejemplo, si especifica **hdi**, creará un clúster Spark denominado spark-hdi__ y un clúster Kafka denominado **kafka-hdi**.

    * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Spark y Kafka.

    * **Contraseña de inicio de sesión de clúster**: contraseña del usuario administrador para los clústeres Spark y Kafka.

    * **Nombre de usuario SSH**: usuario de SSH que se va a crear para los clústeres Spark y Kafka.

    * **Contraseña SSH**: contraseña del usuario de SSH para los clústeres Spark y Kafka.

    * **Ubicación**: región en la que se crearán los clústeres.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Una vez creados los recursos, se le redirigirá a una hoja del grupo de recursos que contiene los clústeres y el panel web.

![Hoja de grupo de recursos de la red virtual y clústeres](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **spark-BASENAME** y **kafka-BASENAME**, donde BASENAME es el nombre que indicó en la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres.

## <a name="get-the-code"></a>Obtención del código

El código del ejemplo que se describe en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="understand-the-code"></a>Comprensión del código

En este ejemplo, se utiliza una aplicación de Scala en un cuaderno de Jupyter Notebook. El código del cuaderno se basa en los siguientes elementos de datos:

* __Agentes de Kafka__: el proceso del agente se ejecuta en cada nodo de trabajo del clúster Kafka. El componente de producción, que es el encargado de escribir los datos en Kafka, necesita la lista de agentes.

* __Hosts de Zookeeper__: los hosts de Zookeeper para el clúster Kafka se utilizan cuando se consumen (se leen) los datos de Kafka.

* __Nombre del tema__: nombre del tema en el que se escriben y se leen los datos. En este ejemplo, el nombre de tema esperado es `sparktest`.

Consulte la sección [Información sobre los hosts de Kafka](#kafkahosts) para más información sobre el agente de Kafka y los hosts de Zookeeper.

El código del cuaderno ejecuta las siguientes tareas:

* Crea un consumidor que lee los datos de un tema de Kafka denominado `sparktest`, cuenta todas las palabras de los datos y almacena las palabras y el recuento en una tabla temporal llamada `wordcounts`.

* Crea un productor que escribe oraciones aleatorias en el tema de Kafka denominado `sparktest`.

* Selecciona los datos en la tabla `wordcounts` para mostrar los recuentos.

Cada celda del proyecto contiene comentarios o una sección de texto donde se explica qué hace el código.

##<a name="a-idkafkahostsakafka-host-information"></a><a id="kafkahosts"></a>Información sobre los hosts de Kafka

Lo primero que debe hacer cuando cree una aplicación que funcione con Kafka en HDInsight es obtener información del agente de Kafka y los hosts de Zookeeper del clúster Kafka. Esta información la utilizarán las aplicaciones cliente para comunicarse con Kafka.

> [!NOTE]
> No se puede acceder al agente de Kafka y a los hosts de Zookeeper directamente desde Internet. Cualquier aplicación que utilice Kafka deberá ejecutarse en el clúster Kafka o en la misma red virtual de Azure que el clúster. En este caso, el ejemplo se ejecuta en un clúster Spark de HDInsight que se encuentra en la misma red virtual.

En el entorno de desarrollo, utilice los comandos siguientes para recuperar la información del agente y Zookeeper. Sustituya __PASSWORD__ por la contraseña de inicio de sesión (administrador) que utilizó al crear el clúster. Reemplace __BASENAME__ por el nombre base que utilizó al crear el clúster.

* Para obtener información sobre el __agente de Kafka__:

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > Si utiliza este comando en Windows PowerShell, puede aparecer un error relacionado con las comillas del shell. En ese caso, utilice el siguiente comando: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'`

* Para obtener los datos del __host de Zookeeper__:

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > Si utiliza este comando en Windows PowerShell, puede aparecer un error relacionado con las comillas del shell. En ese caso, utilice el siguiente comando: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

Los dos comandos devuelven información similar a la siguiente:

* __Agentes de Kafka__: `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Hosts de Zookeeper__: `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> Guarde esta información, ya que la utilizará en otros pasos que se describen en este documento.

## <a name="use-the-jupyter-notebook"></a>Uso de Jupyter Notebook

Para utilizar el cuaderno de ejemplo de Jupyter Notebook, debe cargarlo en el servidor de Jupyter Notebook del clúster Spark. Siga estos pasos para cargar el cuaderno:

1. En el explorador web, utilice la siguiente dirección URL para conectarse al servidor de Jupyter Notebook del clúster Spark. Sustituya __BASENAME__ por el nombre base que utilizó al crear el clúster.

        https://spark-BASENAME.azurehdinsight.net/jupyter

    Cuando se lo soliciten, escriba el nombre de inicio de sesión del clúster (administrador) y la contraseña que utilizó cuando creó el clúster.

2. En la parte superior derecha de la página, utilice el botón __Upload__ (Cargar) para cargar el archivo `KafkaStreaming.ipynb`. En el cuadro de diálogo del explorador de archivos, seleccione el archivo y haga clic en __Abrir__. 

    ![Para seleccionar y cargar un cuaderno, utilice el botón de carga.](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![Seleccione el archivo KafkaStreaming.ipynb.](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Busque la entrada __KafkaStreaming.ipynb__ en la lista de cuadernos y seleccione el botón __Upload__ (Cargar) que está junto a ella.

    ![Utilice el botón Upload (Cargar) que está situado junto a la entrada KafkaStreaming.ipynb para cargar el archivo en el servidor de cuadernos.](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. Una vez cargado el archivo, seleccione la entrada __KafkaStreaming.ipynb__ para abrir el cuaderno. Para completar el ejemplo, siga las instrucciones del cuaderno.

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como el procedimiento descrito en este documento crea los dos clústeres en el mismo grupo de recursos de Azure, puede eliminar el grupo de recursos de Azure Portal. Al eliminar el grupo, se eliminan también todos los recursos creados con el procedimiento descrito en este documento, la red virtual de Azure Virtual Network y la cuenta de almacenamiento que utilizan los clústeres.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a usar Spark para leer y escribir en Kafka. Utilice los vínculos siguientes para conocer otras formas de trabajar con Kafka:

* [Introducción a Apache Kafka en HDInsight](hdinsight-apache-kafka-get-started.md)
* [Uso de MirrorMaker para crear una réplica de Kafka en HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Uso de Apache Kafka con Storm en HDInsight](hdinsight-apache-storm-with-kafka.md)




<!--HONumber=Feb17_HO2-->


