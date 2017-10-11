---
title: "Conexión de Apache Spark a Azure Cosmos DB | Microsoft Docs"
description: "Use este tutorial para aprender sobre el conector de Azure Cosmos DB Spark que le permite conectar Apache Spark a Azure Cosmos DB para realizar agregaciones distribuidas y ciencias de datos en el sistema de base de datos distribuido globalmente multiinquilino de Microsoft diseñado para la nube."
keywords: apache spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: denlee
ms.openlocfilehash: 8ecbb478c81cde25bbd0d1c9ee07ae02b07f8cc7
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Aceleración de análisis de macrodatos en tiempo real con el conector de Spark a Azure Cosmos DB

El conector de Spark a Azure Cosmos DB permite que Azure Cosmos DB actúe como un origen de entrada o un receptor de salida para trabajos de Apache Spark. La conexión de [Spark](http://spark.apache.org/) a [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) acelera la capacidad de resolver problemas de ciencia de datos de avance rápido, donde puede usar Azure Cosmos DB para guardar los datos y consultarlos rápidamente. El conector de Spark a Azure Cosmos DB usa de manera eficiente los índices administrados de forma nativa por Azure Cosmos DB. Los índices permiten columnas actualizables al realizar análisis y aplicar el filtrado de predicados en los datos distribuidos globalmente en rápida evolución, que abarcan Internet de las cosas (IoT), ciencia de datos y escenarios de análisis.

Para trabajar con Spark GraphX y las API Graph de Gremlin de Azure Cosmos DB, consulte [Análisis de gráficos mediante Spark y Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Descargar

Para comenzar, descargue el conector de Spark a Azure Cosmos DB (versión preliminar) del repositorio [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) en GitHub.

## <a name="connector-components"></a>Componentes del conector

El conector utiliza los siguientes componentes:

* [Azure Cosmos DB](http://documentdb.com) permite a los clientes aprovisionar y escalar el rendimiento y el almacenamiento de forma elástica a través de cualquier número de regiones geográficas. Las ofertas de servicio:
   * [Distribución global](distribute-data-globally.md) llave en mano y escala horizontal
   * Garantía de latencias de un solo dígito en el percentil 99
   * [Varios modelos de coherencia bien definida](consistency-levels.md)
   * Garantía de alta disponibilidad con funcionalidades de hospedaje múltiple
   * Todas las características están respaldadas por [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA) completos líderes del sector.

* [Apache Spark](http://spark.apache.org/) es un motor de procesamiento de código abierto eficaz diseñado para ofrecer velocidad, facilidad de uso y análisis sofisticados.

* Consulte [Apache Spark en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) para implementar Apache Spark en la nube para implementaciones críticas mediante [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versiones oficialmente compatibles:

| Componente | Versión |
|---------|-------|
|Spark de Apache|2.0+|
| Scala| 2.11|
| SDK de Java de Azure DocumentDB | 1.10.0 |

Este artículo le ayuda a ejecutar algunos ejemplos sencillos con Python (a través de pyDocumentDB) y las interfaces de Scala.

Existen dos enfoques para conectar Apache Spark y Azure Cosmos DB:
- Usar pyDocumentDB a través del [SDK de Python para Azure DocumentDB](https://github.com/Azure/azure-documentdb-python).
- Crear un conector de Spark a Cosmos DB basado en Java mediante el [SDK para Java de Azure DocumentDB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Implementación de pyDocumentDB
El [SDK pyDocumentDB](https://github.com/Azure/azure-documentdb-python) actual le permite conectar Spark a Azure Cosmos DB como se muestra en el siguiente diagrama:

![Flujo de datos de Spark a Azure Cosmos DB a través de pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Flujo de datos de la implementación de pyDocumentDB

El flujo de datos es el siguiente:

1. El nodo maestro de Spark se conecta al nodo de puerta de enlace de Azure Cosmos DB a través de pyDocumentDB. Un usuario especifica solo las conexiones de Spark y Azure Cosmos DB. Las conexiones a los respectivos nodos maestro y de puerta de enlace son transparentes para el usuario.
2. El nodo de puerta de enlace realiza la consulta en Azure Cosmos DB donde posteriormente se ejecuta la consulta en las particiones de la colección de los nodos de datos. La respuesta a esas consultas se reenvía al nodo de puerta de enlace y ese conjunto de resultados se devuelve al nodo maestro de Spark.
3. Las consultas posteriores (por ejemplo, en un DataFrame de Spark) se envían a los nodos de trabajo de Spark para su procesamiento.

La comunicación entre Spark y Azure Cosmos DB se limita al nodo maestro de Spark y a los nodos de puerta de enlace de Azure Cosmos DB.  Las consultas van tan rápido como permite la capa de transporte entre estos dos nodos.

### <a name="install-pydocumentdb"></a>Instalación de pyDocumentDB
Puede instalar pyDocumentDB en el nodo de controlador mediante **pip**, por ejemplo:

```
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Conexión de Spark a Azure Cosmos DB mediante pyDocumentDB
La simplicidad del transporte de comunicación permite que la ejecución de una consulta de Spark a Azure Cosmos DB mediante pyDocumentDB sea relativamente sencilla.

El fragmento de código siguiente muestra cómo usar pyDocumentDB en un contexto de Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Como se indica en el fragmento de código:

* El SDK de Python para Azure Cosmos DB (`pyDocumentDB`) contiene todos los parámetros de conexión necesarios. Por ejemplo, el parámetro de ubicaciones preferidas elige la réplica de lectura y el orden de prioridad.
*  Importe las bibliotecas necesarias y configure su **masterKey** y **host** para crear el *cliente* de Azure Cosmos DB (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Ejecución de consultas de Spark mediante pyDocumentDB
En los ejemplos siguientes se usa la instancia de Azure Cosmos DB creada en el fragmento de código anterior con las claves de solo lectura especificadas. El siguiente fragmento de código se conecta a la colección **airports.codes** (en la cuenta de DoctorWho, como se ha especificado anteriormente), y ejecuta una consulta para extraer las ciudades con aeropuerto del Estado de Washington.

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Después de haber ejecutado la consulta mediante **query**, el resultado es **query_iterable.QueryIterable**, que se convierte en una lista Python. Una lista Python puede convertirse con facilidad en un dataframe de Spark con el siguiente código:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>¿Por qué usar pyDocumentDB para conectar Spark a Azure Cosmos DB?
La conexión de Spark a Azure Cosmos DB con pyDocumentDB suele utilizarse en escenarios donde:

* Quiera usar Python.
* Desea devolver un conjunto de resultados relativamente pequeño desde Azure Cosmos DB a Spark. Tenga en cuenta que el conjunto de datos subyacente en Azure Cosmos DB puede ser bastante grande. Vaya a aplicar filtros, es decir, ejecutar filtros de predicado, en el origen de Azure Cosmos DB.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Conector de Spark a Azure Cosmos DB

El conector de Spark a Azure Cosmos DB usa el [SDK de Java para Azure DocumentDB](https://github.com/Azure/azure-documentdb-java) y transfiere los datos entre los nodos de trabajo de Spark y Azure Cosmos DB, tal y como se muestra en el diagrama siguiente:

![Flujo de datos del conector de Spark a Azure Cosmos DB](./media/spark-connector/spark-connector.png)

El flujo de datos es el siguiente:

1. El nodo maestro de Spark se conecta al nodo de puerta de enlace de Azure Cosmos DB para obtener el mapa de particiones. Un usuario especifica solo las conexiones de Spark y Azure Cosmos DB. Las conexiones a los respectivos nodos maestro y de puerta de enlace son transparentes para el usuario.
2. Esta información se proporciona en el nodo principal de Spark.  En este momento, debe poder analizar la consulta para determinar las particiones (y sus ubicaciones) dentro de Azure Cosmos DB a las que debe acceder.
3. Esta información se transmite a los nodos de trabajo de Spark.
4. Los nodos de trabajo de Spark se conectan directamente a las particiones de Azure Cosmos DB para extraer los datos y devolverlos a las particiones de Spark de los nodos de trabajo de Spark.

La comunicación entre Spark y Azure Cosmos DB es mucho más rápida porque la transferencia de datos se realiza entre los nodos de trabajo de Spark y los nodos de datos de Azure Cosmos DB (particiones).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Creación del conector de Spark a Azure Cosmos DB
Actualmente, el proyecto del conector usa Maven. Para crear el conector sin dependencias, puede ejecutar:
```
mvn clean package
```
También puede descargar las últimas versiones del archivo JAR de la carpeta de *versiones*.

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Inclusión del archivo JAR de Azure Cosmos DB Spark
Antes de ejecutar ningún código, deberá incluir el archivo JAR de Azure Cosmos DB Spark.  Si va a usar **spark-shell**, puede incluirlo mediante la opción **--jars**.  

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3-jar-with-dependencies.jar
```

Si desea ejecutar el archivo JAR sin dependencias, use el siguiente código:

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3.jar,/$location/azure-documentdb-1.10.0.jar
```

Si usa un servicio de cuaderno como Azure HDInsight Jupyter Notebook en Azure HDInsight, puede usar los comandos **spark magic**:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.10.0.jar","wasb:///example/jars/azure-cosmosdb-spark-0.0.3.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

El comando **jars** permite incluir los dos archivos JAR necesarios para **azure-cosmosdb-spark** (el propio y el SDK de Java para Azure DocumentDB) y excluir **scala-reflect**, a fin de que no interfiera con las llamadas Livy realizadas (Jupyter Notebook > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Conexión de Spark a Azure Cosmos DB mediante el conector
Aunque el transporte de comunicación es un poco más complicado, ejecutar una consulta de Spark en Azure Cosmos DB mediante el conector es mucho más rápido.

El fragmento de código siguiente muestra cómo usar el conector dentro de un contexto de Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Como se indica en el fragmento de código:

- **azure-cosmosdb-spark** contiene todos los parámetros de conexión necesarios, que incluyen las ubicaciones preferidas. Por ejemplo, puede elegir el orden de prioridad y la réplica de lectura.
- Solo tiene que importar las bibliotecas necesarias y configurar la clave principal y el host para crear el cliente de Azure Cosmos DB.

### <a name="execute-spark-queries-via-the-connector"></a>Ejecución de consultas de Spark mediante el conector

En el ejemplo siguiente se usa la instancia de Azure Cosmos DB creada en el fragmento de código anterior con las claves de solo lectura especificadas. El siguiente fragmento de código se conecta a la colección DepartureDelays.flights_pcoll (en la cuenta de DoctorWho, como se especificó anteriormente) mediante la ejecución de una consulta para extraer la información sobre los retrasos de los vuelos que salen de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>¿Por qué usar la implementación del conector de Spark a Azure Cosmos DB?

La conexión de Spark a Azure Cosmos DB con el conector suele usarse en escenarios en que:

* Quiere usar Scala y actualizarlo para incluir un contenedor de Python, tal y como se indicó en [Problema 3: Agregar contenedor de Python y ejemplos](https://github.com/Azure/azure-cosmosdb-spark/issues/3).
* Tiene una gran cantidad de datos que se van a transferir entre Apache Spark y Azure Cosmos DB.

Para darle una idea de la diferencia de rendimiento de consultas, vea la [wiki de ejecuciones de pruebas de consulta](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Ejemplo de agregación distribuida
En esta sección se proporcionan algunos ejemplos de cómo se pueden realizar agregaciones distribuidas y análisis de forma conjunta con la utilización de Apache Spark y Azure Cosmos DB. Azure Cosmos DB ya admite agregaciones, que se describen en el [blog Planet scale aggregates with Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). A continuación se indica cómo puede llevarlo al siguiente nivel con Apache Spark.

Tenga en cuenta que estas agregaciones guardan relación con el [cuaderno del conector de Spark a Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Conexión a los datos de ejemplo sobre vuelos
En estos ejemplos de agregaciones, se accede a algunos datos de rendimiento sobre vuelos almacenados en la base de datos **DoctorWho** de Azure Cosmos DB. Para conectarse a ella, debe utilizar el siguiente fragmento de código:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Con este fragmento de código, también se va a ejecutar una consulta base que transfiere el conjunto filtrado de datos deseado de Azure Cosmos DB a Spark (donde el último puede realizar agregaciones distribuidas). En este caso, se pregunta por los vuelos que salen de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Los resultados siguientes se generaron mediante la ejecución de las consultas del servicio de Jupyter Notebook.  Tenga en cuenta que todos los fragmentos de código son genéricos y no específicos a cualquier servicio.

### <a name="running-limit-and-count-queries"></a>Ejecución de consultas LIMIT y COUNT
Del mismo modo que está familiarizado con SQL y Spark SQL, se va a comenzar con una consulta **LIMIT**:

![Consulta LIMIT de Spark](./media/spark-connector/spark-sql-query.png)

La siguiente consulta es una consulta **COUNT** sencilla y rápida:

![Consulta COUNT de Spark](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>Consulta GROUP BY
En el conjunto siguiente, se van a ejecutar consultas **GROUP BY** con facilidad en la base de datos de Azure Cosmos DB:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Gráfico de consultas GROUP BY de Spark](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Consulta DISTINCT, ORDER BY
Esta es una consulta **DISTINCT, ORDER BY**:

![Gráfico de consultas GROUP BY de Spark](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuación del análisis de datos sobre vuelos
Puede usar las consultas de ejemplo siguientes para continuar el análisis de datos sobre vuelos:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Los 5 destinos (ciudades) principales con vuelos retrasados que salen de Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Gráfico de los principales retrasos de Spark](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Cálculo del valor medio de los retrasos según las ciudades de destino cuyos vuelos salen de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Gráfico de valores medios de retrasos de Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Pasos siguientes

Si aún no lo ha hecho, descargue el conector de Spark a Azure Cosmos DB del repositorio de GitHub [azure-documentdb-spark](https://github.com/Azure/azure-cosmosdb-spark) y explore los recursos adicionales del repositorio:

* [Ejemplos de agregaciones distribuidas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Cuadernos y script de ejemplo](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Es posible que también quiera consultar la [guía de Apache Spark SQL, DataFrames y conjuntos de datos](http://spark.apache.org/docs/latest/sql-programming-guide.html) y el artículo [Apache Spark en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).
