---
title: 'Tutorial: Spark Structured Streaming con Kafka: Azure HDInsight | Microsoft Docs'
description: Aprenda a usar el streaming de Apache Spark para obtener datos dentro o fuera de Apache Kafka. En este tutorial, se transmiten datos con Jupyter Notebook de Spark en HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: 8c7c1b37102e94f00ac6077958952eb52b342668
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939266"
---
# <a name="tutorial-use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Tutorial: Uso de Spark Structured Streaming con Kafka en HDInsight

Este tutorial muestra cómo utilizar Spark Structured Streaming para leer y escribir datos con Apache Kafka en Azure HDInsight.

El flujo estructurado de Spark es un motor de procesamiento de flujo basado en Spark SQL. Permite expresar los cálculos de streaming de la misma forma que el cálculo por lotes de los datos estáticos. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Flujos estructurados con Kafka
> * Crear clústeres de Kafka y Spark
> * Cargar el cuaderno en Spark
> * Uso del cuaderno
> * Limpieza de recursos

Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="prerequisites"></a>requisitos previos

* Experiencia en el uso de Jupyter Notebooks con Spark en HDInsight. Para más información, consulte el documento [Ejecución de consultas interactivas en clústeres de Spark en HDInsight](spark/apache-spark-load-data-run-query.md).

