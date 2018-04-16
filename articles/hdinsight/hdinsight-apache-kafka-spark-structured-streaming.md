---
title: 'Flujo estructurado de Apache Spark con Kafka: Azure HDInsight | Microsoft Docs'
description: Aprenda a usar el streaming de Apache Spark (DStream) para obtener datos dentro o fuera de Apache Kafka. En este ejemplo, se transmiten datos con Jupyter Notebook de Spark en HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Uso del flujo estructurado de Spark con Kafka en HDInsight

Obtenga información sobre cómo usar el flujo estructurado de Spark para leer datos de Apache Kafka en Azure HDInsight.

El flujo estructurado de Spark es un motor de procesamiento de flujo basado en Spark SQL. Permite expresar los cálculos de streaming de la misma forma que el cálculo por lotes de los datos estáticos. Para obtener más información sobre el flujo estructurado, consulte el artículo [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) (Guía de programación de flujo estructurado [alfa]) en Apache.org.

> [!IMPORTANT]
> En este ejemplo, se utiliza Spark 2.2 en HDInsight 3.6.
>
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Spark de HDInsight y un clúster Kafka de HDInsight. Estos dos clústeres se encuentran en una instancia de Azure Virtual Network, lo que permite al clúster Spark comunicarse directamente con el clúster Kafka.
>
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Todos los elementos que utilicen Kafka deben estar en la misma red virtual de Azure. En este tutorial, los clústeres Kafka y Spark se encuentran en una red virtual de Azure. 

En el diagrama siguiente, se muestra cómo fluye la comunicación entre Spark y Kafka:

![Diagrama de clústeres Spark y Kafka en una red virtual de Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> El servicio Kafka se limita a la comunicación dentro de la red virtual. Se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para su comodidad, los siguientes pasos utilizan una plantilla de Azure Resource Manager para crear clústeres Kafka y Spark en una red virtual.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de Azure Resource Manager se encuentra en **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Esta plantilla crea los siguientes recursos:

    * Un clúster de Kafka en HDInsight 3.6.
    * Un clúster de Spark 2.2.0 en HDInsight 3.6.
    * Una instancia de Azure Virtual Network, que contiene los clústeres de HDInsight.

    > [!IMPORTANT]
    > El cuaderno de flujo estructurado que se utiliza en este ejemplo requiere Spark en HDInsight 3.6. Si usa una versión anterior de Spark en HDInsight, recibirá errores al usar dicho cuaderno.

2. Utilice los datos siguientes para rellenar las entradas de la sección **Plantilla personalizada**:

    | Configuración | Valor |
    | --- | --- |
    | La suscripción | Su suscripción de Azure |
    | Grupos de recursos | El grupo de recursos que contiene los recursos. |
    | Ubicación | La región de Azure en que se crean los recursos. |
    | Spark Cluster Name (Nombre de clúster de Spark) | El nombre del clúster de Spark. |
    | Kafka Cluster Name (Nombre de clúster de Kafka) | El nombre del clúster de Kafka. |
    | Cluster Login User Name (Nombre de usuario de inicio de sesión del clúster) | El nombre del usuario administrador de los clústeres. |
    | Cluster Login Password (Contraseña de inicio de sesión del clúster) | La contraseña del usuario administrador de los clústeres. |
    | SSH User Name (Nombre de usuario de SSH) | El usuario de SSH que se crea para los clústeres. |
    | SSH Password (Contraseña de SSH) | La contraseña del usuario de SSH. |
   
    ![Captura de pantalla de la plantilla personalizada](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. 

> [!NOTE]
> Los clústeres pueden tardar hasta 20 minutos en crearse.

## <a name="get-the-notebook"></a>Obtención del cuaderno

El código para el ejemplo descrito en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Carga de los cuadernos

Para cargar el cuaderno del proyecto en el clúster de Spark en HDInsight, siga estos pasos:

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

Ahora que ha aprendido a usar el flujo estructurado de Spark, consulte los siguientes documentos para obtener más información sobre cómo trabajar con Spark y Kafka:

* [Uso del flujo estructurado de Spark (DStream) con Kafka](hdinsight-apache-spark-with-kafka.md)
* [Introducción a Jupyter Notebook y Spark en HDInsight](spark/apache-spark-jupyter-spark-sql.md)