---
title: "Azure Cosmos DB: análisis de gráficos mediante Spark y Apache TinkerPop Gremlin | Microsoft Docs"
description: "Este artículo presenta instrucciones para configurar y ejecutar análisis de gráficos y cálculos en paralelo en Azure Cosmos DB con Spark y TinkerPop SparkGraphComputer."
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: d2ea692c5c353586cc2b653e57eca7ccb8c9c7ce
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: análisis de gráficos mediante Spark y Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) es el servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear bases de datos de documentos, de pares clave-valor y de gráficos, y realizar consultas en ellas. Todas las bases de datos se benefician de las funcionalidades de distribución global y escalado horizontal en Azure Cosmos DB. Azure Cosmos DB admite cargas de trabajo gráficas de procesamiento de transacciones en línea (OLTP) que usan [Apache TinkerPop Gremlin](graph-introduction.md).

[Spark](http://spark.apache.org/) es un proyecto de Apache Software Foundation que se centra en el procesamiento de datos OLAP (procesamiento analítico en línea) de uso general. Spark proporciona un modelo híbrido de computación distribuida basado en memoria/disco, similar al modelo de MapReduce de Hadoop. Puede implementar Apache Spark en la nube mediante [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Mediante la combinación de Azure Cosmos DB y Spark, puede ejecutar tanto cargas de trabajo OLTP como OLAP cuando use Gremlin. En este tutorial se muestra cómo ejecutar consultas de Gremlin en Azure Cosmos DB en un clúster de Azure HDInsight Spark.

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
* Clúster de Azure HDInsight Spark 2.0
* JDK 1.8+ (si no tiene JDK, ejecute `apt-get install default-jdk`.)
* Maven (si no tiene Maven, ejecute `apt-get install maven`.)
* Una suscripción de Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Para más información sobre cómo configurar un clúster Spark de Azure HDInsight, consulte [Aprovisionamiento de clústeres de HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Creación de una cuenta de base de datos de Azure Cosmos DB

En primer lugar, cree una cuenta de base de datos con la API Graph siguiendo estos pasos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Incorporación de una colección

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Obtención de Apache TinkerPop

Obtenga Apache TinkerPop mediante estos pasos:

1. Conéctese en remoto al nodo principal del clúster de HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Clone el código fuente de TinkerPop3, compílelo localmente e instálelo en la caché de Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Instale el complemento Spark-Gremlin. 

    a. La instalación del complemento se administra mediante Grape. Rellene la información de los repositorios de Grape para poder descargar el complemento y sus dependencias. 

      Cree el archivo de configuración de Grape si no está en `~/.groovy/grapeConfig.xml`. Use la configuración siguiente:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Inicie la consola de Gremlin `bin/gremlin.sh`.
        
    c. Instale el complemento Spark-Gremlin, versión 3.3.0-SNAPSHOT, que generó en los pasos anteriores:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Compruebe si `Hadoop-Gremlin` está activado con `:plugin list`. Deshabilite este complemento, ya que podría interferir con el complemento Spark-Gremlin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Preparación de las dependencias de TinkerPop3

Cuando se compiló TinkerPop3 en el paso anterior, el proceso también extrajo todas las dependencias de archivos JAR para Spark y Hadoop en el directorio de destino. Use lo archivos JAR anteriormente instalados con HDI y extraiga las dependencias adicionales solo si es necesario.

1. Vaya al directorio de destino de la consola de Gremlin en `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Mueva todos los archivos JAR que hay en `ext/` a `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Quite todas las bibliotecas JAR que aparezcan en `lib/` que no estén en la siguiente lista:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Obtención del conector de Spark para Azure Cosmos DB

1. Obtenga el conector de Spark para Azure Cosmos DB `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` y el SDK de Java para Cosmos DB `azure-documentdb-1.12.0.jar` en la página del [conector de Spark para Azure Cosmos DB en Github](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. También puede compilarlo localmente. Dado que la versión más reciente de Spark-Gremlin se generó con Spark 1.6.1 y no es compatible con la versión Spark 2.0.2 que actualmente se usa en el conector de Spark para Azure Cosmos DB, puede compilar el código de TinkerPop3 más reciente e instalar manualmente los archivos JAR. Haga lo siguiente:

    a. Clone el conector de Spark para Azure Cosmos DB.

    b. Compile TinkerPop3 (ya se hizo en pasos anteriores). Instale todos los archivos JAR de TinkerPop 3.3.0-SNAPSHOT localmente.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Actualice `tinkerpop.version` `azure-documentdb-spark/pom.xml` a `3.3.0-SNAPSHOT`.
    
    d. Compile con Maven. Los archivos JAR necesarios están ubicados en `target` y `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copie los archivos JAR mencionados anteriormente en un directorio local en ~/azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuya las dependencias en los nodos de trabajo de Spark 

1. Puesto que la transformación de datos de gráficos depende de TinkerPop3, debe distribuir las dependencias relacionadas a todos los nodos de trabajo de Spark.

2. Copie las dependencias de Gremlin mencionadas anteriormente, los archivos JAR del conector de Spark para CosmosDB y el SDK de Java para CosmosDB en los nodos de trabajo. Para ello, haga lo siguiente:

    a. Copie todos los archivos JAR en `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Obtenga la lista de todos los nodos de trabajo de Spark, que se encuentra en el panel de Ambari, en la lista `Spark2 Clients` de la sección `Spark2`.

    c. Copie ese directorio en cada uno de los nodos.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Configuración de las variables de entorno

1. Busque la versión HDP del clúster de Spark. Es el nombre del directorio en `/usr/hdp/` (por ejemplo, 2.5.4.2-7).

2. Establezca hdp.version para todos los nodos. En el panel de Ambari, vaya a la **sección de YARN** > **Configs** (Configuraciones)  > **Advanced** (Avanzadas) y luego haga lo siguiente: 
 
    a. En `Custom yarn-site`, agregue una nueva propiedad `hdp.version` con el valor de la versión de HDP en el nodo principal. 
     
    b. Guarde las configuraciones. Aparecen advertencias, pero puede ignorarlas. 
     
    c. Reinicie los servicios YARN y Oozie tal y como indican los iconos de notificación.

3. Establezca las siguientes variables de entorno en el nodo principal (reemplace los valores según corresponda):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Preparación de la configuración de grafos

1. Cree un archivo de configuración con los parámetros de conexión de Azure Cosmos DB y la configuración de Spark, y colóquelo en `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Actualice `spark.driver.extraClassPath` y `spark.executor.extraClassPath` para incluir el directorio de los archivos JAR que se han distribuido en el paso anterior; en este caso, `/home/sshuser/azure-documentdb-spark/*`.

3. Proporcione los detalles siguientes para Azure Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Carga del gráfico de TinkerPop y su almacenamiento en Azure Cosmos DB
Para demostrar cómo se conserva un gráfico en Azure Cosmos DB, en este ejemplo se usa el gráfico moderno TinkerPop predefinido por TinkerPop. El gráfico se almacena en formato de Kryo y se proporciona en el repositorio de TinkerPop.

1. Como está ejecutando Gremlin en modo YARN, debe permitir que los datos del gráfico estén disponibles en el sistema de archivos de Hadoop. Use los comandos siguientes para crear un directorio y copiar en él el archivo de gráficos local. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Actualice temporalmente el archivo `gremlin-spark.properties` para que use `GryoInputFormat` para leer el gráfico. También indique `inputLocation` como el directorio creado, como en el ejemplo siguiente:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Inicie la consola de Gremlin y, a continuación, cree los siguientes pasos de cálculo para conservar los datos en la colección de Azure Cosmos DB configurada:  

    a. Cree el gráfico `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Use SparkGraphComputer para escribir: `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. Desde el Explorador de datos, puede comprobar que los datos se han conservado en Azure Cosmos DB.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Carga del gráfico desde Azure Cosmos DB y ejecución de las consultas de Gremlin

1. Para cargar el gráfico, edite `gremlin-spark.properties` para establecer `graphReader` en `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Cargue el gráfico, recorra los datos y ejecute con ellos consultas de Gremlin siguiendo estos pasos:

    a. Inicie la consola de Gremlin `bin/gremlin.sh`.

    b. Cree el gráfico con la configuración `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Cree un recorrido de gráfico con SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Ejecute las siguientes consultas de gráfico de Gremlin:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Para ver un registro más detallado, configure el nivel de registro de `conf/log4j-console.properties` en un nivel más detallado.
>

## <a name="next-steps"></a>Pasos siguientes

En este artículo de inicio rápido, ha aprendido cómo trabajar con gráficos mediante la combinación de Azure Cosmos DB y Spark.

> [!div class="nextstepaction"]
