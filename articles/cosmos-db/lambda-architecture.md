---
title: Arquitectura lambda con Azure Cosmos DB y HDInsight (Apache Spark) | Microsoft Docs
description: "En este artículo se describe cómo implementar una arquitectura lambda mediante Azure Cosmos DB, HDInsight y Spark"
keywords: arquitectura lambda
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: 273aeae9-e31c-4a43-b216-5751c46f212e
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: f88f3fb05495b0f3330d5a4cde7718fe89b2f694
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementación de una arquitectura lambda en la plataforma Azure 

Las arquitecturas lambda permiten un procesamiento de datos eficaz de conjuntos de datos masivos. Las arquitecturas lambda usan el procesamiento por lotes, el procesamiento de flujos y una capa de servicio para minimizar la latencia que implica la consulta de macrodatos. 

Para implementar una arquitectura lambda en Azure, puede combinar las siguientes tecnologías para acelerar el análisis de macrodatos en tiempo real:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) es el servicio de base de datos de varios modelos distribuido de forma global líder del sector. 
* [Apache Spark para Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) es un marco de procesamiento que ejecuta aplicaciones de análisis de datos a gran escala.
* La [fuente de cambios](change-feed.md) de Azure Cosmos DB, transmite en secuencias nuevos datos a la capa de procesamiento por lotes para que HDInsight los procese.
* El [Conector de Spark a Azure Cosmos DB](spark-connector.md)

En este artículo se describen los aspectos básicos de una arquitectura lambda basada en el diseño original multicapa y las ventajas de una arquitectura lambda "rediseñada" que simplifica las operaciones.  

