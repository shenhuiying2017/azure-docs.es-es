---
title: 'Tutorial: Procesamiento de datos desde Event Hubs con Apache Spark en Azure HDInsight | Microsoft Docs'
description: Conecte Apache Spark en Azure HDInsight a Azure Event Hubs y procese los datos de streaming.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781414"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Tutorial: Procesamiento de tweets con Azure Event Hubs y Spark en HDInsight

En este tutorial, aprenderá a crear una aplicación de streaming de Apache Spark para enviar tweets a un centro de eventos de Azure y a crear otra aplicación para leerlos desde dicho centro. Para obtener una explicación detallada del streaming de Spark consulte la [información general sobre el streaming de Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight ofrece las mismas funciones de streaming para un clúster de Spark en Azure.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Envío de mensajes a un centro de eventos de Azure
> * Lectura de mensajes de un centro de eventos de Azure

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

* **Complete el artículo [Tutorial: Carga de datos y ejecución de consultas en un clúster de Apache Spark en Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Crear una aplicación de Twitter

Para recibir un flujo de tweets, cree una aplicación en Twitter. Siga las instrucciones para crear una aplicación de Twitter y anote los valores que necesita para finalizar este tutorial.

1. Vaya a la [página de administración de aplicaciones de Twitter](https://apps.twitter.com/).
2. Seleccione **Crear nueva aplicación**.
3. Proporcione los valores siguientes:

    - Nombre: proporcione el nombre de la aplicación. El valor utilizado para este tutorial es **HDISparkStreamApp0423**. Este nombre debe ser único.
    - Descripción: proporcione una descripción breve de la aplicación. El valor utilizado para este tutorial es **una aplicación de streaming sencilla de HDInsight Spark**.
    - Sitio web: indique el sitio web de la aplicación. No es necesario que sea un sitio web válido.  El valor utilizado para este tutorial es **http://www.contoso.com**.
    - Dirección URL de devolución de llamada: puede dejarla en blanco.

4. Seleccione **Yes, I have read and agree to the Twitter Developer Agreement** (Sí, he leído y acepto el acuerdo para desarrolladores de Twitter) y luego seleccione **Create your Twitter application** (Crear la aplicación de Twitter).
5. Seleccione la pestaña **Claves y tokens de acceso** .
6. Seleccione **Create my access token** (Crear mi token de acceso) al final de la página.
7. Anote los valores siguientes de la página.  Necesitará estos valores más adelante en el tutorial:

    - **Clave de consumidor (clave de API)**    
    - **Secreto de consumidor (secreto de API)**  
    - **Token de acceso**
    - **Secreto de token de acceso**   

## <a name="create-an-azure-event-hub"></a>Crear un centro de eventos de Azure

Use este centro de eventos para almacenar tweets.

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
2. Seleccione **Crear un recurso** en la parte superior izquierda de la pantalla.
3. Haga clic en **Internet de las cosas** y en **Event Hubs**.

    ![Creación de un centro de eventos para un ejemplo de streaming de Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Create event hub for Spark streaming example")
4. Escriba los siguientes valores para el nuevo espacio de nombres del centro de eventos:

    - **Nombre**: especifique un nombre para el centro de eventos.  El valor utilizado para este tutorial es **myeventhubns20180403**.
    - **Franja de precios**: seleccione **Estándar**.
    - **Grupo de recursos**: tiene la opción de crear un grupo de recursos o de seleccionar uno existente para el clúster de Spark. 
    - **Ubicación**: para reducir la latencia y los costos, debe seleccionar la misma **ubicación** que la del clúster Apache Spark en HDInsight.

    ![Aportación de un nombre de centro de evento para el ejemplo de streaming de Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Provide an event hub name for Spark streaming example")
5. Seleccione **Crear** para crear el espacio de nombres.

6. Abra el espacio de nombres del centro de eventos mediante las siguientes instrucciones:

    1. En Azure Portal, seleccione **Todos los servicios**.
    2. En el cuadro de filtro, escriba **Event Hubs**.
    3. Haga doble clic en el espacio de nombres que ha creado.
    4. Seleccione **+ Centro de eventos**.

6. En la lista de espacios de nombres de Event Hubs, seleccione el espacio de nombres recién creado.      
5. Seleccione **Event Hubs** y luego seleccione **+ Centro de eventos** para crear un centro de eventos.
  

6. Escriba los siguientes valores:

    - Nombre: asigne un nombre al centro de eventos.
    - Número de particiones: 10
    - Retención de mensajes: 1. 
   
    ![Aportación de detalles del centro de evento para el ejemplo de streaming de Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Provide event hub details for Spark streaming example")

7. Seleccione **Crear**.
8. Seleccione **Directivas de acceso compartido** para el espacio de nombres (tenga en cuenta que no se trata de las directivas de acceso compartido del centro de eventos) y luego seleccione **RootManageSharedAccessKey**.
    
     ![Establecimiento de directivas de Event Hub para el ejemplo de streaming de Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Set Event Hub policies for the Spark streaming example")

9. Guarde los valores **Clave principal** y **Cadena de conexión: clave principal** para usarlos más adelante en este tutorial.

     ![Vista de las claves de directiva de Event Hub para el ejemplo de streaming de Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "View Event Hub policy keys for the Spark streaming example")


## <a name="send-tweets-to-the-event-hub"></a>Envío de tweets al centro de eventos

Necesita crea un cuaderno de Jupyter Notebook y asignarle el nombre **SendTweetsToEventHub**. 

1. Ejecute el código siguiente para agregar las bibliotecas de Maven externas:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Ejecute el siguiente código para enviar tweets al centro de eventos:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Abra el centro de eventos en Azure Portal.  En **Introducción**, verá que algunos gráficos muestran los mensajes enviados al centro de eventos.

## <a name="read-tweets-from-the-event-hub"></a>Lectura de tweets desde el centro de eventos

Necesita crear un cuaderno de Jupyter Notebook y asignarle el nombre **SendTweetsToEventHub**. 

1. Ejecute el código siguiente para agregar una biblioteca de Maven externa:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Ejecute el código siguiente para leer tweets del centro de eventos:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Con HDInsight, los datos se almacenan en Azure Storage o Azure Data Lake Store, por lo que cualquier clúster se puede eliminar de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Si tiene previsto pasar inmediatamente al siguiente tutorial, es aconsejable que no elimine el clúster.

Abra el clúster en Azure Portal y seleccione **Eliminar**.

![Eliminar clúster de HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminar clúster de HDInsight")

También puede seleccionar el nombre del grupo de recursos para abrir la página del grupo de recursos y, después, hacer clic en **Eliminar grupo de recursos**. Al eliminar el grupo de recursos, se eliminan tanto el clúster de HDInsight Spark como la cuenta de almacenamiento predeterminada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

* Leer el mensaje de un centro de eventos.
Vaya al siguiente artículo para saber cómo crear una aplicación de Machine Learning. 

> [!div class="nextstepaction"]
> [Creación de una aplicación de Machine Learning](./apache-spark-ipython-notebook-machine-learning.md)