* Experiencia con el lenguaje de programación [Scala](https://www.scala-lang.org/). El código utilizado en este tutorial está escrito en Scala.

* Debe estar familiarizado con la creación de temas de Kafka. Para más información, consulte el documento [Inicio de Apache Kafka en HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]
> Los pasos que se describen en este documento necesitan un grupo de recursos de Azure que contiene un clúster Spark de HDInsight y un clúster Kafka de HDInsight. Estos dos clústeres se encuentran en una instancia de Azure Virtual Network, lo que permite al clúster Spark comunicarse directamente con el clúster Kafka.
> 
> Para su comodidad, este documento está vinculado con una plantilla que puede crear todos los recursos de Azure necesarios. 
>
> Para más información sobre cómo usar HDInsight en una red virtual, consulte el documento [Extensión de HDInsight con redes virtuales de Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-kafka"></a>Flujos estructurados con Kafka

Spark Structured Streaming es un motor de procesamiento de flujos basado en Spark SQL. Cuando se usa Spark Structured Streaming, puede escribir consultas de flujos estructurados de la misma forma que las consultas por lotes.

Los siguientes fragmentos de código muestran la lectura desde Kafka y el almacenamiento en un archivo. La primera de ellas es una operación por lotes, mientras que la segunda es una operación de streaming:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

En ambos fragmentos de código, se leen datos desde Kafka y se escriben en un archivo. Las diferencias entre los ejemplos son:

| Batch | Streaming |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

La operación de streaming también utiliza `awaitTermination(30000)`, lo que detiene la secuencia después de 30 000 ms. 

Para usar Structured Streaming con Kafka, el proyecto debe tener una dependencia en el paquete `org.apache.spark : spark-sql-kafka-0-10_2.11`. La versión de este paquete debe coincidir con la versión de Spark en HDInsight. Para Spark 2.2.0 (disponible en HDInsight 3.6), puede encontrar la información de dependencia para diferentes tipos de proyectos en [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Para la instancia de Jupyter Notebook que se proporciona con este tutorial, la celda siguiente carga esta dependencia del paquete:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Todos los elementos que utilicen Kafka deben estar en la misma red virtual de Azure. En este tutorial, los clústeres Kafka y Spark se encuentran en una red virtual de Azure. 

En el diagrama siguiente, se muestra cómo fluye la comunicación entre Spark y Kafka:

![Diagrama de clústeres Spark y Kafka en una red virtual de Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> El servicio Kafka se limita a la comunicación dentro de la red virtual. Se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para crear una instancia de Azure Virtual Network y, posteriormente, crear clústeres de Kafka y Spark dentro de ella, siga estos pasos:

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de Azure Resource Manager se encuentra en **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Esta plantilla crea los siguientes recursos:

    * Un clúster de Kafka en HDInsight 3.6.
    * Un clúster de Spark 2.2.0 en HDInsight 3.6.
    * Una instancia de Azure Virtual Network, que contiene los clústeres de HDInsight.

    > [!IMPORTANT]
    > El cuaderno de flujo estructurado que se utiliza en este tutorial requiere Spark 2.2.0 en HDInsight 3.6. Si usa una versión anterior de Spark en HDInsight, recibirá errores al usar dicho cuaderno.

2. Utilice los datos siguientes para rellenar las entradas de la sección **Plantilla personalizada**:

    | Configuración | Valor |
    | --- | --- |
    | La suscripción | Su suscripción de Azure |
    | Grupos de recursos | El grupo de recursos que contiene los recursos. |
    | Ubicación | La región de Azure en que se crean los recursos. |
    | Spark Cluster Name (Nombre de clúster de Spark) | El nombre del clúster de Spark. Los seis primeros caracteres deben ser distintos del nombre de clúster de Kafka. |
    | Kafka Cluster Name (Nombre de clúster de Kafka) | El nombre del clúster de Kafka. Los seis primeros caracteres deben ser distintos del nombre de clúster de Spark. |
    | Cluster Login User Name (Nombre de usuario de inicio de sesión del clúster) | El nombre del usuario administrador de los clústeres. |
    | Cluster Login Password (Contraseña de inicio de sesión del clúster) | La contraseña del usuario administrador de los clústeres. |
    | SSH User Name (Nombre de usuario de SSH) | El usuario de SSH que se crea para los clústeres. |
    | SSH Password (Contraseña de SSH) | La contraseña del usuario de SSH. |
   
    ![Captura de pantalla de la plantilla personalizada](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. 

> [!NOTE]
> Los clústeres pueden tardar hasta 20 minutos en crearse.

## <a name="upload-the-notebook"></a>Carga del cuaderno

Para cargar el cuaderno del proyecto en el clúster de Spark en HDInsight, siga estos pasos:

1. Descargue el proyecto de [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. En el explorador web, conéctese al cuaderno de Jupyter Notebook en el clúster de Spark. En la siguiente URL, reemplace `CLUSTERNAME` por el nombre del clúster de __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Cuando se lo soliciten, escriba el nombre de inicio de sesión del clúster (administrador) y la contraseña que utilizó cuando creó el clúster.

2. En la parte superior derecha de la página, haga clic en el botón __Cargar__ para cargar el archivo __spark-structured-streaming-kafka.ipynb__ en el clúster. Seleccione __Open__ (Abrir) para iniciar la carga.

    ![Para seleccionar y cargar un cuaderno, utilice el botón de carga.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Seleccione el archivo KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Busque la entrada __spark-structured-streaming-kafka.ipynb__ en la lista de cuadernos y haga clic en el botón __Cargar__ que está junto a ella.

    ![Para cargar el cuaderno, utilice el botón de carga de la entrada KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Uso del cuaderno

Cuando se hayan cargado los archivos, seleccione la entrada __spark-structured-streaming-kafka.ipynb__ para abrir el cuaderno. Para aprender a usar el streaming estructurado de Spark con Kafka en HDInsight, siga las instrucciones del cuaderno.

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos creados por este tutorial puede eliminar el grupo de recursos. Al eliminar el grupo de recursos, también se elimina el clúster de HDInsight asociado y otros recursos asociados al grupo.

Para quitar el grupo de recursos mediante Azure Portal:

1. En Azure Portal, expanda el menú en el lado izquierdo para abrir el menú de servicios y elija __Grupos de recursos__ para mostrar la lista de sus grupos de recursos.
2. Busque el grupo de recursos que desea eliminar y haga clic con el botón derecho en __Más__ (...) en el lado derecho de la lista.
3. Seleccione __Eliminar grupo de recursos__ y confirme la elección.

> [!WARNING]
> La facturación del clúster de HDInsight se inicia una vez creado el clúster y solo se detiene cuando se elimina. Se facturan por minuto realizando una prorrata, por lo que siempre debe eliminar aquellos que ya no se estén utilizando.
> 
> Al eliminar un clúster de Kafka en HDInsight se eliminan todos los datos almacenados en Kafka.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a usar Spark Structured Streaming para escribir y leer datos de Kafka en HDInsight. Utilice el siguiente vínculo para aprender a usar Storm con Kafka.

> [!div class="nextstepaction"]
> [Uso de Apache Storm con Kafka](hdinsight-apache-storm-with-kafka.md)