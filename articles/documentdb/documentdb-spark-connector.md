---
title: "Conexión de Apache Spark a Azure Cosmos DB | Microsoft Docs"
description: "Use este tutorial para obtener información sobre el conector de Azure Cosmos DB Spark que le permite conectar Apache Spark a Azure Cosmos DB para realizar agregaciones distribuidas y ciencias de datos en el sistema de base de datos distribuido globalmente multiinquilino de Microsoft diseñado para la nube."
keywords: apache spark
services: cosmosdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6a5db515bec785aa29b29a3096dc20a72056bd08
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Aceleración de análisis de macrodatos en tiempo real con el conector de Spark a Azure Cosmos DB

El conector de Spark a Azure Cosmos DB permite que Cosmos DB actúe como un origen de entrada o un receptor de salida para trabajos de Apache Spark. La conexión de [Spark](http://spark.apache.org/) a [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) acelera la capacidad de resolver problemas de ciencia de datos de avance rápido, donde los datos pueden persistir y consultarse con rapidez mediante Cosmos DB. El conector de Spark a Azure Cosmos DB usa de forma eficaz los índices administrados nativos de Cosmos DB y habilita las columnas actualizables al realizar analíticas, presionar el filtrado de predicados con respecto a los datos distribuidos globalmente sujetos a cambios rápidos, desde IoT y ciencia de datos hasta escenarios de análisis. 

## <a name="download"></a>Descargar

Comience con la descarga del conector de Spark a Azure Cosmos DB (versión preliminar) en el repositorio [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) de GitHub.

## <a name="connector-components"></a>Componentes del conector

El conector utiliza los siguientes componentes:

* [Azure Cosmos DB](http://documentdb.com), el [sistema de base de datos globalmente distribuido](documentdb-distribute-data-globally.md) y multiinquilino de Microsoft, diseñado para la nube. Azure Cosmos DB permite a los clientes aprovisionar y escalar el rendimiento y el almacenamiento de forma elástica a través de cualquier número de regiones geográficas. El servicio ofrece a los desarrolladores una latencia baja garantizada en un 99 %, una disponibilidad alta del 99,99 % garantizada y [varios modelos bien definidos de coherencia](documentdb-consistency-levels.md).

* [Apache Spark](http://spark.apache.org/), que es un motor de procesamiento de código abierto eficaz diseñado para ofrecer velocidad, facilidad de uso y análisis sofisticados. 

* [Apache Spark en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) Puede implementar Apache Spark en la nube para implementaciones críticas mediante [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versiones oficialmente compatibles:

| Componente | Versión |
|---------|-------|
|Spark de Apache|2.0+|
| Scala| 2.11|
| SDK de Java de Azure DocumentDB | 1.9.6 |
 
Este artículo le ayuda a ejecutar algunos ejemplos sencillos con Python (a través de pyDocumentDB) y la interfaz de Scala.

Existen dos enfoques para conectar Apache Spark y Azure Cosmos DB:
- Usar pyDocumentDB a través del [SDK de Python para Azure DocumentDB](https://github.com/Azure/azure-documentdb-python).
- Crear un conector de Spark a DocumentDB basado en Java mediante el [SDK de Java para Azure DocumentDB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Implementación de pyDocumentDB 
El [SDK de pyDocumentDB](https://github.com/Azure/azure-documentdb-python) actual permite conectar Spark a Cosmos DB como se muestra en el diagrama siguiente:

![Flujo de datos de Spark a Cosmos DB a través de pyCosmosDB](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Flujo de datos de la implementación de pyDocumentDB

El flujo de datos es el siguiente:

1. La conexión se realiza desde el nodo principal de Spark al nodo de puerta de enlace de Cosmos DB a través de pyCosmosDB.  Tenga en cuenta que el usuario especifica solo las conexiones de Spark y Cosmos DB; el hecho de que se conecte con los nodos principal y de puerta de enlace correspondientes es transparente para el usuario.
2. Se realiza una consulta de Cosmos DB (a través del nodo de puerta de enlace), donde la consulta posteriormente ejecuta la consulta en particiones de la colección de los nodos de datos. La respuesta para las consultas se envía al nodo de puerta de enlace y ese conjunto de resultados se devuelve al nodo principal de Spark.
3. Las consultas posteriores (por ejemplo, en un DataFrame de Spark) se envían a los nodos de trabajo de Spark para su procesamiento.

La información importante es que la comunicación entre Spark y Cosmos DB se limita al nodo principal de Spark y a los nodos de puerta de enlace de Cosmos DB.  Las consultas van tan rápido como la capa de transporte entre estos dos nodos.

### <a name="installing-pydocumentdb"></a>Instalación de pyDocumentDB
Puede instalar pyDocumentDB en el nodo de controlador mediante **pip**, por ejemplo:

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-cosmos-db-via-pycosmos-db"></a>Conexión de Spark a Cosmos DB a través de pyCosmosDB 
A cambio de la sencillez del transporte de comunicación, la ejecución de una consulta de Spark en Cosmos DB con pyCosmosDB es relativamente sencilla.

El fragmento de código siguiente muestra cómo utilizar pyDocumentDB dentro de un contexto de Spark.

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


# Set keys to connect to Cosmos DB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Como se indica en el fragmento de código:

* El SDK de Python para DocumentDB contiene todos los parámetros de conexión necesarios, incluidas las ubicaciones preferidas (es decir, elegir qué réplica de lectura en qué orden de prioridad).
*  Importe las bibliotecas necesarias y configure su **masterKey** y **host** para crear el *cliente* de Cosmos DB (**pydocumentdb.document_client**).


### <a name="executing-spark-queries-via-pydocumentdb"></a>Ejecución de consultas de Spark a través de pyDocumentDB
Los ejemplos siguientes usan la instancia de Cosmos DB creada en el fragmento de código anterior con las claves especificadas de solo lectura.  El siguiente fragmento de código se conecta a la colección **airports.codes** (en la cuenta de DoctorWho, como se ha especificado anteriormente), y ejecuta una consulta para extraer las ciudades con aeropuerto del Estado de Washington. 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Cosmos DB client will use to connect to the database and collection
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

Después de haber ejecutado la consulta a través de **query**, el resultado es **query_iterable.QueryIterable**, que se convierte en una lista Python. Una lista Python puede convertirse con facilidad en un dataframe de Spark con el siguiente código:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>¿Por qué usar pyDocumentDB para conectar Spark a DocumentDB?
La conexión de Spark a Cosmos DB con pyCosmosDB suele usarse en escenarios en que:

* Desea usar Python.
* Desea devolver un conjunto de resultados relativamente pequeño de DocumentDB a Spark.  Tenga en cuenta que el conjunto de datos subyacente de DocumentDB puede ser bastante grande. Lo más conveniente es aplicar filtros, es decir, ejecutar filtros de predicado, en el origen de DocumentDB.  

## <a name="spark-to-cosmos-db-connector"></a>Conector de Spark a Cosmos DB

El conector de Spark a Cosmos DB usa el [SDK de Java para Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java) y transfiere los datos entre los nodos de trabajo de Spark y Cosmos DB, tal y como se muestra en el diagrama siguiente:

![Flujo de datos del conector de Spark a Cosmos DB](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

El flujo de datos es el siguiente:

1. Una conexión se realiza desde el nodo principal de Spark hasta el nodo de puerta de enlace de Cosmos DB para obtener el mapa de la partición.  Tenga en cuenta que el usuario especifica solo las conexiones de Spark y Cosmos DB; el hecho de que se conecte con los nodos principal y de puerta de enlace correspondientes es transparente para el usuario.
2. Esta información se proporciona en el nodo principal de Spark.  En este momento, debe tener la capacidad de analizar la consulta para determinar a qué particiones (y sus ubicaciones) dentro de Cosmos DB necesita acceder.
3. Esta información se transmite a los nodos de trabajo de Spark...
4. Esto permite a los nodos de trabajo de Spark conectarse directamente a las particiones de Cosmos DB para extraer los datos necesarios y devolver los datos a las particiones de Spark dentro de los nodos de trabajo de Spark.

La información importante es que la comunicación entre Spark y Cosmos DB es mucho más rápida porque la transferencia de datos se realiza entre los nodos de trabajo de Spark y los nodos de datos de Cosmos DB (particiones).

### <a name="building-the-spark-to-cosmos-db-connector"></a>Creación del conector de Spark a Cosmos DB
Actualmente, el proyecto del conector usa Maven. Para crear el conector sin dependencias, puede ejecutar:
```
mvn clean package
```
También puede descargar las últimas versiones del archivo JAR dentro de la carpeta de *versiones*.

### <a name="including-the-azure-documentdb-spark-jar"></a>Incorporación del archivo JAR de Spark en Azure DocumentDB
Antes de ejecutar cualquier código, primero debe incluir el archivo JAR de Spark en Azure DocumentDB.  Si usa **spark-shell**, entonces puede incluir el archivo JAR con la opción **--jars**.  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

También tiene la opción de ejecutar el archivo JAR sin dependencias:

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

Si usa un servicio de cuaderno como Azure HDInsight Jupyter Notebook en Azure HDInsight, puede usar los comandos **spark magic**:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

El comando **jars** permite incluir los dos archivos jar necesarios para **azure-documentdb-spark** (el propio y el SDK de Java de Azure DocumentDB) y excluye **scala-reflect**, a fin de que no interfiera con las llamadas Livy realizadas (Jupyter Notebook > Livy > Spark).

### <a name="connecting-spark-to-cosmos-db-using-the-connector"></a>Conexión de Spark a Cosmos DB mediante el conector
Mientras que el transporte de comunicación es un poco más complicado, ejecutar una consulta de Spark en Cosmos DB mediante el conector es una acción mucho más rápida.

El fragmento de código siguiente muestra cómo utilizar el conector dentro de un contexto de Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Como se indica en el fragmento de código:

- **azure-documentdb-spark** contiene todos los parámetros de conexión necesarios, incluidas las ubicaciones preferidas (por ejemplo, elegir qué réplica de lectura en qué orden de prioridad).
- Solo tiene que importar las bibliotecas necesarias y configurar la clave principal y el host para crear el cliente de Cosmos DB.

### <a name="executing-spark-queries-via-the-connector"></a>Ejecución de consultas de Spark a través del conector

En el ejemplo siguiente se usa la instancia de Cosmos DB creada en el fragmento de código anterior con las claves especificadas de solo lectura. El siguiente fragmento de código se conecta a la colección DepartureDelays.flights_pcoll (en la cuenta de DoctorWho como se especificó anteriormente) mediante la ejecución de una consulta para extraer la información sobre los retrasos de los vuelos que salen de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-cosmos-db-connector-implementation"></a>¿Por qué usar la implementación del conector de Spark a Cosmos DB?

La conexión de Spark a Cosmos DB con el conector suele usarse en escenarios en que:

* Desea usar Scala y actualizarlo para incluir un contenedor de Python, tal y como se indicó en [Problema 3: Agregar contenedor de Python y ejemplos](https://github.com/Azure/azure-documentdb-spark/issues/3).
* Tiene una gran cantidad de datos que se van a transferir entre Apache Spark y Cosmos DB.

Para darle una idea de la diferencia de rendimiento de consultas, vea la [wiki de ejecuciones de pruebas de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Ejemplo de agregación distribuida
En esta sección se proporcionan algunos ejemplos de cómo se pueden realizar agregaciones distribuidas y análisis de forma conjunta con la utilización de Apache Spark y Azure Cosmos DB.  Tenga en cuenta que Azure Cosmos DB ya es compatible con agregaciones, como se describe en el [blog Planet scale aggregates with Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (Agregaciones de escala mundial con Azure Cosmos DB), por lo que se muestra cómo puede llevarlas al siguiente nivel con Apache Spark.

Tenga en cuenta que estas agregaciones guardan relación con el [cuaderno del conector de Spark a Cosmos DB](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-Cosmos DB_Connector.ipynb).

### <a name="connecting-to-flights-sample-data"></a>Conexión a los datos de ejemplo sobre vuelos
Para estos ejemplos de agregaciones, se accede a algunos datos de rendimiento sobre vuelos almacenados en la base de datos **DoctorWho** de Cosmos DB.  Para conectarse a ella, debe utilizar el siguiente fragmento de código:

```
// Import Spark to Cosmos DB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Con este fragmento de código, también se va a ejecutar una consulta base que transfiere el conjunto filtrado de datos deseado de Cosmos DB a Spark (donde el último puede realizar agregaciones distribuidas).  En este caso, se pregunta por los vuelos que salen de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Los resultados siguientes se generaron mediante la ejecución de las consultas del servicio de Jupyter Notebook.  Tenga en cuenta que todos los fragmentos de código son genéricos y no específicos a cualquier servicio.

### <a name="running-limit-and-count-queries"></a>Ejecución de consultas LIMIT y COUNT
Del mismo modo que está familiarizado con SQL y Spark SQL, se va a comenzar con una consulta **LIMIT**:

![Consulta LIMIT de Spark](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

La siguiente consulta es una consulta **COUNT** sencilla y rápida:

![Consulta COUNT de Spark](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>Consulta GROUP BY
En el conjunto siguiente, se van a ejecutar consultas **GROUP BY** con facilidad en la base de datos de Cosmos DB:

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![Gráfico de consultas GROUP BY de Spark](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Consulta DISTINCT, ORDER BY
Esta es una consulta **DISTINCT, ORDER BY**:

![Gráfico de consultas GROUP BY de Spark](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

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
![Gráfico de los principales retrasos de Spark](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Cálculo del valor medio de los retrasos según las ciudades de destino cuyos vuelos salen de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![Gráfico de valores medios de retrasos de Spark](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>Pasos siguientes

Si aún no lo ha hecho, descargue el conector de Spark a Cosmos DB del repositorio de GitHub [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) y explore los recursos adicionales en el repositorio:

* [Ejemplos de agregaciones distribuidas](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Cuadernos y script de ejemplo](https://github.com/Azure/azure-documentdb-spark/tree/master/samples)

Es posible que también desee consultar [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guía de SQL, tramas de datos y conjuntos de datos en Apache Spark) y el artículo [Apache Spark en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).



