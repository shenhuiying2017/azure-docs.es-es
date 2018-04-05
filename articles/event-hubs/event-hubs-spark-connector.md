---
title: Integración de Apache Spark con Azure Event Hubs | Microsoft Docs
description: Integración con Apache Spark para Structured Streaming con Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija;sethm;sagrewal
ms.openlocfilehash: b430b731bdb38f6fe8af347e082fdfb1ef36a945
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integración de Apache Spark con Azure Event Hubs

Azure Event Hubs se integra perfectamente con [Apache Spark](https://spark.apache.org/) para facilitar la compilación de aplicaciones de streaming distribuido. Esta integración es compatible con [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html) y [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). El conector de Event Hubs para Apache Spark está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-spark). Esta biblioteca también está disponible para su uso en proyectos de Maven desde el [repositorio central de Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

En este artículo se muestra cómo crear una aplicación continua en [Azure Databricks](https://azure.microsoft.com/services/databricks/). Aunque en este artículo se utiliza [Azure Databricks](https://azure.microsoft.com/services/databricks/), los clústeres de Spark también están disponibles con [HDInsight](../hdinsight/spark/apache-spark-overview.md).

El ejemplo siguiente utiliza dos cuadernos de Scala, uno para el streaming de los eventos de un centro de eventos y otro para enviar eventos de vuelta.

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure. Si no dispone de una, [cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Una instancia de Event Hubs. Si no dispone de ninguna, [cree una](event-hubs-create.md)
* Una instancia de [Azure Databricks](https://azure.microsoft.com/services/databricks/). Si no dispone de ninguna, [cree una](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Creación de una biblioteca mediante coordenadas de maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`

Haga streaming de eventos del centro de eventos con el código siguiente:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
El código de ejemplo siguiente envía eventos a su centro de eventos con las API de lote de Spark. También puede escribir una consulta de streaming para enviar eventos al centro de eventos.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Pasos siguientes

Ahora sabe cómo configurar un flujo escalable y tolerante a errores mediante el conector de Event Hubs para Apache Spark. Para más información sobre el uso de Event Hubs con Structured Streaming y Spark Streaming, siga estos vínculos:

* [Guía de integración de Structured Streaming y Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Guía de integración de Spark Streaming y Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
