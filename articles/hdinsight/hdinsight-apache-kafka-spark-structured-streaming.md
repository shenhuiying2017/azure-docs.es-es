---
title: 'Flujo estructurado de Apache Spark con Kafka: Azure HDInsight | Microsoft Docs'
description: Aprenda a usar el streaming de Apache Spark (DStream) para obtener datos dentro o fuera de Apache Kafka. En este ejemplo, se transmiten datos con Jupyter Notebook de Spark en HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 565f840e0ac7ec1a68361ac1613da5d42459c979
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>Uso del flujo estructurado de Spark con Kafka (versión preliminar) en HDInsight

Obtenga información sobre cómo usar el flujo estructurado de Spark para leer datos de Apache Kafka en Azure HDInsight.

El flujo estructurado de Spark es un motor de procesamiento de flujo basado en Spark SQL. Permite expresar los cálculos de streaming de la misma forma que el cálculo por lotes de los datos estáticos. Para obtener más información sobre el flujo estructurado, consulte el artículo [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) (Guía de programación de flujo estructurado [alfa]) en Apache.org.

> [!IMPORTANT]
> En este ejemplo se utiliza Spark 2.1 en HDInsight 3.6. La versión de la funcionalidad de flujo estructurado se considera __alfa__ en Spark 2.1.
>
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Spark de HDInsight y un clúster Kafka de HDInsight. Estos dos clústeres se encuentran en una red virtual de Azure, lo que permite al clúster Spark comunicarse directamente con el clúster Kafka.
>
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Cualquier comunicación con Kafka debe realizarse en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka y Spark se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Spark y Kafka en una red virtual de Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> El servicio Kafka se limita a la comunicación dentro de la red virtual. Se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka y Spark, resulta más sencillo utilizar una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y los clústeres Kafka y Spark en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de Azure Resource Manager se encuentra en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    Esta plantilla crea los siguientes recursos:

    * Un clúster de Kafka en HDInsight 3.5
    * Un clúster de Spark en HDInsight 3.6
    * Una red virtual de Azure, que contiene los clústeres de HDInsight.

    > [!IMPORTANT]
    > El cuaderno de flujo estructurado que se utiliza en este ejemplo requiere Spark en HDInsight 3.6. Si usa una versión anterior de Spark en HDInsight, recibirá errores al usar dicho cuaderno.

2. Utilice los datos siguientes para rellenar las entradas de la sección **Implementación personalizada**:
   
    ![Implementación personalizada de HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: cree un nuevo grupo de recursos o seleccione uno existente. Este grupo contiene el clúster de HDInsight.

    * **Ubicación**: seleccione una ubicación geográfica próxima a usted.

    * **Base Cluster Name** (Nombre base del clúster): este valor se utiliza como nombre base en los clústeres Spark y Kafka. Por ejemplo, si especifica **hdi**, creará un clúster Spark denominado spark-hdi__ y un clúster Kafka denominado **kafka-hdi**.

    * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Spark y Kafka.

    * **Contraseña de inicio de sesión de clúster**: contraseña del usuario administrador para los clústeres Spark y Kafka.

    * **Nombre de usuario SSH**: usuario de SSH que se va a crear para los clústeres Spark y Kafka.

    * **Contraseña SSH**: contraseña del usuario de SSH para los clústeres Spark y Kafka.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Una vez que se han creado los recursos, aparece una página de resumen.

![Información sobre un grupo de recursos de la red virtual y los clústeres](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **spark-BASENAME** y **kafka-BASENAME**, donde BASENAME es el nombre que indicó en la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres.

## <a name="get-the-kafka-brokers"></a>Obtención de los agentes de Kafka

En este ejemplo, el código se conecta a los hosts de los agentes de Kafka del clúster de Kafka. Para buscar las direcciones de los dos hosts de los agentes de Kafka, use el siguiente ejemplo de PowerShell o Bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Cuando se lo pidan, escriba la contraseña de la cuenta de inicio sesión del clúster (admin).

> [!NOTE]
> En este ejemplo, se espera que `$CLUSTERNAME` contenga el nombre del clúster de Kafka.
>
> En este ejemplo se utiliza la utilidad [jq](https://stedolan.github.io/jq/) para analizar datos a partir del documento JSON.

La salida será similar al siguiente texto:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Guarde esta información, ya que se utilizará en las siguientes secciones de este documento.

## <a name="get-the-notebooks"></a>Obtención de los cuadernos

El código del ejemplo que se describe en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Carga de los cuadernos

Siga estos pasos para cargar los cuadernos del proyecto en el clúster de Spark en HDInsight:

1. En el explorador web, conéctese al cuaderno de Jupyter Notebook en el clúster de Spark. En la siguiente URL, reemplace `CLUSTERNAME` por el nombre del clúster de Kafka:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Cuando se lo soliciten, escriba el nombre de inicio de sesión del clúster (administrador) y la contraseña que utilizó cuando creó el clúster.

2. En la parte superior derecha de la página, utilice el botón __Upload__ (Cargar) para cargar el archivo __Stream-Tweets-To_Kafka.ipynb__ en el clúster. Seleccione __Open__ (Abrir) para iniciar la carga.

    ![Para seleccionar y cargar un cuaderno, utilice el botón de carga.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Seleccione el archivo KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Busque la entrada __Stream-Tweets-To_Kafka.ipynb__ en la lista de cuadernos y seleccione el botón __Upload__ (Cargar) que está junto a ella.

    ![Para cargar el cuaderno, utilice el botón de carga de la entrada KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. Repita los pasos del 1 a 3 para cargar el cuaderno __Spark-Structured-Streaming-From-Kafka.ipynb__.

## <a name="load-tweets-into-kafka"></a>Carga de tweets en Kafka

Cuando se hayan cargado los archivos, seleccione la entrada __Stream-Tweets-To_Kafka.ipynb__ para abrir el cuaderno. Siga los pasos descritos en el cuaderno para cargar tweets en Kafka.

## <a name="process-tweets-using-spark-structured-streaming"></a>Procesamiento de tweets mediante el flujo estructurado de Spark

En la página principal de Jupyter Notebook, seleccione la entrada __Spark-Structured-Streaming-From-Kafka.ipynb__. Siga los pasos descritos en el cuaderno para cargar tweets de Kafka mediante el flujo estructurado de Spark.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar el flujo estructurado de Spark, consulte los siguientes documentos para obtener más información sobre cómo trabajar con Spark y Kafka:

* [Uso del flujo estructurado de Spark (DStream) con Kafka](hdinsight-apache-spark-with-kafka.md)
* [Introducción a Jupyter Notebook y Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md)