Si desea obtener información general de la arquitectura lambda y de los recursos disponibles en el ejemplo de este tipo de arquitectura, vea el vídeo siguiente:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>¿Qué es una arquitectura lambda?
Una arquitectura lambda es una arquitectura de procesamiento de datos genérica, escalable y con tolerancia a errores que se utiliza en escenarios de procesamiento por lotes y mejora de la latencia, tal y como lo describe [Nathan Marz](https://twitter.com/nathanmarz).

![Diagrama que muestra una arquitectura lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Origen: http://lambda-architecture.net/

Los principios básicos de una arquitectura lambda se describen en el diagrama anterior según se indica en [https://lambda-architecture.net](http://lambda-architecture.net/).

 1. Todos los **datos** se insertan en *ambas capas,* la *capa de procesamiento por lotes* y la *capa de velocidad*.
 2. La **capa de procesamiento por lotes** tiene un conjunto de datos maestro (un conjunto inmutable, solo de anexación de datos sin procesar), y permite calcular previamente las vistas de lote.
 3. La **capa de servicio** tiene vistas de lote para consultas rápidas. 
 4. La **capa de velocidad** permite compensar el tiempo de procesamiento (en la capa de servicio) y se ocupa solo de los datos recientes.
 5. Todas las consultas se pueden resolver mediante la combinación de los resultados de las vistas de lote y las vistas en tiempo real, o haciendo ping en ellas individualmente.

A medida que avancemos, podremos implementar esta arquitectura usando solo lo siguiente:

* Una o varias colecciones de Azure Cosmos DB
* Clúster de HDInsight (Apache Spark 2.1)
* Spark Connector [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Capa de velocidad

Desde una perspectiva operativa, mantener dos flujos de datos al tiempo que se garantiza el estado correcto de los datos puede ser una tarea muy complicada. Para simplificar las operaciones, utilice el [soporte de la fuente de cambios de Azure Cosmos DB](change-feed.md) para mantener el estado de la *capa de procesamiento por lotes* al tiempo que revela el registro de cambios de Azure Cosmos DB a través de la *API de fuente de cambios* para su *capa de velocidad*.  
![Diagrama que resalta los nuevos datos, la capa de velocidad y la parte del conjunto de datos maestro de la arquitectura lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

¿Qué es lo importante en estas capas?

 1. Todos los **datos** se insertan *solo* en Azure Cosmos DB, lo cual puede evitar problemas de multidifusión.
 2. La **capa de procesamiento por lotes** tiene un conjunto de datos maestro (un conjunto inmutable, solo de anexación de datos sin procesar), y permite calcular previamente las vistas de lote.
 3. La **capa de servicio** se analiza en la sección siguiente.
 4. La **capa de velocidad** utiliza HDInsight (Apache Spark) para leer la fuente de cambios de Azure Cosmos DB. Esto le permite conservar los datos así como consultarlos y procesarlos de forma simultánea.
 5. Todas las consultas se pueden resolver mediante la combinación de los resultados de las vistas de lote y las vistas en tiempo real, o haciendo ping en ellas individualmente.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Ejemplo de código: flujos estructurados de Spark en una fuente de cambios de Azure Cosmos DB
Antes de ejecutar un prototipo rápido de la fuente de cambios de Azure Cosmos DB como parte de la **capa de velocidad**, puede probarlo mediante datos de Twitter como parte del ejemplo que se describe en [Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Procesamiento de flujos de cambios mediante la fuente de cambios de Azure Cosmos DB). Para comenzar de inmediato con la salida de Twitter, consulte el ejemplo de código de [Stream feed from Twitter to Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed) (Fuente de flujos de Twitter a Cosmos DB). En el ejemplo anterior va a cargar datos de Twitter en Azure Cosmos DB y, a continuación, puede configurar el clúster de HDInsight (Apache Spark) para que se conecte a la fuente de cambios. Para más información sobre cómo realizar esta configuración, consulte [Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Configuración del conector de Apache Spark en Azure Cosmos DB).  

El fragmento de código siguiente muestra cómo configurar `spark-shell` para ejecutar un trabajo de flujos estructurados para que se conecte a una fuente de cambios de Azure Cosmos DB que revisa en tiempo real el flujo de datos de Twitter para llevar a cabo un recuento del intervalo de ejecución.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Para obtener ejemplos de código completos, consulte [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), incluido:
* [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Consulta de streaming a partir de la fuente de cambios de Cosmos DB.scala)
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Consulta de etiquetas de streaming a partir de la fuente de cambios de Cosmos DB.scala)

El resultado de esto es una consola de `spark-shell`, que ejecuta continuamente un trabajo de flujos estructurados que realiza un recuento de intervalos en los datos de Twitter que proceden de la fuente de cambios de Azure Cosmos DB. La siguiente imagen muestra la salida del trabajo de flujos y los recuentos de intervalos.

![Salida de streaming que muestra el recuento de intervalos en los datos de Twitter que proceden de la fuente de cambios de Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Para más información sobre la fuente de cambios de Azure Cosmos DB, consulte:

* [Compatibilidad con la fuente de cambios en Azure Cosmos DB](change-feed.md)
* [Introducing the Azure CosmosDB Change Feed Processor Library](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/) (Introducción a la biblioteca de procesadores de la fuente de cambios de Azure Cosmos DB)
* [Stream Processing Changes: Azure CosmosDB change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/) (Procesamiento de flujo de cambios: fuente de cambios de Azure CosmosDB + Apache Spark)

## <a name="batch-and-serving-layers"></a>Capas de proceso por lotes y de servicio
Puesto que los nuevos datos se cargan en Azure Cosmos DB (donde la fuente de cambios se está usando para la capa de velocidad), aquí es donde reside el **conjunto de datos maestro** (un conjunto inmutable, solo de anexación de datos sin procesar). De aquí en adelante, utilice HDInsight (Apache Spark) para realizar las funciones de cálculo previo de la **capa de procesamiento por lotes** a la **capa de servicio**, tal y como se muestra en la siguiente imagen:

![Diagrama que muestra la capa de procesamiento por lotes y la capa de servicio de la arquitectura lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

¿Qué es lo importante en estas capas?

 1. Todos los **datos** se insertan solo en Azure Cosmos DB (para evitar problemas de multidifusión).
 2. La **capa de procesamiento por lotes** tiene un conjunto de datos maestro (un conjunto inmutable, solo de anexación de datos sin procesar) almacenado en Azure Cosmos DB. Mediante HDI Spark, puede calcular previamente las agregaciones que se van a almacenar en las vistas de procesamiento por lotes calculadas.
 3. La **capa de servicio** es una base de datos de Azure Cosmos DB con colecciones para el conjunto de datos maestro y las vistas de lotes procesadas.
 4. La **capa de velocidad** se analizará más adelante en este artículo.
 5. Todas las consultas se pueden resolver mediante la combinación de los resultados de las vistas de lote y las vistas en tiempo real, o haciendo ping en ellas individualmente.

### <a name="code-example-pre-computing-batch-views"></a>Ejemplo de código: Cálculo previo de las vistas de lotes
Para mostrar la ejecución de vistas calculadas previamente en el **conjunto de datos maestro** desde Apache Spark a Azure Cosmos DB, use los siguientes fragmentos de código de los blocs de notas [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) (Arquitectura lambda rediseñada: Capa de procesamiento por lotes) y [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) (Arquitectura lambda rediseñada: De capa de procesamiento por lotes a capa de servicio). En este escenario, utilice los datos de Twitter almacenados en Azure Cosmos DB.

Empecemos por crear la configuración de la conexión a los datos de Twitter de Azure Cosmos DB con el siguiente código de PySpark.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

A continuación, ejecutemos la siguiente instrucción SQL de Spark para determinar los 10 hashtags principales del conjunto de tweets. Vamos a ejecutar esta consulta SQL de Spark en Jupyter Notebook sin el gráfico de barras de salida que aparece justo después de este fragmento de código.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Gráfico que muestra el número de tweets por hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Ahora que tiene la consulta, vamos a guardarla de nuevo en una colección usando el conector de Spark para guardar los datos de salida en otra colección diferente.  En este ejemplo, use Scala para presentar la conexión. De forma parecida a la del ejemplo anterior, cree la configuración de la conexión para guardar la trama de datos de Apache Spark en una colección diferente de Azure Cosmos DB.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Después de especificar el modo `SaveMode` (que indica si se debe `Overwrite` o `Append` los documentos), cree una trama de datos `tweets_bytags` similar a la consulta SQL de Spark del ejemplo anterior.  Con la trama de datos `tweets_bytags` creada, puede guardarla mediante el método `write` con el parámetro `writeConfig` especificado anteriormente.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Esta última instrucción ha guardado la trama de datos de Spark en una nueva colección de Azure Cosmos DB. Desde la perspectiva de una arquitectura lambda, se trata de su **vista de lote** de la **capa de servicio**.
 
#### <a name="resources"></a>Recursos

Para obtener ejemplos de código completos, consulte [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), incluido:
* Lambda Architecture Rearchitected - Batch Layer (Arquitectura lambda rediseñada: Capa de procesamiento por lotes) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected - Batch to Serving Layer (Arquitectura lambda rediseñada: De capa de procesamiento por lotes a capa de servicio) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Capa de velocidad
Como ya se indicó anteriormente, el uso de la biblioteca de la fuente de cambios de Azure Cosmos DB le permite simplificar las operaciones entre las capas de procesamiento por lotes y la de velocidad. En esta arquitectura, use Apache Spark (a través de HDInsight) para realizar las consultas de los *flujos estructurados* en los datos. También es posible que desee conservar temporalmente los resultados de las consultas de los flujos estructurados para que otros sistemas puedan acceder a estos datos.

![Diagrama que muestra la capa de velocidad de la arquitectura lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Para ello, cree una colección de Azure Cosmos DB independiente para guardar los resultados de las consultas de los flujos estructurados.  Esto hace que se permita a otros sistemas acceder a esta información y no solo a Apache Spark. Al igual que con la característica Período de vida (TTL) de Cosmos DB, puede configurar los documentos para que se eliminen automáticamente después de una duración establecida.  Para más información sobre la característica Período de vida de Azure Cosmos DB, consulte [Hacer que caduquen automáticamente los datos de colecciones de Azure Cosmos DB con período de vida](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Arquitectura lambda: rediseñada
Como se indica en las secciones anteriores, puede simplificar la arquitectura lambda original mediante el uso de los siguientes componentes:
* Azure Cosmos DB
* La biblioteca de la fuente de cambios de Azure Cosmos DB para evitar la necesidad de realizar la multidifusión de los datos entre las capas de procesamiento por lotes y la de velocidad
* Apache Spark en HDInsight
* El conector de Spark para Azure Cosmos DB

![Diagrama que muestra el rediseño de la arquitectura lambda mediante Azure Cosmos DB, Spark y la API de fuente de cambios de Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

Con este diseño, solo tiene dos servicios administrados, Azure Cosmos DB y HDInsight. Juntos, administran las capas de procesamiento por lotes, de servicio y de velocidad de la arquitectura lambda. Esto simplifica no solo las operaciones, sino también el flujo de datos. 
 1. Todos los datos se insertan en Azure Cosmos DB para su procesamiento.
 2. La capa de procesamiento por lotes tiene un conjunto de datos maestro (un conjunto inmutable, solo de anexación de datos sin procesar), y permite calcular previamente las vistas de lote.
 3. La capa de servicio tiene vistas de lote para consultas rápidas de datos.
 4. La capa de velocidad permite compensar el tiempo de procesamiento (en la capa de servicio) y se ocupa solo de los datos recientes.
 5. Todas las consultas se pueden resolver mediante la combinación de los resultados de las vistas de lote y las vistas en tiempo real.

### <a name="resources"></a>Recursos

 * **Nuevos datos**: la [fuente de flujos de Twitter a CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), que es el mecanismo para insertar nuevos datos en Azure Cosmos DB.
 * **Capa de procesamiento por lotes:** esta capa se compone del *conjunto de datos maestro* (un conjunto inmutable, solo de anexación de datos sin procesar) y tiene la capacidad de calcular previamente las vistas de lotes de los datos que se insertan en la **capa de servicio**.
    * El cuaderno **Lambda Architecture Rearchitected - Batch Layer** (Arquitectura lambda rediseñada: Capa de procesamiento por lotes) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) realiza consultas en el *conjunto de datos maestro* de las vistas de lotes.
 * **Capa de servicio:** la **capa de servicio** se compone de los datos calculados previamente que dan lugar a las vistas de lotes (por ejemplo agregaciones, segmentaciones de datos específicas, etc.) para realizar consultas rápidas.
    * El cuaderno **Lambda Architecture Rearchitected - Batch to Serving Layer** (Arquitectura lambda rediseñada: De capa de procesamiento por lotes a capa de servicio) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) inserta los datos de lotes en la capa de servicio, es decir, Spark consulta una colección de lotes de tweets, la procesa y la almacena en otra colección diferente (un lote calculado).
* **Capa de velocidad:** la **capa de velocidad** está compuesta por Spark, que usa la fuente de cambios de Azure Cosmos DB para leer y actuar inmediatamente. Los datos también se pueden guardar en *RT calculados* para que otros sistemas puedan consultar los datos procesados en tiempo real en vez de ejecutar una consulta en tiempo real ellos mismos.
    * El script de escala [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Consulta de streaming a partir de la fuente de cambios de Cosmos DB) ejecuta una consulta de streaming a partir de la fuente de cambios de Azure Cosmos DB para calcular un recuento de intervalos desde el shell de Spark.
    * El script de escala [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Consulta de etiquetas de streaming a partir de la fuente de cambios de Cosmos DB) ejecuta una consulta de streaming a partir de la fuente de cambios de Azure Cosmos DB para calcular un recuento de etiquetas de intervalos desde el shell de Spark.
  
## <a name="next-steps"></a>pasos siguientes
Si aún no lo ha hecho, descargue el conector de Spark a Azure Cosmos DB del repositorio de GitHub [azure-documentdb-spark](https://github.com/Azure/azure-cosmosdb-spark) y explore los recursos adicionales del repositorio:
* [Arquitectura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Ejemplos de agregaciones distribuidas](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Cuadernos y script de ejemplo](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Demostraciones de flujos estructurados](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Demostraciones de fuente de cambios](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream processing changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Procesamiento de flujo de cambios mediante la fuente de cambios de Azure Cosmos DB y Apache Spark)

Es posible que también quiera consultar la [guía de Apache Spark SQL, DataFrames y conjuntos de datos](http://spark.apache.org/docs/latest/sql-programming-guide.html) y el artículo [Apache Spark en Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
